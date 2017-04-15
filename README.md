docker-hosts-updater
----------

Automatic update `/etc/hosts` on start/stop containers which defined `hostname` option.

Usage
-----

Start up `docker-hosts-updater`:

    % docker run -d --restart=always --name docker-hosts-updater -v /var/run/docker.sock:/var/run/docker.sock -v /etc/hosts:/opt/hosts grachev/docker-hosts-updater
    
Start containers with `hostname` option

    % docker run -d --hostname nginx.local nginx
      
Try to ping from host

    % ping nginx.local

Subdomains
-----
Add label `subdomains` with one or more subdomains separated by space:

    % docker run -d --hostname nginx.local --label subdomains="www api" nginx
    % ping nginx.local
    % ping www.nginx.local
    % ping api.nginx.local
