CSS Inspired Map Styling
=====================

_Notes, sketches, and frameworks inspired by October 2011 map styling workshop held in SF_

Question: Core bits should be universal. Extras should be wrapped in a Carto 2.0 designator?

===

#Goals:

1. CSS-like map design language. Syntax extends the foundation laid by [Cascadenik](https://github.com/mapnik/Cascadenik/wiki/Dictionary) and [Carto](https://github.com/mapbox/carto) (implied 1.0) and the power of Less.js
2. Better support for reference-style maps by formalizing the use of @variables for color swatches, graphic styles, and text character styles
3. ¿Add support for thematic data cartography with data statistics like MIN, MAX, MEAN and use of those keywords in high-level filters that are under a dozen lines of code? Allow for both loose and hard connections between data and symbology.


#Result should be:

1. Reference documentation and examples that are compelling.
2. Syntax should be easy to write for average designer, reusable, and shareable. 
3. Syntax should be fast for the renderer draw. Text and shields are special cases we want to allow for. Mike put this as it's &#60;/div&#62; friendly.
4. Should have reasonable style defaults / implementation routines that make sense for the designer and for the renderer. Reasonable defaults make it quick to explore a dataset (not just present the final results). Example: draw points to see a distribution, classify points, lines and polygons by thematic attribute and color quickly. See: [NVK's Thematic Carto Tools repo](https://github.com/nvkelso/thematic-carto-tools)
5. Core syntax is interoperable with other style languages. Extensions allow extra awesome things. 
6. Clearly identify ambiguities (and document recommended solutions).
7. Standard adhers to [Semantic Versioning](http://semver.org/). Watch background video ([youtube](http://www.youtube.com/watch?v=k2h2lvhzMDc)). _- MM and NVK add 18 May 2012._
8. First principles based so it's easy to implement across software. _- NVK add 19 May 2012._

NOTE: Extensions might include data formats (csv, shp in zip), data statistics (count, min, max), data locations (local/remote/offline), experimental styling keywords (feature class heigherachy ala scalemaster.org?), styles with absolute and relative values (gradients, choropleth/quantiles/nat'lbreaks/etc, lighter, darker, smaller text, larger text), and rendering (like for shields?), interactivity (one layer, multiple layers).


#Atomic bits (nouns, verbs):

1. **CSS target**: anchor, stroke, fill, label/text, data. 
`Note: reimagined a bit from basic HTML CSS and Cascadenik, Carto. Anchor is new in 1.0, label/text is better in 1.0/2.0/3.0??, data formalized in 1.0/2.0?`
1. **Size by**: anchors, strokes, and labels have a 2d extrusion measurement (eg: 2px) 
`For images, the size is implied by a non-repeat dimension of the source image. Others are determined by default style CSS. Continues from Cascadenik/Carto 1.0`
1. **Ink with** for color (mask) areas (extrusion of stroke, or fill): rgb, cmyk, hsv, gradient, image (with repeat options). 
`With alpha. All color spaces/images/gradient are coallpsed in "ink" in Carto 2.0`
1. **Object geometry type filter**: point, line, polygon, continuous-field aka raster. 
`New in 2.0`
1. **Object geometry component filter**: The component polys, rings, & vertex. 
`New in 3.0`
1. **Rendering target**: (pivots off geometry type) registration, edge, interior, LABEL, INTERACTIVITY. 
`New in 3.0. With optional masks and positioning/attachment hints.`


#Core bits (Map CSS 1.0):

_All these must be supported to meet 1.0 compliance_

1. basic from  css: **Basic CSS syntax** - stroke, fill, text. Require `color:#rrggbb` and `opacity=1.0`. 
1. from cascadenik: **Filters** for feature attributes eg: `FIELDNAME=value` or `FIELDNAME>value`
1. new  carto  2.0: **display:none** - like `!important`, but for not showing stuff, regarless of other rules. 
1. from cascadenik: **DataSourcesConfig** an XML tag similar to the Stylesheet tag that allows you to externalize elements in an easy to manage format. [More info »](https://github.com/mapnik/Cascadenik/wiki/Managing-Data-Sources)
1. from carto  1.0: **FontSets**: very important for multilingual characters and UTF-8 labels, font fallbacks
1. new  carto  2.0: **Point geoms have an edge** available for stroking. **THIS IS THE ONLY SHOW STOPPER.**

#Advanced bits (Map CSS 2.0):

_All these must be supported to meet 2.0 compliance_

1. basic from  css: **Advanced CSS syntax** - RGBA, CYMK, HSV, etc other color spaces.
1. from cascadenik: **Local versus remote** data files.
1. from carto  1.0: **@variables** for color swatches, this is preprocessor to actual result. also can be used for graphic styles, and text character styles. _Moved 23 May 2012 per TMCW_
1. from carto  1.x: **RegEx filters** like: `/* a regular expression over name */ #world[name =~ "A.*"]`
1. from carto  1.0: **Nested style attachments (layer)** `::` are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. **OR should we move away to a procedure versus declarative?**
1. from carto  1.0: **style instances (features)** `/` similar to nested styles, but within the same original layer. 
1. from carto  1.0: **variable expresions** evaluation of variable expressions: `( @variable-stroke-width + 5 )` .... modifying color swatches, stroke-widths, and @variables, etc.
1. new  carto  2.0: **@media zoom selection** [zoom] from Cascadenik and Carto 1.0 allowed, but also now: `@media (zoom > 3) and (zoom < 10) {  #lakes {    /* style */   } }`
1. new  carto  2.0: **geometry type selectors** (point, line, polygon, raster) eg: `geom_type_selector.classname { ... }` or `.classname _geom_type_selector { ... }` (which is less CSS like, but more like the sketches below)  **THIS IS THE ONLY SHOW STOPPER.**

#Super advanced bits (Map CSS 3.0):

_All these must be supported to meet 3.0 compliance_

_Note: some are likely to remain vender specific implementations, -vender-propertyname eg: -mapnik-line-stroke-gradient, bla bla bla_

1. new  carto  2.0: **data variables/expansion** functional expansion of column values per feature `[name]` but for property values? Held up by upstream Mapnik changes.
1. from carto  1.0: **functional expressions** evaluation of variable expressions with a predefined function: `function( @variable-stroke-width )`
1. new  carto  2.0: **Selectors** for object _advanced_ **geometry components**: inner outer rings, vertex index and first, last. eg: `.classname geom_component_selector { ... }`
1. new  carto  2.0: **Dependant attachments** `&&` depend on the previous bits being rendered in that selection. Useful for text labels to require the anchor symbolization being placed. eg: point&&stroke
1. new  carto  2.0: **Shorthand style properties** eg: `stroke: url(...) #9f0 repeat 20.0;` is now allowed, and expands to `{ stroke-image:url(...); stroke-color:#9f0; stroke-image-repeat:true; stroke-size:20.0px }`
1. new  carto  2.0: **Special rendering targets w/r/t attachements**: interior, edge, registration.
1. new  carto  2.0: **Rendering/compositing targets** are implied but can be explicate or overridden: fill-, stroke-, anchor-, and data-. _Note: color:#hex is implied as fill-color:#hex._
1. new  carto  2.0: **Blending modes** ala Photoshop, Illustrator, new compositing branch of Mapnik, cutting edge CSS.
1. new  carto  2.0: **gradients on strokes** - The future come-ith.

#File structure

* MSS for the Map CSS styling definition.
* MML for the datasources and their layering. 

    _NOTE 1: Map CSS MML spec can embed the MSS part rather than linking to external file (the default)._

    _NOTE 2: Map CSS MML spec can be implemented in either JSON and XML formats._
    
    _NOTE 3: Data source definition can be inline or an import in the MML. From cascadnik/carto 2.0. ¿Easy in XML format, harder in JSON?_


#Reasonable defaults:

_Note: the stye="..." examples need to be reworked.

* Text -> geom_centroid -> Registration/Text : 
    `{ text-size:12px; text-color:#000000; text-face:Arial; }` --**or**-- 
    `style="12px black Arial"` (in version 3.0)
* Anchor -> Point -> Registration: 
    `{ stroke-width:2px; stroke-color:#000000; cap:round; }` --**or**-- 
    `style="2px #000000 round;"` (in version 3.0)
* Stroke -> Line -> Edge: 
    `{ stroke-width:1px; stroke-color:#000000; cap:square; }` --**or**-- 
    `style="1px #000000 square;"` (in version 3.0)
* Fill -> Polygon -> Interior: 
    `{ fill-color:#eeeeee; }` --**or**-- 
    `style="grey"` (in version 3.0)
* Raster Pixels -> Point > Registration : 
    Per pixel colored by the RGB channel values. CSS tk tk tk. Kinda like a RGB #hex gradient per 0-255 values?


#&etc

* What promises are we making, what is arbitrary up to the user?
* Set reasonable expectations


#Full details:

[Read more »](https://github.com/nvkelso/carto-css-map-styling/blob/master/full_details.md)

Syntax details, implementation recommendations, avoids, demos, open questions, housekeeping, quotes, display:none, carto 1.x nitpicks, more questions.


#Sketches

**Basic:**

![Carto2_basic](https://github.com/nvkelso/carto-css-map-styling/raw/master/images/carto_simple.png)

**Medium:**

![Carto2_medium](https://github.com/nvkelso/carto-css-map-styling/raw/master/images/carto_medium.png)

**Complex:**

![Carto2_complex](https://github.com/nvkelso/carto-css-map-styling/raw/master/images/carto_complex.png)