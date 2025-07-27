
# Sass & SCSS Best Practices Guide
## For Large-scale Angular Projects

---

## Table of Contents
1. Introduction
2. Sass/SCSS Fundamentals
    - Syntax
    - Variables
    - Nesting
    - Imports: @use vs @import
    - Mixins
    - Extend
    - Mathematical and Color Functions
    - Types of Values
3. Intermediate Sass
    - Built-in Functions (lists, maps, math, string, selector, meta)
    - Advanced Mixins & Parameters
    - Loops: @for, @each, @while
    - Conditionals: @if, @else
    - Lists & Maps: Usage and Conversion
    - Variable Interpolation
    - Partials & File Organization
4. Advanced Sass Patterns
    - Custom Functions
    - Advanced Map/List Operations
    - Modules: @use, @forward, Namespaces, as
    - Scope & Privacy (public/private exposure)
    - Defensive Programming (!default, existence checks)
    - Architecture (BEM, ITCSS, SMACSS)
    - Performance Optimization & Compilation
    - Utility Libraries & Frameworks
    - Metaprogramming
    - Theming Automation
    - Library Versioning & Index Files
    - Patterns for Scalability & Maintenance
5. Angular Integration & Large Project Strategies
    - Recommended Folder Structure
    - main.scss & Single Compilation
    - Per-component SCSS
    - Imports Simplification
    - Theming & Dynamic Styles
    - Linting, Testing, and Documentation
6. Documentation Guidelines
    - How to Document Sass Files, Mixins, and Variables
    - Templates and Examples
7. Resources

---

## 1. Introduction
This guide is designed for front-end developers working with Sass/SCSS in large-scale Angular projects. It covers foundational and advanced topics, real-world usage, file organization strategies, theming, and tips for maintainable, scalable CSS architectures.

---

## 2. Sass/SCSS Fundamentals

### Syntax: SCSS vs SASS
- **SCSS**: CSS-like, uses curly braces and semicolons. Recommended for most projects.
- **SASS**: Indented syntax, no braces or semicolons. Less common.

**Example (SCSS):**
```scss
body {
  color: blue;
}
```
**Example (SASS):**
```sass
body
  color: blue
```

### Variables
Store reusable values for colors, spacing, typography, etc.
```scss
$primary-color: #3498db;
.button {
  background-color: $primary-color;
}
```

### Nesting
Use for logical, shallow nesting of selectors—avoid more than 2-3 levels. Use `&` for pseudo-classes, BEM modifiers, etc.
```scss
.card {
  padding: 1rem;
  .title {
    font-size: 1.5rem;
  }
  &:hover {
    box-shadow: 0 2px 8px #ccc;
  }
}
```
**Best Practice:**
- Keep nesting shallow (max 2-3 levels), prefer classes over element selectors.
- Avoid nesting HTML tags inside classes.

### Imports: @import vs @use
- **@import**: Old, all variables/mixins/functions are global. Can lead to conflicts.
- **@use**: Modern, everything is namespaced. Prevents conflicts, enables modular design.
- **@use 'file' as *;**: Removes namespace, similar to @import, but only recommended for small projects.
- **@forward**: Re-exports variables/mixins/functions for use elsewhere. Use with `show`/`hide` to control API.
```scss
// _variables.scss
$font-stack: Helvetica, sans-serif;
// styles.scss
@use 'variables';
body {
  font-family: variables.$font-stack;
}
```

### Mixins
Define reusable blocks of CSS with optional parameters. Use for logic, flexibility, or when parameters are needed.
```scss
@mixin center-flex {
  display: flex;
  justify-content: center;
  align-items: center;
}
.header {
  @include center-flex;
}
```

### Extend
Share identical styles among selectors. Best for fusing simple, fixed groups of rules. Avoid overusing: can generate bloated, hard-to-read CSS.
```scss
%rounded {
  border-radius: 8px;
}
.button {
  @extend %rounded;
}
.card {
  @extend %rounded;
}
```

