# Virtus Symfony CMF

## Installation

    npm install
    composer install
    php bin/console doctrine:schema:update --force
    php bin/console doctrine:fixtures:load --append --fixtures=vendor/mikoweb/vsymfo-panel-bundle/DataFixtures/Install
    php bin/console doctrine:fixtures:load --append --fixtures=vendor/mikoweb/vsymfo-user-bundle/DataFixtures/Install
    php bin/console doctrine:fixtures:load --append --fixtures=src/App/PanelBundle/DataFixtures/Install
    php bin/console vsymfo:theme:install vsymfo-panel-theme backend_panel
    php bin/console vsymfo:js:build --env=prod
    php bin/console vsymfo:js:install --env=prod
    php bin/console document:assets --base_path=/

### Related links

- [Download Composer](https://getcomposer.org/download/)
- [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/)
- [WebUI CLI Installation](https://github.com/mikoweb/node-webui-installer)
- [Symfony Documentation](http://symfony.com/doc/current/index.html)

## Documentation

- [How to use CRUD](CRUD.md)
- [The Manager Service](Manager-Service.md)
- [Security](Security.md)
