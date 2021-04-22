# Finch CSS

**_Finch_ is a Sass framework for writing scalable CSS.**

Finch defines a series of modules to enable consistent, intuitive CSS authoring within a design system. Developers could simply use the compiled css (from the `dist/`) directory, but it is intended that *Finch* would be included in a projects Sass build. In that scenario, Finch can either be used as a whole, or by loading in the individual modules that are required (recommended!). A quick example of each here follows:

## Using Finch:

```scss
// project sass file (e.g. styles.scss)

// load in the root 'index' file from the Finch module,
// Sass will automatically create a `finch` namespace, or you
// can call it something else with the `@use '<path>' as f` syntax.
@use '../node_modules/finch';

// then we can use all the wondeful things that Finch has for us:
p:not([class]) {
  @include finch.text-style($size: lead, $weight: light);
}
```
## Using Finch modules:

We think this is the best way to use Finch. Pull in the modules that you need and configure them with your custom maps and variables (see `src/typography/typography.md` for detailed documentation of this module):

```scss
// project sass file (e.g. styles.scss)

// load in the index file from the Finch module, and call it
// something usable :)
@use '../node_modules/finch/typography' as type;

// now we can use the typography module:
p:not([class]) {
  @include type.text-style($size: lead, $weight: light);
}
```