### Mathematical & Color Functions
Mathematical: `+`, `-`, `*`, `/`, `%` (add, subtract, multiply, divide, modulo)
Color functions: `lighten`, `darken`, `mix`, `rgba`, `opacify`, `transparentize`, `adjust-hue`, etc. Only available in Sass, not in native CSS (except limited `calc()` and new color functions in modern CSS).

### Types of Values
- **Numbers:** `16px`, `1rem`, `50%`
- **Strings:** `'bold'`, `'Roboto'`, `'center'`
- **Colors:** `#ff9800`, `rgb(34,34,34)`, `blue`
- **Booleans:** `true`, `false`
- **Null:** `null`
- **Lists:** `10px 20px 30px`, `('a', 'b', 'c')`
- **Maps:** `(primary: #222, secondary: #fff)`
```scss
$list: 20px 30px 15px 12px;
.box {
  margin: $list; // margin: 20px 30px 15px 12px;
}
```

---

## 3. Intermediate Sass

### Built-in Functions
- **Lists:** `length`, `nth`, `index`, `join`, `append`, `zip`
- **Maps:** `map-get`, `map-has-key`, `map-merge`, `map-remove`, `map-keys`, `map-values`
- **Math:** `round`, `ceil`, `floor`, `abs`, `max`, `min` (use with `@use 'sass:math';`)
- **String:** `str-length`, `str-insert`, `str-index`, `str-slice`
- **Selector:** `selector-nest`, `selector-append`
- **Meta:** `type-of`, `unit`, `unitless`, `variable-exists`
```scss
$spacing: 8px, 16px, 32px;
$first: nth($spacing, 1); // 8px
$sizes: (small: 12px, large: 48px);
@debug map-get($sizes, large); // 48px
@use "sass:math";
$rounded: math.round(2.7); // 3
$str: "button";
$len: str-length($str); // 6
```

### Advanced Mixins & Parameters
Use arguments, default values, and conditionals in mixins for flexibility.
```scss
@mixin flex($align: center, $justify: center) {
  display: flex;
  align-items: $align;
  justify-content: $justify;
}
.container {
  @include flex(flex-start, space-between);
}
```

### Loops: @for, @each, @while
- `@for`: Numeric ranges.
- `@each`: Lists or maps.
- `@while`: Loop while condition is true.
```scss
$themes: (
  primary: #3498db,
  danger: #e74c3c,
  success: #2ecc71
);
@each $name, $color in $themes {
  .btn-#{$name} {
    background: $color;
  }
}
```

### Conditionals: @if, @else
Add logic to your styles, especially useful in mixins.
```scss
@mixin theme($mode) {
  @if $mode == light {
    background: #fff;
    color: #111;
  } @else {
    background: #111;
    color: #fff;
  }
}
.body-light { @include theme(light); }
.body-dark { @include theme(dark); }
```

### Lists & Maps: Usage and Conversion
Use lists for ordered values, maps for key-value pairs. Convert maps to lists: `map-keys($map)`, `map-values($map)`
```scss
$colors: (primary: #3498db, secondary: #6c757d);
$color-keys: map-keys($colors); // (primary, secondary)
$color-values: map-values($colors); // (#3498db, #6c757d)
```

### Variable Interpolation
Use `#{$variable}` inside selectors, property names, or values for dynamic class/property generation.
```scss
$spacings: 4px, 8px, 16px;
@each $space in $spacings {
  .p-#{$space} { padding: $space; }
}
$side: left;
.margin-#{$side} {
  margin-#{$side}: 1rem;
}
```

### Partials & File Organization
Divide files by responsibility: variables, mixins, components, layout, pages, themes, utilities. Use `_` prefix for partials; import only in main entry files. Centralize all imports in a `main.scss` or similar. Optionally use index files with `@forward` to simplify imports.
```
/styles
  /abstracts   // variables, mixins, functions
  /base        // resets, typography
  /components  // button, card, modal, etc.
  /layout      // grid, container
  /pages       // login, dashboard
  /themes      // light, dark
  main.scss    // main entry
```
(...document continues...)
