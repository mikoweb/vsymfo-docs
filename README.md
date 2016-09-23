# Virtus Symfony CMF

## Instalation

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
