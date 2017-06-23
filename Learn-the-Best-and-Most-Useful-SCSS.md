# [Learn the Best and Most Useful SCSS](https://egghead.io/courses/learn-the-best-and-most-useful-scss)

- [x] 1. Transpile SCSS to CSS using node-sass
- [x] 2. Organize SCSS into Multiple Files with Partials
- [x] 3. Organize Styles with SCSS Nesting and the Parent Selector
- [x] 4. Use SCSS Variables for Readable and Maintainable Stylesheets
- [x] 5. Use Standard Built-in SCSS Functions for Common Operations
- [x] 6. Reuse Styles with the SCSS @mixin Directive
- [x] 7. Reuse Styles with the SCSS @extend Directive
- [x] 8. Write similar classes with the SCSS @for Control Directive
- [x] 9. Loop Over Data with the SCSS @each Control Directive
- [x] 10. Write Custom Functions with the SCSS @function Directive

## [1. Transpile SCSS to CSS using node-sass](https://egghead.io/lessons/css-transpile-scss-to-css-using-node-sass)

> In this lesson we learn how to set up SCSS and transpile your SCSS code into CSS with node-sass.

```bash
$ yarn add --dev node-sass
```
`package.json`

```json
{
  "scripts": {
    "start": "node-sass -o css scss"
  }
}
```

## [2. Organize SCSS into Multiple Files with Partials](https://egghead.io/lessons/css-organize-scss-into-multiple-files-with-partials)

> Tired of dealing with monolithic CSS files? Are requests for multiple CSS files hurting your performance? In this lesson we learn how to separate our styles with SCSS partials, and how SCSS imports compile to one file so there's only one request.

Prepend underscore(`_`) to partial Sass file. So pre-processor not generate CSS that matched 1 to 1 from Sass. But CSS file that is imported in Sass is generated to another resource.

## [3. Organize Styles with SCSS Nesting and the Parent Selector](https://egghead.io/lessons/css-organize-styles-with-scss-nesting-and-the-parent-selector)

> SCSS nesting can produce DRYer code by targeting child elements without having to write the parent class. Nesting up to 3 levels deep can help us understand relationships between styles. The SCSS parent selector represents the parent class, so it can DRY up targeting pseudo-elements/classes and be an asset for naming conventions. In this lesson we learn how to remove the redundancy of targeting pseudo-elements/classes, and child elements by taking advantage of SCSS’s nesting and parent selectors.

```sass
.spiderman {
  padding: 1rem 2rem;

  &::before {
    display: block;
    content: url('');
  }

  img {
    transition: transform .8s ease-in-out;

    &:hover { transform: rotate(360deg); }
  }
}
```

## [4. Use SCSS Variables for Readable and Maintainable Stylesheets](https://egghead.io/lessons/css-use-scss-variables-for-readable-and-maintainable-stylesheets)

> Updating the same color or metric multiple times is no fun. SCSS variables improve maintainability by allowing one name to represent a value that can be used anywhere. SCSS variables also give us the power of creating our own naming conventions to express the intent of specific values. In this lesson we learn how to use SCSS variables to assign user friendly names to colors/metrics so one value change will update that value in all the right places.

## [5. Use Standard Built-in SCSS Functions for Common Ope rations](https://egghead.io/lessons/css-use-standard-built-in-scss-functions-for-common-operations)

> We can use javascript for color and opacity variations, math, list and map logic or to see if something exists. SCSS includes functions for a wide range of common use cases for logic in our styles. In this lesson we look at some of the more useful color functions to improve development velocity, readability, and simplify the code. Be sure to checkout all the SCSS functions: http://sass-lang.com/documentation/Sass/Script/Functions.html

- `rgb()`
- `linear-gradient()`
- `lighten()`
- `darken()`
- `mix()`
- `complment()`

## [6. Reuse Styles with the SCSS @mixin Directive](https://egghead.io/lessons/css-reuse-styles-with-the-scss-mixin-directive)

> Copy/pasting the same code is redundant and updating copy/pasted code slows development velocity. Mixins are reusable chunks of code that are included, similar to calling a function, instead of copy/pasted.

> Mixins have some nice features:
> - Arguments just like functions.
> - Arguments can have default values and optional values.
> - Named arguments allow us to use optional and default arguments when the mixin is included.
> - Variable arguments allow us to have a dynamic number of arguments when the mixin is included.
> - The @content directive allow us to add additional styles when the mixin is included.

> In this lesson we learn how to DRY up the code with the SCSS @mixin directive and make copy/paste a thing of the past.

```sass
@mixin make-transitions($transitions...) { transition: $transitions; }

@mixin make-character($base-color: #a83b24, $mix-color: #fffaa6, $border: null) {
  $light-color: lighten($base-color, 20%);
  $cbc: complement($base-color);

  &-text {
    color: mix($base-color, $mix-color, 75%);
  }

  img { @content; }
}
```

## [7. Reuse Styles with the SCSS @extend Directive](https://egghead.io/lessons/css-reuse-styles-with-the-scss-extend-directive)

> We can write reusable styles with the SCSS @extend or @mixin directives. Which one is better? It depends. A better question is, how do they differ?
  Extends:
  change the source order, mixins don’t.
  maintain relationships, mixins don’t.
  share inherited traits, mixins don’t.
  can extend multiple classes, mixins don’t.
  can create multiple class declarations in the compiled CSS, mixins don’t.
  can use the placeholder selector, mixins don’t.

