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
