

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
    $books = DB::select('SELECT * FROM books where author LIKE '%Scott%');
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

## Authentication

You can have artisan do setup the authentication framework or manually do it by putting routes for login/register in your routes.php file and then having blades in \resources\views\auth (login.blade.php) & (register.blade.php) - I like the manual method best but if you want the automated version:

```php
php artisan make:auth
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

# Using a pre-processor (Gulp)

This example uses Gulp and Laravel Elixir.

## Install Gulp (I only install locally)

```bash
$ npm install gulp --save-dev
```

## Install Laravel Elixir  
There is a package.json in the root of Laravel directory structure with defaults for Elixir and Webpack. Doing an npm install
will install those.

```bash
$ npm install
```

## Run Gulp

Since Gulp is installed only to the application directory you need to point to the directory.

```bash
$ .node_modules/.bin/gulp
```

to have Gulp watch

```bash
$ ./node_modules/.bin/gulp watch
```

To minify CSS & JavaScript
```bash
$ ./node_modules/.bin/gulp --production
```
