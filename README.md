[![Build Status](https://travis-ci.org/Polymer/lazy-imports.svg?branch=master)](https://travis-ci.org/Polymer/lazy-imports)

# \<link rel="lazy-import"\>

This repo implements declarative, lazy, HTML Imports.

Normal HTML Imports are eager, meaning that they are loaded and evaluated in order first, before any code that follows. You can get a large performance improvement by lazily loading code at runtime, so that you only load the minimal amount of code needed to display the current view. This is a key piece of [the PRPL pattern](https://developers.google.com/web/fundamentals/performance/prpl-pattern/).

To do lazy loading of your HTML you can use javascript APIs like [`Polymer.importHref`](https://www.polymer-project.org/2.0/docs/api/#function-Polymer.importHref). What this repo adds to that is a _declarative_ way to describe the resources that you will import lazily, and a method for loading them. Because this kind of lazy import is declarative, the [polymer analyzer](https://github.com/Polymer/polymer-analyzer), [polymer linter](https://github.com/Polymer/polymer-linter), and [polymer bundler](https://github.com/Polymer/polymer-bundler) all understand them, giving you accurate lint warnings and sharded bundling without any configuration needed, just your source code.

To use lazy imports, write an HTML import as usual, except:

  1) give it a `rel` attribute of `lazy-import` instead of `import`
  2) give each import a `group`  attribute; you'll use this as a key later
  3) put the lazy import inside the `<dom-module>` of your element, but outside of its `<template>`.

Then apply the `LazyImportsMixin` mixin (or `LazyImportsBehavior`) to your element and call the `this.importLazyGroup('group-name')` method when you want to load code for that group, e.g. when the user navigates to a new page in your app. The `importLazyGroup` method returns a Promise that resolves once the imports have finished loading and executing.

## Changes in 2.0
A promise polyfill is not included by default but is necessary if running with webcomponents v0 polyfill (`webcomponents/webcomponentsjs#^0.7`). In webcomponents v1 polyfill (`webcomponents/webcomponentsjs#^1.0`), a promise polyfill is conditionally loaded using the `webcomponents-loader.js` or always loaded with `webcomponents-lite.js`. If you are looking for a promise polyfill, you can take a look at [promise-polyfill](https://github.com/PolymerLabs/promise-polyfill) or [es6-promise](https://github.com/stefanpenner/es6-promise)

## Examples

### Polymer 2.0 – LazyImportsMixin

```html
<link rel="import" href="../../polymer/polymer-element.html">
<link rel="import" href="../lazy-imports-mixin.html">

<dom-module id="upgrade-button">
  <link rel="lazy-import" group="lazy" href="lazy-element.html">
  <template>
    <button on-click="buttonPressed">Upgrade Element</button>
    <lazy-element>When upgraded, this element will have a red border</lazy-element>
  </template>
  <script>
    class UpgradeButton extends Polymer.LazyImportsMixin(Polymer.Element) {
      static get is() { return 'upgrade-button'; }
      buttonPressed() {
        this.importLazyGroup('lazy').then((results) => {
          console.log(results);
          this.dispatchEvent(new CustomEvent('import-loaded', results));
        });
      }
    }
    customElements.define(UpgradeButton.is, UpgradeButton);
  </script>
</dom-module>
```

### Polymer 1.0 and Polymer 2.0 Hybrid – LazyImportsBehavior

```html
<link rel="import" href="../../polymer/polymer.html">
<link rel="import" href="../lazy-imports-behavior.html">

<dom-module id="upgrade-button">
  <link rel="lazy-import" group="lazy" href="lazy-element.html">
  <template>
    <button on-click="buttonPressed">Upgrade Element</button>
    <lazy-element id="lazy">When upgraded, this element will have a red border</lazy-element>
  </template>
  <script>
    Polymer({
      is: 'upgrade-button',
      behaviors: [Polymer.LazyImportsBehavior],
      buttonPressed: function() {
        this.importLazyGroup('lazy').then(function(results) {
          console.log(results);
          this.fire('import-loaded', results);
        }.bind(this));
      }
    });
  </script>
</dom-module>
```

