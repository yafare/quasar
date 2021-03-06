---
title: App Handling Assets
---
You will notice in the project structure we have two directories for assets: `/src/statics/` and `/src/assets/`. What is the difference between them? Some are static assets while the others are processed and embedded by the build system.

So let's try to answer the question above. We'll first talk about using regular assets then we'll see what the difference is for static assets.

## Regular assets - /src/assets
In `*.vue` components, all your templates and CSS are parsed by `vue-html-loader` and `css-loader` to look for asset URLs. For example, in `<img src="./logo.png">` and `background: url(./logo.png)`, `"./logo.png"` is a relative asset path and will be resolved by Webpack as a module dependency.

Because `logo.png` is not JavaScript, when treated as a module dependency, we need to use `url-loader` and `file-loader` to process it. Quasar CLI has already configured these webpack loaders for you, so you basically get features such as filename fingerprinting and conditional base64 inlining for free, while being able to use relative/module paths without worrying about deployment.

Since these assets may be inlined/copied/renamed during build, they are essentially part of your source code. This is why it is recommended to place Webpack-processed assets inside `/src/assets`, along side other source files. In fact, you don't even have to put them all in `/src/assets`: you can organize them based on the module/component using them. For example, you can put each component in its own directory, with its static assets right next to it.

### Asset Resolving Rules

Relative URLs, e.g. `./assets/logo.png` will be interpreted as a module dependency. They will be replaced with a auto-generated URL based on your Webpack output configuration.

URLs prefixed with `~` are treated as a module request, similar to `require('some-module/image.png')`. You need to use this prefix if you want to leverage Webpack's module resolving configurations. Quasar provides `assets` Webpack alias out of the box, so it is recommended that you use it like this: `<img src="~assets/logo.png">`. Notice `~` in front of 'assets'.

## Static Assets - /src/statics
Root-relative URLs, e.g. `/statics/logo.png` or `statics/logo.png` are not processed at all. This should be placed in `src/statics/`. These won't be processed by Webpack at all. The statics folder is simply copied over to the distributable folder as-is.

Quasar has some smart algorithms behind the curtains which ensure that no matter what you build (SPA, PWA, Cordova, Electron), your statics are correctly referenced *if and only if* all your statics start with `statics/` string. For this reason, do not use `/statics` as URL.

```html
<!-- Good! -->
<img src="statics/logo.png">

<!-- BAD! Don't! -->
<img src="/statics/logo.png">
```

## Vue Binding Requires Statics Only
Please note that whenever you bind "src" to a variable in your Vue scope, it must be one from the statics folder. The reason is simple: the URL is dynamic, so Webpack (which packs up assets at compile time) doesn't know which file you'll be referencing at runtime, so it won't process the URL.

```html
<template>
  <!-- imageSrc MUST reference a file from /src/statics -->
  <img :src="imageSrc">
</template>

<script>
export default {
  data () {
    return {
      <!--
        Referencing /src/statics.
        Notice string doesn't start with a slash. (/)
      -->
      imageSrc: 'statics/logo.png'
    }
  }
}
</script>
```

You can force serving static assets by binding `src` to a value with Vue. Instead of `src="statics/path/to/image"` use `:src="'statics/path/to/image'"`. Please note the usage of single and double quotes.

## Getting Asset Paths in JavaScript

In order for Webpack to return the correct asset paths, you need to use `require('./relative/path/to/file.jpg')`, which will get processed by `file-loader` and returns the resolved URL. For example:

``` js
computed: {
  background () {
    return require('./bgs/' + this.id + '.jpg')
  }
}
```

Note the above example will include every image under `./bgs/` in the final build. This is because Webpack cannot guess which of them will be used at runtime, so it includes them all.
