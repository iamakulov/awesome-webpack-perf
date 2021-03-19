![](https://user-images.githubusercontent.com/2953267/87531172-0044e600-c69a-11ea-952b-695fb6db8705.png)

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
  - [Zero-runtime libraries](#zero-runtime-libraries)
- [Images](#images)
  - [Image compression tools: universal](#image-compression-tools-universal)
  - [Image compression tools: for a single format](#image-compression-tools-for-a-single-format)
  - [Other tools](#other-tools)
- [Fonts](#fonts)
- [Gzip/Brotli](#gzipbrotli)
- [Service workers](#service-workers)
- [`<link rel>` and `<script async>`](#link-rel-and-script-async)
- [Prerendering](#prerendering)
- [Progressive web apps (PWA)](#progressive-web-apps-pwa)
- [Analysis tools](#analysis-tools)
  - [Bundle contents](#bundle-contents)
  - [Code duplicates](#code-duplicates)
  - [Various tools](#various-tools)
- [Webpack build performance](#webpack-build-performance)
- [Other web performance lists](#other-web-performance-lists)

## Built-in stuff

- [`mode: 'production'`](https://webpack.js.org/configuration/mode/) in the webpack config enables the common production optimizations
- [`optimization.splitChunks`](https://webpack.js.org/plugins/split-chunks-plugin/) in the webpack config enables splitting one bundle into smaller chunks. This helps to load less JS for each page and cache better
- [`optimization.runtimeChunk`](https://webpack.js.org/configuration/optimization/#optimizationruntimechunk) in the webpack config enables splitting webpack’s runtime code into a separate chunk. This improves long-term caching

## JS minifiers

JS minifiers are tools that make JS payloads smaller.

- [`uglifyjs-webpack-plugin`](https://www.npmjs.com/package/uglifyjs-webpack-plugin) – Uglify is an ES5 minifier
- [`terser-webpack-plugin`](https://www.npmjs.com/package/terser-webpack-plugin) – Terser is a fork of Uglify that has support for ES2015+. Ships with webpack
- [`babel-minify-webpack-plugin`](https://www.npmjs.com/package/babel-minify-webpack-plugin) – Babel Minify is a minifier built in the Babel pipeline. It supports all syntax Babel supports
- [`closure-webpack-plugin`](https://www.npmjs.com/package/closure-webpack-plugin) – Closure Compiler is a minifier developed by Google. It has several advanced optimizations that are unsafe for some apps but compress better
- [`esbuild-webpack-plugin`](https://www.npmjs.com/package/esbuild-webpack-plugin) – ESBuild is a JS&TS compiler/minifier written in Go. It’s ~50-100× faster [compared to native JavaScript tools](https://github.com/evanw/esbuild#javascript-benchmark).

## CSS

### Minifiers

CSS minifiers are tools that make the volume of CSS smaller.

There’re three popular competing CSS minifiers: _CSSO_, _CSS Nano_ and _Clean CSS_. They’re mostly similar in the compression quality and supported features, so if you’re picking one, just choose the one you prefer more.

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

- [`purgecss-webpack-plugin`](https://www.npmjs.com/package/purgecss-webpack-plugin) analyzes the app code and styles and removes CSS rules that aren’t used anywhere. Works great with CSS frameworks like Bootstrap

### Extraction plugins

By default (with a simple `style-loader`), all styles imported in the app are added into the JS bundle. CSS extraction plugins help to move these styles into a separate `.css` file – which helps with faster rendering and better caching.

- [`mini-css-extract-plugin`](https://www.npmjs.com/package/mini-css-extract-plugin) is the de-facto default solution for extracting styles in modern webpack
- [`extract-text-webpack-plugin`](https://www.npmjs.com/package/extract-text-webpack-plugin) was the most popular plugin for extracting styles in webpack 1-3
- [`extract-css-chunks-webpack-plugin`](https://www.npmjs.com/package/extract-css-chunks-webpack-plugin) has the same API as `mini-css-extract-plugin` but offers better hot module replacement support

### Critical CSS plugins

Critical CSS is an approach for rendering the site faster. With Critical CSS, for each page, you extract the rules needed for the initial render and inline them. Then, you load the remaining styles asynchronously. [More details](https://3perf.com/talks/web-perf-101/#critical-css)

- [`html-critical-webpack-plugin`](https://www.npmjs.com/package/html-critical-webpack-plugin) runs the [`critical`](https://www.npmjs.com/package/critical) tool on every webpack build. Uses a headless browser, returns styles only for [the above-the-fold content](https://whatis.techtarget.com/definition/above-the-fold)
- [`critters-webpack-plugin`](https://www.npmjs.com/package/critters-webpack-plugin) renders HTML in a JSDom environment on every webpack build. Doesn’t use a headless browser (= less heavy); returns all styles needed by the page, not only the above-the-fold ones (= may fix some `html-critical-webpack-plugin` glitches)
- [`isomorphic-style-loader`](https://www.npmjs.com/package/isomorphic-style-loader) helps to extract critical styles during server-side rendering

## CSS-in-JS

### Minification

CSS-in-JS libraries typically provide Critical CSS support out of the box and need fewer manual optimizations. However, they still need minification.

- [`minify-cssinjs-loader`](https://www.npmjs.com/package/minify-cssinjs-loader) works with all CSS-in-JS libraries thanks to regex-based matching. Does basic compression of style strings
- _Library-specific Babel plugins_. Many popular CSS-in-JS libraries have Babel plugins specifically created for them. They typically do a better job at optimization:
  - [babel-plugin-styled-components](https://www.npmjs.com/package/babel-plugin-styled-components)
  - [babel-plugin-emotion](https://www.npmjs.com/package/babel-plugin-emotion)

### Zero-runtime libraries

Most CSS-in-JS libraries ship with a runtime – a chunk of JavaScript that runs in the browser and makes the library work. That makes them easy to use but [brings noticeable performance costs](https://css-tricks.com/the-unseen-performance-costs-of-modern-css-in-js-libraries/).

However, there’re also several CSS-in-JS libraries that don’t use a runtime and don’t suffer from worse performance. They rely on build-time transformations instead:

- [`linaria`](https://www.npmjs.com/package/linaria): works with `linaria/loader` and `mini-css-extract-plugin` ([instructions](https://github.com/callstack/linaria/blob/master/docs/BUNDLERS_INTEGRATION.md#webpack))
- [`astroturf`](https://www.npmjs.com/package/astroturf): works with `astroturf/loader` and `mini-css-extract-plugin` ([instructions](https://github.com/4Catalyzer/astroturf#setup))

## Images

### Image compression tools: universal

All the tools below optimize `.png`, `.jpg`, `.gif` and `.svg` images. They’re based on [`imagemin`](https://www.npmjs.com/package/imagemin) and imagemin plugins, so they typically result in a similar level of compression.

Pick plugins over loaders. Plugins will optimize images that were produced by other loaders or plugins, whereas loaders will only trigger for files from your source code.

- [`image-webpack-loader`](https://www.npmjs.com/package/image-webpack-loader) ![](https://img.shields.io/npm/dw/image-webpack-loader)
- [`img-loader`](https://www.npmjs.com/package/img-loader) ![](https://img.shields.io/npm/dw/img-loader)
- [`imagemin-webpack`](https://www.npmjs.com/package/imagemin-webpack) ![](https://img.shields.io/npm/dw/imagemin-webpack)
- [`imagemin-webpack-plugin`](https://www.npmjs.com/package/imagemin-webpack-plugin) ![](https://img.shields.io/npm/dw/imagemin-webpack-plugin)

### Image compression tools: for a single format

The below tools focus on a specific format of images.

- [`svgo-loader`](https://www.npmjs.com/package/svgo-loader) optimizes `.svg` images by passing them through [`svgo`](https://github.com/svg/svgo)
- [`svg-sprite-loader`](https://www.npmjs.com/package/svg-sprite-loader) combines multiple `.svg` images into a single sprite
- [`webp-loader`](https://www.npmjs.com/package/webp-loader) converts images to the `webp` format

### Other tools

- [`lqip-loader`](https://www.npmjs.com/package/lqip-loader) generates low-quality image placeholders which you can use for lazy-loading images. Just like in Medium:

  ![](https://i.imgur.com/uBsYSNd.png)

- [`responsive-loader`](https://www.npmjs.com/package/responsive-loader) resizes one image to multiple various sizes. Works great with `<img srcset>` or `<picture>`
- [`svg-url-loader`](https://www.npmjs.com/package/svg-url-loader) generates 20-30% smaller `data`-urls for inline SVG images

## Fonts

- [`google-fonts-webpack-plugin`](https://www.npmjs.com/package/google-fonts-webpack-plugin) downloads Google Fonts to the build directory for self-hosting
- [`fontmin-webpack`](https://www.npmjs.com/package/fontmin-webpack) minifies icon fonts to just what’s used

## Gzip/Brotli

Gzip/Brotli compressors compress text so it’s smaller when served over the network.

Normally, this is done by a server like Apache or Nginx on runtime; but you might want to pre-build compressed assets to save the runtime cost.

- [`compression-webpack-plugin`](https://www.npmjs.com/package/compression-webpack-plugin) works for Gzip and Brotli
- [`brotli-webpack-plugin`](https://www.npmjs.com/package/brotli-webpack-plugin) works for Brotli

## Service workers

- [`service-worker-loader`](https://www.npmjs.com/package/service-worker-loader) takes a file, emits it separately from the bundle, and exports a function to register the file as a service worker:

  ```js
  import registerServiceWorker from 'service-worker-loader!./sw.js';

  registerServiceWorker({ scope: '/' });
  ```

- [`workbox-webpack-plugin`](https://www.npmjs.com/package/workbox-webpack-plugin) prefetches all webpack assets in the background and makes the app ready for working offline. It is based on Google’s [`workbox`](https://developers.google.com/web/tools/workbox) library that simplifies common usages of service workers

- [`offline-plugin`](https://www.npmjs.com/package/offline-plugin) also prefetches all webpack assets in the background and makes the app ready for working offline. It falls back to AppCache in browsers that don’t support service workers

## `<link rel>` and `<script async>`

- [`preload-webpack-plugin`](https://www.npmjs.com/package/preload-webpack-plugin) preloads asynchronous chunks¹ with `<link rel="preload">` or `<link rel="prefetch">`
- [`html-webpack-preconnect-plugin`](https://www.npmjs.com/package/html-webpack-preconnect-plugin) adds `<link rel="preconnect">` for a separate domain (e.g., an API server)
- [`script-ext-html-webpack-plugin`](https://www.npmjs.com/package/script-ext-html-webpack-plugin) adds `async` or `defer` attributes to bundle scripts

<small>¹ [Asynchronous chunks](https://webpack.js.org/guides/code-splitting/#dynamic-imports) are chunks that are created when you use `import()`.</small>

## Prerendering

Prerendering tools run an app during the build and return the HTML the app generates. This is an alternative to server-side rendering and helps to deliver the content to the user immediately – instead of making them wait until the bundle is loaded.

- [`prerender-spa-plugin`](https://www.npmjs.com/package/prerender-spa-plugin)
- [`prerender-loader`](https://www.npmjs.com/package/prerender-loader)

## Progressive web apps (PWA)

- [`webpack-pwa-manifest`](https://www.npmjs.com/package/webpack-pwa-manifest) generates a `manifest.json` and resizes app icons for a PWA

## Analysis tools

### Bundle contents

The following tools show relative sizes of all bundled modules. Use them to figure out what takes so much size and can be removed:

- [`webpack-bundle-analyzer`](https://www.npmjs.com/package/webpack-bundle-analyzer) is a webpack plugin. During the build, it generates an interactive HTML page with all bundle modules:

  ![](https://cloud.githubusercontent.com/assets/302213/20628702/93f72404-b338-11e6-92d4-9a365550a701.gif)

  <sup>(Animation credits: `webpack-bundle-analyzer`)</sup>

- [Webpack Visualizer](https://chrisbateman.github.io/webpack-visualizer/) is a website that operates on webpack stats (`webpack --json > stats.json`). It lets you upload the `stats.json` file and see the bundle contents:

  ![](https://user-images.githubusercontent.com/2953267/84274060-16f69b00-ab38-11ea-8338-e17f38fa6335.png)

- [`source-map-explorer`](https://www.npmjs.com/package/source-map-explorer) is a CLI tool that generates bundle stats based on source maps. It’s less detailed than `webpack-bundle-analyzer` – but you don’t need to reconfigure webpack to run it:

  ![](https://raw.githubusercontent.com/danvk/source-map-explorer/HEAD/screenshot.png)

  <sup>(Image credits: `source-map-explorer`)</sup>

- [`bundle-wizard`](https://www.npmjs.com/package/bundle-wizard) is a CLI tool that also generates bundle stats based on source maps. But, unlike `source-map-explorer`, it does that for a full page and includes all bundles:

  ![](https://user-images.githubusercontent.com/2953267/84275303-9173ea80-ab39-11ea-9c00-477e9c639dc1.png)

### Code duplicates

These tools help to find and remove duplicated code inside the bundles:

- [Bundle Buddy](https://www.npmjs.com/package/bundle-buddy) shows which bundles include which module. Use it to find duplicated code and fine-tune code splitting:

  ![](https://user-images.githubusercontent.com/2953267/83957155-36f33980-a86e-11ea-8fd6-44aa1b487c18.png)

- [`duplicate-package-checker-webpack-plugin`](https://www.npmjs.com/package/duplicate-package-checker-webpack-plugin) prints a warning if a bundle includes multiple versions of the same library:

  ![](https://raw.githubusercontent.com/darrenscerri/duplicate-package-checker-webpack-plugin/master/screenshot.png)

  <sup>(Image credits: `duplicate-package-checker-webpack-plugin`)</sup>

### Various tools

- [Webpack Analyse](https://github.com/webpack/analyse) shows all modules in the bundle – and _why_ they are bundled. Use it to understand [why you are loading that specific huge library](https://twitter.com/iamakulov/status/1337088053765140481):

  ![](https://user-images.githubusercontent.com/2953267/66723475-3c54cd00-ee22-11e9-96ff-751ba888d56d.png)
  
- [Statoscope](https://statoscope.tech) also shows all modules present in the bundle – and why they are bundled. It’s like the Webpack Analyse (the previous tool) but modern and more convenient:

  ![](https://user-images.githubusercontent.com/2953267/111714862-47076300-885b-11eb-8629-6880be562a2b.png)

- [Bundlephobia](https://bundlephobia.com) reports bundle sizes of JS libraries:

  ![](https://user-images.githubusercontent.com/2953267/66723322-4970bc80-ee20-11e9-8dfd-fef59fc7ef2f.png)

- [`webpack-dashboard`](https://www.npmjs.com/package/webpack-dashboard) reports sizes of modules and warnings like duplicated files during development:

  ![](https://camo.githubusercontent.com/3f5446837855aef7cb671a3c0ca6b9d351cf8045/687474703a2f2f692e696d6775722e636f6d2f714c3664584a642e706e67)

  <sup>(Image credits: `webpack-dashboard`)</sup>

## Webpack build performance

The following tools show how to optimize your webpack build speed.

- [`speed-measure-webpack-plugin`](https://www.npmjs.com/package/speed-measure-webpack-plugin) is a webpack plugin. During the build, the plugin measures your webpack build speed, giving an output like this:

  ![](https://raw.githubusercontent.com/stephencookdev/speed-measure-webpack-plugin/master/preview.png)

<sup>(Animation credits: `speed-measure-webpack-plugin`)</sup>

## Other web performance lists

- [Awesome WPO](https://github.com/davidsonfellipe/awesome-wpo) – A curated list of Web Performance Optimization
- [Webpack Libs Optimizations](https://github.com/GoogleChromeLabs/webpack-libs-optimizations) – A collection of Babel and webpack plugins to optimize the size of various popular libraries

## Contribute

Contributions welcome! Read the [contribution guidelines](contributing.md) first.

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0)

To the extent possible under law, Ivan Akulov has waived all copyright and related or neighboring rights to this work.
