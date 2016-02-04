# Design Team Standards
In order to create code that is easy for others to understand and maintain, it
is imperative that all developers work from the same set of standards.

## Our Principles
*What* we code, *how* we code, and *why* we code the way we do are all equally
important pieces of development.

### Device and Browser Support
A project SOW may declare required support. If not, we support "modern"
default browsers using modern Operating Systems. As of Nov 2015, that list
includes:
- Internet Explorer 9+
- Microsoft Edge
- Google Chrome 43+
- Mozilla Firefox 40+
- Safari 8+


- Windows 7+
- OSX 10.9+
- iOS 8+
- Android 4.3+

This list is not meant to be definitive, just an example of what is meant by
"modern" browsers (actively supported, widely used, etc.).

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
- Use [semantic markup](http://www.bbc.co.uk/guidelines/futuremedia/technical/semantic_markup.shtml).
- Use self-documenting code as much as possible, and explicitly comment
  inelegant code.
- Not use hacks, browser-specific code, or be too clever or write any otherwise
  hard to maintain code.
- Not use tables for anything other than tabular data.  Even then, there is
  probably a better way to present it.
- Not use repeating code whenever possible.  Be [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).

[EditorConfig](http://editorconfig.org/) can be used to define and enforce these rules per project.
Plugins are available for most IDEs and editors.

### Separation of concerns

#### HTML = content
HTML should be semantically written and not contain any inline CSS or JS.

#### CSS = presentation
[This page](https://isobar-idev.github.io/code-standards/#css_formatting_css) has some excellent basic standards for CSS, specifically the "Formatting CSS", "Specificity", "ID Selectors", "Vendor Prefixes", and "Box Model" sections.

- ["Sans underscore" style BEM](https://en.bem.info/method/naming-convention/) seems to work well, but as long as *some* standard is followed when writing CSS, the resulting code should be easier to understand and maintain.
- ".js-" prefixed classes should rarely be referenced in CSS files.

#### JS = behavior
- Avoid JS/jQuery animations when CSS animation can be used.

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

---

#### .block-grid
We have used a "block grid" for things just as media galleries, but there are a few drawbacks that need to be considered when using it. [http://justifygrid.com/](http://justifygrid.com/) has a good explaination of how it works, bugs and workarounds, etc.

---

### File Architecture
`/core` is the Design Team's home.  All CSS and JS live in this directory.  A standard setup would be along the lines of:
```
/core
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
    [site].js
  /scss
    /base
    /objects
    /settings
    /tools
    /utilities
```

We have a back-end process (Package Manager) that will compile SASS, minify, and concatenate files into user configured bundles that are included by the Master template.

---

### Configuration Files
#### *.Variables.config
XML document that allows variables to be used through out JS/SASS/CSS files for consistency.  For example, we can set the max-width of the content area to variable:
```
<Variable>
  <Name>$max-width</Name>
  <Value>68em</Value>
</Variable>
```
#### *.Packages.config
XML document that groups JS/SASS/CSS files together into pacakges that can be concatenated and minified.  Generally, we have one CSS file with all of the compiled SASS, and two JS files; one to be included in `<head>` with critical JS (such as jQuery and Modernizr), and one to be included at the end of the document with all other JS (plugins, site-specific JS, etc.).

CSS example:
```
<Package xsi:type="CssPackage" Name="site.css" Combine="true" Minify="true">
  <Bundle MediaQuery="">
    <Resource Source="/core/components/wsol-tabs/css/wsol.tabs.css" />
    <Resource Source="/core/scss/site.scss" />
  </Bundle>
</Package>
```

JS examples:
```
<Package xsi:type="JsPackage" Name="site.head.js" Combine="true" Minify="true">
  <Resource Source="/core/components/jquery/dist/jquery.js" />
  <Resource Source="/core/components/modernizr/modernizr.js" />
</Package>
```
```
<Package xsi:type="JsPackage" Name="naperville.js" Combine="false" Minify="false">
  <!-- Components -->
  <Resource Source="/core/components/enquire/dist/enquire.js" />
  <Resource Source="/core/components/wsol-appendaround/js/wsol.appendaround.js" />
  <Resource Source="/core/components/wsol-equal-heights/js/wsol.equalHeights.js" />
  <Resource Source="/core/components/wsol-tabs/js/wsol.tabs.js" />
  <Resource Source="/core/components/wsol-accordion/js/wsol.accordion.js" />
  <!-- Site-specific scripts -->
  <Resource Source="/core/js/plugins.js" />
  <Resource Source="/core/js/wsol.js" />
</Package>
```

---

### Common Components
#### .hlist / .vlist
Horizontal and Vertical lists.
#### [Accordions](https://github.com/websolutions/accordion)
Content accordions that toggle content when clicked.
#### [Tabs](https://github.com/websolutions/tabs)
Content tabs that toggle content when clicked.
#### [EqualHeights](https://github.com/websolutions/equal-heights)
JS solution to float drops.  Potential alternative with using .block-grid ([see above](#-block-grid)), but that has it's own flaws.
#### [AppendAround](https://github.com/websolutions/append-around)
JS solution for moving HTML nodes to elsewhere in the DOM at breakpoints.  Potential alternative with using CSS flexbox when that has wider support.
#### [Media Blocks](https://jsfiddle.net/jnky3yc1/)
A [design pattern](http://www.stubbornella.org/content/2010/06/25/the-media-object-saves-hundreds-of-lines-of-code/) for an image and text.
#### Navigation
jQuery plugin [SmartMenus](http://www.smartmenus.org/) has been working well for us, and is 508 complaint.
#### Carousel
jQuery plugin [Slick](http://kenwheeler.github.io/slick/).
