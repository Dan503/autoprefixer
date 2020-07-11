# Grid IE support

## Contents <!-- omit in toc -->

- [Can I use CSS Grid in IE?](#can-i-use-css-grid-in-ie)
- [Suggested articles and resources](#suggested-articles-and-resources)
- [Enabling CSS Grid translations](#enabling-css-grid-translations)
  - [PostCSS options object](#postcss-options-object)
  - [Control comments in your CSS file](#control-comments-in-your-css-file)
  - [An environment variable](#an-environment-variable)
- [No-autoplacement limitations](#no-autoplacement-limitations)
  - [Both columns and areas need to be defined](#both-columns-and-areas-need-to-be-defined)
  - [Repeat auto-fit and auto-fill are not supported](#repeat-auto-fit-and-auto-fill-are-not-supported)
  - [When changing the `grid gap` value, columns and areas need to be re-declared](#when-changing-the-grid-gap-value-columns-and-areas-need-to-be-re-declared)
- [Grid Autoplacement support in IE](#grid-autoplacement-support-in-ie)
  - [Beware of enabling autoplacement in old projects](#beware-of-enabling-autoplacement-in-old-projects)
  - [Autoplacement limitations](#autoplacement-limitations)
    - [Both columns and rows must be defined](#both-columns-and-rows-must-be-defined)
    - [Repeat auto-fit and auto-fill are not supported](#repeat-auto-fit-and-auto-fill-are-not-supported-1)
    - [No manual cell placement or column/row spans allowed inside an autoplacement grid](#no-manual-cell-placement-or-columnrow-spans-allowed-inside-an-autoplacement-grid)
    - [Do not create `::before` and `::after` pseudo elements](#do-not-create-before-and-after-pseudo-elements)
    - [When changing the `grid gap` value, columns and rows must be re-declared](#when-changing-the-grid-gap-value-columns-and-rows-must-be-re-declared)

## Can I use CSS Grid in IE?

Yes... with some limitations.

If the `grid` option is set to `"autoplace"` or `"no-autoplace"`, Autoprefixer will be able to automatically translate your modern CSS Grid code into something that that Internet Explorer 10 and 11 (IE) can understand. Autoprefixer is limited by what the IE rendering engine is capable of replicating though. This means that there are still some translations that will _never_ be possible (eg. `grid-auto-columns` and `grid-auto-rows`).

**This is not a feature that you can just enable and then forget about!**

You will need to test **every grid layout in IE**. Even though it may require some extra testing, this is still a very useful tool for achieving IE support with modern CSS Grid code. Many companies around the world are already using this feature in production, like Financial Times and Yandex.

## Suggested articles and resources

In addition to the information found in this documentation, the following articles and resources contain more information that will help you successfully use CSS Grid in IE:

- [The definitive 4 part guide about how to use CSS Grid in IE] ([Part 2] being especially useful).
- [Rachel Andrew article], outdated but still worth a read.
- [`postcss-gap-properties`] to use new `gap` property instead of old `grid-gap`.
- [`postcss-grid-kiss`] has alternate "everything in one property" syntax, which makes using Autoprefixer's Grid translations a bit safer.

[the definitive 4 part guide about how to use css grid in ie]: https://css-tricks.com/css-grid-in-ie-debunking-common-ie-grid-misconceptions/
[part 2]: https://css-tricks.com/css-grid-in-ie-css-grid-and-the-new-autoprefixer/
[rachel andrew article]: https://rachelandrew.co.uk/archives/2016/11/26/should-i-try-to-use-the-ie-implementation-of-css-grid-layout/
[`postcss-gap-properties`]: https://github.com/jonathantneal/postcss-gap-properties
[`postcss-grid-kiss`]: https://github.com/sylvainpolletvillard/postcss-grid-kiss

## Enabling CSS Grid translations

Autoprefixer has CSS Grid translations disabled by default. This is because Autoprefixer's Grid translations should only be used by people who understand what the limitations are and are willing to work within those limitations. That is why it is an opt-in model rather than opt-out.

There are a few ways that you can enable CSS Grid translations in Autoprefixer.

### PostCSS options object

```js
autoprefixer({ grid: "autoplace" });
```

The PostCSS options object can accept a `grid` property that accepts one of 3 values:

- `false` (default): Prevent Autoprefixer from outputting CSS Grid translations.
- `"autoplace"` (recommend): Enable Autoprefixer CSS Grid translations _including_ Autoprefixers limited autoplacement support.
- `"no-autoplace"`: Enable Autoprefixer CSS Grid translations but _excluding_ Autoprefixers limited autoplacement support.

### Control comments in your CSS file

```css
/* autoprefixer grid: autoplace */

html {
  /* CSS styles */
}
```

If you are using a preprocessor like [SCSS](https://sass-lang.com/), make sure that the compiler isn't stripping out the comment before Autoprefixer has a chance to read it.

```scss
// This does not work because SCSS strips out the comment
// autoprefixer grid: autoplace

html {
  // SCSS styles
}
```

```scss
// This WILL work because the comment is retained in the output CSS
/* autoprefixer grid: autoplace */

html {
  // SCSS styles
}
```

Valid Control comments for controlling CSS Grid are:

- `/* autoprefixer grid: off */` (default): Disable CSS Grid translations.
- `/* autoprefixer grid: autoplace */` (recommend): Enable CSS Grid translations and _enable_ limited autoplacement support
- `/* autoprefixer grid: no-autoplace */`: Enable CSS Grid translations and _disable_ limited autoplacement support

Find out more about control comments in the [control comments documentation](https://github.com/postcss/autoprefixer#control-comments).

### An environment variable

```
AUTOPREFIXER_GRID=autoplace
```

Some programs like [Create React App] do not give you easy access to the configuration files and if you are using a CSS-in-JS styling solution like [JSS] you will not be able to add control comments to your code. This is when using an environment variable is the best option.

For information on how to implement the environment variable, see the [environment variables documentation].

> (**@ai** would it make sense to move the environment variables documentation into this file?)

[create react app]: https://create-react-app.dev/docs/getting-started
[jss]: https://cssinjs.org/
[environment variables documentation]: https://github.com/postcss/autoprefixer#environment-variables

## Grid No-autoplacement support in IE

If the `grid` option is set to `"no-autoplace"`, no-autoplacement (meaning that the grid cells are positionated manually through `grid-template` or `grid-template-areas`) support is added to Autoprefixers grid translations. You can also use
the `/* autoprefixer grid: no-autoplace */` control comment or
`AUTOPREFIXER_GRID=no-autoplace npm build` environment variable.

Remember that Autoprefixer will only place grid cells if both `grid-template-areas`
and `grid-template-columns` has been set.

The `grid-template-areas` is neeed for let Autoprefixer know exactly how your grid layout looks like, this paved the way for Autoprefixer to also
support (in a limited capacity) `grid-gap` property, while `grid-template-columns` is needed for the different handling of IE browser of the property, where `auto` value, instead of expanding the grd-cells to `1fr`, it will always shrink them down to the value of `max-content`.

```css
/* Input CSS */

/* autoprefixer grid: no-autoplace */

.grid {
  display: grid;
  grid-gap: 10px;
  grid-template:
    "nav  main" 100px /
    1fr 1fr;
}

.main {
  grid-area: main;
}

.nav {
  grid-area: nav;
}
```

```css
/* Output CSS */

/* autoprefixer grid: no-autoplace */

.grid {
  display: -ms-grid;
  display: grid;
  grid-gap: 10px;
  -ms-grid-rows: 100px 10px 100px;
  -ms-grid-columns: 1fr 10px 1fr;
  grid-template:
    "nav  main" 100px /
    1fr 1fr;
}

.main {
  -ms-grid-row: 1;
  -ms-grid-column: 1;
  grid-area: main;
}

.nav {
  -ms-grid-row: 1;
  -ms-grid-column: 3;
  grid-area: nav;
}
```

In addition, you can simply change the `grid-template` or `grid-template-areas` property with a media query and Autoprefixer will automatically update all of the grid cell coordinates for you.

```css
/* Input CSS */

/* autoprefixer grid: no-autoplace */

.grid {
  display: grid;
  grid-gap: 10px;
  grid-template:
    "content" 100px
    "img" 100px /
    1fr 1fr;
}

@media (min-width: 600px) {
  .grid {
    grid-gap: 10px;
    grid-template:
      "content  img" 100px /
      1fr 1fr 1fr;
  }
}

.content {
  grid-area: content;
}

.img {
  grid-area: img;
}
```

```css
/* Output CSS */

/* autoprefixer grid: no-autoplace */

.grid {
  display: -ms-grid;
  display: grid;
  grid-gap: 10px;
  -ms-grid-rows: 100px 10px 100px;
  -ms-grid-columns: 1fr 10px 1fr;
  grid-template:
    "content" 100px
    "img" 100px /
    1fr 1fr;
}

@media (min-width: 600px) {
  .grid {
    grid-gap: 10px;
    -ms-grid-rows: 100px;
    -ms-grid-columns: 1fr 10px 1fr 10px 1fr;
    grid-template:
      "content  img" 100px /
      1fr 1fr 1fr;
  }
}

.content {
  -ms-grid-row: 1;
  -ms-grid-column: 1;
  grid-area: content;
}

.img {
  -ms-grid-row: 3;
  -ms-grid-column: 1;
  grid-area: img;
}

@media (min-width: 600px) {
  .content {
    -ms-grid-row: 1;
    -ms-grid-column: 1;
  }
  .img {
    -ms-grid-row: 1;
    -ms-grid-column: 3;
  }
}
```

## No-autoplacement limitations

### Both columns and areas need to be defined

As mentioned before in the introduction, both `grid-template-areas` and `grid-template-columns` need to be defined.

```css
.not-allowed {
  display: grid;
  grid-template-areas: "alpha delta";
}

.is-allowed {
  display: grid;
  grid-template-areas: "alpha delta";
  grid-template-columns: repeat(2, 1fr);
}
```

### Repeat auto-fit and auto-fill are not supported

The `repeat(auto-fit, ...)` and `repeat(auto-fill, ...)` grid functionality relies on
knowledge from the browser about screen dimensions and the number of available grid
items for it to work properly. Autoprefixer does not have access to this information
so unfortunately this little snippet will _never_ be IE friendly.

```css
.grid {
  /* This will never be IE friendly */
  grid-template-columns: repeat(auto-fit, min-max(200px, 1fr));
}
```

### When changing the `grid gap` value, columns and areas need to be re-declared

If you wish to change the size of a `grid-gap`, you will need to redeclare the grid columns and areas, for the mainly reason that Autoprefixer cannot inherit safetly the `grid` properties without the risk to break the layout in the final translations.

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-areas: "alpha beta";
  grid-gap: 50px;
}

/* This will *NOT* work in IE */
@media (max-width: 600px) {
  .grid {
    grid-gap: 20px;
  }
}

/* This will *NOT* work in IE */
.grid.small-gap {
  grid-gap: 10px;
}
```

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-areas: "alpha beta";
  grid-gap: 50px;
}

/* This *WILL* work in IE */
@media (max-width: 600px) {
  .grid {
    grid-template-columns: 1fr 1fr;
    grid-template-areas: "alpha beta";
    grid-gap: 20px;
  }
}

/* This *WILL* work in IE */
.grid.small-gap {
  grid-template-columns: 1fr 1fr;
  grid-template-areas: "alpha beta";
  grid-gap: 10px;
}
```

## Grid Autoplacement support in IE

If the `grid` option is set to `"autoplace"`, limited autoplacement support is added to Autoprefixers grid translations. You can also use
the `/* autoprefixer grid: autoplace */` control comment or
`AUTOPREFIXER_GRID=autoplace npm build` environment variable.

Autoprefixer will only autoplace grid cells if both `grid-template-rows`
and `grid-template-columns` has been set. If `grid-template`
or `grid-template-areas` has been set, Autoprefixer will use area based
cell placement instead.

Autoprefixer supports autoplacement by using `nth-child` CSS selectors.
It creates [number of columns] x [number of rows] `nth-child` selectors.
For this reason Autoplacement is only supported within the explicit grid.

```css
/* Input CSS */

/* autoprefixer grid: autoplace */

.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto auto;
  grid-gap: 20px;
}
```

```css
/* Output CSS */

/* autoprefixer grid: autoplace */

.grid {
  display: -ms-grid;
  display: grid;
  -ms-grid-columns: 1fr 20px 1fr;
  grid-template-columns: 1fr 1fr;
  -ms-grid-rows: auto 20px auto;
  grid-template-rows: auto auto;
  grid-gap: 20px;
}

.grid > *:nth-child(1) {
  -ms-grid-row: 1;
  -ms-grid-column: 1;
}

.grid > *:nth-child(2) {
  -ms-grid-row: 1;
  -ms-grid-column: 3;
}

.grid > *:nth-child(3) {
  -ms-grid-row: 3;
  -ms-grid-column: 1;
}

.grid > *:nth-child(4) {
  -ms-grid-row: 3;
  -ms-grid-column: 3;
}
```

### Beware of enabling autoplacement in old projects

Be careful about enabling autoplacement in any already established projects that have
previously not used Autoprefixer's grid autoplacement feature before.
Consider that if Autoprefixer it doesn't find both `grid-template-columns` and `grid-template-rows` it will throw a warning similar to this:

`autoprefixer: C:\Users\User\Desktop\Project\styles\scss\main.css:3983:5: Autoplacement does not work without grid-template-rows property`.

For example, if this was your html:

```html
<div class="grid">
  <div class="grid-cell"></div>
</div>
```

The following CSS will not work as expected with the autoplacement feature enabled:

```css
/* Unsafe CSS when Autoplacement is enabled */

.grid-cell {
  grid-column: 2;
  grid-row: 2;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);
}
```

Swapping the rules around will not fix the issue either:

```css
/* Also unsafe to use this CSS */

.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);
}

.grid-cell {
  grid-column: 2;
  grid-row: 2;
}
```

One way to deal with this issue is to disable autoplacement in the
grid-declaration rule:

```css
/* Disable autoplacement to fix the issue */

.grid {
  /* autoprefixer grid: no-autoplace */
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);
}

.grid-cell {
  grid-column: 2;
  grid-row: 2;
}
```

The absolute best way to integrate autoplacement into already existing projects
though is to leave autoplacement turned off by default and then use a control
comment to enable it when needed. This method is far less likely to cause
something on the site to break.

```css
/* Disable autoplacement by default in old projects */
/* autoprefixer grid: no-autoplace */

/* Old code will function the same way it always has */
.old-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);
}
.old-grid-cell {
  grid-column: 2;
  grid-row: 2;
}

/* Enable autoplacement when you want to use it in new code */
.new-autoplace-friendly-grid {
  /* autoprefixer grid: autoplace */
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, auto);
}
```

Note that the `grid: "no-autoplace"` setting and the
`/* autoprefixer grid: no-autoplace */` control comment share identical
functionality to the `grid: true` setting and the `/* autoprefixer grid: on */`
control comment. There is no need to refactor old code to use `no-autoplace`
in place of the old `true` and `on` statements.

### Autoplacement limitations

#### Both columns and rows must be defined

Autoplacement only works inside the explicit grid. The columns and rows need to be defined
so that Autoprefixer knows how many `nth-child` selectors to generate.

```css
.not-allowed {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.is-allowed {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(10, auto);
}
```

#### Repeat auto-fit and auto-fill are not supported

Like in the `no-autoplacement` grid the `repeat(auto-fit, ...)` and `repeat(auto-fill, ...)` grid functionality are not supported.

```css
.grid {
  /* This will never be IE friendly */
  grid-template-columns: repeat(auto-fit, min-max(200px, 1fr));
}
```

#### No manual cell placement or column/row spans allowed inside an autoplacement grid

Elements must not be manually placed or given column/row spans inside
an autoplacement grid. Only the most basic of autoplacement grids are supported.
Grid cells can still be placed manually outside the the explicit grid though.
Support for manually placing individual grid cells inside an explicit
autoplacement grid is planned for a future release.

```css
.autoplacement-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, auto);
}

/* Grid cells placed inside the explicit grid
    will break the layout in IE */
.not-permitted-grid-cell {
  grid-column: 1;
  grid-row: 1;
}

/* Grid cells placed outside the
    explicit grid will work in IE */
.permitted-grid-cell {
  grid-column: 1 / span 2;
  grid-row: 4;
}
```

If manual cell placement is required, we recommend using `grid-template` or
`grid-template-areas` instead:

```css
.page {
  display: grid;
  grid-gap: 30px;
  grid-template:
    "head head"
    "nav  main" minmax(100px, 1fr)
    "foot foot" /
    200px 1fr;
}
.page__head {
  grid-area: head;
}
.page__nav {
  grid-area: nav;
}
.page__main {
  grid-area: main;
}
.page__footer {
  grid-area: foot;
}
```

#### Do not create `::before` and `::after` pseudo elements

Let's say you have this HTML:

```html
<div class="grid">
  <div class="grid-cell"></div>
</div>
```

And you write this CSS:

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto;
}

.grid::before {
  content: "before";
}

.grid::after {
  content: "after";
}
```

This will be the output:

```css
.grid {
  display: -ms-grid;
  display: grid;
  -ms-grid-columns: 1fr 1fr;
  grid-template-columns: 1fr 1fr;
  -ms-grid-rows: auto;
  grid-template-rows: auto;
}

.grid > *:nth-child(1) {
  -ms-grid-row: 1;
  -ms-grid-column: 1;
}

.grid > *:nth-child(2) {
  -ms-grid-row: 1;
  -ms-grid-column: 2;
}

.grid::before {
  content: "before";
}

.grid::after {
  content: "after";
}
```

IE will place `.grid-cell`, `::before` and `::after` in row 1 column 1.
Modern browsers on the other hand will place `::before` in row 1 column 1,
`.grid-cell` in row 1 column 2, and `::after` in row 2 column 1.

See this [Code Pen](https://codepen.io/daniel-tonon/pen/gBymVw) to see a visualization
of the issue. View the Code Pen in both a modern browser and IE to see the difference.

Note that you can still create `::before` and `::after` elements as long as you manually
place them outside the explicit grid.

#### When changing the `grid gap` value, columns and rows must be re-declared

If you wish to change the size of a `grid-gap`, you will need to redeclare the grid columns and rows, for the mainly reason that Autoprefixer cannot inherit safetly the `grid` properties without the risk to break the layout in the final translations.

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto;
  grid-gap: 50px;
}

/* This will *NOT* work in IE */
@media (max-width: 600px) {
  .grid {
    grid-gap: 20px;
  }
}

/* This will *NOT* work in IE */
.grid.small-gap {
  grid-gap: 10px;
}
```

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto;
  grid-gap: 50px;
}

/* This *WILL* work in IE */
@media (max-width: 600px) {
  .grid {
    grid-template-columns: 1fr 1fr;
    grid-template-rows: auto;
    grid-gap: 20px;
  }
}

/* This *WILL* work in IE */
.grid.small-gap {
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto;
  grid-gap: 10px;
}
```
