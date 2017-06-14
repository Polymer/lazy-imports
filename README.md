# \<link rel="lazy-import"\> 

[![Build Status](https://img.shields.io/travis/Polymer/lazy-imports.svg?style=flat-square)](https://travis-ci.org/Polymer/lazy-imports)

This repo implements declarative, lazy, HTML Imports. Normal HTML Imports are eager, meaning that they are loaded and evaluated in order first, before any code that follows. There is also a [javascript API](https://www.polymer-project.org/1.0/docs/devguide/instance-methods#imports-and-urls) for performing HTML Imports, allowing lazy loading of code at any point in time. Lazy loading lets you build an app in a sharded way, so that only the minimal amount of code is loaded to display the current view. This is a key piece of [the PRPL pattern](https://developers.google.com/web/fundamentals/performance/prpl-pattern/).

What this repo adds on top of the above is a declarative way to describe the resources that you will import lazily, and a small mixin for using those declarative imports. The [polymer analyzer](https://github.com/Polymer/polymer-analyzer), [polymer linter](https://github.com/Polymer/polymer-linter), and [polymer bundler](https://github.com/Polymer/polymer-bundler) all understand these declarative lazy imports, giving you accurate lint warnings and sharded bundling without any configuration needed.

To use lazy imports, write an HTML import as usual, except:

  1) give it a `rel` attribute of `lazy-import` instead of `import`
  2) give each import a `group` attribute, you'll use that as a key later
  3) put the lazy import inside the `<dom-module>` of your element, but outside of its template.
  
Then apply the LazyImportsMixin mixin (or LazyImportsBehavior) to your element and call the `this.lazyImportGroup('group-name')` when you want to load code for that group, e.g. when the user navigates to a new page in your app. The `lazyImportGroup` method returns a Promise that resolves once the imports have finished loading and executing.

## Examples

### Polymer 2.0 – LazyImportsMixin

```html
<link rel="import" href="../../polymer/polymer.html">
<link rel="import" href="../../paper-button/paper-button.html">
<link rel="import" href="../lazy-imports-mixin.html">

<dom-module id="upgrade-button">
  <link rel="lazy-import" group="lazy" href="lazy-element.html">
  <template>
    <paper-button on-click="buttonPressed">Upgrade Element</paper-button>
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
<link rel="import" href="../../paper-button/paper-button.html">
<link rel="import" href="../lazy-imports-behavior.html">

<dom-module id="upgrade-button">
  <link rel="lazy-import" group="lazy" href="lazy-element.html">
  <template>
    <paper-button id="button" on-tap="buttonPressed">Upgrade Element</paper-button>
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

