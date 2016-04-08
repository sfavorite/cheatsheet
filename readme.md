

# DWA Cheat Sheet

These notes assume you have knowledge or PHP, Laravel, & Git. I use these as
refresher notes not detailed instructions.

## Create An Application

Change to the directory you want the appliction to be under. So if you want
/var/www/htdocs to be the parent directory.

```
cd /var/www/htdocs
composer create-project laravel/laravel myapplication --prefer-dist
```

## Rename APP

To rename your app from...well 'app', change to the application root
(i.g. /var/www/htdocs/p3).
```
php artisan app:name myappname
```

## APP_KEY

You may need/want to generate an APP_KEY for your environment file.
```
php artisan key:generate
```

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

## Updating vs Installing
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
## Packages With Composer

To search for a package with composer
```
composer search debugbar
```

Install a package with composer


```
composer require programmer/his-cool-app
composer require barryvdh/laravel-debugbar
```
You can now check composer.json and will see the new package is included.



## Manually Add A Package

In composer.json in "require" or "require-dev" if for development only manually add the package.

```
"require": {
    "php": ">=5.5.9",
    "laravel/framework": "5.2.*",
    "barryvdh/laravel-debugbar": "^2.2",
    ***"rych/random": "dev-master"***
},
```

Now you must update composer.

```
composer update
```

You can use '$random = new Rych\Random\Random()' add 'use \Rych\Random\Random' at the top of the routes.php file or create an alias in app.php to use Random

Which option is best?
* Using the class once
    * Go with the full namespace $random = new Rych\Random\Random();
* Using class multiple times in a single file
    * Set use \Rych\Random\Random; at the top of the file.
* Using the class multiple times throughout your application
    * Create an alias

### Example Using The New Package

When installing debugbar copy:
Barryvdh\Debugbar\ServiceProvider::class,

into the file **config/app.php** and in the section **'providers' => [**
at the end paste the Barryvdh\Debugbar\ServiceProvider::class,

Next create an alias 'Debugbar' => Barryvdh\Debugbar\Facade::class

In the same app.php file in the section **'aliases' => [** add the line 'Debugbar' => Barryvdh\Debugbar\Facade::class

## Create A Package alias

In 'app.php' under 'aliases' => add

```
'Debugbar' => Barry\Debugbar\Facade::class,
'Random' => 'Rych\Random\Random',

```
Notice that Rych\Random\Random is in single quotes. Since it is not a class you must put quotes around this line.

## Repositories

General PHP

https://packagist.org

Laravel Specific

https://packalyst.com


# Views and Blades

Setup the route.

```
Route::get('book/show/{title}', 'BookController@getShow');
```

Setup the controller.
```
public function getShow($title) {
        return view('books.show')->with('title', $title);

}
```

Now you need a blade.php file. Go to resources->views and create a directory. In this example we use **books**.

Next we need a file. Since we are looking for a view named 'books.show' (see above) we have the first part books (Laravel will look in the directory books) we create a file called 'show.blade.php'. Laravel will automatically look in the resources->views directory.


# Custom configuration of Packages

Create a copy config in app/config
```
php artisan vendor:publish
```

Add to .env

DEBUGBAR_ENABLED = True


# Create a migration using artisan

```bash
$ php artisan make:migration create_books_table
```

# Run migrations

```bash
$ php artisan migrate
```

# Start Fresh on DB

This will reset and re-run your migrations...giving you a clean DB.
It will do all the down methods and then all the up methods.

```bash
$ php artisan migrate:refresh
```

# Do all migration downs

```bash
$ php artisan migrate:rollback
```

# Seeders
First create a seed table file.

```bash
php artisan make:seeder BooksTableSeeder
```
Now fill out the BooksTableSeeder.php file under database\seeds
To populate the database run.
```bash
php artisan db:seed
```

## Roll back and re-run migartions with seeds

```bash
php artisan migrate:refresh --seed
```
