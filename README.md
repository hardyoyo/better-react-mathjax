
<!-- prettier-ignore-start -->
# A simple React component for MathJax #

Up-to-date component for using MathJax in React. Focuses on being versatile and making the use of MathJax in 
React a pleasant experience with several methods to deal with flashes of untypeset content, both with respect to initial
rendering as well as dynamic updates. Simple to use but with many configuration options.

## Basic workflow ##

`better-react-mathjax` introduces two React components - `MathJaxContext` and `MathJax`. For MathJax to work with React, 
wrap the outermost component containing math (or the entire App) in a `MathJaxContext` component. Then simply use `MathJax` components at 
different levels for the actual math. In the typical case, the content can be everything from a subtree of 
the DOM to a portion of text in a long paragraph. The `MathJaxContext` is responsible for downloading MathJax and 
providing it to all wrapped `MathJax` components that typeset math.

### Features ###

* Supports both MathJax version 2 and 3
* Supports local copy of MathJax or copy supplied via CDN
* Built in a modular fashion on top of MathJax with direct access to MathJax via the MathJax configuration.  
* Either put your math into the DOM with React first and let MathJax typeset afterwards (v. 2 and 3), or typeset with MathJax 
  first and add it to the DOM afterwards (v. 3 only).
* Hide your components before they are typeset to avoid flashes of untypeset content and make the use of MathJax a 
  pleasant experience.

## Examples ##

The first 3 are basic examples with zero configuration standard setup using MathJax version 3 with default MathJax config 
and no extra options.

### Example 1: Basic example with Latex ####

