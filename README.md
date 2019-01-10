# Design Team Standards
In order to create code that is easy for others to understand and maintain, it
is imperative that all developers work from the same set of standards.

## Our Principles
*What* we code, *how* we code, and *why* we code the way we do are all equally
important pieces of development.

### Device and Browser Support
A project SOW may declare required support. If not, we support "modern"
browsers using modern Operating Systems. As of Jan 2019, that list includes:

- Internet Explorer 11
- Microsoft Edge [17+]
- Google Chrome [70+]
- Mozilla Firefox [63+]
- Safari 11+


- Windows 10+
- OSX 10.10+
- iOS 11.4+
- Android 5.1+

This list is not meant to be definitive, just an example of what is meant by
"modern" browsers (actively supported, widely used, etc.). Generally speaking, we
should support the two latest versions of a browser/family.

Note that client location may also influnce the set of supported browsers. For
example, Opera Mini is rarely used in the U.S., but is roughly 5% of the global
share.  Likewise, iPhone and Android use is roughly equal in the U.S., but
gloablly, Android is by far the most common mobile OS.

[caniuse.com](http://caniuse.com/usage-table) and [StatCounter](http://gs.statcounter.com/)
are excellent resources for determining general usage, and site-specific
analytics can help inform the supported browser set for a client.

It is also important to utilize [Progressive Enhancement](https://en.wikipedia.org/wiki/Progressive_enhancement)
to ensure content is visible and accessible in browsers we do *not* officially
support, those with non-standard settings (such as JavaScript being disabled),
or those using assistive devices such as screen readers.

We have dev boxes set up with various versions of Internet Explorer to test with using RDP:
- ie8.wsoldev.com
- ie9.wsoldev.com
- ie10.wsoldev.com
- ie11.wsoldev.com

You can also use local virtual machines to test using:
- [VirtualBox](VirtualBox), and
- [modern.ie](http://modern.ie)

#### Using Modern Technologies
Performance and Progressive Enhancement must be top priorities when deciding
what is appropriate to use.

Helpful sites and technologies:
- [caniuse.com](http://caniuse.com)
- [Modernizr](http://modernizr.com) for testing browser capabilities
- JavaScript polyfills


---
## Code Quality

### Best Practices / 10 Commandments of Code

Thou shalt:
- Use 2 spaces instead of tabs.
- Use UTF-8.
- Use LF endings.
- Use single quotes in CSS/JS.
- Use [semantic markup](http://www.bbc.co.uk/guidelines/futuremedia/technical/semantic_markup.shtml).
- Use self-documenting code as much as possible, and explicitly comment
  inelegant code.
- Not use hacks, browser-specific code, or be too clever or write any otherwise
  hard to maintain code.
- Not use tables for anything other than tabular data.  Even then, there is
  probably a better way to present it.
- Not use repeating code whenever possible.  Be [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).
- Create a separate feature branch for each bug using the bug ID.

[EditorConfig](http://editorconfig.org/) can be used to define and enforce these rules per project.
Plugins are available for most IDEs and editors.

### Separation of concerns

#### HTML = content
HTML should be semantically written and not contain any inline CSS or JS.
- Single semantic class names should be used as much as possible.

#### CSS = presentation
[This page](https://isobar-idev.github.io/code-standards/#css_formatting_css) has some excellent basic standards for CSS, specifically the "Formatting CSS", "Specificity", "ID Selectors", "Vendor Prefixes", and "Box Model" sections.

- ["Sans underscore" style BEM](https://en.bem.info/method/naming-convention/) should be used when writing CSS to make the resulting code easier to understand and maintain.
- CSS properties should be alphabetized for easier scanning.
- With single semantic class names in the markup, pull in all needed additional styles via mixins and extends.
- ".js-" prefixed classes should rarely be referenced in CSS files.

#### JS = behavior
- Avoid JS/jQuery animations when CSS animation can be used.
- Main JS file should be named `[project].js`

#### CSS/JS Linting
Linting code can be helpful for both initial development and ongoing support.  Most editors and IDEs can be configured to [automatically lint your code](https://github.com/showcases/clean-code-linters).

---

### The Grid
#### .section > .inner > .unit Structure

Our back-end code allows end users to create column structures that utilize specific CSS classes to work:
```
<div class="section">
  <div class="inner">
    <div class="unit size1of2">...</div>
    <div class="unit size1of2">...</div>
  </div>
</div>
```
for example, would create a section with two-columns.  This markup must be supported, but columns can always stack on narrow screens, etc.

Because this grid is already set up, we tend to use to to build the entire site.

`.section` is analagous to a "row" that spans the entire width of the viewport;

`.inner` typically has a `max-width` set on wider breakpoints to keep content centered; and

`.unit` is analagous to a "column" within a `.section`

These structures can be nested as needed, but with a single `.section` containing multiple levels of `.inner > .unit`.  For example:
```
<div class="section">
  <div class="inner">
    <div class="unit size1of2">
      <div class="inner">
        <div class="unit size1of2">...</div>
        <div class="unit size1of2">...</div>
      </div>
    </div>
    <div class="unit size1of2">...</div>
  </div>
</div>
```
would result (on wide screens) in 3 columns, 25%, 25%, and 50% widths respectively.

See the 'grid' directory for examples.

---

#### Columns and Grids
To simplify back-end code and resulting markup, consider using CSS for columns and grids.

##### Columns
Columns display "cards" in top-to-bottom, left-to-right order like this (on a wide enough breakpoint):
```
A C E G
B D F H
```
This can be achieved using CSS multi-columns. If support for IE 9 and earlier are needed, use a polyfill such as [this one](https://github.com/hamsterbacke23/multicolumn-polyfill). Using the example above, the code would be something like:
```
<div class="columns">
  <div class="card">A</div>
  <div class="card">B</div>
  <div class="card">C</div>
  <div class="card">D</div>
  <div class="card">E</div>
  <div class="card">F</div>
  <div class="card">G</div>
  <div class="card">H</div>
</div>

.columns {
  -webkit-column-count: 2;
  -moz-column-count: 2;
  column-count: 2;
  -webkit-column-gap: 40px;
  -moz-column-gap: 40px;
  column-gap: 40px;
}

.card {
  display: block;
}
```

##### Grids
Grids display "cards" in left-to-right, top-to-bottom order like this (on a wide enough breakpoint):
```
A B C D
E F G H
```
This can be achieved using flexbox. Using the example above, the code would be something like:
```
<div class="grid">
  <div class="card">A</div>
  <div class="card">B</div>
  <div class="card">C</div>
  <div class="card">D</div>
  <div class="card">E</div>
  <div class="card">F</div>
  <div class="card">G</div>
  <div class="card">H</div>
</div>

.grid {
  align-items: stretch;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-between;
}

.card {
  width: 20%;
}
```

---

#### .block-grid
We have used a "block grid" for things just as media galleries, but there are a few drawbacks that need to be considered when using it. [http://justifygrid.com/](http://justifygrid.com/) has a good explaination of how it works, bugs and workarounds, etc.

---

### File Architecture
`/core` is the Design Team's home.  All CSS and JS live in this directory.  A standard setup would be along the lines of:
```
/core
  /compiled
  /components
    /enquire
    /jquery
    /modernizr
    /wsol-tabs
    ...
  /images
    /content
    /ui
  /js
    /modules
    [site].js
  /scss
    /base
    /objects
    /settings
    /tools
    /utilities
```

Gulp tasks should be set up to compile SASS, minify, and concatenate asset files, which output to `/core/compiled`

---

### Common Components
#### [.hlist / .vlist](https://github.com/websolutions/list-types)
Horizontal and Vertical lists.
#### [Accordions](https://github.com/websolutions/accordion)
Content accordions that toggle content when clicked.
#### [Tabs](https://github.com/websolutions/tabs)
Content tabs that toggle content when clicked.
#### [EqualHeights](https://github.com/websolutions/equal-heights)
JS solution to float drops when not using flexbox.
#### [AppendAround](https://github.com/websolutions/append-around)
JS solution for moving HTML nodes to elsewhere in the DOM at breakpoints.  Potential alternative with using CSS flexbox when that has wider support.
#### [Media Blocks](https://jsfiddle.net/jnky3yc1/)
A [design pattern](http://www.stubbornella.org/content/2010/06/25/the-media-object-saves-hundreds-of-lines-of-code/) for an image and text.
#### Navigation
jQuery plugin [SmartMenus](http://www.smartmenus.org/) has been working well for us, and is 508 complaint.
#### Carousel
jQuery plugin [Slick](http://kenwheeler.github.io/slick/).
