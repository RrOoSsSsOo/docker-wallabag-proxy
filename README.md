# docker-wallabag-proxy
Nginx settings that permits to proxy a wallabag docker

## How it works
Compared to original `wallabag/docker` (https://github.com/wallabag/docker) the only change is these new lines into `nginx.conf` (`/etc/nginx/nginx.conf` of docker)
```
...
server {
    listen 81;

    location / {
        proxy_set_header Accept-Encoding "";
        proxy_pass http://localhost:80/;
        sub_filter_types *;
        sub_filter_once off;
        sub_filter "https://your-wallabag-url-instance.com/" "/";
    }
}
...
```
They create a new server for remote proxy on port 81, leaving untouched the original service on port 80, but rewrite/overwriting default `https://your-wallabag-url-instance.com` (`SYMFONY__ENV__DOMAIN_NAME` parameter) from all files (.html, .css, ...)

## Limitation
Wallabag (and Symfony behind it) redirect from default address to `/login` page.

If you try to reach the proxed `your-ip:your-port` the redirect is made to `localhost/login` that is not reachable, you need to: 
1. Connect to `your-ip:your-port/login` and authenticate
2. Connect to `your-ip:your-port` and using Wallabag

## Easy guide for Synology NAS docker
Into File Manager create the folder structure `docker/wallabag/etc/nginx`

Create/upload the file `nginx.conf` of this GitHub repository.

![Synology-guide-1](https://user-images.githubusercontent.com/1734343/148547046-caf46941-88f5-4746-aa96-deaa64189b4d.png)

Install the `docker` app, downoload `wallabag/wallabag` from Registry and double click on it in Image.

On `Volume` tab click `Add File` and select from File Manager `docker/wallabag/etc/nginx/nginx.conf` and write `/etc/nginx/nginx.conf` into `Mount path`.

![Synology-guide-2](https://user-images.githubusercontent.com/1734343/148547300-db5949bf-eb32-4c53-992d-6e435f16555b.png)

<sub>PS: the mount of folder `docker/wallabag/var/www/wallabag/data` to mount data `/var/www/wallabag/data` permits the easy upgrade of docker new image versions mantaining databases and users password.</sub>

On `Port Settings` tab click `Add` and specify a `Local Port` for `Container Port` `81`.

![Synology-guide-3](https://user-images.githubusercontent.com/1734343/148547803-fa0925cb-8253-44b6-9905-c66ef1f1c684.png)
