docker-hosts-updater
----------
Automatic update `/etc/hosts` on start/stop containers.

Requirements
-----
* **Native linux**  
_This tool has no effect on macOS or windows, because docker on these OS run in 
VM and you can't directly access from host to each container via ip.
Yet you can pass traffic through loadbalancer, see section above._  

Usage
-----
Start up `docker-hosts-updater`:

```bash
$ docker run -d --restart=always \
    --name docker-hosts-updater \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /etc/hosts:/opt/hosts \
    ghcr.io/soundwaveuwu/docker-hosts-updater
```
      
Try to ping from host
```bash
$ ping <container_name>
```

Default hosts
-----
By default this program adds records with container's name and hostname. 
To disable it you can use environments `FALLBACK_CONTAINER_HOSTNAME` and `FALLBACK_CONTAINER_NAME` and set them to `False`:    
```bash
$ docker run -d --restart=always \
    --name docker-hosts-updater \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /etc/hosts:/opt/hosts \
    -e FALLBACK_CONTAINER_HOSTNAME=False \
    -e FALLBACK_CONTAINER_NAME=False \
    ghcr.io/soundwaveuwu/docker-hosts-updater
```

Multiple Hosts
-----
You can add multiple hosts, separate them by semicolon:

```bash
$ docker run --label ru.grachevko.dhu="nginx.local;nginx.ru" nginx
$ ping nginx.local
$ ping nginx.ru
```

Subdomains
-----
Add multiple subdomains by using brackets `{www,api}.nginx.local`:

```bash
$ docker run -d --label ru.grachevko.dhu="{www,api}.nginx.local" nginx
$ ping nginx.local
$ ping www.nginx.local
$ ping api.nginx.local
```

Priority
----
If you want to run two containers with same hosts and want one override another, 
just add priority after colon:

```bash
$ docker run -d --label ru.grachevko.dhu="nginx.local" nginx
$ docker run -d --label ru.grachevko.dhu="nginx.local:10" nginx
$ ping nginx.local
```
Container with greater priority will be used. Default priority 0. 
If priority is the same then early created container will be used.

Load Balancer
----
In order to pass the traffic through the loadbalancer you should define container's name or valid IPv4. 
Just add one more colon and container name after it.
```bash
$ docker run -d --name lb nginx
$ docker run -d --label ru.grachevko.dhu="nginx1.local:0:lb" nginx
$ docker run -d --label ru.grachevko.dhu="nginx2.local:0:127.0.0.1" nginx
$ ping nginx1.local // ip of lb
$ ping nginx2.local // ip of lb
```
Keep in mind, loadbalancer container must have fixed name.
