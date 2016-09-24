# Virtus Symfony CMF

## Installation

    composer install
    npm install
    webui install
    webui grunt
    grunt copy-panel-theme
    php bin/console doctrine:schema:update --force
    php bin/console doctrine:fixtures:load --append --fixtures=vendor/mikoweb/vsymfo-panel-bundle/DataFixtures/Install
    php bin/console doctrine:fixtures:load --append --fixtures=vendor/mikoweb/vsymfo-user-bundle/DataFixtures/Install
    php bin/console doctrine:fixtures:load --append --fixtures=src/App/PanelBundle/DataFixtures/Install
    php bin/console document:assets --base_path=/

### Related links

- [Download Composer](https://getcomposer.org/download/)
- [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/)
- [WebUI CLI Installation](https://github.com/mikoweb/node-webui-installer)
- [Installing the Grunt CLI](http://gruntjs.com/getting-started#installing-the-cli)
- [Install Bower](https://bower.io/#install-bower)
- [Symfony Documentation](http://symfony.com/doc/current/index.html)

## Documentation

- [CRUD](CRUD.md)
