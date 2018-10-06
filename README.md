## Install and configure modules

Init Nodejs project

```sh
npm init -y
```

Install Webpack on DEV

```sh
npm i --save-dev webpack webpack-cli
```

Create ./src/index.js with any javascript content

```javascript
console.log('hello');
```

Add to package.json

```javascript
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
},
```

Run Webpack to create the  dist (production) folder

```sh
npm run build
```

Create in the root forder webpack.config.js

```javascript
const path = require('path');

module.exports =  {
    // we could remove it because is the default value
    entry: path.join(__dirname, 'src', 'index'),
    output: {
        filename: 'bundle[hash].js',
        path: path.resolve(__dirname, 'dist')
    }
};
```

Install the servers on DEV

```sh
npm i -D webpack-dev-server json-server
```

Update package.json

```javascript
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --mode=production",
    "start": "webpack-dev-server --mode=development --hot --inline",
},
```

Install the following plugins on DEV

```sh
npm i -D clean-webpack-plugin webpack-merge css-loader ejs ejs-html-loader file-loader html-loader html-webpack-plugin image-webpack-loader mini-css-extract-plugin node-sass optimize-css-assets-webpack-plugin sass-loader style-loader uglifyjs-webpack-plugin
```

Install Eslint on DEV

```sh
npm i -D eslint
npm i -D eslint-plugin-import
npm i -D eslint-config-airbnb-base
```

Configure Eslint with popular > airbnb > no react > javascript > Y

```sh
eslint --init i
```

Update .eslintrc.js

```javascript
module.exports = {
    "extends": "airbnb-base",
    "plugins": [
        "import"
    ],
    "rules": {
      "comma-dangle": ["error", "never"],
      "import/no-unresolved": "off",
      "linebreak-style": ["error", "windows"]
    },
    "env": {
      "browser": true,
      "node": true
    },
    "globals": {
      "document": false
    }
};
```

Install the following plugins

```sh
npm i dotenv pubsub-js query-string@5 webpack-plugin-critical whatwg-fetch
```

Install Babel

```sh
npm i @babel/core @babel/preset-env @babel/polyfill babel-polyfill babel-loader
```

Create in the root forder .babelrc

```javascript
{
  "presets": [
    [
      "@babel/preset-env"
    ]
  ]
}
```

Install Surge as Global

```sh
npm install --global surge
```

Configure Surge and create a file named CNAME with your domain on it

```sh
surge
```

Create in the root forder CNAME with your domain on it

```sh
mydomain.surge.sh
```

Create in the root forder .gitignore

```sh
src/data/db.json
node_modules
dist
.env
```

Create in the root forder .env

```sh
API_URL= http://localhost:8000/
```

Create in the root forder .env.example

```sh
API_URL=
```

Update package.json

```javascript
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "build": "webpack --mode=production",
  "deploy": "webpack --mode=production && cp CNAME dist/CNAME && surge dist",
  "eslint": "eslint",
  "start": "webpack-dev-server --mode=development --hot --inline",
  "server": "json-server --watch --port=8000 ./src/data/db.json"
},
```

Update webpack.config.js

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const merge = require('webpack-merge');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const { CriticalPlugin } = require('webpack-plugin-critical');
const dotenv = require('dotenv');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
```

```javascript
const commonConfig = {
  entry: {
    posts: ['@babel/polyfill', 'whatwg-fetch', path.join(__dirname, 'src', 'pages', 'posts', 'index')],
    post: ['@babel/polyfill', 'whatwg-fetch', path.join(__dirname, 'src', 'pages', 'post', 'index')]
  },
  output: {
    filename: '[name][hash].js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env': dotenv.parsed
    }),
    page({
      title: 'Posts',
      template: path.join(__dirname, 'src', 'pages', 'posts', 'index.html'),
      chunks: ['posts'],
      filename: path.resolve(__dirname, 'dist', 'index.html')
    }),
    page({
      title: 'Post',
      template: path.join(__dirname, 'src', 'pages', 'post', 'index.html'),
      chunks: ['post'],
      filename: path.resolve(__dirname, 'dist', 'post', 'index.html')
    })
  ],
  module: {
    rules: [
      {
        test: /\.(js)$/,
        exclude: /node_modules/,
        use: 'babel-loader'
      },
      {
        test: /\.(gif|png|jpe?g|svg)$/i,
        use: [
          'file-loader',
          {
            loader: 'image-webpack-loader',
            options: {
              name: 'assets/[name].[hash].[ext]'
            }
          }
        ]
      },
      {
        test: /\.(html|ejs)$/,
        use: ['html-loader', 'ejs-html-loader']
      }
    ]
  },
  resolve: {
    alias: {
      components: path.resolve(__dirname, 'src', 'components'),
      assets: path.resolve(__dirname, 'src', 'assets'),
      styles: path.resolve(__dirname, 'src', 'styles'),
      utils: path.resolve(__dirname, 'src', 'utils'),
      data: path.resolve(__dirname, 'src', 'data'),
      services: path.resolve(__dirname, 'src', 'services'),
      pages: path.resolve(__dirname, 'src', 'pages')
    }
  },
  devtool: 'source-map'
};
```

```javascript
const devConfig = {
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [
          'style-loader',
          'css-loader',
          'sass-loader'
        ]
      }
    ]
  },
  devServer: {
    overlay: true,
    port: 3000
  }
};
```

```javascript
const prodConfig = {
  optimization: {
    minimizer: [
      new OptimizeCSSAssetsPlugin(
        {
          cssProcessorOptions: { map: { inline: false } }
        }
      ),
      new UglifyJsPlugin({
        cache: true,
        parallel: true,
        sourceMap: true // set to true if you want JS source maps
      })
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].[hash].css'
    }),
    new CleanWebpackPlugin(['dist']),
    new CriticalPlugin({
      src: path.join(__dirname, 'src', 'pages', 'posts', 'index.html'),
      inline: true,
      minify: true,
      dest: path.join(__dirname, 'dist', 'index.html')
    })
  ],
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          'sass-loader'
        ]
      }
    ]
  }
};
```

```javascript
module.exports = (env, argv) => (
  argv.mode === 'development'
    ? merge(commonConfig, devConfig)
    : merge(commonConfig, prodConfig)
);
```

## Create file structure

Inside the src folder create the following structure:

* assets
* components  
  * footer
  * header
  * image
  * post
  * posts

* data
* pages
  * partials
  * post
  * posts
  
* services
* styles
* utils

Inside the styles folder create:

* utils.scss
* settings.scss
* layout.scss
* _normalize.scss
* base.scss
* main.scss

Inside the components folder create:

* footer
  * footer-styles.scss
  
* header
  * header-styles.scss
  * header-component.js

* image
  * image-styles.scss
  * image-component.js

* post
  * post-styles.scss
  * post-component.js

* post-detail
  * post-detail-styles.scss
  * post-detail-component.js

* posts
  * posts-styles.scss
  * posts-component.js

Inside the data folder create:

* db.json
* db.sample.json
  
Inside the pages folder create:

* partials
  * head.ejs
  * header.ejs
  * footer.ejs

* post
  * index.html
  * index.js

* posts
  * index.html
  * index.js

Inside the services folder create:

* API-service.js
* post-service.js

Inside the utils folder create:

* html.js

Delete the initial index.js

## Run the project on DEV

Run the json server

```sh
npm run server
```

Run the webpack DEV server

```sh
npm run start
```
