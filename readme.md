# DWA Cheat Sheet

These notes assume you have knowledge or PHP, Laravel, & Git. I use these as
refresher notes not detailed instructions.

## Create a Laravel Application

Change to the directory you want the appliction to be under. So if you want
/var/www/htdocs to be the parent directory.

```
cd /var/www/htdocs
composer create-project laravel/laravel myapplication --prefer-dist
```

N.B.

I don't do the above to create projects any more, I currently do the following.

$ composer global require "laravel/installer"

Add ~/.composer/vendor/bin/laravel to your path (sorry this is OS specific)

To create a new laravel app.

$ laravel new 'app_directory'

## Permissions

You may have to change the remote server permissions. I have not had to do this step.

Make the user www-data own the storage directory and all sub-directories and files
```bash
cd /var/www/htdocs/newapp
sudo chown -R www-data storage
sudo chown -R www-data bootstrap/cache
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

## Development vs Production

Best practice is to develop only on your...well development server. Push changes to
github, or your local git server, and pull from that server to your production server.

Create the project locally then push to github

### Development Server
#### Push to github

Copy your local/development copy to the git server of your choice.

```bash
git push github master
```

### Production Server

Clone the repository to your production server using one of the methods below.

#### Private Repositories without ssh key (via https)
```bash
sudo git clone https://yourname:yourpassword@github.com/sfavorite/personal
```

#### Public Repositories without ssh key (via https)
```bash
sudo git clone https://github.com/sfavorite/personal
```

#### Repositories with ssh key (public or private via ssh)
```bash
sudo git clone git@github.com/sfavorite/personal
```

Once you have the repository on your production server

#### Pull changes from github to your local and then do a composer update
```
git remote add github git@github.com:sfavorite/your_project
git pull github master
```
and do updates with
```
sudo git pull origin master
composer install
```


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

# Javascript

Use yarn as your javascript package manager. To install vue-router with yarn
```bash
$ yarn install
$ yarn add vue-router
```

# Controllers

To create a basic controller using artisan:

```bash
$ php artisan make:controller MyNewController
```

If you want the controller to have all of the CRUD verb (index, update, create, store, etc...) add the --resource flag to the end.

To create a CRUD contoller
```bash
$ php artisan make:controller MyNewController --resource
```

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

You can also add the --create flag to your migration which will add the table to your
migration for you. This should only be done with a new table

```bash
$ php artisan make:migration create_books_table --create=books
```

For an existing table that you want to modify you can use the --table flag.

```bash
$ php aritsan make:migration add_category_id_to_books --table=books
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

# Rollback The Last Migration Operation (all migrations ran in the last batch)

```bash
php artisan migrate:rollback
```

# Seeders
First create a seed table file.

```bash
php artisan make:seeder BookTableSeeder
```
Now fill out the BooksTableSeeder.php file under database\seeds


To populate the database run.

```bash
php artisan db:seed
```

## Roll back and re-run migrations with seeds

```bash
php artisan migrate:refresh --seed
```

# Database

## QueryBuilder

The DB facade is the QueryBuilder.

To use the DB facade you can access it with a slash in front of DB: \DB::table...
or add a use statement.

### Read
```php
    // Use the QueryBuilder to get all the books
    $books = DB::table('books')->get();

    // Output the results
    foreach($books as $book) {
        echo $book->title . '<br>';
    }
```

### Read 2 - where

```php
    $books = \DB::table('books')->where('author', 'LIKE', '%Scott%')->get();
    $books = \DB::table('books')->where('author', 'LIKE', '%Scott%')->orWhere('published', '>', 1950 )->get();

```

### Create
```php
DB::table('books')->insert([
    'created_at' => \Carbon\Carbon::now()->toDateTimeString(),
    'updated_at' => \Carbon\Carbon::now()->toDateTimeString(),
    'title' => 'The Great Gatsby',
    'author' => 'F. Scott Fitzgerald',
    'published' => 1925,
    'cover' => 'http://img2.imagesbn.com/p/9780743273565_p0_v4_s114x166.JPG',
    'purchase_link' => 'http://www.barnesandnoble.com/w/the-great-gatsby-francis-scott-fitzgerald/1116668135?ean=9780743273565',
]);        
```

### Run native SQL commands
```php
    $books = DB::select('SELECT * FROM books where author LIKE '%Scott%'');
```

## Models and Eloquent ORM


Make a model, which will interface with the table you want to work with.

```php
php artisan make:model Book
```

### Create
In the controller (or route.php)

```php
    # Instantiate a new Book Model object
    $book = new \App\Book();
    # Set the parameters
    # Note how each parameter corresponds to a field in the table
    $book->title = 'Harry Potter';
    $book->author = 'J.K. Rowling';
    $book->published = 1997;
    $book->cover = 'http://prodimage.images-bn.com/pimages/9780590353427_p0_v1_s484x700.jpg';
    $book->purchase_link = 'http://www.barnesandnoble.com/w/harry-potter-and-the-sorcerers-stone-j-k-rowling/1100036321?ean=9780590353427';
    # Invoke the Eloquent save() method
    # This will generate a new row in the `books` table, with the above data
    $book->save();
    return 'Added: '.$book->title;
 ```
### Read

