CSS Inspired Map Styling
=====================

_Notes, sketches, and frameworks inspired by October 2011 map styling workshop held in SF_

Question: Core bits should be universal. Extras should be wrapped in a Carto 2.0 designator?

===

#Goals:

1. CSS-like map design language. Syntax extends the foundation laid by [Cascadenik](https://github.com/mapnik/Cascadenik/wiki/Dictionary) and [Carto](http://mapbox.com/carto/) ([overview](https://github.com/mapbox/carto)) and the power of Less.js. [GeoServer](http://docs.geoserver.org/stable/en/user/community/css/index.html) is also joining the mix.
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

        Note: reimagined a bit from basic HTML CSS and Cascadenik, Carto. 
        Marker is new in 1.0 (was point).
        Text is more consistent in 1.0 and powerful in 2.0.
        Interactivity and data formalized 2/3?
        
1. **Size by**: anchors, strokes, and labels have a 2d extrusion measurement (eg: 2px) 

        For images, the size is implied by a non-repeat dimension of the source image. 
        Others are determined by default style CSS. Continues from Cascadenik/Carto 1.0.
    
1. **Ink with** for color (mask) areas (extrusion of stroke, or fill): rgb, cmyk, hsv, gradient, image (with repeat options). 

        With alpha. All color spaces/images/gradient are coallpsed in "ink" in Carto 2.0

1. **Object geometry type filter**: point, line, polygon, continuous-field aka raster. 

        New in 2.0

1. **Object geometry component filter**: The component polys, rings, & vertex. 
    
        New in 3.0
    
1. **Rendering target**: (pivots off geometry type) registration, edge, interior, LABEL, DATA, INTERACTIVITY. 
        
        New in 3.0. With optional masks and positioning/attachment hints.


#Core bits (Mappy CSS 1.0):

_All these must be supported to meet 1.0 compliance_

1. **Basic CSS syntax: stroke, fill, marker (was point, also talked about anchor)**

        * Properties consist of name/value pairs separated by colons (:).
             
        * They are grouped per selector by { ... }.

    _Supported by: Cascadenik (only #rgb and #rrggbb, not 'black'), Carto (all), GeoServer (all except those not already supported by GeoTools renderer: fill-repeat, marker-placement, marker-overlap, marker-spacing), Gmaps?_
        
         `fill-color: #rgb;` or `fill-color: #rrggbb;` or `fill-color: black;` or `fill-color: url("image.png");` - Default is none.
    
         `fill-repeat: ...;` only comes to play when fill-color is set to an image. Default is ...

         `fill-opacity: 1.0;` - ratio range between 0.0 and 1.0. Default is 1.0

         `stroke-width: 2px;` - Default is none.
        
         `stroke-color: #rrggbb;`  or `stroke-color: black;`.  - Default is none. Note: `stroke-color: url("image.png");` is an advanced feature.
    
         `stroke-opacity: 1.0;`  - ratio range between 0.0 and 1.0. Default is 1.0

         `stroke-cap: round;` or `stroke-cap: butt;`  or `stroke-cap: square;` 

         `stroke-join: miter;` or `stroke-join: round;` or `stroke-join: bevel;`

         `stroke-dash-array: [a,b...]`

         `marker-width: 2px;` - Default is none. This results in a circle of diameter 2. If an image is specified, it overrides the image's native size.
    
         `marker-color: #rrggbb;` or `marker-color: url("image.png");` or `marker: url("image.png");` -- the last one of these is weird but interesting
        
         `marker-opacity: 1.0;`  - ratio range between 0.0 and 1.0. Default is 1.0

         `marker-placement: point;` or `marker-placement: line;`

         `marker-overlap: false;` or `marker-overlap: true;` - defaults to false.

         `marker-spacing: 100px;` - 

    
1. **Filters** for feature attributes. Exactly equal, not equal, less than, greater than, less than or equal to, greater than or equal to based on class or id selectors.

    _Supported by: Cascadenik, Carto, GeoServer (actually embeds [ECQL](http://docs.geoserver.org/latest/en/user/filter/ecql_reference.html))_

        #world[population = 100]
        #world[population != 100]
    
        #world[population > 100]
        #world[population < 100]
        #world[population >= 100]
        #world[population <= 100]
                
        No filter:
        
        * { ... } in Cascadenik and GeoServer, think this is true in Carto too.
        
        Combining filters:
        
        Combination is done in the usual CSS way. A rule with two filters separated by a 
        comma affects any features that match either filter, while a rule with two filters
        separated by only whitespace affects only features that match both filters. Here’s
        an example using a basic attribute filter (described below):
        
        /* Matches places where the lake is flooding */
        [rainfall>12] [lakes>1] {
            fill: black;
        }
        
        /* Matches wet places */
        [rainfall>12], [lakes>1] {
            fill: blue;
        }
    
1. **Point geoms have an edge** available for stroking.

    _Supported by: none... Should be easy to implement in Cascadenik, Carto for Mapnik using the marker versus point XML syntax?_
    
         In Mappy CSS: `marker-width: 2px;` translates to Mapnik XML `point-height: 2px; point-width: 2px;`

         In Mappy CSS: `marker-image: url('image.png');` translates to Mapnik XML `marker-image: url('image');`

    _In GeoServer, implemented by supporting polygon styling options on a ``:mark`` pseudoelement:_

         [prop > 100] {
             mark: symbol("circle");
         }

         [prop > 100] :mark {
             stroke: blue;
         }

         // or, with nested rules:
         /////////////////////////

         [prop > 100] {
             mark: symbol("circle");
             :mark { 
                 stroke: blue;
             }
         }

1. **Basic CSS syntax: text labels** - New "label once" `text-name:[fieldname]` where the [fieldname] grabs attributes per feature on render. This is extended either with `::` layer attachements or `keyword-attachments` to gain multiple text labels, shield symbolizers, etc.

    _Supported by: Carto, GeoServer (uses slightly different [notation](http://docs.geoserver.org/stable/en/user/community/css/values.html#labels)). Easy to implement in Cascadenik?_
            
        #world {
          text-name: "[NAME]";
          text-face-name: "Arial";
        }
        
    NOTE: In the end, text-name: [NAME] should be the ideal. This is supported only by GeoServer now.
        
    NOTE: In GeoServer, this is:
        
        {
            label: [STATE_ABBR];
            font-family: "Times New Roman";
            font-style: normal;
            font-size: 14;
        }

1. **Markers instead of points**: or `anchor`? Marker is more widely supported in Gmaps world. Mapnik's `point` should be hidden in this generic spec. _JL, NVK and NVK on 25 May and 29 May._

    _Supported by: none... should be easy to implement in Cascadenik and Carto?_

         for an image, like Gmaps now: `marker-image: url('...');`
    
         for a circle, the default: `stroke-size: 2px;`
    
         for a square: `stroke-size: 2px; stroke-cap: butt;`

1. **For backwards compatability: points are are synonym for markers** Point would have a depreciation, new implementations might optionally support. _MM and NVK 29 May._

    _Supported by: none... should be easy to implement in Cascadenik and Carto?_

         Both `marker-image: url('...');` and `point-image: url('...');` would translate to `marker-image: url('...');` before exporting to the native rendering environment syntax.

1. **Layers, classes, ids, and layer metadata** - Layers are defined in the MML file and can include class="class1 class2 class3" or id="id1". Layers could have a name. Layers could have metadata.

    _Supported by: Cascadenik, Carto, GeoSever_

        .classname1 { ... }
        .classname2 { ... }
        .classname3 { ... }

        #id1 { ... }
        
        GeoServer also allows:
        
        layername {}
        
        The layer metadata isn't built now but could be addressed by:
        
        .classname { &[layer_metadata_filter=value] { ... } }
        
        versus
        
        .classname { [feature_field_filter=value] { ... } }
        
1. **Zoom and scale denominator**

    _Supported by: Cascadenik, Carto, GeoSever_

    [zoom>14] supported by Cascadnik and Carto.
    
    [@scale>100000] supported by GeoServer. JL likes something like this. Elaboration: syntax shouldn't compete with fieldnames.
    [scale-denominator>100000] suppored by Cascadenik now.

    Question: 
    
    [@zoom>14] should this be our future target?
        

#Advanced bits (Mappy CSS 2.0):

_All these must be supported to meet 2.0 compliance_

1. **Advanced CSS syntax** - RGBA, CYMK, HSV, etc other color spaces.

    _Supported by: Carto rgb(), rgba(), hsl(). GeoServer has support for only rgb()._
    
        #line {
          line-color: #ff0;                   # Carto, Cascadenik, GeoServer
          line-color: #ffff00;                # Carto, Cascadenik, GeoServer
          line-color: yellow;                 # Carto, Cascadenik, GeoServer
          line-color: rgb(255, 255, 0);       # Carto, GeoServer
          line-color: rgba(255, 255, 0, 1);   # Carto
          line-color: hsl(100, 50%, 50%);     # Carto
        }

1. **@variables** for color swatches, this is preprocessor to actual result. _Moved 23 May 2012 per TMCW, split off graphic styles and text character styles 29 May_

    _Supported by: Cascadenik (2.4.0), Carto (1.0)_
    
        @orange: #f90;
        #road-a {  line-width: 2; line-color: @orange; }
        
        expands to:
        
        #road-b { line-width: 2; line-color: #f90 }

1. ~~**@media zoom selection** [zoom] from Cascadenik and Carto 1.0 allowed, extended like below:  _Proposed by JL, liked by NVK and MM._~~

    Deprecitated for [@zoom=10], see above in Core section!
    
    _Supported by: none?_
    
        ~~`@media (zoom > 3) and (zoom < 10) {  #lakes {    /* style */   } }`~~

1. **geometry type selectors** (point, line, polygon, raster) eg: `geom_type_selector.classname { ... }` or `.classname geom_type_selector { ... }` (which is less CSS like, but more like the sketches below)  Advanced: only test the first feature's type in a data store, assumes homogenious data in a single data store. **THIS IS THE ONLY SHOW STOPPER, might be relatively easy to implement in Cascadenik?** 

    _Supported by: GeoServer (eg: mark-geometry: [vertices(geom)];). Should be easy to add to Cascadenik._
    
        `.classname [@geom=polygon] { ... }` 
        `.classname [@geom=line] { ... }` 
        `.classname [@geom=point] { ... }` 
        
        or

        `geom_type_selector.classname { ... }` or 
        `.classname geom_type_selector { ... }` or
                
        * {
          marker-symbol: 'circle';
          maker-geometry: [vertices(the_geom)];
        }

1. **Advanced CSS syntax: stroke**

    _Supported by: GeoServer (``stroke``, not ``stroke-color``GeoServer (``stroke``, not ``stroke-color``. ``stroke-repeat`` property takes keyword values indicating whether to use the image as a fill pattern or a stroke following the line)_
        
         `stroke-color: url("image.png");` or `stroke-background: url("image.png");`
    
1. **display:none** - like `!important`, but for not showing stuff, regardless of other rules. 

    Default is `display:map`

    _Supported by: Cascadenik (2.1.0)_
    
        .class[filter=value] { display:none; }

1. **FontSets**: very important for multilingual characters and UTF-8 labels, font fallbacks

    _Supported by: Carto_
    
        #world {
          text-name: "[NAME]";
          text-size: 11;
          text-face-name: "Georgia Regular", "Arial Italic";
        }        

1. **default.css** for geometry type selectors results in layers auto display TRUE instead of FALSE. Override with `display:none;`

    _Supported by: none... Predicated on "geometry type selectors" above._
    
        See section below.

1. **Advanced CSS syntax: shields**

    _Supported by: Carto, Cascadnik, GeoServer_
        
         In Carto:
         .class::layer_attachment { `shield-... }`
        
         In Cascadenik:
         .class NAME { `shield-... }`

         Question: can you accomplish the same thing with && dependent property rending... 
         or is this special enough to be a special case, even if that is true?


#Super advanced bits (Mappy CSS 3.0):

1. **variable expresions** modifying color swatches, stroke-widths, and @variables, etc.
    
    The basic set proposed for HTML's CSS 3.0: +, -, *, /. Any others?

    _Supported by: Carto_
    
        `( @variable-stroke-width + 5 )`

1. **RegEx filters** 

    _Supported by: Carto (1.x), GeoServer (uses alternate [syntax](http://docs.geoserver.org/latest/en/user/filter/ecql_reference.html#predicate))_
    
         `/* a regular expression over name */ #world[name =~ "A.*"]`
         
         Note: GeoServer also supports: [LIKE](http://docs.geoserver.org/stable/en/user/community/css/filters.html), but not explicate RegEx.

1. **layer-based style attachments** `::` are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. Useful for applying multiple labels, shields, or graphic styles. **OR should we move away to a procedure versus declarative?** See also, "repeated properties" below.

    _Supported by: Carto. Partial support by Cascadenik via keyword-based style attachments, see below._
    
        #world {
          line-color: #fff;
          line-width: 3;
          }
        
          #world::outline {
            line-color: #000;
            line-width: 6;
            }
            
1. **feature-based style instances** `/` similar to nested styles, but within the same original layer. 

    _Supported by: Carto_
    
        #roads {
          casing/line-width: 6;
          casing/line-color: #333;
          line-width: 4;
          line-color: #666;
        }

1. **keyword-based style attachments** `.roads name { text-size:12px; text-color:#000; }` and `.roads ref { shield... }` are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. Useful for applying multiple labels, shields, or graphic styles.

    _Supported by: Cascadenik, should be depreciated?_
    
        #world NAME {
          text-face-name: "Arial";
        }
        
        expands to:
        
        #world {
          text-name: "[NAME]";
          text-face-name: "Arial";
        }

1. **nesting combinator filters** 

    _Supported by: Cascadenik (2.3.0), Carto_ Debate over the &[] versus [] formatting.
    
        `.layer[foo=1] { &[bar=2] { ... } }` versus 
        
        `.layer[foo=1] { [bar=2] { ... } }`
        
        Has implications for supporting filtering based on layer metadata, an unsupported feature as of now.


1. **string expressions for keys other than text-name** 

    Is this a duplicate of Super Advanced Bits: **data variables/expansion**???
    
    _Supported by: Carto (partial)?_
    
        `text-name: "[fieldname]"` now
        
        future:
        
        `text-color: "#[fieldname]"`
        `shield-file: "stuff/[fieldname].png"
    
1. **repeated properties** - Like layer-based style attachements (`::`), see above.

    [Read more »](http://docs.geoserver.org/stable/en/user/community/css/multivalues.html)

    _Supported by: GeoServer_
    
        `* { stroke: black, white; }`
        
        Question: Should " " space be equivelant to "," comma? 
        Space implies both, where comma implies fallback (in case of text-name, also for line-array).
        
        
1. **nested styles** - from Less.js and Carto.

    _Supported by: Carto_

        /* Applies to all layers with .land class */
        .land {
          line-color: #ccc;
          line-width: 0.5;
          polygon-fill: #eee;
          /* Applies to #lakes.land */
          #lakes {
            polygon-fill: #000;
          }
        }
        
1. **mixins** for graphic styles, and text character styles. _Split off per TMCW, NVK, and MM conversation on 29 May_

    _Supported by: none..._
    
        @orange: #f90;
        .roads { line-width: 2; line-color: @orange;  }
        #road-a { .roads; }
        #road-b { .roads; line-color: #00c }
        
        expands to:
        
        #road-a { line-width: 2; line-color: #f90; }
        #road-b { line-width: 2; line-color: #00c }


#Extremely advanced bits (Mappy CSS 4.0):

_All these must be supported to meet 3.0 compliance_

_Note: some are likely to remain vender specific implementations, -vender-propertyname eg: -mapnik-line-stroke-gradient, bla bla bla_

1. **data variables/expansion** functional expansion of column values per feature `[name]` but for property values? Held up by upstream Mapnik changes.

    _Supported by: none..._

1. **functional expressions** evaluation of variable expressions with a predefined function.

    _Supported by: Carto_
    
        `function( @variable-stroke-width )`
        
        #world {
          polygon-fill: @mybackground + #222;
          line-color: darken(@mybackground, 10%);
        }

1. **Selectors** for object _advanced_ **geometry components**: inner outer rings, vertex index and first, last.

    _Supported by: none..._
    
        `.classname [@geom_part=outer_ring] { ... }`
        `.classname [@geom_part=inner_ring] { ... }`
        `.classname [@geom_part=vertex[0]] { ... }`
        `.classname [@geom_part=vertex[vertex.len-1]] { ... }`

1. **Dependant attachments** `&&` depend on the previous bits being rendered in that selection. Useful for text labels to require the anchor symbolization being placed. eg: point&&stroke

    _Supported by: none..._

1. **Shorthand style properties** 

    _Supported by: none..._
    
        `stroke: url(...) #9f0 repeat 20.0;`
        
        expands to:
        
        `{ stroke-image:url(...); stroke-color:#9f0; stroke-image-repeat:true; stroke-size:20.0px }`

1. **Special rendering targets w/r/t attachements (placement)**: interior, edge, registration, vertexes, parts there-of.

    _Supported by: none..._
    
    _GeoServer gets partway there with the ["geometry transformation"](http://docs.geoserver.org/stable/en/user/styling/sld-extensions/geometry-transformations.html) SLD extension_
        
        In Carto and Cascadnik, this is: ???
        
        line-pattern
        polygon-pattern

1. **Rendering/compositing targets** are implied but can be explicate or overridden: fill-, stroke-, anchor-, and data-. _Note: color:#hex is implied as fill-color:#hex._

    _Supported by: none..._

1. **Blending modes** ala Photoshop, Illustrator, new compositing branch of Mapnik, cutting edge CSS.

    _Supported by: none..._

1. **gradients on strokes** - The future come-ith.

    _Supported by: none..._

1. **heterogenious geometry type selectors** (point, line, polygon, raster) eg: `geom_type_selector.classname { ... }` or `.classname _geom_type_selector { ... }` (which is less CSS like, but more like the sketches below)  Advanced: tests per feature for feature type, important for GeoJSON etc. **THIS IS THE ONLY SHOW STOPPER.**

    _Supported by: none..._


#File structure

* **MSS** for the Mappy CSS styling definition.
* **MML** for the datasources and their layering. 

    _NOTE 1: Mappy CSS MML spec can embed the MSS part rather than linking to external file (the default)._

    _NOTE 2: Mappy CSS MML spec can be implemented in either XML (Cascadenik) or JSON (Carto) formats._
    
    _NOTE 3: Data source definition can be inline or an import in the MML. From cascadnik/carto 2.0. ¿Easy in XML format, harder in JSON?_

For the MML layer setup:

1. **Local versus remote** data files.

    _Supported by: Carto, Cascadenik_
    
    Carto does this as of the 0.2.0 release via separate [millstone](https://github.com/mapbox/millstone).

1. **DataSourcesConfig** an XML tag similar to the Stylesheet tag that allows you to externalize elements in an easy to manage format. [More info »](https://github.com/mapnik/Cascadenik/wiki/Managing-Data-Sources)

    _Supported by: Cascadenik via native XML property. Hard to accomplish in Carto's JSON format?_
            
        [DEFAULT]
        natural_earth_110m_base_url = http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m
        
        [natural_earth_land_110m]
        file = %(natural_earth_110m_base_url)s/physical/110m-land.zip

1. **interactivity templates**: should apply both to full vector and UTF8 grid approximations.

    _Supported by: UTFGRID spec, passed thru by Carto and Cascadenik?_
    
    Note: Carto supports passthru of the interactivity templates in TileMill, but doesn't support it explicately.
    
    The interactivity spec Carto wraps is [utfgrid-spec](https://github.com/mapbox/utfgrid-spec/tree/master/1.2).


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