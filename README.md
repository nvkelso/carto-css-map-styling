CSS Inspired Map Styling
=====================

_Notes, sketches, and frameworks inspired by October 2011 map styling workshop held in SF_

Question: Core bits should be universal. Extras should be wrapped in a Carto 2.0 designator?

===

#Goals:

1. CSS-like map design language. Syntax extends the foundation laid by [Cascadenik](https://github.com/mapnik/Cascadenik/wiki/Dictionary) and [Carto](http://mapbox.com/carto/) and the power of Less.js. [GeoServer](http://docs.geoserver.org/stable/en/user/community/css/index.html) is also joining the mix.
2. Better support for reference-style maps by formalizing the use of @variables for color swatches, and mixins for graphic styles and text character styles.
3. ~~¿Add support for thematic data cartography with data statistics like MIN, MAX, MEAN and use of those keywords in high-level filters that are under a dozen lines of code? Allow for both loose and hard connections between data and symbology.~~


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

1. **Basic CSS syntax: stroke, fill, marker (was point, also talked about anchor)**

    _Supported by: Cascadenik (only #rgb and #rrggbb, not 'black'), Carto (all), GeoServer (unkown), Gmaps?_
    
         `fill-color: #rgb;` or `fill-color: #rrggbb;` or `fill-color: black;` or `fill-color: url("image.png");` - Default is none.
    
         `fill-repeat: ...;` only comes to play when fill-color is set to an image. Default is ...

         `fill-opacity: 1.0;` - ratio range between 0.0 and 1.0. Default is 1.0

         `stroke-width: 2px;` - Default is none.
        
         `stroke-color: #rrggbb;`  or `stroke-color: black;`.  - Default is none. Note: `stroke-color: url("image.png");` is an advanced feature.
    
         `stroke-opacity: 1.0;`  - ratio range between 0.0 and 1.0. Default is 1.0

         `stroke-cap: round;` or `stroke-cap: butt;` 

         `marker-width: 2px;` - Default is none.
    
         `marker-color: #rrggbb;` or `marker-color: url("image.png");` or `marker: url("image.png");` -- the last one of these is weird but interesting
        
         `marker-opacity: 1.0;`  - ratio range between 0.0 and 1.0. Default is 1.0
    
1. **Filters** for feature attributes. Exactly equal, less than, greater than, less than or equal to, greater than or equal to.

    _Supported by: Cascadenik, Carto_
    
         `.class [FIELDNAME=value]`
         `.id [FIELDNAME>value]`
         `.id [FIELDNAME<value]`
         `.class [FIELDNAME<=value]`
         `.class [FIELDNAME>=value]`

1. ~~**display:none** - like `!important`, but for not showing stuff, regardless of other rules. ~~
1. ~~**DataSourcesConfig** an XML tag similar to the Stylesheet tag that allows you to externalize elements in an easy to manage format. [More info »](https://github.com/mapnik/Cascadenik/wiki/Managing-Data-Sources)~~
1. ~~**FontSets**: very important for multilingual characters and UTF-8 labels, font fallbacks~~
1. **Point geoms have an edge** available for stroking.

    _Supported by: none... Should be easy to implement in Cascadenik, Carto for Mapnik using the marker versus point XML syntax?_
    
         In Map CSS: `marker-width: 2px;` translates to Mapnik XML `point-height: 2px; point-width: 2px;`

         In Map CSS: `marker-image: url('image.png');` translates to Mapnik XML `marker-image: url('image');`

1. **Basic CSS syntax: text labels** - New "label once" `text-name:[fieldname]` where the [fieldname] grabs attributes per feature on render. This is extended either with `::` layer attachements or `keyword-attachments` to gain multiple text labels, shield symbolizers, etc.

    _Supported by: none... Easy to implement in Cascadenik_

1. **Markers instead of points**: or `anchor`? Marker is more widely supported in Gmaps world. Mapnik's `point` should be hidden in this generic spec. _JL, NVK and NVK on 25 May and 29 May._

    _Supported by: none... should be easy to implement in Cascadenik and Carto?_

         for an image, like Gmaps now: `marker-image: url('...');`
    
         for a circle, the default: `stroke-size: 2px;`
    
         for a square: `stroke-size: 2px; stroke-cap: butt;`

1. **For backwards compatability: points are are synonym for markers** Point would have a depreciation, new implementations might optionally support. _MM and NVK 29 May._

    _Supported by: none... should be easy to implement in Cascadenik and Carto?_

         Both `marker-image: url('...');` and `point-image: url('...');` would translate to `marker-image: url('...');` before exporting to the native rendering environment syntax.


#Advanced bits (Map CSS 2.0):

_All these must be supported to meet 2.0 compliance_

1. **Advanced CSS syntax** - RGBA, CYMK, HSV, etc other color spaces.

    _Supported by: Carto_

1. **Local versus remote** data files.

    _Supported by: Cascadenik_

1. **@variables** for color swatches, this is preprocessor to actual result. _Moved 23 May 2012 per TMCW, split off graphic styles and text character styles 29 May_

    _Supported by: Cascadenik (2.4.0), Carto (1.0)_

1. **RegEx filters** 

    _Supported by: Carto (1.x)_
    
         `/* a regular expression over name */ #world[name =~ "A.*"]`

1. **layer-based style attachments** `::` are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. Useful for applying multiple labels, shields, or graphic styles. **OR should we move away to a procedure versus declarative?** See also, "repeated properties" below.

    _Supported by: Carto. Partial support by Cascadenik via keyword-based style attachments, see below._

1. **feature-based style instances** `/` similar to nested styles, but within the same original layer. 

    _Supported by: Carto_

1. **variable expresions** evaluation of variable expressions: `( @variable-stroke-width + 5 )` .... modifying color swatches, stroke-widths, and @variables, etc.

    _Supported by: Carto_

1. **@media zoom selection** [zoom] from Cascadenik and Carto 1.0 allowed, but also now: `@media (zoom > 3) and (zoom < 10) {  #lakes {    /* style */   } }` _Proposed by JL._

    _Supported by: none?_

1. **geometry type selectors** (point, line, polygon, raster) eg: `geom_type_selector.classname { ... }` or `.classname _geom_type_selector { ... }` (which is less CSS like, but more like the sketches below)  Advanced: only test the first feature's type in a data store, assumes homogenious data in a single data store. **THIS IS THE ONLY SHOW STOPPER, might be relatively easy to implement in Cascadenik?**

    _Supported by: GeoServer (eg: mark-geometry: [vertices(geom)];). Should be easy to add to Cascadenik._

1. **interactivity templates**: should apply both to full vector and UTF8 grid approximations.

    _Supported by: kinda in Carto?_

1. **Advanced CSS syntax: stroke**

    _Supported by: none..._
        
         `stroke-color: url("image.png");` or `stroke-background: url("image.png");`
    
1. **display:none** - like `!important`, but for not showing stuff, regardless of other rules. Default is `display:map`

    _Supported by: Cascadenik (2.1.0)_

1. **DataSourcesConfig** an XML tag similar to the Stylesheet tag that allows you to externalize elements in an easy to manage format. [More info »](https://github.com/mapnik/Cascadenik/wiki/Managing-Data-Sources)

    _Supported by: Cascadenik via native XML property. Hard to accomplish in Carto's JSON format?_

1. **FontSets**: very important for multilingual characters and UTF-8 labels, font fallbacks

    _Supported by: Carto?_

1. **keyword-based style attachments** `.roads name { text-size:12px; text-color:#000; }` and `.roads ref { shield... }` are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. Useful for applying multiple labels, shields, or graphic styles.

    _Supported by: Cascadenik_

1. **mixins** for graphic styles, and text character styles. _Split off per TMCW, NVK, and MM conversation on 29 May_

    _Supported by: none..._

1. **nesting combinator filters** `.layer[foo=1] { &[bar=2] { ... } }` versus `.layer[foo=1] { [bar=2] { ... } }`

    _Supported by: Cascadenik (2.3.0), Carto_ Debate over the &[] versus [] formatting.

1. **default.css** for geometry type selectors results in layers auto display TRUE instead of FALSE. Override with `display:none;`

    _Supported by: none... Predicated on "geometry type selectors" above._

1. **string expressions for keys other than text-name** `text-name: "[fieldname]"` so `text-color: "#[fieldname]"` or `shield-file: "stuff/[fieldname].png"` is valid, too.

    _Supported by: Carto (partial)?_
    
1. **repeated properties** - Like layer-based style attachements (`::`). eg: `* { stroke: black, white; }`

    _Supported by: GeoServer_
    
    
    


#Super advanced bits (Map CSS 3.0):

_All these must be supported to meet 3.0 compliance_

_Note: some are likely to remain vender specific implementations, -vender-propertyname eg: -mapnik-line-stroke-gradient, bla bla bla_

1. **data variables/expansion** functional expansion of column values per feature `[name]` but for property values? Held up by upstream Mapnik changes.

    _Supported by: none..._

1. **functional expressions** evaluation of variable expressions with a predefined function: `function( @variable-stroke-width )`

    _Supported by: Carto_

1. **Selectors** for object _advanced_ **geometry components**: inner outer rings, vertex index and first, last. eg: `.classname geom_component_selector { ... }`

    _Supported by: none..._

1. **Dependant attachments** `&&` depend on the previous bits being rendered in that selection. Useful for text labels to require the anchor symbolization being placed. eg: point&&stroke

    _Supported by: none..._

1. **Shorthand style properties** eg: `stroke: url(...) #9f0 repeat 20.0;` is now allowed, and expands to `{ stroke-image:url(...); stroke-color:#9f0; stroke-image-repeat:true; stroke-size:20.0px }`

    _Supported by: none..._

1. **Special rendering targets w/r/t attachements (placement)**: interior, edge, registration, vertexes, parts there-of.

    _Supported by: none... A little in GeoServer?_

1. **Rendering/compositing targets** are implied but can be explicate or overridden: fill-, stroke-, anchor-, and data-. _Note: color:#hex is implied as fill-color:#hex._

    _Supported by: none..._

1. **Blending modes** ala Photoshop, Illustrator, new compositing branch of Mapnik, cutting edge CSS.

    _Supported by: none..._

1. **gradients on strokes** - The future come-ith.

    _Supported by: none..._

1. **heterogenious geometry type selectors** (point, line, polygon, raster) eg: `geom_type_selector.classname { ... }` or `.classname _geom_type_selector { ... }` (which is less CSS like, but more like the sketches below)  Advanced: tests per feature for feature type, important for GeoJSON etc. **THIS IS THE ONLY SHOW STOPPER.**

    _Supported by: none..._


#File structure

* **MSS** for the Map CSS styling definition.
* **MML** for the datasources and their layering. 


    _NOTE 1: Map CSS MML spec can embed the MSS part rather than linking to external file (the default)._

    _NOTE 2: Map CSS MML spec can be implemented in either XML (Cascadenik) or JSON (Carto) formats._
    
    _NOTE 3: Data source definition can be inline or an import in the MML. From cascadnik/carto 2.0. ¿Easy in XML format, harder in JSON?_


#Reasonable defaults:

_Note: if the defaults.mss file is included, the default for layers is ON instead of off"_

_Note: the stye="..." examples need to be reworked._

* **Text -> geom_centroid -> Registration/Text**: 

        `(optional geom-selector) { text-size:12px; text-color:#000000; text-face:Arial; }` --**or**-- 

        `style="12px black Arial"` (in version 3.0)

* **Anchor -> Point -> Registration**: 

        `point-geom-selector { stroke-width:2px; stroke-color:#000000; cap:round; }` --**or**-- 

        `style="2px #000000 round;"` (in version 3.0)

* **Stroke -> Line -> Edge**: 

        `line-geom-selector { stroke-width:1px; stroke-color:#000000; cap:square; }` --**or**-- 

        `style="1px #000000 square;"` (in version 3.0)

* **Fill -> Polygon -> Interior**: 

         `polygon-geom-selector { fill-color:#eeeeee; }` --**or**-- 

         `style="grey"` (in version 3.0)

* **Raster Pixels -> Point > Registration**: 

         Per pixel colored by the RGB channel values. Kinda like a RGB #hex gradient per 0-255 values?
         CSS tk tk tk.


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