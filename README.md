# WordpressPack

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Features](#features)
- [Requirements](#requirements)
- [Getting Started](#getting-started)
- [Developing Locally](#developing-locally)
- [Building for Production](#building-for-production)
- [Changing ports](#changing-ports)
- [Project Structure](#project-structure)
- [Local Database Backup](#local-database-backup)
- [Local Database Restore](#local-database-restore)
- [Author](#author)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Features

- Modern JavaScript through Webpack
- Live reload via BrowserSync
- SCSS support
- Easy dev environments with Docker Compose
- Stateless, immutable plugin management via Composer
- Helpful HTML5 Router for firing JS based on WordPress page slug.
- Nothing else.

## Requirements

- Node.js
- Yarn
- PHP and Composer
- Docker for Mac / Windows
- Docker Compose

## Getting Started

```bash
git clone git@github.com:plowzzer/wordpressPack.git
yarn install / npm install
composer install # if you want plugins ( not required )
docker-compose up
```

## Developing Locally

To work on the theme locally, open another window/tab in terminal and run:

```bash
yarn start
```

ou

```bash
npm start
```

This will open a browser, watch all files (php, scss, js, etc) and reload the
browser when you press save.

## Building for Production

To create an optimized production build, run:

```bash
yarn build
```

ou 

```bash
npm run-script build
```

This will minify assets, bundle and uglify javascript, and compile scss to css.
It will also add cachebusting names to then ends of the compiled files, so you
do not need to bump any enqueued asset versions in `functions.php`.

## Changing ports

There are two ports involved, the port of the dockerized WordPress instance,
and the port the Browser Sync runs on. To change the port of the dockerized
WordPress instance go into [`docker-compose.yml`](docker-compose.yml#L25) and
modify `ports`.

```yml
# docker-compose.yml
 ...
  ports:
    - "9009:80" # only need to change `9009:80` --> localhost:9009
 ...
```

If you want to change the port you develop on (the default is 4000), then open
[`scripts/webpack.config.js`](scripts/webpack.config.js#L119) and modify
`BrowserSyncPlugin`'s `port` option. If you changed the WordPress port above,
be sure to also change `proxy` accordingly. Don't forget the trailing slash.

```js
// scripts/webpack.config.js
...
new BrowserSyncPlugin({
  notify: false,
  host: 'localhost',
  port: 4000, // this is the port you develop on. Can be anything.
  logLevel: 'silent',
  files: ['./*.php'],
  proxy: 'http://localhost:9009/', // This port must match docker-compose.yml
}),
...
```

## Project Structure

```bash
.
├── 404.php
├── archive.php
├── comments.php
├── composer.json                # Compose dependencies (plugins)
├── composer.lock                # Composer lock file
├── docker-compose.yml           # Docker Compose configuration
├── footer.php
├── functions.php
├── header.php
├── index.php
├── package.json                 # Node.js dependencies
├── page.php
├── screenshot.png               
├── search.php
├── sidebar.php
├── single.php
├──scripts                       # Build / Dev Scripts
│   ├── build.js                 # Build task
│   ├── start.js                 # Start task
│   └── webpack.config.js        # Webpack configuration
└──src
    ├── index.js                 # JavaScript entry point
    ├── inc                      # Writen functions
    ├── routes                   # Routes
    │   ├── common.js            # JS that will run on EVERY page
    │   └── <xxx>.js             # JS that will run on pages with <xxx> slug
    ├── languages                # Multiple Languages support
    ├── template-parts           # Multiple template parts support
    ├── style.scss               # SCSS style entry point (objects are in styles folder)
    ├── styles                   # SCSS
    │   ├── elements (...)
    │   ├── forms (...)
    │   ├── layout (...)
    │   ├── media (...)
    │   ├── mixins (...)
    │   ├── modules (...)
    │   ├── navigation (...)
    │   ├── shop (...)          # For Woocommerce features
    │   ├── site (...)
    │   ├── typography (...)
    │   ├── variables-site (...)
    │   ├── _normalize.scss
    │   ├── _woocommerce.scss
    │   └── ...
    └── util
        ├── Router.js            # HTML5 Router, DO NOT TOUCH
        └── camelCase.js         # Helper function for Router, DO NOT TOUCH
```

## Local Database Backup

Here's how to dump your local database with Docker into a `.sql` file

```aidl
docker exec -it host_db_1 /usr/bin/mysqldump -u username -ppassword database_name > backup.sql
```

## Local Database Restore

Restore a previous database backup

```aidl
docker exec -i host_db_1 /usr/bin/mysql -u username -ppassword database_name < backup.sql
```

## Author

- Pedro Pizzo [@plowzzer](https://twitter.com/plowzzer)
- Inspired by Jaredpalmer [@jaredpalmer](https://twitter.com/jaredpalmer) Presspack - https://github.com/jaredpalmer/presspack