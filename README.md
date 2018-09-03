Para situar los scripts es mejor ponerlos en el head pero con el atributo async

Instalamos un servidor 

```
npm install http-server -g
```

Y generamos los modulos de webpack

```
npm init -y
npm i --save-dev webpack webpack-cli
```

Ejecutamos webpack para crear la carpeta dist


```
.\node_modules\.bin\webpack
```

Añadimos a package.json


```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
},
```

Ejecutamos webpack igual que anteriormente

```
npm run webpack
```

Creamos en el root webpack.config.js

```
const path = require('path');

module.exports =  {
    // lo podriamos quitar porque es el valor por defecto
    entry: path.join(__dirname, 'src', 'index'),
    output: {
        filename: 'bundle[hash].js',
        path: path.resolve(__dirname, 'dist')
    }
};
```

Instalamos el servidor de desarrollo de webpack

```
npm i -D webpack-dev-server
```

Actualizamos package.json

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --mode=development",
    "start": "webpack-dev-server --mode=development"
},
```

Actualizamos webpack.config.js

```
devServer: {
    open: true, // abre el navegador
    overlay: true, // errores en pantalla sin mirar la consola
    port: 3000,
    hot: true // refrescar automaticamente
}
```

Ejecutamos webpack 

```
npm run start
```

Como queremos meter index.html en la carpeta dist, instalamos el siguiente modulo

```
npm i -D html-webpack-plugin
```

Actualizamos webpack.config.js

```
var path = require('path');
var HTMLWebpackPlugin = require('html-webpack-plugin');

module.exports =  {
    // lo podriamos quitar porque es el valor por defecto
    entry: path.join(__dirname, 'src', 'index'),
    output: {
        filename: 'bundle[hash].js',
        path: path.resolve(__dirname, 'dist')
    },
    devServer: {
        open: true, // abre el navegador
        overlay: true, // errores en pantalla sin mirar la consola
        port: 3000,
        hot: true // refrescar automaticamente
    },
    plugins: [
        new HTMLWebpackPlugin(
            {
                title: 'Play',
                template: path.join(__dirname, 'src', 'index.html'),
                minify: {
                    collapseWhitespace: true
                }
            }
        )
    ]
};
```

Para usar ES6, como no funciona en todos los navegadores, se utiliza un transpilador (conversor a ES5) como Babel 

```
npm i -D @babel/core @babel/preset-env babel-loader
```

Creamos un archivo .babelrc

```
{
  "presets": [
    [
      "@babel/preset-env"
    ]
  ]
}

```

Añadimos a webpack.config.js

```
module: {
    rule: [
        {
            test: /\.(js)$/,
            exclude: /node_modules/,
            use: 'babel-loader'
        }
    ]
}
```

Volvemos a ejecutar el build

```
npm run build
```

Instalamos un loader de css

```
npm install --save-dev style-loader css-loader
```

Instalar eslint global

```
npm i -g eslint
npm i -g eslint
npm i -g eslint-plugin-import
npm i -g eslint-config-airbnb-base
```

Configurar eslint para popular > airbnb > no react > javascript > Y

```
eslint --init i
```

Creamos un archivo .eslintrc.js

```
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

Instalamos SASS

```
npm install --save-dev sass-loader node-sass
```

Instalamos este modulo para hacer merge de configuraciones en el webpack.config.js

npm i -D webpack-merge
