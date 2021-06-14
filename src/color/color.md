# Color module

The *Finch Color module* is unlike the others in that it contains no property files. This is because there is only really one property in question - `color`. Any other use of color is in the context of another property (border-color, outline-color, background-color etc). It therefore makes most sense to write a color API that can be easily consumed in any context.

This document contains:
- **Color Definitions:** An explanation of how colors are created, generated and categorised.
- **Functions:** An overview of the functions at the heart of the color module.
- **Configuration:** A description of how the module can be adapted and customised.

## Color Definitions

The `color` module defines a `$color-palette` map that contains a number of key - value pairs for different types of color. *Finch* organises the color definitions into 3 main groups. Whilst not a requirement, it is recommended that you stick to these categories when introducing your own, custom colors into the palette. The categories are:

- **Theme colors:** these colors are labelled according to their use within the design system/brand. Examples are `main`, `secondary` etc...
- **Named colors:** these colors are labelled directly with color names like `red`, `orange` etc. Your custom values could be more creative - `duckegg-blue`, `racing-green` etc.
- **Contextual colors:** these colors are labelled based on their role/purpose. Examples are `error`, `warning`, `success` etc...

The `$color-palette` map is then used to generate further colors. Each color within the `$color-palette` is used as a basis for producing 11 "shades", the keys for which range from 0, 100, 200...900, 1000 - with the lightness increasing in correlation with the key. Regardless of the base color, `0` will always be black and `1000` will always be white.
The `base` color will appear in the `shades` map twice for each color, once under its correct position in the aforementioned order (e.g. if the base color has a lightness of `32%`, it will sit at `300`, or `78%` will sit at `700`) and again under the key `base`, for ease of reference.

---

## Functions

### use()

The heart of the `color` module is the `use` function, which aims to create as convenient an API as possible for using *Finch* colors. The function accepts 2 arguments:

- **$key:** a color 'key' - the name of one of the colours in the `$color-palette` map. E.g. `main`, `violet` or `info`.
- **$shade:** an optional (defaults to `null`) parameter that will adjust the lightness of the target color. Valid values are between 0 - 1000, in increments of 100 (500, 600, 700...).

The function returns the value of the specified color, normally in hex format, but sometimes as a keyword (`white`, `black` for example).
Some examples of using the `use` function follow:

```scss
@use 'finch/color' as clr;

.title { color: clr.use(main); }
// full disclosure, I have no idea if this looks any good - just put some
// random values in for an example :)
.card {
  background-color: clr.use(indigo, 800);
  color: clr.use(indigo, 200);
}
```

---

## Configuration

When you use the `color` module, you can configure it with a number of variables that are declared as `!default` in the module's index. The layout module currently has the following configurable variables:

- $set-responsive
- $set-utilities
- $use-pure
- $colors

Passing in a custom map under the `colors` variable name will see your values be merged with (or override where there are naming colisions) the default values set by *Finch*. A simple example of this follows:

```scss
// bring in the layout module and pass in our own margins.
@use 'finch/color' as clr with ($colors: (
  // we override Finch's default `red` with our own
  red: #FF2200,
  // and add a new "light pink" color to the palette
  light-pink: #FFEEEE,
));
```

The `$use-pure` variable accepts a boolean value and, when true, will see the named colors in *Finch's* `$color-palette` map overridden with "pure" colors. "Pure" in this sense just means an unopinionated definition of the color, so `red` would simply be `rgb(255, 0, 0);` and blue `rgb(0, 0, 255);`.

### Utilities

The *set-responsive* and *set-utilities* maps are special configuration maps. Both of them hold a list of all the properties that the module handles as 'keys', and sets a boolean value against each one. These are *all* `false` by default and must be manually/intentionally overridden by the user.
The boolean values from these maps are iterated over to generate utility classes for the properties and their values, and for each breakpoint, if the `responsive` flag is `true`. Each utility class also creates an `!important` version, the class name for which simply appends a '!'.

```scss
@use 'finch/layout' as lyt with (
  $set-utilities: (
    color: true,
    background-color: true,
  ),
);

// classes like `.color:main` and `.bg-color:warning` are now available.
```

```scss
@use 'finch/layout' as lyt with (
  $set-utilities: (
    color: true,
    background-color: true,
  ),
  $set-responsive: (
    color: true,
    background-color: true,
  ),
);

// classes like `.color:yellow`, `.background-color:dark!` and `.color:green@sm`
// are now available.
```
