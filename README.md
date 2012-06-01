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
2. Syntax should be easy to write for average designer, reusable, and shareable. _- NVK stubbed out designer focused Tools directory 30 May._
3. Syntax should be fast for the renderer draw. Text and shields are special cases we want to allow for. Mike put this as it's &#60;/div&#62; friendly.
4. Should have reasonable style defaults / implementation routines that make sense for the designer and for the renderer. Reasonable defaults make it quick to explore a dataset (not just present the final results). Example: draw points to see a distribution, classify points, lines and polygons by thematic attribute and color quickly. See: [NVK's Thematic Carto Tools repo](https://github.com/nvkelso/thematic-carto-tools)
5. Core syntax is interoperable ~~with other style languages~~ between renderer implementations (Mapnik, GeoServer, etc) and between ~~flavors~~ implementation dialects for the same renderer. Extensions allow extra awesome things. _- NVK on 31 May._
6. Clearly identify ambiguities (and document recommended solutions).
7. Standard adhers to [Semantic Versioning](http://semver.org/). Watch background video ([youtube](http://www.youtube.com/watch?v=k2h2lvhzMDc)). _- MM and NVK add 18 May 2012._
8. First principles based so it's easy to implement across software. _- NVK add 19 May 2012._

NOTE: Extensions might include data formats (csv, shp in zip), data statistics (count, min, max), data locations (local/remote/offline), experimental styling keywords (feature class heigherachy ala scalemaster.org?), styles with absolute and relative values (gradients, choropleth/quantiles/nat'lbreaks/etc, lighter, darker, smaller text, larger text), and rendering (like for shields?), interactivity (one layer, multiple layers).


#Atomic bits (nouns, verbs):

1. **CSS target**: marker, stroke, fill, text, shield, and ¿data? and ¿interactivity?. 

        Note: reimagined a bit from basic HTML CSS and Cascadenik, Carto. 
        Marker is new in 1.0 (was `point`, for a while `anchor`).
        Text is more consistent in 1.0 and powerful in 2.0.
        Interactivity and data formalized 2/3/4? Separate but parellel formats?
        
1. **Size by**: markers, strokes, and labels have a 2d extrusion measurement (eg: 2px) 

        For images, the size is implied by a non-repeat dimension of the source image. 
        Others are determined by default style CSS. Continues from Cascadenik/Carto 1.0.
    
1. **Ink with** for color (mask) areas (extrusion of stroke, or fill): rgb, cmyk, hsv, gradient, image (with repeat options). 

    Note: All color spaces/images/gradient are coallpsed in "ink" verb in Carto 2.0?

        1.0: `color:#rgb` and `color:#rrggbb` with `opacity:1.0`
            
        2.0: Other color spaces. See below.
        
        3.0: gradients as colors? see **gradients on strokes** section far below.

1. **Object geometry type filter**: point, line, polygon, continuous-field aka raster. 

        New in 2.0

1. **Object geometry component filter**: The component polys, rings, & vertex. 
    
        New in 4.0
    
1. **Rendering target**: (pivots off geometry type) registration, edge, interior, LABEL, DATA, INTERACTIVITY. 
        
        New in 4.0. With optional masks and positioning/attachment hints.


#Core bits (Mappy CSS 1.0):

_All these must be supported to meet 1.0 compliance_

1. **Basic CSS syntax: stroke, fill, marker** - marker was point, also talked about anchor

    Properties consist of name/value pairs separated by colons (:).
             
    They are grouped per selector by { ... }.
    
    To be visible:
        
        stroke needs **both** stroke-width and stroke-color defined.
        fill needs **only** fill defined.
        markers need either a) **both** stroke-width and stroke-color or b) **only** marker-image set.

    _Supported by: Cascadenik (only #rgb and #rrggbb, not 'black'), Carto (all), GeoServer (all except those not already supported by GeoTools renderer: fill-repeat, marker-placement, marker-overlap, marker-spacing), Gmaps?_
        
    **fill**
    
    `fill-color: #rgb;` or `fill-color: #rrggbb;` or `fill-color: black;` or `fill-color: url("image.png");` - Default is none.
    
        WARNING: Carto/Cascadenik seems to accomplish fill-color: url("image.png") this way now:
        
        polygon-pattern-file: url()
        polygon-pattern-width 4px;
        polygon-pattern-height: 4px;
        
        It would need to take fill-color and expand it to the Mapnik native syntax.
    
    `fill-repeat: ...;` only comes to play when fill-color is set to an image. Default is ...

    `fill-opacity: 1.0;` - ratio range between 0.0 and 1.0. Default is 1.0

    **stroke**

    `stroke-width: 2px;` - Default is none.
        
    `stroke-color: #rrggbb;`  or `stroke-color: black;`.  - Default is none. 
    
    WARNING: `stroke-color: url("image.png");` is an advanced feature, see below.
    
    `stroke-opacity: 1.0;`  - ratio range between 0.0 and 1.0. Default is 1.0

    `stroke-cap: round;` or `stroke-cap: butt;`  or `stroke-cap: square;` Default is butt, matching all other styling languages?

    `stroke-join: miter;` or `stroke-join: round;` or `stroke-join: bevel;`

    `stroke-dash-array: 1.1, 2.2, 3.3;`

    **marker**

    `marker-width: 2px;` - Default is none. This results in a circle of diameter 2. If an image is specified, it overrides the image's native size.
    
    `marker-color: #rrggbb;` or `marker-color: url("image.png");` or `marker: url("image.png");` -- the last one of these is weird but interesting. More like GeoServer, too?
        
    `marker-cap: round;` or `marker-cap: butt;`  or `marker-cap: square;`. Default is butt, resulting in a square marker? In Mappy CSS 2.0 defaults, does this turn into `marker-cap: round;` as default to get circles instead of squares with the fallback of squares?

    `marker-opacity: 1.0;`  - ratio range between 0.0 and 1.0. Default is 1.0

    `marker-placement: point;` or `marker-placement: line;`

    `marker-overlap: false;` or `marker-overlap: true;` - defaults to false.

    `marker-spacing: 100px;` - tk tk tk

    **rasters**
    
    Seems like this should just be gradient stretches like described in **gradients on strokes** section far below.
    
    
1. **Filters** for feature attributes. Exactly equal, not equal, less than, greater than, less than or equal to, greater than or equal to based on class or id selectors.

    _Supported by: Cascadenik, Carto, GeoServer (actually embeds [ECQL](http://docs.geoserver.org/latest/en/user/filter/ecql_reference.html))_

    **Basic filters:**

        #world[population = 100]
        #world[population != 100]
    
        #world[population > 100]
        #world[population < 100]
        #world[population >= 100]
        #world[population <= 100]
                
    **No filter:**
        
    `* { ... }` - in Cascadenik, Carto, and GeoServer.
        
    **Combining filters:**
        
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
            
    NOTE: In the end, text-name: [NAME] should be the ideal. This is supported only by GeoServer now.

    Mappy CSS:

        #world {
          text-name: [NAME];
          text-face-name: "Arial";
        }

    Carto:
    
        #world {
          text-name: "[NAME]";
          text-face-name: "Arial";
        }
        
    Cascadenik:
    
        #world NAME {
          text-face-name: "Arial";
        }

    GeoServer:
        
        {
            label: [STATE_ABBR];
            font-family: "Times New Roman";
            font-style: normal;
            font-size: 14;
        }
        
    Note: GeoServer allows string constants, not just fieldnames! Cool: http://docs.geoserver.org/stable/en/user/community/css/values.html

1. **Markers instead of points**: or `anchor`? Marker is more widely supported in Gmaps world. Mapnik's `point` should be hidden in this generic spec. _JL, NVK and NVK on 25 May and 29 May._

    _Supported by: none... should be easy to implement in Cascadenik and Carto?_

        { marker-image: url('...'); }               /* for an image, like Gmaps now */
        
        { stroke-size: 2px; }                       /* for a circle, the Mappy CSS 2.0 default  
                                                       which expands to 
                                                       stroke-size: 2px; stroke-cap: cap; */
    
        { stroke-size: 2px; stroke-cap: butt; }     /* for a square */

    Question: How do we size a maker? px values? are % allowed?
    
        { marker-image: url('...'); }                        /* native size of that image asset */
        { marker-image: url('...'); marker-size: 200%; }     /* 200% of the native size */
        { marker-image: url('...'); marker-size: 4px; }      /* absolute 4px dimension, expands to
                                                                         marker-width:4px; 
                                                                         marker-height:4px; */

    Are marker-width and marker-height allowed as well as marker-size? 

        { marker-image: url('...'); marker-width: 4px; 
                                    marker-height: 8px; }    /* for an image, like Gmaps now */

1. **For backwards compatability: points are are synonym for markers** Point would have a depreciation, new implementations might optionally support. _MM and NVK 29 May._

    _Supported by: Cascadenik, Carto use points instead of markers now. Shouldbe depreciated?_

    Both `marker-image: url('...');` and `point-image: url('...');` would translate to `marker-image: url('...');` before exporting to the native rendering environment syntax.

1. **Layers, classes, ids, and layer metadata** - Layers are defined in the MML file and can include class="class1 class2 class3" or id="id1". Layers could have a name. Layers could have metadata.

    _Supported by: Cascadenik, Carto, GeoSever_

        .classname1 { ... }
        .classname2 { ... }
        .classname3 { ... }

        #id1 { ... }
        
    GeoServer also allows:
        
        layername {}
        
    QUESTION: Should that be embrased in Mappy CSS? Not in the 1.0, but somewhere 2.0 to 4.0?

    The layer metadata isn't built now but could be addressed by:
        
        .classname { &[layer_metadata_filter=value] { ... } }
        
    versus
        
        .classname { [feature_field_filter=value] { ... } }
        
    Warning: two differeint &[] and [] implementations especially re layer meta data has other implications, promts other discussions and incompatabilities.
        
1. **Zoom and scale denominator**

    _Supported by: Varients by Cascadenik, Carto, GeoSever_

        [@zoom>14]
        [@scale>100000]

    Now:
    
    `[zoom>14]` - supported by Cascadnik and Carto. Should be depreciated?
    
    `[@scale>100000]` - supported by GeoServer. JL likes something like this. Elaboration: syntax shouldn't compete with fieldnames.
    
    `[scale-denominator>100000]` - suppored by Cascadenik now, could support @scale instead in the future?.
    
1. **!important** - ensure your setting takes precedence in the CSS parser over filters, appearance styling that would otherwise be more important in order-of-operations.

    _Supported by: Cascadenik, Carto_
    
        .class {
          line-color: #ff0 !important; 
        }
        

#Basic bits (Mappy CSS 2.0):

_All these must be supported to meet 2.0 compliance_

1. **Advanced CSS syntax for colors** - RGBA, CYMK, HSV, etc other color spaces.

    _Supported by: Carto rgb(), rgba(), hsl(). GeoServer has support for only rgb()._
    
        #line {
          line-color: #ff0;                   /* Carto, Cascadenik, GeoServer */
          line-color: #ffff00;                /* Carto, Cascadenik, GeoServer */
          line-color: yellow;                 /* Carto, Cascadenik, GeoServer */
          line-color: rgb(255, 255, 0);       /* Carto, GeoServer */
          line-color: rgba(255, 255, 0, 1);   /* Carto */
          line-color: hsl(100, 50%, 50%);     /* Carto */
        }

