# Magento 2 Docker

A collection of Docker images for running Magento 2 through nginx and on the command line.

## Quick Start

    cp composer.env.sample composer.env
    # ..put the correct tokens into composer.env

    mkdir magento

    docker-compose run cli magento-installer
    docker-compose up -d
    docker-compose restart

## Configuration

Configuration is driven through environment variables.  A comprehensive list of the environment variables used can be found in each `Dockerfile` and the commands in each `bin/` directory.

* `PHP_MEMORY_LIMIT` - The memory limit to be set in the `php.ini`
* `MAGENTO_RUN_MODE` - Valid values, as defined in `Magento\Framework\App\State`: `developer`, `production`, `default`.
* `MAGENTO_ROOT` - The directory to which Magento should be installed (defaults to `/var/www/magento`)
* `COMPOSER_GITHUB_TOKEN` - Your [GitHub OAuth token](https://getcomposer.org/doc/articles/troubleshooting.md#api-rate-limit-and-oauth-tokens), should it be needed
* `COMPOSER_MAGENTO_USERNAME` - Your Magento Connect public authentication key ([how to get](http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html))
* `COMPOSER_MAGENTO_PASSWORD` - Your Magento Connect private authentication key
* `DEBUG` - Toggles tracing in the bash commands when exectued; nothing to do with Magento`
* `PHP_ENABLE_XDEBUG` - When set to `true` it will include the Xdebug ini file as part of the PHP configuration, turning it on. It's recommended to only switch this on when you need it as it will slow down the application.
* `UPDATE_UID_GID` - If this is set to "true" then the uid and gid of `www-data` will be modified in the container to match the values on the mounted folders.  This seems to be necessary to work around virtualbox issues on OSX.

A sample `docker-compose.yml` is provided in this repository.

## CLI Usage

A number of commands are baked into the image and are available on the `$PATH`. These are:

* `magento-command` - Provides a user-safe wrapper around the `bin/magento` command.
* `magento-installer` - Installs and configures Magento into the directory defined in the `$MAGENTO_ROOT` environment variable.

It's recommended that you mount an external folder to `/root/.composer/cache`, otherwise you'll be waiting all day for Magento to download every time the container is booted.

CLI commands can be triggered by running:

    docker-compose run cli magento-installer

Shell access to a CLI container can be triggered by running:

    docker-compose run cli bash

## Sendmail

All images have sendmail installed for emails, however it is not enabled by default. To enable sendmail, use the following environment variable:

    ENABLE_SENDMAIL=true

*Note:* If sendmail has been enabled, make sure the container has a hostname assigned using the `hostname` field in `docker-compose.yml` or `--hostname` parameter for `docker run`. If the container does not have a hostname set, sendmail will attempt to discover the hostname on startup, blocking for a prolonged period of time.

## Implementation Notes

* In order to achieve a sane environment for executing commands in, a `docker-environment` script is included as the `ENTRYPOINT` in the container.

## Credits

Thanks to [Mark Shust](https://twitter.com/markshust) for his work on [docker-magento2-php](https://github.com/mageinferno/docker-magento2-php) that was used as a basis for this implementation.  You solved a lot of the problems so I didn't need to!
