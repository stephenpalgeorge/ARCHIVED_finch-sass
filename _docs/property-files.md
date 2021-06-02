# Property files

Property files occur in every module of *Finch* and they are, more or less, what they sound like - a file that deals with a particular css property. Some are, however, far more complex than others. This document outlines the key features that you are likely to find in a 'property file', and the rationale behind them.

Before it is all explained, here's an example of a (fairly standard) property file in its entirity. The commented numbers/headings are not in the original file but just included for ease of reference below:

```scss
// 1. IMPORTS
// ----------
@use 'sass:map';
@use 'sass:list';
@use '../../config';

// 2. MAP DECLARATIONS
// ----------
$finch-font-sizes: (
  small-print: map.get(config.$size-scale, 300),
  base: map.get(config.$size-scale, 400),
  lead: map.get(config.$size-scale, 500),
  sub-heading: map.get(config.$size-scale, 800),
  heading: map.get(config.$size-scale, 900),
);
$set-font-sizes: () !default;
$sizes: map.merge($finch-font-sizes, $set-font-sizes);

// 3. GETTER
// ----------

/// Getter function
/// @param $key one of the keys from the `$sizes` map.
/// @return {Length | null} the font-size declaration associated with the given `$key`, or `null` if `$key` is not found.
@function get-font-size($key) {
  // return the given value immediately if it is a reserved keyword for this property:
  $spec-values: (xx-small, x-small, small, medium, large, x-large, xx-large, xxx-large);
  @if list.index(config.$global-values, $key) or list.index($spec-values, $key) {
    @return $key;
  }
  // try and find the value in the map and either return it, or handle the error case below.
  $value: map.get($sizes, $key);
  @if $value {
    @return $value;
  }

  @warn 'no font-size found for "#{$key}"';
  @return null;
}
```

These are the core parts of a property file. Some property files also declare their own mixins, or might, in some rare cases, actually handle more than one property (e.g. the `borders` file sets all of the above for `border-width` and `border-radius`, and does some handling for other `border` related properties).

## 1. IMPORTS

This is fairly self explanatory - we bring in all of the modules that we need for that file. The built-in sass modules come first, then our own module dependencies thereafter.

## 2. MAP DECLARATIONS

This is a common pattern in *Finch*. We declare a map for the default values (in the example this is `$finch-font-sizes`) and then an empty map, declared as `!default` (in the example this is `$set-font-sizes`). These two maps are then merged into one `$sizes` map. Whatever is passed into the `$set-*` map will be merged in with the hard-coded values declared by Finch. By setting the configurable `$set-*` map *after* the hard-coded `$finch-*` map, we ensure that any user-specified values take precedence and override the *Finch* values.

## 3. GETTER

We declare a function that gets one of the values out of the map. This gives us a robust API that handles errors and allows keyword values to be used.

---

This gives us everything we need to start composing mixins that bring multiple properties together.