1. **@variables** for color swatches, this is preprocessor to actual result. _Moved 23 May 2012 per TMCW, split off graphic styles and text character styles 29 May_

    _Supported by: Cascadenik (2.4.0), Carto (1.0)_
    
        @orange: #f90;
        #road-a {  line-width: 2; line-color: @orange; }
        
    expands to:
        
        #road-b { line-width: 2; line-color: #f90 }

1. ~~**@media zoom selection** [zoom] from Cascadenik and Carto 1.0 allowed, extended like below:  _Proposed by JL, liked by NVK and MM._~~

    Deprecitated for [@zoom=10], see above in Core section!
    
    ~~_Supported by: none?_~~
    
    ~~`@media (zoom > 3) and (zoom < 10) {  #lakes {    /* style */   } }`~~

1. **geometry type selectors** (point, line, polygon, raster) eg: ~~`geom_type_selector.classname { ... }`~~ or ~~`.classname geom_type_selector { ... }`~~ (which is less CSS like, but more like the sketches below)  Advanced: only test the first feature's type in a data store, assumes homogenious data in a single data store. **THIS IS THE ONLY SHOW STOPPER, might be relatively easy to implement in Cascadenik?** 

    _Supported by: GeoServer kinda sorta (eg: mark-geometry: [vertices(geom)];). Should be easy to add to Cascadenik._
    
        .classname[@geom=polygon] { ... }
        .classname[@geom=line] { ... } 
        .classname[@geom=point] { ... }
        
    or ~~`geom_type_selector.classname { ... }`~~
        
    or ~~`.classname geom_type_selector { ... }`~~
                
    GeoServer does it now like:
    
        * {
          marker-symbol: 'circle';
          maker-geometry: [vertices(the_geom)];
        }

