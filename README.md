# \<lazy-imports\>

Declarative lazy HTML imports

## Examples

```html
<link rel="import" href="../../polymer/polymer.html">
<link rel="import" href="../../paper-button/paper-button.html">
<link rel="import" href="../lazy-imports.html">
<dom-module id="upgrade-button">
  <link rel="lazy-import" group="lazy" href="lazy-element.html">
  <template>
    <paper-button on-tap="buttonPressed">Upgrade Element</paper-button>
    <lazy-element>When upgraded, this element will have a red border</lazy-element>
  </template>
  <script>
  Polymer({
    is: 'upgrade-button',
    properties: {},
    buttonPressed: function() {
      this.importLazyGroup("lazy").then(function(results) {
        console.log(results);
        this.fire("import-loaded", results);
      }.bind(this));
    },
    behaviors: [Polymer.LazyImportsBehavior]
  })
  </script>
</dom-module>
```
