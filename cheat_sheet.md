# DWA Cheat Sheet

## Setup application


## Git

Start a repository
```
git init
git add --all
git commit -m 'Initial Commit'
git remote add github git@github.com:sfavorite/stuff.git
git push github master
```

To view remotes
```
git remote -v
```

## Github







# Packages
When adding packages on the development server do 'composer update' this will read
composer.json and resolve "fuzzy" versions and update composer.lock with the actual
version used (no fuzzy part).

Development server
```
composer update
```

On the production server do 'compsoer install' and it will get the packages and versions from composer.lock so the version matches what was used in development.

Production server
```
composer install
```
## Composer

To search for a package with composer
```
composer search debugbar
```

Install a package with composer


```
composer require programmer/his-app
composer require barryvdh/laravel-debugbar
```
You can now check composer.json and will see the new package is included.

## Manually add package

In composer.json in "require" or "require-dev" if for development only manually add the package.

```
"require": {
    "php": ">=5.5.9",
    "laravel/framework": "5.2.*",
    "barryvdh/laravel-debugbar": "^2.2",
    ***"rych/random": "dev-master"***
},
'''

Now you must update composer.

```
composer update
```

### Example using the new package

When installing debugbar copy:
Barryvdh\Debugbar\ServiceProvider::class,

into the file **config/app.php** and in the section **'providers' => [**
at the end paste the Barryvdh\Debugbar\ServiceProvider::class,

Next create an alias 'Debugbar' => Barryvdh\Debugbar\Facade::class

In the same app.php file in the section **'aliases' => [** add the line 'Debugbar' => Barryvdh\Debugbar\Facade::class


## Repositories

General PHP

https://packagist.org

Laravel Specific

https://packalyst.com
