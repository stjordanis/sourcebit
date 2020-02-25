<img alt="Sourcebit logo" src="https://raw.githubusercontent.com/stackbithq/sourcebit/master/Sourcebit.svg?sanitize=true" width="300">

![Node CI](https://github.com/stackbithq/sourcebit/workflows/Node%20CI/badge.svg?branch=master)
[![npm version](https://badge.fury.io/js/sourcebit.svg)](https://badge.fury.io/js/sourcebit)

> Sourcebit helps developers build data-driven JAMstack sites by pulling data from any third-party resource.

## Table of contents

- [Introduction](#introduction)
- [Getting started](#getting-started)
  - [Manual installation](#manual-installation)
  - [Manual configuration](#manual-configuration)
- [Usage](#usage)
  - [As a CommonJS module](#as-a-commonjs-module)
  - [As a command-line tool](#as-a-command-line-tool)
  - [Disabling cache](#disabling-cache)
- [Plugin directory](#plugin-directory)
  - [Source plugins](#source-plugins)
  - [Target plugins](#target-plugins)
  - [Other plugins](#other-plugins)
- [Contributing](#contributing)

## Introduction

Sourcebit connects to multiple data sources, for example data in a headless CMS like Contentful or Sanity, to a destination, such as a JAMstack site built using Jekyll or Hugo. The easiest way to understand how this works is to see it in action via our [video demo on YouTube](https://www.youtube.com/watch?v=BrZbWMXB4TQ).

[![](https://img.youtube.com/vi/fPvfeP1lzTY/0.jpg)](https://www.youtube.com/watch?v=fPvfeP1lzTY)

Sourcebit works through the use of two types of plugins:

- _Source plugins_ are responsible for fetching data, normalizing it to a standard format, and placing the resulting entries on sets of data called _data buckets_. Subsequently, any combination of plugins may consume, transform and persist these data buckets in any way they like.
- _Target plugins_ are tasked with writing data into a format and location that other programs – such as static site generators – expect. A target plugin is not required, however. This is useful for situations where the source will be called via code, such as in a site built with tools like Next.js.

Read more about the [anatomy of a plugin](https://github.com/stackbithq/sourcebit/wiki/Anatomy-of-a-plugin).

## Getting started

To ease the process of configuring Sourcebit, we've created a command-line tool that provides an interactive setup process that will install the project and any necessary plugins. It asks a series of questions defined by each plugin and generates the necessary configuration file (`sourcebit.js`) so that you can get up and running in no time.

To start this process, in your project directory run `npx create-sourcebit` or, if you've already installed Sourcebit, `npm init sourcebit`.

### Manual installation

Sourcebit is distributed as an [npm module](https://www.npmjs.com/package/sourcebit). We recommend using the interactive setup process, but if you would like to manually install it and add it as a dependency to your project, run:

```
npm install sourcebit --save
```

### Manual configuration

The interactive setup process will generate the necessary configuration for you. Manual configuration can be useful in scenarios where you need to customize the existing behavior generated by the setup process or if you are creating a specific configuration that the setup process does not yet support.

Everything about Sourcebit, including its plugins and their parameters, is configured in a JavaScript object that lives in a file called `sourcebit.js`. Each plugin can specify options and/or functions as seen in the basic code example below.

It looks something like this:

```js
module.exports = {
  plugins: [
    {
      module: require("sourcebit-some-plugin-1"),
      options: {
        pluginOption1: "foo",
        pluginOptino2: "bar"
      }
    },
    {
      module: require("sourcebit-some-plugin-2"),
      options: {
        pluginFunction1: (a, b) => a + b
      }
    }
  ]
};
```

It's important to note that while every plugin block is defined with the `module` and `options` properties, the actual options passed to each options block varies widely and is defined and documented by each plugin.

## Usage

### As a CommonJS module

To use Sourcebit as a CommonJS module, include it in your project and call its `fetch` method.

```js
const sourcebit = require("sourcebit");
const config = require("./sourcebit.js");
const options = {};

sourcebit.fetch(config, options).then(data => {
  console.log(data);
});
```

### As a command-line tool

To use Sourcebit as a command-line tool, once it is installed, run the `sourcebit fetch` command in your project directory.

```
$ sourcebit fetch
```

Note that if you do not have your local npm_modules on your PATH, you will need to run `./node_modules/.bin/sourcebit fetch` or [follow the instructions here](https://coderwall.com/p/i5z1cg/automatically-update-path-with-proper-node_modules-bin) to update your PATH.

When working on content in the CMS, you can automatically update content locally with changes made in the CMS by using the `--watch` flag.

```
$ sourcebit fetch --watch
```

### Disabling cache

Sourcebit core has built-in support for caching. Whenever a plugin uses `setPluginContext` to save data to its context object, it gets persisted to a file named `.sourcebit-cache.json` on disk. When `sourcebit fetch` is next executed, the context object will be automatically populated with the cached content before the `bootstrap` method is invoked.

To disable cache, add the flag `--no-cache` to the `sourcebit fetch` command if you're using the CLI. If you're using the CommonJS module, set `cache: false` in the `options` object (i.e. the second parameter of `fetch()`).

## Plugins

Sourcebit is designed to be completely extensible. Documentation on how to build plugins can be found on our [wiki](https://github.com/stackbithq/sourcebit/wiki).

* [Anatomy of a plugin](https://github.com/stackbithq/sourcebit/wiki/Anatomy-of-a-plugin)
* [Configuration](https://github.com/stackbithq/sourcebit/wiki/Configuration)
* [Data normalization](https://github.com/stackbithq/sourcebit/wiki/Data-normalization)
* [Debugging](https://github.com/stackbithq/sourcebit/wiki/Debugging)
* [Plugin API](https://github.com/stackbithq/sourcebit/wiki/Plugin-API)
* [Plugin registry](https://github.com/stackbithq/sourcebit/wiki/Plugin-registry)
* [Writing files to disk](https://github.com/stackbithq/sourcebit/wiki/Writing-files-to-disk)

### Plugin directory

#### Source plugins

- [`sourcebit-sample-plugin`](http://npmjs.com/package/sourcebit-sample-plugin): A sample plugin with mock data, for demonstration/educational purposes.
- [`sourcebit-source-contentful`](http://npmjs.com/package/sourcebit-source-contentful): A source plugin for [Contentful](https://www.contentful.com/).
- [`sourcebit-source-sanity`](http://npmjs.com/package/sourcebit-source-contentful): A source plugin for [Sanity](https://sanity.io/).

#### Target plugins

- [`sourcebit-target-jekyll`](http://npmjs.com/package/sourcebit-target-jekyll): A target plugin for the [Jekyll](https://www.jekyllrb.com/) static site generator.

#### Other plugins

- [`sourcebit-transform-assets`](https://github.com/stackbithq/sourcebit-transform-assets): A plugin for downloading remote assets

## Contributing

If you'd like to contribute to Sourcebit, either by adding a feature to the core application or by creating a brand new plugin, check out our [contributing guidelines](https://github.com/stackbithq/sourcebit/blob/master/CONTRIBUTING.md).