Standard setup using MathJax version 3 with default MathJax config and no extra options.

    export default function App() {
    
        return (
            <MathJaxContext>
                  <h2>Basic MathJax example with Latex</h2>
                  <MathJax>{"\\(\\frac{10}{4x} \\approx 2^{12}\\)"}</MathJax>
            </MathJaxContext>
        );

https://codesandbox.io/s/better-react-mathjax-basic-example-latex-bj8gd

### Example 2: Basic example with AsciiMath ####

Using AsciiMath requires importing a specific loader (see [MathJax](http://docs.mathjax.org/en/latest/input/asciimath.html) documentation for further information).

    export default function App() {
        const config = {
            loader: { load: ["input/asciimath"] }
        };
        
        return (
            <MathJaxContext config={config}>
                <h2>Basic MathJax example with AsciiMath</h2>
                <MathJax>{"`frac(10)(4x) approx 2^(12)`"}</MathJax>
            </MathJaxContext>
        );
    }

https://codesandbox.io/s/better-react-mathjax-basic-example-asciimath-ddy4r

### Example 3: Basic example with MathML ####

MathML is supported natively by a few but far from all browsers. It might be problematic to use with Typescript (no types for
MathML included in this package).

    export default function App() {
        return (
            <MathJaxContext>
                <h2>Basic MathJax example with MathML</h2>
                <MathJax>
                    <math xmlns="http://www.w3.org/1998/Math/MathML">
                        <mrow>
                            <mrow>
                                <mfrac>
                                    <mn>10</mn>
                                    <mi>4x</mi>
                                </mfrac>
                            </mrow>
                            <mo>&#x2248;</mo>
                            <mrow>
                                <msup>
                                    <mn>2</mn>
                                    <mn>12</mn>
                                </msup>
                            </mrow>
                        </mrow>
                    </math>
                </MathJax>
            </MathJaxContext>
        );
    }

https://codesandbox.io/s/better-react-mathjax-basic-example-mathml-20vv6

### Example 4: Elaborate example with Latex ###
https://codesandbox.io/s/better-react-mathjax-example-latex-3vsr5

### Example 5: Elaborate example with AsciiMath ###
https://codesandbox.io/s/better-react-mathjax-example-asciimath-p0uf1

### Example 6: Elaborate example with MathML ###
Sandbox link: https://codesandbox.io/s/better-mathjax-example-1-3vsr5



# API #

The following three properties can be set on **both** the `MathJaxContext` and `MathJax` components. When set on a 
`MathJaxContext` component, they apply to all wrapped `MathJax` components except those on which the property in 
question is set on the individual `MathJax` component, which then takes precedence.

---

### `hideUntilTypeset: "first" | "every" | undefined` ###

Controls whether the content of the `MathJax` component should be hidden until after typesetting is finished.

**Default**: `undefined` (no content is hidden at any time)

* `first`: The `MathJax` component is hidden until its content has been typeset the first time after which the component 
  remains visible throughout its lifetime.


* `every`: The same behaviour as when this property is set to `first`, but in addition, the `MathJax` component is now 
  hidden and made visible every time it is typeset. With `renderMode` set to `pre` this has no effect and is treated as
  `first`. When `renderMode` is set to `post`, the component is typeset anew on every render and so this setting might
  result in "blinking" content as an alternative to flashes of untypeset content.

### `renderMode: "pre" | "post" | undefined` ###

Controls how typesetting by MathJax is done in the DOM. This may affect display of untypeset content. 

**Default**: `post`

* `post`: All `children` of the `MathJax` component are added to the DOM by React first and then MathJax processes the 
  wrapped content (in the DOM). This implies that MathJax cannot know if the content has changed or not between renderings
  and so typesetting takes place on every render. This mode might give rise to flashes of untypeset content since the content 
  is in the DOM before MathJax has typeset it. In this `renderMode` MathJax can inspect the context in the DOM and adapt 
  its output to it in different ways (for example font size).
  

* `pre`: Math is passed via the `text` property (only strings), which must be set with math without delimiters, and 
  processed by MathJax *before* it is inserted into the DOM. This mode also requires `typesettingOptions` to be set with 
  the name of the function to use for the typesetting as well as an optional configuration object with typesetting details. 
  In this `renderMode`, MathJax only typesets when the `text` property changes. Since MathJax cannot look at the context 
  (in the DOM) of the math, limited automatic adaptation to surrounding content can be done and fine-tuning has to be 
  specified in the optional `options` object of the `typesettingOptions` property. **Note**: The `pre` value can only be 
  used with MathJax version 3.

### `typesettingOptions: { fn: TypesettingFunction, options: object | undefined} | undefined` ###

Used to control typesetting when `renderMode` is set to `pre`. Controls which typesetting function to use and an optional
object with typesetting details.

**Default**: `undefined` (no conversion function is supplied which throws an error when `renderMode` is `pre`)

* `fn`: The name of the MathJax function to use for typesetting. This is *only* used, and *must* be specified, when 
  `renderMode` is set to `pre` and should be one of the following strings: `tex2chtml`, `tex2chtmlPromise`, `tex2svg`, 
  `tex2svgPromise`, `tex2mml`, `tex2mmlPromise`, `mml2chtml`, `mml2chtmlPromise`, `mml2svg`, `mml2svgPromise`, 
  `mml2mml`, `mml2mmlPromise` , `asciimath2chtml`, `asciimath2chtmlPromise`, `asciimath2svg`, `asciimath2svgPromise`, `asciimath2mml` or 
  `asciimath2mmlPromise`. The value is the name of a function that MathJax generates based on the input configuration,
  as given to the wrapping `MathJaxContext`, as per [the docs](http://docs.mathjax.org/en/latest/web/typeset.html#converting-a-math-string-to-other-formats).
  For example, for the `tex2chtml` function to be available, the configuration given to the `MathJaxContext` component 
  must (explicitly or by use of default) contain a Latex input processor and an HTML output processor.
  

* `options`: An object with additional parameters to control the typesetting when `renderMode` is set to `pre`. Since this 
  typesetting is done outside of the DOM context in which the resulting math will be inserted, MathJax cannot adapt the output 
  to the surrounding content, which is why this can be done manually by the typesetting function. More information 
  about this object can be found in the [the docs](http://docs.mathjax.org/en/latest/web/typeset.html#converting-a-math-string-to-other-formats).

## `MathJaxContext` component ##

---
### `config: object | undefined` ###

Controls MathJax and is passed to MathJax as its config.

**Default**: `undefined` (default MathJax configuration is used)

MathJax configuration object. Make sure it corresponds to the version used. More information can be found in 
[the docs](http://docs.mathjax.org/en/latest/web/configuration.html).

### `src: string | undefined` ###

The location of MathJax.


**Default**: `undefined` (default CDN `https://cdnjs.cloudflare.com` is used)

Local or remote url to fetch MathJax from. More information about hosting your own copy of MathJax can be found
[here](http://docs.mathjax.org/en/latest/web/hosting.html).

### `version: 2 | 3 | undefined` ###

MathJax version to use.

**Default**: `3`

Version of MathJax to use. If set, make sure that any configuration and url to MathJax uses the same version. If `src`
is not specified, setting `src`to `2` currently makes use of version 2.7.9 and setting it to `3` uses 3.1.2.

### `onStartUp((mathJax: any) => void) | undefined` ###

Callback to be called when MathJax has loaded successfully but before the MathJax object has been made available
to wrapped `MathJax` components. The MathJax object is handed as an argument to this callback which is a good place
to do any further configuration which cannot be done through the input configuration object.

**Default**: `undefined`

### `onLoad(() => void) | undefined` ###

Callback to be called when MathJax has loaded successfully and after the MathJax object has been made available to the
wrapped `MathJax` components. This marks the last step of the startup phase in the `MathJaxContext` component when
MathJax is loaded.

**Default**: `undefined`

### `onError((error: any) => void) | undefined` ###

Callback to handle errors in the startup phase when MathJax is loaded.

**Default**: `undefined`

## `MathJax` component ##

---
### `inline: boolean | undefined` ###

Whether the wrapped content should be in an inline or block element. When `renderMode` is `post`, this refers to the 
wrapper component that this `MathJax` component uses (in the case of Latex, the user might still have both display
and inline math inside unless `renderMode` is set to `pre` in which case the Latex will be typeset as inline math
if this property is set to `true` and as display math otherwise).

**Default**: `false`

### `onInitTypeset(() => void) | undefined` ###

Callback for when the content has been typeset for the first time. Can typically be used for hiding content in a
coordinated way across different elements until all are in a representative state.

**Default**: `undefined`

### `text: string | undefined` ###

*Required* and *only* used when `renderMode` is set to `pre`. Should be the math string to convert without any delimiters.
Requires `typesettingOptions` to be set and version to be `3`. If `renderMode` is `post`, this property is ignored.

**Default**: `undefined`

### `dynamic: boolean | undefined` ###

Indicates whether the content of the `MathJax` component may change after initial rendering. When set to `true`,
typesetting should be done repeatedly (every render with `renderMode` set to `post` and whenever the
`text` property changes with `renderMode` set to `pre`). With this property set to `false`, only initial typesetting will
take place and any changes of the content will not get typeset.

**Default**: `false`

***

***Any additional props will be spread to the root element of the `MathJax` component which is a `span` with `display`
set to `inline` when the `inline` property is set to `true`, otherwise `block`. The `display` can be overriden via
`style` or `className` props if needed (then the `inline` property will have no effect). A ref is not possible to set 
as this functionality is used by the `MathJax` component itself.***

## Fighting flashes of untypeset content ##
Using MathJax as is is, as seen from the basic examples above, fairly simple, but the real challenge is to use it in a way
so that the user doesn't see flashes of untypeset content. Apart from making MathJax available to React in a simple and 
straightforward way, this is what this library focuses on.

### Static content ###
Static content does not have the `dynamic` property set to `true` and is typeset once when the component mounts. If the
component remounts, the procedure repeats. Before the content is typeset, the user might see the raw content which might be 
a negative experience. There are several ways to solve this:
* Set `hideUntilTypeSet` to `first` on individual `MathJax` components.
* Coordinate hiding with `onInitTypeset` to show bigger blocks or the entire page once all `MathJax` components
have finished the initial typesetting. Coordinate with `MathJaxContext` via the `onStartUp` callback.
  
### Dynamic content ###
Dynamic content is even harder to work with since it might update several times during the time a `MathJax` component is
mounted. With this content, the `dynamic` property should be set to `true` which implies that typesetting will
be attempted repeatedly (after every render if `renderMode` is set to `post` and when the `text` property changes
if `renderMode` is set to `pre`). If not handled correctly, updates might look bad to the user if the content is
visible before typesetting. Apart from the general considerations below, there are a few strategies to try in order
to solve this:

* Set `hideUntilTypeset` to `every`. This might result in a short blink instead but might be a better option in some cases
than to show content that is not typeset. Try to put your `MathJax` components outside of parents that often rerender to
  avoid unnecessary rerenderings (and accompanying blinking).
* Set `renderMode` to `pre`. With this mode, the `MathJax` component typesets the math content **before** inserting it into
the DOM which should remove the flash of untypeset content in some scenarios, however not in all as indicated below. One 
downside with this setting is that MathJax cannot access the context of the math and so it cannot adapt generated content
to it; manual fine-tuning might be necessary even though this is not always the case.
  
### General considerations regarding flashes of untypeset content ###

* Currently, MathJax version 3 gives rise to flickering in both Chrome (some) and Safari (heavy) on dynamic updates. 
  This is not connected to this package and does not seem to be related to the actual typesetting but how the CSS is 
  injected into and applied by the browser. Alas, untypeset content will be visible despite described methods. In this 
  case, the remedy is to use version 2, where seamless typesetting is still possible in all attempted browsers.
* The best cross-browser experience at this time is achieved with version 2, with disabled `fast-preview` and 
  `processSectionDelay` set to `0` for a smooth experience. This is done by including `"fast-preview": { disabled: true }` in the MathJax config 
  object given to the `MathJaxContext` and adding `mathJax => mathJax.Hub.processSectionDelay = 0` as the `onStartup` 
  callback to the same. With this strategy, setting `hideUntilTypeset` to `every` is typically not even needed. 
  Nonetheless, coordinate initial typesetting with `hideUntilTypeset` and / or `onInitTypeset` callbacks.

## General Considerations (don't skip) ##

* **Don't** nest `MathJax` components since this will result in the same math content being typeset multiple times by
  different `MathJax` components. This is unnecessary work.

* React has an [unresolved issue](https://github.com/facebook/react/issues/20891) with DOM nodes with mixed literal and 
  expression content, such as `<div> This is literal and { "this is in an expression" }</div>`, when used together with 
  DOM manipulation via refs. For this reason, when the `dynamic` property is set to `true`, always make sure that the 
  expression containing math is not mixed with literal content. The following list summarizes this:
  
  * **Don't**: `<p>An example is the equation $10x^4 = 100$</p>`
  * **Don't**: `<p>An example is the equation { "$10x^4 = 100$" }</p>`
  * **Don't**: `<p>An example is the equation <span>$10x^4 = 100$</span></p>`
  * **Don't**: `<p>An example is the equation <span> { "$10x^4 = 100$" }</span></p>` (literal space inside `span`)  
  * **Do**: `<p>{ "An example is the equation $10x^4 = 100$" }</p>`
  * **Do**: `<p>An example is the equation <span>{ "$10x^4 = 100$" }</span></p>`
    
  For static content, this does not matter since it is the interplay between how React handles updates to this content and
  the manipulation of the same via refs that causes problems.
  
* **Don't** wrap content that may rerender on its own. State changes must come from outside the wrapping `MathJax`
  component; if only its children rerender but not the parent `MathJax` component, math will not be typeset. If you have 
  this situation, simply wrap smaller portions of math content in `MathJax` components instead until the state lies 
  outside all `MathJax` components.
  
* **Do** use `renderMode` set to `post` first and don't try `renderMode` set to `pre` unless you're unhappy wth the results.

* If you do not achieve the effect you want, play around with what content you wrap in `MathJax` components and where in the
hierarchy they are kept. You can always replace a larger (more complex) component wrapped in a `MathJax` component with 
  one or several smaller parts of it wrapped in `MathJax` components.
  
* Remember that MathJax does initial typesetting on the whole document both in version 2 and 3. This can be turned off 
  but with it, a document can be typeset with only a `MathJaxContext` component. This, however, is not the intended use
  of this library and removes many of the additional options.
  
* The documented API above is only the additional functionality offered in the layer provided by this library. Most 
  other options and behaviours are properties of the MathJax library itself and thus configurable with the `config` 
  object. 
  
## Last but not least ... ##
MathJax was not written for use in React and React was not written with MathJax in mind so we have to massage them into
getting along and working in tandem!

## Wish list ##
* Types for the MathJax object.

## MathJax documentation ##
* Version 3: https://docs.mathjax.org/en/latest/

* Version 2: https://docs.mathjax.org/en/v2.7-latest/ 

## Github ##

File problems or contribute on Github: https://github.com/fast-reflexes/better-react-mathjax

## Changelog ##
v. 1.0 - Release

## License

This project is licensed under the terms of the
[MIT license](/LICENSE).


Uppdtaeing
-lägg till exempel med optimal config
-skriv klart examples (elaborate)
-testa i olika browsrar
-förstå exemplen om mixed content
- Ta bort utkommenterad kod
- vilka browsrar det är testat med etc...

<!-- prettier-ignore-end -->