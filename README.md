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

* **Object geometry type**: point, line, polygon, continuous-field aka raster. _Even the component polys, rings, & vertex. New in 2.0_
* **CSS target**: anchor, stroke, fill, label/text, data. _Anchor is new in 2.0, label/text is better in 2.0, data formalized in 2.0_
* **Rendering target** (optional/implied in 2.0): (pivots off geometry type) registration, edge, interior, LABEL, INTERACTIVITY. _With optional masks and positioning/attachment hints._
* **Ink with** for color (mask) areas (extrusion of stroke, or fill): rgb, cmyk, hsv, gradient, image (with repeat options). _With alpha. All color spaces/images/gradient are coallpsed in "ink" in Carto 2.0_
* **Size by**: anchors, strokes, and labels have a 2d extrusion measurement (eg: 2px) _For images, the size is implied by a non-repeat dimension of the source image. Others are determined by default style CSS. Continues from Cascadenik/Carto 1.0_


#Core bits (Map CSS 1.0):

_All these must be supported to meet 1.0 compliance_

* basic from  css: **Basic CSS syntax** - stroke, fill, text. Require RGB and Opactity. 
* from cascadenik: **Filters** for feature attributes (FIELDNAME=value or FIELDNAME>value)
* new  carto  2.0: **display:none** - like !important, but for not showing stuff, regarless of other rules. 
* from cascadenik: **DataSourcesConfig** an XML tag similar to the Stylesheet tag that allows you to externalize elements in an easy to manage format. [More info »](https://github.com/mapnik/Cascadenik/wiki/Managing-Data-Sources)
* from carto  1.0: **FontSets**: very important for multilingual characters and UTF-8 labels, font fallbacks
* new  carto  2.0: **Point geoms have an edge** available for stroking. **THIS IS THE ONLY SHOW STOPPER.**

#Advanced bits (Map CSS 2.0):

_All these must be supported to meet 2.0 compliance_

* basic from  css: **Advanced CSS syntax** - RGBA, CYMK, HSV, etc other color spaces.
* from cascadenik: **Local versus remote** data files.
* from carto  1.0: **@variables** for color swatches, this is preprocessor to actual result. also can be used for graphic styles, and text character styles. _Moved 23 May 2012 per TMCW_
* from carto  1.x: **RegEx filters** this a new, unversioned bit, grrr. Awesome, though. _Optional?_
* from carto  1.0: **Nested style attachments (layer)** (::) are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. **OR should we move away to a procedure versus declarative?**
* from carto  1.0: **style instances (features)** (/) similar to nested styles, but within the same original layer. 
* from carto  1.0: **variable expresions** evaluation of variable expressions: _( @variable-stroke-width + 5 )_.... modifying color swatches, stroke-widths, and @variables, etc.
* new  carto  2.0: **@media zoom selection** [zoom] from Cascadenik and Carto 1.0 allowed, but also now: @media (zoom > 3) and (zoom < 10) {  #lakes {    /* style */   } }
* new  carto  2.0: **geometry type selectors** (point, line, polygon, raster) eg: _geom_type_selector.classname { ... }_ or _.classname _geom_type_selector { ... }_ (which is less CSS like, but more like the sketches below)  **THIS IS THE ONLY SHOW STOPPER.**

#Super advanced bits (Map CSS 3.0):

_All these must be supported to meet 3.0 compliance_

_Note: some are likely to remain vender specific implementations, -vender-propertyname eg: -mapnik-line-stroke-gradient, bla bla bla_

* new  carto  2.0: **data variables/expansion** functional expansion of column values per feature [name] but for property values? Held up by upstream Mapnik changes needed.
* new  carto  2.0: **Selectors** for object _advanced_ **geometry components**: inner outer rings, vertex index and first, last. eg: _.classname geom_type_selector { ... }_
* new  carto  2.0: **Dependant attachments** (&&) depend on the previous bits being rendered in that selection. Useful for text labels to require the anchor symbolization being placed. eg: point&&stroke
* from carto  1.0: **functional expressions** evaluation of variable expressions with a predefined function: _function( @variable-stroke-width )_
* new  carto  2.0: **Shorthand style properties** eg: `stroke: url(...) #9f0 repeat 20.0;` is now allowed, and expands to `{ stroke-image:url(...); stroke-color:#9f0; stroke-image-repeat:true; stroke-size:20.0px }`
* new  carto  2.0: **Special rendering targets w/r/t attachements**: interior, edge, registration.
* new  carto  2.0: **Rendering/compositing targets** are implied but can be explicate or overridden: fill-, stroke-, anchor-, and data-. _Note: color:#hex is implied as fill-color:#hex._
* new  carto  2.0: **Blending modes** ala Photoshop, Illustrator, new compositing branch of Mapnik, cutting edge CSS.
* new  carto  2.0: **gradients on strokes** - 

#File structure

* MSS for the Map CSS styling definition.
* MML for the datasources and their layering. _Map CSS MML spec can be implemented in either JSON and XML formats._
* cascadnik/carto 2.0: data source definition can be inline or an import in the MML. _easy in XML format, harder in JSON?_


#Reasonable defaults:

* Text -> geom_centroid -> Registration/Text : style="12px black Arial" --**or**-- { text-size:12px; text-color:#000000; text-face:Arial; }
* Anchor -> Point -> Registration: style="2px #000000 round;" --**or**-- { stroke-width:2px; stroke-color:#000000; cap:round; }
* Stroke -> Line -> Edge: style="1px #000000 square;" --**or**-- { stroke-width:1px; stroke-color:#000000; cap:square; }
* Fill -> Polygon -> Interior: style="grey" --**or**-- { fill-color:#eeeeee; }
* Raster Pixels -> Point > Registration : Per pixel colored by the RGB channel values. CSS tk tk tk. Kinda like a RGB #hex gradient per 0-255 values?


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