# Finch CSS

**_Finch_ is a Sass framework for writing design systems in scalable CSS.**

Finch defines a series of modules to enable consistent, intuitive CSS authoring within a design system. It is intended that *Finch Sass* would be included in a projects Sass build. Finch can either be used as a whole, or by loading in the individual modules that are required (recommended!). A quick example of each here follows:

## Using Finch:

```scss
// project sass file (e.g. styles.scss)

// load in the root 'index' file from the Finch module,
// Sass will automatically create a `finch` namespace, or you
// can call it something else with the `@use '<path>' as f` syntax.
@use '../node_modules/finch';

// then we can use all the wondeful things that Finch has for us:
p:not([class]) {
  @include finch.text-format($size: lead, $weight: light);
}
```
## Using Finch modules:

We think this is the best way to use Finch. Pull in the modules that you need and configure them with your custom maps and variables:

```scss
// project sass file (e.g. styles.scss)

// load in the index file from the Finch module, and call it
// something usable :)
@use '../node_modules/finch/typography' as typ;

// now we can use the typography module:
p:not([class]) {
  @include typ.text-format($size: lead, $weight: light);
}
```

## Configuration and customisation:

Part of the *Finch* philosophy is that *everything* should be customisable to your requirements, but we provide sensible (and hopefully good-looking) defaults for anything that you don't directly configure. Again, we'll stick with the typography module to show this in action.

```scss
// import your custom font:
@import url('https://fonts.googleapis.com/css2?family=Dancing+Script&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Newsreader&display=swap');

// pass it into the module
@use '../node_modules/finch/typography' as typ with (
  $font-families: (
    cursive: 'Dancing Script', cursive,
    serif: 'Newsreader', serif,
  ),
);

// now when you use any of the typography font family stuff, it'll use your font!
// So this would set the font-family of h1 elements to "Newsreader", and anything with the
// `.handwriting` class to "Dancing Script".
h1 {
  @include typ.text-format($family: serif);
}

.handwriting {
  @include typ.text-format($family: cursive, $weight: light);
}
```

For a full list of all the config options, see each module's own docs.

## Modules

*Finch* currently contains the modules listed below. This is a quick reference. Full documentation for each module is provided separately.

- **Layout:** The layout module handles everything to do with how content is placed on a page. Typical/common properties that are owned by this module include `display`, `padding`, `margin`, `z-index`. This module also exposes some helpful mixins that will aid in achieving consistent, reusable compositions.
- **Typography:** The typography module handles everything to do with how text is rendered on a page. Common uses are for things like `font-size`, `font-family`, `letter-spacing`. This module also exposes some helpful mixins that will aid in achieving consistent, reusable text styles.