```php
    $books = \App\Book::all();
    $this->printBooks($books);
```

### Update

```php
    # First get a book to update
    $book = \App\Book::where('author', 'LIKE', '%Scott%')->first();
    # If we found the book, update it
    if($book) {
        # Give it a different title
        $book->title = 'The Really Great Gatsby';
        # Save the changes
        $book->save();
        echo "Update complete; check the database to see if your update worked...";
    }
    else {
        echo "Book not found, can't update.";
    }
```

### Delete

```php
    # First get a book to delete
    $book = \App\Book::where('author', 'LIKE', '%Scott%')->first();
    # If we found the book, delete it
    if($book) {
      # Goodbye!
      $book->delete();
      return "Deletion complete; check the database to see if it worked...";
    }
    else {
      return "Can't delete - Book not found.";
    }
```

## Authentication (Laravel 6)

You can have artisan do setup the authentication framework or manually do it by putting routes for login/register in your routes.php file and then having blades in \resources\views\auth (login.blade.php) & (register.blade.php) - I like the manual method best but if you want the automated version:

To create the scaffolding
```bash
composer require laravel/ui --dev

```

To create the views
```bash
php artisan ui vue --auth
```

## Database

When making a Model with table relationships we need to define both sides of the relationship with one table being the parent the other the child. In the authors/books model the author is the parent and the book is the child.

There are two steps in the Laravel framework to create databases & relationships: setup the foreign keys in the migrations and establish the relationship in the Model .

1) In the migration you set the column and the foreign key it points to.

```php
Schema::table('books', function (Blueprint $table) {
    # Create a column to hold the key in the child table.
    $table->integer('author_id')->unsigned();

    # Associate the new column, author_id, to the id in the authors table.
    $table->foreign('author_id')->references('id')->on('authors');
});
```

2) In the Model set the parent child relationship so queries are executed correctly.

When defining models in Laravel the parent side (authors in the example) needs a 'hasOne', 'hasMany', 'etc'.

When defining the child side we use 'belongsTo', 'belongsToMany', 'etc'.

So if you have two tables authors and books the authors table will need a 'hasMany' and the Book model will need a belongsTo.

1) In the Parent (Author Model)*

```php
    public function book() {
        return $this->hasMany('\Foobooks\Book');
    }
```

2) In the Child (Book Model)*

```php
    public function author() {
        return $this->belongsTo('\Foobooks\Author');
    }
```

N.B: In a many to many both sides use belongsToMany.


# Vue.js

I had to put the #app element in the load event handler.  

```javascript
window.addEventListener('load', function() {
    const app = new Vue({
        el: '#app'
    });
})
```

# Using Tinker

Run tinker

```bash
$ php artisan tinker
```

## Create a 'user'

```tinker
>>> factory(App\User::class)->create()
```

Output
```
=> App\User {#722
     name: "Maxine Kunze",
     email: "gbauch@example.net",
     updated_at: "2017-06-28 12:02:31",
     created_at: "2017-06-28 12:02:31",
     id: 2,
   }
```

Create a 100 users
```tinker
>>> factory('App\User', 100)->create()
```


## Working with the user

```tinker
>>> App\User::find(2)
```

# Laravel Mix

Laravel Mix helps to take the pain out of webpack. Note that Laravel Mix needs to be run with npm not yarn.

## npm

Run npm install to install all the javascript files in package.json

```bash
$ npm install
```

## Files

webpack.mix.js - the configuration file for laravel mix
webpack.config.js - configuration file for webpack. This file is stored in node_modules -> laravel-mix -> setup. This file normally won't need to be changed.
package.json - scripts are placed in the...well scripts section.

## Some configuration options

Files placed in an array will be compiled to on file. Adding .extract will put vendor files into vendor.js
Create source maps with .sourceMaps()

mix.js(['resources/assets/js/app.js', 'resources/assets/js/forum.js'], 'public/js').extract(['vue', 'axios', 'jquery', 'bootstrap'])
    .js('resources/assets/js/shared_state.js', 'public/js')
   .sass('resources/assets/sass/app.scss', 'public/css').sourceMaps()
   .version(); // Add versioning to the files

## Running

To run once and created non-minified javascript files
```bash
// Run all Mix tasks...
$ npm run dev
```

To run and watch for changes, recompiling your js/css files
```bash
$ npm run watch
```
To run for production (minified code created)
```bash
// Run all Mix tasks and minify output...
$ npm run prod
```

# Custom validation

To make custom validation rules like alpha and spaces.

To AppServiceProvider.php add the line

```
use Illuminate\Support\Facades\Validator;
```

To the boot parameter add your custom validator

```
public function boot()
{
    //Add this custom validation rule.
    Validator::extend('alpha_spaces', function ($attribute, $value, $parameters, $validator) {
        // This will only accept alpha and spaces.
        // If you want to accept hyphens use: /^[\pL\s-]+$/u.
        return preg_match('/^[\pL\s]+$/u', $value);
    });
}
```

In validation.php add an error message to match your new validation

```
'alpha_spaces'         => 'The :attribute may only contain letters and spaces.',
```    

Add your shiny new validation to a controller.

```
$validated = Validator::make($request->all(), [
    'key' => 'alpha_spaces|Required',
]);
```
