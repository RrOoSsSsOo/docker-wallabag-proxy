# docker-wallabag-proxy
AppKernel.php file that permits to Wallabag to ignore 'domain_name' / 'SYMFONY__ENV__DOMAIN_NAME' parameter (`https://your-wallabag-url-instance.com`) and provide service from any domain or behind any proxy.

## How it works
Compared to original `wallabag/wallabag` (https://github.com/wallabag/wallabag) and `wallabag/docker` (https://registry.hub.docker.com/r/wallabag/wallabag) the only change is a comment into `AppKernel.php` (`app/AppKernel.php` of wallabag, `/var/www/wallabag/app/AppKernel.php` of wallabag docker)
```diff
...
        $loader->load(function ($container) {
            if ($container->getParameter('use_webpack_dev_server')) {
                $container->loadFromExtension('framework', [
                   'assets' => [
                       'base_url' => 'http://localhost:8080/',
                   ],
               ]);
            } else {
                $container->loadFromExtension('framework', [
                    'assets' => [
-                        'base_url' => $container->getParameter('domain_name'),
+                        // 'base_url' => $container->getParameter('domain_name'),
                    ],
                ]);
            }
        });
...
```
The standard wallabag redirect to (defaul) `https://your-wallabag-url-instance.com` (or custom `domain_name` / `SYMFONY__ENV__DOMAIN_NAME` parameter) after connetion to the service. Also all URLs of images and CSS contains the same domain.

With this comment Symfony behind wallabag doesn't use a specific `base_url` but 'relative' to the domain that expose the service. 

Even if the service is behind one or more proxies there will be no problems.

## Easy guide for Synology NAS docker
Into File Manager create the folder structure `docker/wallabag/var/www/wallabag/app`

Create/upload the file `AppKernel.php` of this GitHub repository.

![Synology-guide-1](https://user-images.githubusercontent.com/1734343/148996724-e4796cad-e246-4bd5-901f-72332a3a1ce0.png)

Install the `docker` app, downoload `wallabag/wallabag` from Registry and double click on it in Image.

On `Volume` tab click `Add File` and select from File Manager `docker/wallabag/var/www/wallabag/app/AppKernel.php` and write `/var/www/wallabag/app/AppKernel.php` into `Mount path`. 

The docker will see, and use, the modified file.

![Synology-guide-2](https://user-images.githubusercontent.com/1734343/148997416-b719f008-4000-4757-aea5-152731398076.png)

<sub>PS: the mount of folder `docker/wallabag/var/www/wallabag/data` to mount data `/var/www/wallabag/data` permits the easy upgrade of docker new image versions mantaining databases and users password.</sub>

On `Port Settings` tab click `Add` and specify a `Local Port` for `Container Port` `80`.

![Synology-guide-3](https://user-images.githubusercontent.com/1734343/148997871-0616193a-1b58-4ae6-89e8-4dcc46c61e5e.png)

You can now provide a wallabag service for any domain. 
