# Nginx / PHP docker image for development environment

A Docker Nginx / PHP-FPM image with some useful packages for development environments.
Do not use it for production.


## Configuration

Nginx, php5, Xdebug, composer, phpunit, phing

Nginx serve a phpinfo() page. To test it and obtain information about the configuration:

    docker run -d -p 80:80 -p 443:443 alexisno/nginx-php-dev

* `docker run ... alexisno/nginx-php-dev` Run Nginx and PHP-FPM in a new container
* `-d` Detached mode: run container in the background
* `-p 80:80 -p 443:443` Publish the container's ports 80 and 443 on the host so you can connect to the server

Open your brower at http://localhost/ and https://localhost/

Composer, phpunit and phing are available globally.

PHP is configured to send mail via a [`alexisno/mailcatcher-dev`](https://github.com/AlexisNo/docker-mailcatcher-dev) container.
Launch a [`alexisno/mailcatcher-dev`](https://github.com/AlexisNo/docker-mailcatcher-dev) container and use `--link mailcatcher:my-mailcatcher-container`.

Xdebug is configurated to accept any connection. Just send the appropriate request parameters.


## Common usage

This image should be used as a basic image for any project.

* Create an apache virtualhost for the development environment.
* Create a Dockerfile with your project dependencies and add the virtualhost to it's configuration.
* Create you own image with `docker build` or `docker-compose build`.

Run your new image with a command similar to this:

    docker run -d -p 80:80 -p 443:443 -v /path/to/your/project/sources:/var/www/project-name -v /path/to/project/data/apache/logs:/var/log/apache2 your-image-tag


## Self signed certificate

The image comes with a `gencert` command to generate self signed certificates.

Usage in child Dockerfile:

    RUN gencert <domain>

VirtualHost configuration:

    server {
      listen 80;
      listen 443 ssl;

      ssl_certificate     /etc/ssl/certs/<domain>.crt;
      ssl_certificate_key /etc/ssl/private/<domain>.key;

      server_name <domain>;

      # Complete with your configuration
      # ...
    }

Replace `<domain>` with the hostname you use for the development environment.
