# Resource Projects and World Bank Data integration Visualizations

## Technology

#### Frontend

We're using `nuxt ^1.0.0` as our frontend framework.  For the maps visualisations we use `D3 ^5.5.0`. Finally for the application styles we use the node implementation of sass: `node-sass ^4.2.0`.

## Requirements

- Node 7.4+
- NPM

## Installation

First install dependencies running in your projects folder inside the terminal:

```

npm install

````
## Usage
To run the project in development mode you need to execute the following command on your terminal:

```

npm run dev

```
This will run the project on `127.0.0.1:3000`  on your browsers window, with hot-reload activated.
To run the project in production mode you need to:

First, build the project for production with the following command:
```

 npm run build

 ```
 This will build the project in production mode with minification.

 Afterwards you'll need to run the production server with the following command:
 ```

 npm start

 ```
 This will run the project on `127.0.0.1:3000`  on your browser's window on production mode.
