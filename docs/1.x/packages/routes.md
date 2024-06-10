# Routes

[[TOC]]

To learn in detail about Routes, you can visit the Laravel documentation [here](https://laravel.com/docs/10.x/routing).

## Directory Structure

- Create a **`Routes`** folder inside **`packages/Webkul/Blog/src`** and create two files named **`admin-routes.php`** and **`shop-routes.php`**. The updated directory structure will look like this:

    ```
    └── packages
        └── Webkul
            └── Blog
                └── src
                    ├── ...
                    └── Routes
                        ├── admin-routes.php
                        └── shop-routes.php
    ```

- **`admin-routes.php`**: This file is for admin routes. Add the following code to this file:

  ```php
  <?php

  use Illuminate\Support\Facades\Route;
  use Webkul\Blog\Http\Controllers\Admin\PostController;

  Route::group(['middleware' => ['web', 'admin'], 'prefix' => config('app.admin_url')], function () {
      Route::get('/blog', [PostController::class, 'index']);
  });
  ```

- **`shop-routes.php`**: This file is for shop routes. Add the following code to this file:

  ```php
  <?php

  use Illuminate\Support\Facades\Route;
  use Webkul\Blog\Http\Controllers\Shop\PostController;

  Route::group(['middleware' => ['web', 'theme', 'locale', 'currency']], function () {
      Route::get('/blogs', [PostController::class, 'index']);
  });
  ```

## Loading Routes

- Now, we need to register our routes in the service provider's boot method, which is located in **`BlogServiceProvider.php`**.

  ```php
  <?php

  namespace Webkul\Blog\Providers;

  use Illuminate\Support\ServiceProvider;

  /**
   * BlogServiceProvider
   *
   * @copyright 2023 Webkul Software Pvt. Ltd. (http://www.webkul.com)
   */
  class BlogServiceProvider extends ServiceProvider
  {
      /**
       * Bootstrap services.
       *
       * @return void
       */
      public function boot()
      {
          //... 
          
          $this->loadRoutesFrom(__DIR__ . '/../Routes/admin-routes.php');

          $this->loadRoutesFrom(__DIR__ . '/../Routes/shop-routes.php');
      }
  }
  ```