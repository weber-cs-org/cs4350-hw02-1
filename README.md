# Homework 02 - Points **100**

## Instructions

### 00 - Setup

#### Create `rest-api` project

1. **Accept** the Homework #2 in Canvas.
2. **Open** a terminal to the root directory of the assignment.
3. **Execute** `composer create-project --prefer-dist laravel/lumen rest-api`.
4. **Execute** `cd rest-api`.
5. **Execute** `php -S localhost:8000 -t public`.
5. **Open** a browser of your choice.
6. **Navigate** to `http://localhost:8080`.
7. Do you see `Lumen (5.8.12) (Laravel Components 5.8.*)`?
8. Use <CTRL-C> to exit `php -S localhost:8000 -t public`.

### 01 - Database and Migrations

You'll be building a ReST API that supports CRUD (Create, Read, Update, Delete) operations.
Here are the endpoints:

- GET /products - **Read** all product resources
- GET /products/{id} - **Read** a single product resource
- POST /products - **Create** a new product resource
- PUT /products/{id} - **Update** a single product resource
- DELETE /products/{id} - **Delete** a single product resource

A database will be needed.

#### Migrations

- **Execute** `php artisan make:migration create_products_table`
- Open hw020 > rest-api > database > migrations > `xxxx_xx_xx_xxxxxx_create_products_table.php`
- Paste the following text
```
$table->string('name');
$table->integer('price');
$table->longText('description');
```
into the function `public function up()` between `$table->bigIncrements('id');` and `$table->timestamps();`.
- Edit the `.env` with your specific database configuration.
- Run the command `php artisan migrate`.

### 02 - Models, Controllers and Routes

#### Models

1. **Edit** the file `app.php` in the `bootstrap`.
2. **Uncomment** lines 24 and 26, `$app->withFacades();` and `$app->withEloquent();`.
3. **Create** a file named `Product.php` in `app/` and paste this code:
```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
Class Product extends Model 
{
protected $fillable = ['name', 'price', 'description'];
}
```

#### Controllers

1. **Create** a file named `ProductController.php` in the `app\Http\Controllers` directory.
2. **Add** the following code:
```php
<?php
namespace App\Http\Controllers;
use App\Product;
use Illuminate\Http\Request;
class ProductController extends Controller
{
    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function index()
    {
     
     $products = Product::all();
     return response()->json($products);
    }
     public function create(Request $request)
     {
        $product = new Product;
       $product->name= $request->name;
       $product->price = $request->price;
       $product->description= $request->description;
       
       $product->save();
       return response()->json($product);
     }
     public function show($id)
     {
        $product = Product::find($id);
        return response()->json($product);
     }
     public function update(Request $request, $id)
     { 
        $product= Product::find($id);
        
        $product->name = $request->input('name');
        $product->price = $request->input('price');
        $product->description = $request->input('description');
        $product->save();
        return response()->json($product);
     }
     public function destroy($id)
     {
        $product = Product::find($id);
        $product->delete();
         return response()->json('product removed successfully');
     }
    }
```
- `index()` returns all products as `JSON` formatted response.
- `create()` creates a new `product` and returns a `JSON` formatted response.
- `show()` returns a single product based on the `id`.  `JSON` formatted response.
- `update` updates a product based on the `id`.
-`delete` removes a product based on the `id` and returns a `success` message.


### 03 - Database Seeding

#### Seed the Database

To be able to tests the `endpoints` the database should have some data in it.  Putting data in a database is called `seeding` it.

1. **Run** at the command prompt `php artisan make:seed ProductsTableSeeder`.
2. **Edit** the file in the `database/seeds` directory named `ProductsTableSeeder.php`.
3. Use the following code:
```php
<?php
use App\Product;
use Illuminate\Database\Seeder;
class ProductsTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $products = factory(Product::class, 10)->create();
    }
}
```
4. **Edit** the file `DatabaseSeeder.php` in the `database\seeds` directory with the following code:
```php
<?php

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $this->call('ProductsTableSeeder');
    }
}
```
5. **Edit** the file `ModelFactory.php` in the `database/factories` directory with this code:
```php
<?php
$factory->define(App\Product::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'price' => rand(0, 300),
        'description'=>$faker->text,
    ];
});
```
6. **Run** this command next `php artisan db:seed`.

### 04 - Routes


#### Routes  

Routes are also called endpoints.  The words can be used interchangeably.

1. **Open** the file named `web.php` in the `routes` directory.
2. **Add** the following code:
```php
<?php
/*
|--------------------------------------------------------------------------
| Application Routes
|--------------------------------------------------------------------------
|
| Here is where you can register all of the routes for an application.
| It is a breeze. Simply tell Lumen the URIs it should respond to
| and give it the Closure to call when that URI is requested.
|
*/
$router->group(['prefix'=>'products'], function() use($router) {
    $router->get('/', 'ProductController@index');
    $router->post('/', 'ProductController@create');
    $router->get('/{id}', 'ProductController@show');
    $router->put('/{id}', 'ProductController@update');
    $router->delete('/{id}', 'ProductController@destroy');
});
```

### 05 - Testing

#### Testing the ReST API

1. **Download** the `Postman` app which is a ReST API Client and Tester.