> Mixins:
  can accept arguments, extends don’t.
  can pass additional content, extends don’t.
  repeat code when compiled, extends group class names together.
  work well wIth media queries, extends have a limited interaction wIth media queries.

> In this lesson we learn about writing reusable styles with the @extend directive and how it compares to the @mixin directive.

## [8. Write similar classes with the SCSS @for Control Directive](https://egghead.io/lessons/css-write-similar-classes-with-the-scss-for-control-directive)

> Writing similar classes with minor variations, like utility classes, can be a pain to write and update. Sometimes just a single character is the only difference between classes and updating the defining parameter means we need to change it for every class name and value. We can write a class one time and the @for directive can write all the minor variations for us. If the similar classes need to be updated, with the help of the @for directive, they only need to be updated once. In this lesson we learn how to leverage the power of the SCSS @for control directive to relieve the pain.

```sass
@for $i from 1 to 10 {
  .order { order: $i; }
}
```

```sass
@for $i from 1 through 10 {
  .order { order: $i; }
}
```

```sass
@for $i from 1 through 10 {
  $value: .5 * $i;

  .mt-half-#{$i}rem {
    margin-top: #{$value}rem;
  }
}
```

```sass
@for $i from 1 through 10 {
  $value: .5 * $i;
  $has-decimal: floor($value) != $value;
  $class-name: if($has-decimal, #{$value - .5}pt5, $value);

  .mt-#{$class-name} {
    margin-top: #{$value}rem;
  }
}
```

```sass
@for $i from 1 through 10 {
  $value: .5 * $i;

  [class~="mt-#{$value}"] {
    margin-top: #{$value}rem;
  }
}
```

## [9. Loop Over Data with the SCSS @each Control Directive](https://egghead.io/lessons/css-loop-over-data-with-the-scss-each-control-directive)

> The SCSS @for directive is great when we know how many iterations are required and we only need 1 variable. What if we need multiple variables to iterate over data sets? Hello SCSS maps and the @each control directive! In this lesson, we’ll check out iterating with lists and looping over data sets with maps and the @each directive.

```scss
.container {
  border: 2px solid red;
  margin: 0.5rem auto;
  font-size: 2rem;
}

.logo {
  height: 250px;
  width: 250px;
  margin: 0.5rem auto;
}

@each @hero in wonder-woman, spiderman, batman, superman {
  .#{$hero}-logo {
    background-image: url("../images/#{$hero}-logo.gif");
  }
}


$superheroes: wonder-woman, spiderman, batman, superman
@each @hero in $superheroes {
  .#{$hero}-logo {
    background-image: url("../images/#{$hero}-logo.gif");
  }
}
```

> Maps

```scss
$breakpoints: (sm: 375px, md: 768px, lg: 1200px);
@each $size, $bp in $breakpoints {
  @media only screen and (min-width: $bp) {
    .container-width {
      width: $bp;
      &::before { content: "#{$size}"; }
    }
  }
}
```

```scss
$breakpoints: (sm: 375px, md: 768px, lg: 1200px);
$container-widths: (sm: 250px, md: 500px, lg: 750px);
@each $size, $bp in $breakpoints {
  @media only screen and (min-width: $bp) {
    .container-width {
      width: map-get(container-widths, $size);
    }
  }
}
```

## [10. Write Custom Functions with the SCSS @function Directive](https://egghead.io/lessons/css-write-custom-functions-with-the-scss-function-directive)

> Writing SCSS @functions is similar to writing functions in other programming languages; they can accept arguments and have return statements. SCSS provides a ton of great features, but sometimes we need to roll our own function. We can do that too! They’re useful when the desired functionality isn’t available from the built in SCSS functions and a mixin just won’t do. In this lesson, we learn how and when to use SCSS function directives.

```scss
@function background($color) {
  @return $color;
}
```
> Sass functions cannot affect anything outside of their scope. We can try to update a variable in the function, but the function won't update a variable defined outside of itself.

```scss
$thing: thing;

@function background($color) {
  $think: stuff;
  @return $color;
}

.mad { background: background(red); }
.thing { content: "#{$thing}"; }  // thing, No mutation to stuff
```
> Must have return value

> Sass function can have default, optional value and variable values.

```scss
$translucent-color: rgba(silver, 0.5);

@function background($color: $translucent-color, $img: null, $xtra-values...) {
  $url: if($img, url('./super/duper/really/long/path/to/your/images/#{$img}.jpg'), null);
  @return $color $url $xtra-values;
}
```

> Mixins return reusable blocks of styles. Functions return reusable values.

```scss
// _@function-directive.scss

$ratio: 1.2;

@function font-scale(@exponent, $base-font-size: 14px, $ratio: 1.2) {
  $value: 1;

  @for $i from 1 through $exponent {
    $value: $value * $ratio;
  }

  @return if($exponent > 0, $base-font-size * $value, $base-font-size);
}

.stuff { font-size: font-scale(0); }
.things { font-size: font-scale(2); }
```

```scss
@for $i from 1 through 6 {
  @exponent: 7 - $i;

  h#{$i} { font-size: font-scale($exponent) }
}
```
