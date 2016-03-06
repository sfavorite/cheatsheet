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
