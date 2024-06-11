# Assets

[[TOC]]

## Directory Structure

- To organize our assets, we need to create the following folder structure in the **`Resources`** folder:
  - Create a folder named **`assets`**.
  - Inside the **`assets`** folder, create the following folders: **`sass`**, **`js`**, and **`images`**.
  - In the **sass** folder, add the files **_admin.scss_**.
  - In the **js** folder, add the file **app.js**.

Here's the updated directory structure:

```
└── packages
    └── Webkul
        └── Category
            └── src
                ├── ...
                └── Resources
                    ├── ...
                    └── assets
                        ├── sass
                        │   └── admin.scss
                        ├── js
                        │   └── app.js
                        └── images
                    
```

## Compiling Assets

To compile the assets, perform the following steps:

1. Create a **`package.json`** and **`webpack.mix.js`** file inside the root of your package (**`packages/Webkul/Category`**).

2. Copy the following code into the **`package.json`** file:

    ```json
    {
      "scripts": {
        "dev": "npm run development",
        "development": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "watch": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "watch-poll": "npm run watch -- --watch-poll",
        "hot": "cross-env NODE_ENV=development node_modules/webpack-dev-server/bin/webpack-dev-server.js --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
        "prod": "npm run production",
        "production": "cross-env NODE_ENV=production node_modules/webpack/bin/webpack.js --no-progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
      },
      "devDependencies": {
        "cross-env": "^7.0.2",
        "laravel-mix": "^5.0.1",
        "laravel-mix-merge-manifest": "^0.1.2",
        "sass": "^1.62.0",
        "sass-loader": "^8.0.2",
        "vue-template-compiler": "^2.7.14"
      }
    }
    ```

3. Copy the following code into the **`webpack.mix.js`** file:

    ```javascript
    const mix = require("laravel-mix");

    if (typeof mix == "undefined") {
      const { mix } = require("laravel-mix");
    }

    require("laravel-mix-merge-manifest");

    if (mix.inProduction()) {
      var publicPath = "publishable/assets";
    } else {
      var publicPath = "../../../public/vendor/webkul/category/assets";
    }

    mix.setPublicPath(publicPath).mergeManifest();

    mix.disableNotifications();

    mix
      .copyDirectory(
        __dirname + "/src/Resources/assets/images",
        publicPath + "/images"
      )
      .js([__dirname + "/src/Resources/assets/js/app.js"], "js/category.js")
      .sass(
        __dirname + "/src/Resources/assets/sass/app.scss",
        "css/category.css"
      )
      .options({
        processCssUrls: false,
      });

    if (mix.inProduction()) {
      mix.version();
    }
    ```

4. Run **`npm install`** in the root of your package (**`packages/Webkul/Category`**) to install all the dependencies.

Now, your **`scss`** and **`js`** files are ready to use. You can write all your CSS and JS code here.

To compile the assets, run **`npm run prod`**. This will compile all your CSS, JS, and images into the publishable folder. Finally, make sure to register your publishable in the **`CategoryServiceProvider`**.

::: tip
You can also use **`npm run watch`**, which watches for changes in your assets and automatically updates the public path. When you're done making changes, run **`npm run prod`** to compile all your changes and move them to the publishable folder.
:::

## Load Assets From Package

- To load the assets from your package, add the following code to the **`CategoryServiceProvider.php`** file:

    ```php
    <?php

    namespace Webkul\Category\Providers;

    use Illuminate\Support\ServiceProvider;

    /**
     * CategoryServiceProvider
    *
    * @copyright 2024 Webkul Software PVT. LTD.
    */
    class CategoryServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap services.
         *
         * @return void
         */
        public function boot()
        {
            //... 

            $this->publishes([
                __DIR__ . '/../../publishable/assets' => public_path('vendor/webkul/category/assets'),
            ], 'public');
        }
    }
    ```

### Linking Stylesheets

To link the stylesheets to the layouts, follow these steps:

1. Inside each **layouts** folder, create a file called **`style.blade.php`**.

2. Copy the following code into the **`admin/layouts/style.blade.php`** file:

    ```html
    <link rel="stylesheet" href="{{ asset('vendor/webkul/category/assets/css/admin.css') }}"/>
    ```

### Event Listener

- To ensure that the admin layouts include our CSS, we should add an event listener. You can add the following code to the **`CategoryServiceProvider.php`** file:

    ```php
    <?php

    namespace Webkul\Category\Providers;

    use Illuminate\Support\ServiceProvider;
    use Illuminate\Support\Facades\Event;

    /**
     * CategoryServiceProvider
    *
    * @copyright 2024 Webkul Software PVT. LTD.
    */
    class CategoryServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap services.
         *
         * @return void
         */
        public function boot()
        {
            //... 

            $this->publishes([
                __DIR__ . '/../../publishable/assets' => public_path('vendor/webkul/category/assets'),
            ], 'public');

            Event::listen('admin.layout.head', function($viewRenderEventManager) {
                $viewRenderEventManager->addTemplate('category::admin.layouts.style');
            });
        }

        /**
         * Register services.
         *
         * @return void
         */
        public function register()
        {
        }
    }
    ```