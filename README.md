<!-- To edit this file, edit /src/README.md, not /README.md -->

# style-mod

Minimal CSS module shim for generating CSS rules and anonymous class
names for sets of style declarations and attaching such a set to a
document or shadow root.

Using it would look something like this:

```javascript
const {StyleModule} = require("style-mod")
const myModule = new StyleModule({
  main: {
    fontFamily: "Georgia, 'Nimbus Roman No9 L'",
    margin: "0"
  },
  callout: {
    color: "red",
    fontWeight: "bold",
    "&:hover": {color: "orange"}
  }
})
StyleModule.mount(document, myModule)
document.body.className = myModule.main
```

This code is open source, released under an MIT license.
    
## Documentation

### class StyleModule

 * `new `**`StyleModule`**`(spec: Object< Style >, options: ?{generateClasses: ?boolean}) → StyleModule`\
   Instances of this class bind the property names from `spec` to CSS
   class names that assign the styles in the corresponding property
   values, unless `generateClasses` is `false`, in which case the
   property names in the spec are treated as plain CSS selectors.

   A style module can only be used in a given DOM root after it has
   been _mounted_ there with `StyleModule.mount`.

   Style modules should be created once and stored somewhere, as
   opposed to re-creating them every time you need them. The amount of
   CSS rules generated for a given DOM root is bounded by the amount
   of style modules that were used. So to avoid leaking rules, don't
   create these dynamically, but treat them as one-time allocations.

 * `static `**`newName`**`() → string`\
   Generate a new unique CSS class name.

 * `static `**`mount`**`(root: Document | ShadowRoot, modules: [StyleModule] | StyleModule)`\
   Mount the given set of modules in the given DOM root, which ensures
   that the CSS rules defined by the module are available in that
   context.

   Rules are only added to the document once per root.

   Rule order will follow the order of the modules, so that rules from
   modules later in the array take precedence of those from earlier
   modules. If you call this function multiple times for the same root
   in a way that changes the order of already mounted modules, the old
   order will be changed.


Where the `Style` type is defined as:

 * **`Style`**`: Object< Style | string >`\
   A style is an object that, in the simple case, maps CSS property
   names to strings holding their values, as in `{color: "red",
   fontWeight: "bold"}`. The property names can be given in
   camel-case—the library will insert a dash before capital letters
   when converting them to CSS.

   If you include an underscore in a property name, it and everything
   after it will be removed from the output, which can be useful when
   providing a property multiple times, for browser compatibility
   reasons.

   A property called `specificity` has a special meaning: if it holds
   a number _N_, greater than 0, the selector for the class will have
   _N_ extra dummy classes added, and those dummy classes will also be
   present in the class name string created for the style. This allows
   you to create rules that take precedence over other rules, even
   when they are defined earlier.

   A property in a style object can also be a sub-selector, which
   extends the current context to add a pseudo-selector or a child
   selector. Such a property should contain a `&` character, which
   will be replaced by the current selector. For example `{"&:before":
   {content: '"hi"'}}`. Sub-selectors and regular properties can
   freely be mixed in a given object. Any property containing a `&` is
   assumed to be a sub-selector.

   Finally, a property can specify an @-block to be wrapped around the
   styles defined inside the object that's the property's value. For
   example to create a media query you can do `{"@media screen and
   (min-width: 400px)": {...}}`.


