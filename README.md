laravel-runtime
===========

A prebuilt runtime with all the necessary dependencies to run a basic Laravel project. This repo assumes:

* That you're using pgsql/PostgreSQL as your database
* Your project includes Laravel Horizon (for the Worker image)

This repo generates a Docker image that includes:

* In the App image:
  * The official PHP 8.3 Apache image
  * PHP Extensions: pgsql pdo pdo_pgsql pdo_mysql mysqli zip pcntl bcmath curl dom mbstring gd redis ssh2
  * Latest version of composer pre-installed
  * An Apache site defintion that enables URL rewriting
  * `openssh-client` for the `ssh` and `scp` commandline utilities

* In the Worker image:
  * The official PHP 8.3 CLI image
  * PHP Extensions: gd pdo_pgsql zip pcntl bcmath curl dom mbstring redis ssh2
  * Supervisor for running background jobs + a config file to run Horizon
  * `artisan schedule:run` every minute via Supervisor
  * `openssh-client` for the `ssh` and `scp` commandline utilities

* In the Omnibus image:
  * The App + Worker images combined
  * Includes apache, php-fpm and supervisor for Horizon

These images publish to a public repository.

## Sample: Dockerfile for App container

Use a Dockerfile like this to build your app container:

```Dockerfile
FROM ghcr.io/woganmay/laravel-runtime-app:latest

WORKDIR /var/www/html

# Copy application code
COPY --chown=runtime . /var/www/html

# Run Apache by default
CMD ["apache2-foreground"]
```

This build assumes that the Dockerfile is relative to your project root, so that index.php copies into /var/www/html/ on the container.

## Sample: Dockerfile for Worker/Omnibus container

```Dockerfile
FROM ghcr.io/woganmay/laravel-runtime-worker:latest

WORKDIR /var/www/html

# Copy the application code
COPY --chown=runtime . /var/www/html
```

This build assumes that the Dockerfile is relative to your project root, so that index.php copies into /var/www/html/ on the container.