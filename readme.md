# Awesome Webpack Perf [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of webpack tools and plugins that help make the web faster

## Contents

- [Built-in stuff](#built-in-stuff)
- [JS minifiers](#js-minifiers)
- [CSS](#css)
  - [Minifiers](#minifiers)
  - [Other optimizers](#other-optimizers)
  - [Extraction plugins](#extraction-plugins)
  - [Critical CSS plugins](#critical-css-plugins)
- [CSS-in-JS](#css-in-js)
  - [Minification](#minification)
- [Images](#images)
  - [Image compression tools](#image-compression-tools)
  - [Other tools](#other-tools)
- [Service workers](#service-workers)
- [Preloading and preconnecting](#preloading-and-preconnecting)
- [Prerender](#prerender)
- [Progressive web apps (PWA)](#progressive-web-apps-pwa)
- [Analysis tools](#analysis-tools)
- [Build-time audit tools](#build-time-audit-tools)
- [Other web performance lists](#other-web-performance-lists)

## Built-in stuff

- [`mode: 'production'`](https://webpack.js.org/configuration/mode/) in the webpack config enables the common production optimizations
- [`optimization.splitChunks`](https://webpack.js.org/plugins/split-chunks-plugin/) in the webpack config enables splitting one bundle into smaller chunks. This helps to load less JS for each page and cache better

## JS minifiers

JS minifiers are tools that make JS payloads smaller.

- [`uglifyjs-webpack-plugin`](https://www.npmjs.com/package/uglifyjs-webpack-plugin) – Uglify is an ES5 minifier
- [`terser-webpack-plugin`](https://www.npmjs.com/package/terser-webpack-plugin) – Terser is a fork of Uglify that has support for ES2015+. Ships with webpack
- [`babel-minify-webpack-plugin`](https://www.npmjs.com/package/babel-minify-webpack-plugin) – Babel Minify is a minifier built in the Babel pipeline. It supports all syntax Babel supports
- [`closure-webpack-plugin`](https://www.npmjs.com/package/closure-webpack-plugin) – Closure Compiler is a minifier developed by Google. It has several advanced optimization that are unsafe for some apps but compress better

## CSS

### Minifiers

CSS minifiers are tools that make the volume of CSS smaller.

There’re three popular competing CSS minifiers: _CSSO_, _CSS Nano_ and _Clean CSS_. They’re mostly similar in the compression quality and supported features, so if you’re picking one, just choose one you prefer more.

- **CSSO** ![](https://img.shields.io/npm/dw/csso):

  - [`csso-loader`](https://www.npmjs.com/package/csso-loader)
  - [`postcss-csso`](https://www.npmjs.com/package/postcss-csso) is a PostCSS plugin that can be used with [`postcss-loader`](https://www.npmjs.com/package/postcss-loader)
  - [`csso-webpack-plugin`](https://www.npmjs.com/package/csso-webpack-plugin) only works when you use [a CSS extraction plugin](#extraction-plugins). However, it compresses CSS better than the loader – e.g., it can merge rules from multiple source files

- **CSS Nano** ![](https://img.shields.io/npm/dw/cssnano):
  - [`cssnano`](https://www.npmjs.com/package/cssnano) is a PostCSS plugin that can be used with [`postcss-loader`](https://www.npmjs.com/package/postcss-loader)
  - [`optimize-css-assets-webpack-plugin`](https://www.npmjs.com/package/optimize-css-assets-webpack-plugin), technically, works with any minifier, but ships (and works best) with `cssnano`.\
     This plugin only works when you use [a CSS extraction plugin](#extraction-plugins). However, it compresses CSS better than the loader – e.g., it can merge rules from multiple source files
- **Clean CSS** ![](https://img.shields.io/npm/dw/clean-css):
  - [`clean-css-loader`](https://www.npmjs.com/package/clean-css-loader)
  - [`postcss-clean`](https://www.npmjs.com/package/postcss-clean) is a PostCSS plugin that can be used with [`postcss-loader`](https://www.npmjs.com/package/postcss-loader)

### Other optimizers

- [`purgecss-webpack-plugin`](https://github.com/FullHuman/purgecss-webpack-plugin) analyzes the app code and styles and removes CSS rules that aren’t used anywhere. Works great with CSS frameworks like Bootstrap

### Extraction plugins

By default (with a simple `style-loader`), all styles imported in the app are added into the JS bundle. CSS extraction plugins help to move these styles into a separate `.css` file – which helps with faster rendering and better caching.

- [`mini-css-extract-plugin`](https://www.npmjs.com/package/mini-css-extract-plugin) is the de-facto default solution for extracting styles in modern webpack
- [`extract-text-webpack-plugin`](https://www.npmjs.com/package/extract-text-webpack-plugin) was the most popular plugin for extracting styles in webpack 1-3
- [`extract-css-chunks-webpack-plugin`](https://www.npmjs.com/package/extract-css-chunks-webpack-plugin) has the same API as `mini-css-extract-plugin` but offers better hot module replacement support

### Critical CSS plugins

Critical CSS is an approach for rendering the site faster. With Critical CSS, for each page, you extract rules needed for the initial render and inline them. Then, you load the remaining styles asynchronously. TODO: link to more details

- [`html-critical-webpack-plugin`](https://github.com/anthonygore/html-critical-webpack-plugin) runs the [`critical`](https://www.npmjs.com/package/critical) tool on every webpack build. Uses a headless browser, returns styles only for [the above-the-fold content](https://whatis.techtarget.com/definition/above-the-fold)
- [`critters`](https://github.com/GoogleChromeLabs/critters) renders HTML in a JSDom environment on every webpack build. Doesn’t use a headless browser (= less heavy); returns all styles needed by the page, not only the above-the-fold ones (= may fix some `html-critical-webpack-plugin` glitches)
- [`isomorphic-style-loader`](https://github.com/kriasoft/isomorphic-style-loader) helps to extract critical styles during server-side rendering

## CSS-in-JS

CSS-in-JS libraries typically provide Critical CSS support out of the box and need less optimizations. However, if you write CSS-in-JS styles in JS strings:

```js
const buttonStyles = css`
  color: red;
`;
```

they still need minification.

### Minification

- [`minify-cssinjs-loader`](https://github.com/zaaack/minify-cssinjs-loader) works with all CSS-in-JS libraries thanks to regex-based matching. Does basic compression of style strings
- _Library-specific Babel plugins_. Many popular CSS-in-JS libraries have Babel plugins specifically created for them. They typically do a better job at optimization:
  - [babel-plugin-styled-components](https://github.com/styled-components/babel-plugin-styled-components)
  - [babel-plugin-emotion](https://www.npmjs.com/package/babel-plugin-emotion)

## Images

### Image compression tools

All the tools below use [`imagemin`](https://github.com/imagemin/imagemin) and imagemin plugins for optimizing images, so they typically provide the same level of compression.

You should prefer plugins over loaders. Plugins will optimize images that were produced by other loaders or plugins, whereas loaders will only trigger for files from your source code.

- [`image-webpack-loader`](https://www.npmjs.com/package/image-webpack-loader) ![](https://img.shields.io/npm/dw/image-webpack-loader)
- [`img-loader`](https://www.npmjs.com/package/img-loader) ![](https://img.shields.io/npm/dw/img-loader)
- [`imagemin-webpack`](https://www.npmjs.com/package/imagemin-webpack) ![](https://img.shields.io/npm/dw/imagemin-webpack)
- [`imagemin-webpack-plugin`](https://www.npmjs.com/package/imagemin-webpack-plugin) ![](https://img.shields.io/npm/dw/imagemin-webpack-plugin)

### Other tools

- [`lqip-loader`](https://www.npmjs.com/package/lqip-loader) generates low-quality image placeholders which you can use for lazy-loading images. Just like in Medium:

  ![](https://i.imgur.com/uBsYSNd.png)

- [`webp-loader`](https://www.npmjs.com/package/webp-loader) converts images to webp
- [`responsive-loader`](https://www.npmjs.com/package/responsive-loader) resizes one image to multiple various sizes. Works great with `<img srcset>` or `<picture>`
- [`svg-url-loader`](https://www.npmjs.com/package/svg-url-loader) generates 20-30% smaller `data`-urls for inline SVG images

## Service workers

Both plugins below generate a service worker that prefetches all webpack assets in the background and adds offline support into the application:

- [`workbox-webpack-plugin`](https://www.npmjs.com/package/workbox-webpack-plugin) prefetches all webpack assets in the background and makes the app ready for working offline. It is based on the Google’s [`workbox`](https://developers.google.com/web/tools/workbox) library that simplifies common usages of service workers
- [`offline-plugin`](https://www.npmjs.com/package/offline-plugin) also prefetches all webpack assets in the background and makes the app ready for working offline. It falls back to AppCache in browsers that don’t support service workers

## Preloading and preconnecting

- [`preload-webpack-plugin`](https://www.npmjs.com/package/preload-webpack-plugin) preloads asynchronous chunks¹ with `<link rel="preload">` or `<link rel="prefetch">`
- [`html-webpack-preconnect-plugin`](https://www.npmjs.com/package/html-webpack-preconnect-plugin) adds `<link rel="preconnect">` for a separate domain (e.g., an API server)

¹ [Asynchronous chunks](https://webpack.js.org/guides/code-splitting/#dynamic-imports) are chunks that are created when you use dynamic `import()`

## Prerender

Prerendering tools run an app during the build and return the HTML the app generates. This is an alternative to server-side rendering and helps to deliver the content to the user immediately – instead of making them wait until the bundle is loaded.

- [`prerender-spa-plugin`](https://www.npmjs.com/package/prerender-spa-plugin)
- [`prerender-loader`](https://www.npmjs.com/package/prerender-loader)

## Progressive web apps (PWA)

- [`webpack-pwa-manifest`](https://github.com/arthurbergmz/webpack-pwa-manifest) generates a `manifest.json` and resizes app icons for a PWA

## Analysis tools

- [`webpack-bundle-analyzer`](https://www.npmjs.com/package/webpack-bundle-analyzer) generates a view of the bundle content. Use it to figure out what takes so much size in the bundle:

  ![](https://cloud.githubusercontent.com/assets/302213/20628702/93f72404-b338-11e6-92d4-9a365550a701.gif)

  <sup>(Animation credits: `webpack-bundle-analyzer`)</sup>

- [`source-map-explorer`](https://www.npmjs.com/package/source-map-explorer) also generates a view of the bundle contents. It’s less detailed than `webpack-bundle-analyzer` but only needs a source map to run:

  ![](https://raw.githubusercontent.com/danvk/source-map-explorer/HEAD/screenshot.png)

  <sup>(Image credits: `source-map-explorer`)</sup>

- [Webpack Analyse](https://github.com/webpack/analyse) shows all modules present in the bundle – and relationships between them. Use it to understand why a specific suspicious module is bundled:

  ![](https://user-images.githubusercontent.com/2953267/66723475-3c54cd00-ee22-11e9-96ff-751ba888d56d.png)

- [Bundlephobia](https://bundlephobia.com) reports bundle sizes of JS libraries:

  ![](https://user-images.githubusercontent.com/2953267/66723322-4970bc80-ee20-11e9-8dfd-fef59fc7ef2f.png)

## Build-time audit tools

- [`webpack-dashboard`](https://github.com/FormidableLabs/webpack-dashboard) reports sizes of modules and warnings like duplicated files during development:

  ![](https://camo.githubusercontent.com/3f5446837855aef7cb671a3c0ca6b9d351cf8045/687474703a2f2f692e696d6775722e636f6d2f714c3664584a642e706e67)

  <sup>(Image credits: `webpack-dashboard`)</sup>

- [`duplicate-package-checker-webpack-plugin`](https://github.com/darrenscerri/duplicate-package-checker-webpack-plugin) prints a warning if a bundle includes multiple versions of the same library:

  ![](https://raw.githubusercontent.com/darrenscerri/duplicate-package-checker-webpack-plugin/master/screenshot.png)

  <sup>(Image credits: `duplicate-package-checker-webpack-plugin`)</sup>

## Other web performance lists

- [Awesome WPO](https://github.com/davidsonfellipe/awesome-wpo) – A curated list of Web Performance Optimization
- [Webpack Libs Optimizations](https://github.com/GoogleChromeLabs/webpack-libs-optimizations) – A collection of Babel and webpack plugins to optimize the size of various popular libraries

## Contribute

Contributions welcome! Read the [contribution guidelines](contributing.md) first.

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0)

To the extent possible under law, Ivan Akulov has waived all copyright and related or neighboring rights to this work.
