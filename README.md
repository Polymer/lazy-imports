# \<link[rel="lazy-import"]\> [![Build Status](https://img.shields.io/travis/Polymer/lazy-imports.svg?style=flat-square)](https://travis-ci.org/Polymer/lazy-imports)

Declarative lazy HTML imports

## Examples

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

### Polymer 2.0 - Using Mixin

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