1. **Advanced CSS syntax: stroke background color and attached markers**

    _Supported by: Cascadenik, Carto, GeoServer kinda sorta._
        
        stroke-color: url("image.png");

    or 
    
        stroke-background: url("image.png");
    
    WARNING: GeoServer uses `stroke` instead of `stroke-color`. That could change, @David?

    **attached markers on the stroke** now in Carto and Cascadenik:
    
        .class {
            line-pattern-file: url()
            line-pattern-width: 4px;        
            line-pattern-height 4px;
        }

    But that doesn't use the pattern >>**inside**<< the color inkable area, like in the stroke-color / stroke-background section above. 
    
    Rather it attaches a pattern to the edge of the line as a series of markers? See rendering targets section below, overview here.

    Should the syntax be cleaned up?
    
        .class {
            marker-image: url()
            marker-position: registration (default)|
                             line (kinda in Cascadenik/Carto/Mapnik)|
                             interior (kinda a polygon fill)|
                             edge|
                             vertex|
                             complex-geom-operators] 
        }
    
    Note: Geoserver uses `stroke-repeat` property takes keyword values indicating whether to use the image as a fill pattern or a stroke following the line).
    
1. **display:none** - like `!important`, but for not showing stuff, regardless of other rules. 

    Default is `display:map`

    _Supported by: Cascadenik (2.1.0) as display:none, but not yet attr of 0._
    
        .class[filter=value] { display:none; }
    
    Attributes of 0 should be parsed as display:none;
    
        .class[filter=value] { stroke-width:0px; }


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

    _Supported by: Carto, Cascadnik, GeoServer?_
        
    In Carto:

         .class::layer_attachment { `shield-... }`
        
    In Cascadenik:
    
         .class NAME { `shield-... }`

    For GeoServer:
    
        I don't see actual highway shields detailed here:    
        http://docs.geoserver.org/stable/en/user/community/css/styled-marks.html
        
        From David:
        
        In GeoServer SLD we support Graphic elements as a child of TextSymbolizer elements. 
        If present, such a graphic will be positioned behind the label regardless of where
        the label is actually rendered (due to conflict resolution, etc.) and hidden if
        the label is hidden.  Optionally, the graphic is also stretched to ensure that the
        label fits within it.  I think this is similar to how shield graphics behave in Mapnik, right?

        There are some screenshots and SLD examples in Andrea Aime's talk from FOSS4G 2010:
        http://2010.foss4g.org/presentations_show.php?id=3588
        I don't think the GeoServer user manual has caught up yet.

    Question: can you accomplish the same thing with && dependent property rending... 
    or is this special enough to be a special case, even if that is true?
         
1. **well-known-symbols marker images** - where image could be png, gif, jpg, svg, etc?. Ideally vector so sizing can be applied and still looks good.

    _Supported by: GeoServer_
    
    For w-k-s symbol() syntax:
    
        .class {
          marker-image: symbol(circle);
        }

    or, ala GeoServer:
    
        .class {
            marker-symbol: 'circle';
        }

    Versus arbitrary/generic url():

        .class {
          marker-image: url(path/circle.png);
        }

    For instance, GeoServer’s CSS module provides a collection of predefined symbols that you can use and combine to create simple marks, strokes, and fill patterns without needing an image editing program. You can access these symbols via the symbol() CSS function. For example, the built-in circle symbol makes it easy to create a simple ‘dot’ marker for a point layer:

        * {
          mark: symbol(circle);
        }
    
    Symbols work anywhere you can use a url() to reference an image (ie, you can use symbols for stroke and fill patterns as well as markers.)
        
    **Symbol Names:**
        
    _GeoServer extensions can add extra symbols (such as the chart:// symbol family which allows the use of charts as symbols via a naming scheme similar to the Google Charts API). However, there are a few symbols that are always available:_
        
        circle
        square
        triangle
        arrow
        cross
        star
        x
        shape://horizline
        shape://vertline
        shape://backslash
        shape://slash
        shape://plus
        shape://times


#Advanced bits (Mappy CSS 3.0):

1. **variable expresions** modifying color swatches, stroke-widths, and @variables, etc.
    
    The basic set proposed for HTML's CSS 3.0: +, -, *, /. Any others?

    _Supported by: Carto_
    
        ( @variable-stroke-width + 5 )

1. **RegEx filters** 

    _Supported by: Carto (1.x), GeoServer (uses alternate [syntax](http://docs.geoserver.org/latest/en/user/filter/ecql_reference.html#predicate))_
    
         /* a regular expression over name */ #world[name =~ "A.*"]
         
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
    
        .layer[foo=1] { &[bar=2] { ... } }
        
    versus 
        
        .layer[foo=1] { [bar=2] { ... } }
        
    WARNING: Has implications for supporting filtering based on layer metadata, an unsupported feature as of now. Cascadenik can easily do the [] instead of &[], but in light of the other question. And it makes it less Less.js like?

    
1. **repeated properties** - Like anonymous layer-based style attachements (`::`), see above. 

    Technically the ::name bit after the :: is fluff anyway? :: is still useful for grouping multiple graphic appearances onto a single duplicate of the layer (both of these are useful).

    [Read more »](http://docs.geoserver.org/stable/en/user/community/css/multivalues.html)

    _Supported by: GeoServer_
    
        * { stroke: black, white; }
        
    QUESTION: Should " " space be equivelant to "," comma? 
    
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

_All these must be supported to meet 4.0 compliance_

_Note: some are likely to remain vender specific implementations, -vender-propertyname eg: -mapnik-line-stroke-gradient, bla bla bla_

1. **data variables/expansion** aka string expressions for keys other than text-name. 

    Functional expansion of column values per feature `[name]` but for property values? Held up by upstream Mapnik changes?

    _Supported by: Carto (partial)?_
               
        text-color: #[fieldname]
        shield-file: "stuff/[fieldname].png"

1. **functional expressions** evaluation of variable expressions with a predefined function.

    _Supported by: Carto_
    
        function( @variable-stroke-width )
        
        #world {
          polygon-fill: @mybackground + #222;
          line-color: darken(@mybackground, 10%);
        }
    
    This example doesn't make sense to me: NVK.

1. **Selectors** for object _advanced_ **geometry components**: inner outer rings, vertex index and first, last.

    _Supported by: none..._
    
        .classname [@geom_part=outer_ring] { ... }
        .classname [@geom_part=inner_ring] { ... }
        .classname [@geom_part=vertex[0]] { ... }
        .classname [@geom_part=vertex[vertex.len-1]] { ... }

1. **Dependant attachments** `&&` depend on the previous bits being rendered in that selection. Useful for text labels to require the anchor symbolization being placed. eg: point&&text.

    _Supported by: none..._
    
        point&&text { ... }
        
    Needs a better example.

1. **Shorthand style properties** 

    _Supported by: none..._
    
        stroke: url(...) #9f0 repeat 20.0;
        
    expands to:
        
        { stroke-image:url(...); stroke-color:#9f0; stroke-image-repeat:true; stroke-size:20.0px }

1. **Special rendering targets w/r/t attachements (placement)**: interior, edge, registration, vertexes, parts there-of.

    _Supported by: none..._
    
    _GeoServer gets partway there with the ["geometry transformation"](http://docs.geoserver.org/stable/en/user/styling/sld-extensions/geometry-transformations.html) SLD extension_
        
        In Carto and Cascadnik, this is: ???
        
        line-pattern
        polygon-pattern

1. **Rendering/compositing targets** are implied but can be explicate or overridden: fill-, stroke-, anchor-, and data-. _Note: color:#hex is implied as fill-color:#hex._

    _Supported by: Kinda Carto, Cascadenik for line-pattern-symbolizer only, see above._
    
        .class[@position=registration|edge|interior|vertex|complex-geom-operators] { ... }

    or
        
        .class { marker-position:registration|edge|interior|vertex|complex-geom-operators; }


1. **Blending modes** ala Photoshop, Illustrator, new compositing branch of Mapnik, cutting edge CSS.

    _Supported by: Carto?..._
    
    In the new branch targeting Mapnik 2.2?

1. **gradients on strokes** - The future come-ith.

    _Supported by: none..._


    1. GRADIENT-TYPE: linear|radial
    
        Note: This should already be supported for classifying a raster image
        (which hasn't been spec'd here).
        
        1a. Type: linear|radial - Default is linear.
        
    2. GRADIENT-ANGLE: value
    3. GRADIENT-ASPECT-RATIO: value
    
            Default: 0 - measured in degrees. for angle. default: left to right with no tilt applied.
            Default: 100% - measured in %. for aspect-ratio. for the circle width, height.
            
    4. GRADIENT-RAMP-TYPE: ratio|absolute
        
            Ratio: lowest data value to highest data value, so the renderer
        needs to be able to do that basic stat. Stops at % in between with color
        chips (color + opacity) associated.
        
            Absolute values: user specified data values with color chips (color + 
            opacity) associated.
                    
    5. GRADIENT-RAMP-STOPS: array of positions either in value or % format.

            Default: 0%, 100%;      - starting at the first vertex, ending at the last vertex

    5. GRADIENT-RAMP-COLORS: array of color and optional opacity values.
    
            Default: #fff, #000;      - white to black at 100% opacity, starting at the first vertex, ending at the last vertex

            ~~Default: #fff 100% 0, #000 100% 100;      - white to black at 100% opacity, starting at the first vertex, ending at the last vertex~~
            
            Problem that opacity is % but position is either number or a percent. Ambiguity.
        
    5. GRADIENT-RAMP-OPACITY: array of color and optional opacity values.

            Default: 100%, 100%;     - at 100% opacity, starting at the first vertex, ending at the last vertex

    6. GRADIENT-ALIGNMENT: within|along|across
    
            Default: within
    
        `within` - Default. The stroke is just treated like a mask onto
        a polygon-based gradient. Default since it's easy to engineer, relatively
        speaking, and similar to polygon implementation.
        
        `along` - End to end: first vertex to last vertex.
        
        `across` - Side to side out from the center of the line to the 
        edges of the stroke width.
        
    7. GRAIDENT-REGISTRATIONS: array of values either ratio|absolute type.
    
            Default: 0%, 100%;      - starting at the lowest value up to the highest value.
    
        think about this the same as `stretch, but where the
        measure is % ratio distance along a line, or absolute page units (px at the
        current zoom scale) across a line.
        
        For absolute values, the design tool would figure this out for you and hard code 
        it? The renderer shouldn't need to know that?
        
    8. GRADIENT-REVERSED: true|false
            
            Default: false.
            
        a nice to have. Often data comes inverted, this is a quick fix around that. 
        More GISy less designy. But makes both happy in the end.


1. **heterogenious geometry type selectors** (point, line, polygon, raster) eg: `geom_type_selector.classname { ... }` or `.classname _geom_type_selector { ... }` (which is less CSS like, but more like the sketches below)  Advanced: tests per feature for feature type, important for GeoJSON etc. **THIS IS THE ONLY SHOW STOPPER.**

    _Supported by: none..._
    
    Per feature evaluation of, versus assuming the layer's first feature is indicative of homogenous feature types.

        .classname[@geom=polygon] { ... }
        .classname[@geom=line] { ... } 
        .classname[@geom=point] { ... }
        
    or?
    
        .classname { [@geom=polygon] { ... } }
        .classname { [@geom=line] { ... } }
        .classname { [@geom=point] { ... } }

        
#Reasonable Mappy CSS 2.0 defaults:

WARNING: if the defaults.mss file is included, the default for layer visibilty becomes ON instead of off.

* **Text -> geom_centroid -> Registration/Text**: 

        .class[@geom=*] { text-size:12px; text-color:#000000; text-face:Arial; text-placement: [point, line, centroid, etc?] }

* **Marker -> Point -> Registration**: 

        .class[@geom=point] { stroke-width:2px; stroke-color:#000000; cap:round; }

* **Stroke -> Line -> Edge**: 

        .class[@geom=line] { stroke-width:1px; stroke-color:#000000; cap:square; }

* **Fill -> Polygon -> Interior**: 

         .class[@geom=polygon] { fill-color:#eeeeee; }

* **Raster Pixels -> Point > Registration**: 

         .class[@geom=raster] { ... }

         Per pixel colored by the RGB channel values. Kinda like a RGB #hex gradient per 0-255 values?
         CSS tk tk tk.


#File structure

* **MSS** for the Mappy CSS styling definition. See above.
* **MML** for the datasources and their layering. See below.

    _NOTE 1: Mappy CSS MML spec can embed the MSS part rather than linking to external file (the default)._

    _NOTE 2: Mappy CSS MML spec can be implemented in either [XML](https://github.com/mapnik/Cascadenik/wiki/Examples) (Cascadenik) or [JSON]() (Carto) formats._
    
    **Overview**
    
        Map object with several properties including:
            srs string --- for the projection, usually web mercator but can be any proj4 string.
            background-color: #rrggbb etc.
            background-image (should be background-color: url(...); ?)
            buffer: 20px;
            base:path/subdir/
            font-directory: path/subdir
        Stylesheet usually as an external (local) include. Inline MSS is allowed.
            See above for spec.
        Layer definitions with properties:     
            "class": "name1",
            "id": "value2",
            "name": "name3",
            "srs": "+proj=latlong +ellps=WGS84 +datum=WGS84 +no_defs",
            "Datasource": {
                "file": "world_borders",
                "type": "shape"
            }
    
    **XML:** Used by Cascadenik.
    
        <?xml version="1.0" encoding="utf-8"?>
        <Map srs="+proj=latlong +ellps=WGS84 +datum=WGS84 +no_defs">
            <Stylesheet><![CDATA[
                Map
                {
                    map-bgcolor: #69f;
                }
        
                Layer
                {
                    line-width: 1;
                    line-color: #696;
                    polygon-fill: #6f9;
                }
            ]]></Stylesheet>
            <Layer srs="+proj=latlong +ellps=WGS84 +datum=WGS84 +no_defs">
                <Datasource>
                    <Parameter name="type">shape</Parameter>
                    <Parameter name="file">world_borders</Parameter>
                </Datasource>
            </Layer>
        </Map>
    
    Use XML when any of these are important:
    
        1. Allows multi-line DB queries in MML where JSON does not allow unescaped line breaks. This creates more legible code.
        2. Can add inline comments using standard XML <!-- comment --> syntax. This allows notes to now self and future self and for collaborators.
        3. Can temporarily disable a block of code, also using <!-- disabled --> syntax.
        4. DataSourcesConfig, see below.
    
    **JSON:** Used by Carto.
    
        {
            "srs": "+proj=merc +a=6378137 +b=6378137 +lat_ts=0.0 +lon_0=0.0 +x_0=0.0 +y_0=0.0 +k=1.0 +units=m +nadgrids=@null +wktext +no_defs +over",
            "Stylesheet": [{"id":"style.mss","data":"Map {\n  background-color: #fff;\n}\n\n#world {\n  line-color: #ccc;\n  line-width: 0.5;\n  polygon-fill: #eee;\n}"}],
            "Layer": [{
                "id": "world",
                "name": "world",
                "srs": "+proj=latlong +ellps=WGS84 +datum=WGS84 +no_defs",
                "Datasource": {
                    "file": "world_borders",
                    "type": "shape"
                }
            }]
        }
    
    _NOTE 3: Data source definition can be inline or an import in the MML. Cascadnik now. Easy in XML format, harder in JSON?_

##For the MML layer setup:

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



#&etc

* What promises are we making, what is arbitrary up to the user?
* Set reasonable expectations


#Full details:

[Read more »](https://github.com/nvkelso/carto-css-map-styling/blob/master/full_details.md)

Syntax details, implementation recommendations, avoids, demos, open questions, housekeeping, quotes, display:none, carto 1.x nitpicks, more questions.


#Sketches

**Basic:**

![MappyCSS_basic](https://github.com/nvkelso/carto-css-map-styling/raw/master/images/carto_simple.png)

**Medium:**

![MappyCSS_medium](https://github.com/nvkelso/carto-css-map-styling/raw/master/images/carto_medium.png)

**Complex:**

![MappyCSS_complex](https://github.com/nvkelso/carto-css-map-styling/raw/master/images/carto_complex.png)

**Extended examples:**

WARNING: the `.layer interior {marker-image: url() }` example below is incorrect. It should show markers just inside the polygon, with none spilling outside. More like a mask or a `polygon-fill: url()`? Or is there a slight difference?

![MappyCSS_Extended](https://github.com/nvkelso/carto-css-map-styling/raw/master/images/carto_extended.png)