---
title: Webpack and Optimization Introduction
date: 2022-10-25 01:33:19 +0800
categories: [front-end, tools]
tags: [webpack]     # TAG names should always be lowercase
---

## 1. What is Webpack?
Webpack is a module bundler for JavaScript applications. It processes all types of files (JavaScript, CSS, images, etc.) in a project and bundles them into one or more optimized files that can be served to the browser.

### Core Concepts
- **Entry**: The starting point where Webpack begins to build its dependency graph. It defines the main file of the application.
- **Output**: Specifies where the bundled files will be saved and how they will be named.
- **Loaders**: Transformations applied to files before bundling, such as converting Sass to CSS or ES6+ to ES5 JavaScript.
- **Plugins**: Extend Webpack's capabilities with more complex tasks, such as code minification or generating an HTML file.
- **Mode**: Webpack has three modes: `development`, `production`, and `none`. The mode controls how Webpack optimizes the output.

## 2. Installing Webpack
To get started with Webpack, install Webpack and its CLI:

```bash
npm install --save-dev webpack webpack-cli
```

## 3. Basic Webpack Configuration
Create a `webpack.config.js` file to configure Webpack. A simple example looks like this:

```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  mode: 'development', // For development, change to 'production' for production build
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      {
        test: /\.(png|jpg|gif)$/,
        use: ['file-loader'],
      },
    ],
  },
  devServer: {
    contentBase: './dist',
  },
};
```

## 4. Running Webpack
Once Webpack is set up, add the following script to `package.json` for easy execution:

```json
{
  "scripts": {
    "build": "webpack"
  }
}
```

Run Webpack using:

```bash
npm run build
```

For development with hot reloading, use Webpack Dev Server:

```bash
npm install --save-dev webpack-dev-server
```

Add a new script:

```json
{
  "scripts": {
    "start": "webpack serve --open"
  }
}
```

Run it using:

```bash
npm start
```

## 5. Further Optimization

### Code Splitting
Code splitting is an essential optimization strategy in Webpack, especially as projects grow larger. It helps reduce initial loading times and improve page responsiveness. Webpack provides several ways to achieve code splitting.

#### Using `SplitChunksPlugin` or `optimization.splitChunks`

Webpack has a built-in `SplitChunksPlugin` that automatically packages shared dependencies into separate files to avoid duplicate loading.

To enable code splitting, you can add the following basic configuration in `webpack.config.js`:

```javascript
module.exports = {
  // Other configuration options

  optimization: {
    splitChunks: {
      chunks: 'all',  // Split both asynchronous and synchronous dependencies
      minSize: 20000, // Minimum size (in bytes) for a chunk to be created
      maxSize: 0,     // Maximum size for a chunk, set to 0 to not limit
      minChunks: 1,   // At least 1 module needs to be shared for splitting
      maxAsyncRequests: 30,  // Maximum number of async requests
      maxInitialRequests: 30, // Maximum parallel requests for entry points
      automaticNameDelimiter: '~', // Delimiter for file names
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10, // Priority, higher values have higher priority
          name: 'vendors',
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true,
        },
      },
    },
  },
};

```

#### Dynamic Imports
Webpack also supports dynamic imports (`import()`) to load modules on demand rather than including everything at the initial load. This method is useful for single-page applications (SPAs) and other scenarios to implement lazy loading.

```javascript
// Static import
import moduleA from './moduleA';

// Dynamic import (code splitting)
import('./moduleA').then(moduleA => {
  moduleA.doSomething();
});
```

### Production Mode Optimization
In production mode, Webpack automatically optimizes the output by:
- **Minifying the code**: Removes unnecessary characters and spaces.
- **Tree Shaking**: Eliminates unused code.
- **Module Concatenation**: Combines small modules to reduce overhead.

Set the mode to `production` in `webpack.config.js`:

```javascript
module.exports = {
  mode: 'production',
  // Other configurations...
};
```

### Source Maps for Debugging
Source maps help trace the original code in the browser's developer tools, even after the code is minified.

Enable source maps in `webpack.config.js`:

```javascript
module.exports = {
  devtool: 'source-map', // Full source maps for development
  mode: 'production',
  devtool: 'cheap-module-source-map', // Lightweight source maps for production
};
```

Different types of Source Maps impact performance and debugging experience differently:

- **source-map**: Generates complete Source Maps, offering the best debugging experience but slower to generate and larger in file size.
- **cheap-module-source-map**: A lighter Source Map that maps only to the line and not to the exact column, suitable for production.
- **eval-source-map**: Faster and suitable for development environments.


That's all! Hope this guide helps you. Happy coding! ヾ(≧▽≦*)o



