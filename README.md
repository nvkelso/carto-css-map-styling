CSS Inspired Map Styling
=====================

_Notes, sketches, and frameworks inspired by October 2011 map styling workshop held in SF_

Question: Core bits should be universal. Extras should be wrapped in a Carto 2.0 designator?

===

#Goals:

1. CSS-like map design language. Syntax extends the foundation laid by [Cascadenik](https://github.com/mapnik/Cascadenik/wiki/Dictionary) and [Carto](http://mapbox.com/carto/) ([overview](https://github.com/mapbox/carto)) and the power of Less.js. [GeoServer](http://docs.geoserver.org/stable/en/user/community/css/index.html) is also joining the mix.
2. Better support for reference-style maps by formalizing the use of @variables for color swatches, and mixins for graphic styles and text character styles.
3. ~~¿Add support for thematic data cartography with data statistics like MIN, MAX, MEAN and use of those keywords in high-level filters that are under a dozen lines of code? Allow for both loose and hard connections between data and symbology.~~ [GeoServer has 3 great simple functions for much of this now in SLD](http://blog.opengeo.org/2012/08/02/thematic-map-creation-with-sld-is-now-much-easier/) - [Direct link to reference docs](http://docs.geoserver.org/latest/en/user/filter/function_reference.html#transformation-functions)


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
        Note: Lines can end either with or without semicolons at block ends, per CSS spec. Cascadenik (2.3.0)
    
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


#Mappy CSS bits

1. [Core bits (Mappy CSS 1.0)](https://github.com/nvkelso/carto-css-map-styling/blob/master/1_CORE_BITS.md)

    1. **Basic CSS syntax: stroke, fill, marker, raster** - marker was point, also talked about anchor
    1. **Filters** for feature attributes. Exactly equal, not equal, less than, greater than, less than or equal to, greater than or equal to based on class or id selectors.
    1. **Point geoms have an edge** available for stroking.
    1. **Basic CSS syntax: text labels** - New "label once" `text-name:[fieldname]` where the [fieldname] grabs attributes per feature on render. This is extended either with `::` layer attachements or `keyword-attachments` to gain multiple text labels, shield symbolizers, etc.
    1. **Markers instead of points**: or `anchor`? Marker is more widely supported in Gmaps world. Mapnik's `point` should be hidden in this generic spec. _JL, NVK and NVK on 25 May and 29 May._
    1. **For backwards compatability: points are are synonym for markers** Point would have a depreciation, new implementations might optionally support. _MM and NVK 29 May._
    1. **Layers, classes, ids, and layer metadata** - Layers are defined in the MML file and can include class="class1 class2 class3" or id="id1". Layers could have a name. Layers could have metadata.
    1. **Zoom and scale denominator**
    1. **!important** - ensure your setting takes precedence in the CSS parser over filters, appearance styling that would otherwise be more important in order-of-operations.       

2. [Basic bits (Mappy CSS 2.0)](https://github.com/nvkelso/carto-css-map-styling/blob/master/2_BASIC_BITS.md)

    1. **Advanced CSS syntax for colors** - RGBA, CYMK, HSV, etc other color spaces.
    1. **@variables** for color swatches, this is preprocessor to actual result. _Moved 23 May 2012 per TMCW, split off graphic styles and text character styles 29 May_
    1. **geometry type selectors** (point, line, polygon, raster) eg: ~~`geom_type_selector.classname { ... }`~~ or ~~`.classname geom_type_selector { ... }`~~ (which is less CSS like, but more like the sketches below)  Advanced: only test the first feature's type in a data store, assumes homogenious data in a single data store. **THIS IS THE ONLY SHOW STOPPER, might be relatively easy to implement in Cascadenik?** 
    1. **Advanced CSS syntax: stroke background color and attached markers**
    1. **display:none** - like `!important`, but for not showing stuff, regardless of other rules. Speeds up stylesheets by ignoring unused elements in renderer specific export.
    1. **FontSets**: very important for multilingual characters and UTF-8 labels, font fallbacks
    1. **default.css** for geometry type selectors results in layers auto display TRUE instead of FALSE. Override with `display:none;`
    1. **Advanced CSS syntax: shields**
    1. **well-known-symbols marker images** - where image could be png, gif, jpg, svg, etc?. Ideally vector so sizing can be applied and still looks good.
    1. **Advanced Mappy CSS: raster colorizer**

3. [Advanced bits (Mappy CSS 3.0)](https://github.com/nvkelso/carto-css-map-styling/blob/master/3_ADVANCED_BITS.md)

    1. **variable expresions** modifying color swatches, stroke-widths, and @variables, etc.
    1. **RegEx filters** 
    1. **layer-based style attachments** `::` are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. Useful for applying multiple labels, shields, or graphic styles. **OR should we move away to a procedure versus declarative?** See also, "repeated properties" below.
    1. **feature-based style instances** `/` similar to nested styles, but within the same original layer. 
    1. **keyword-based style attachments** `.roads name { text-size:12px; text-color:#000; }` and `.roads ref { shield... }` are a repeated version of the exact selection, but with a different appearance styling on a new virtual layer. Useful for applying multiple labels, shields, or graphic styles.
    1. **nesting combinator filters** 
    1. **repeated properties** - Like anonymous layer-based style attachements (`::`), see above. 
    1. **nested styles** - from Less.js and Carto.
    1. **mixins** for graphic styles, and text character styles. _Split off per TMCW, NVK, and MM conversation on 29 May_

4. [Extremely advanced bits (Mappy CSS 4.0)](https://github.com/nvkelso/carto-css-map-styling/blob/master/4_EXTREMELY_ADVANCED_BITS.md)

    1. **data variables/expansion** aka string expressions for keys other than text-name. 
    1. **functional expressions** evaluation of variable expressions with a predefined function.
    1. **Selectors** for object _advanced_ **geometry components**: inner outer rings, vertex index and first, last.
    1. **Dependant attachments** `&&` depend on the previous bits being rendered in that selection. Useful for text labels to require the anchor symbolization being placed. eg: point&&text.
    1. **Shorthand style properties** 
    1. **Special rendering targets w/r/t attachements (placement)**: interior, edge, registration, vertexes, parts there-of.
    1. **Rendering/compositing targets** are implied but can be explicate or overridden: fill-, stroke-, anchor-, and data-. _Note: color:#hex is implied as fill-color:#hex._
    1. **Blending modes** ala Photoshop, Illustrator, new compositing branch of Mapnik, cutting edge CSS.
    1. **gradients on strokes** - The future come-ith.
    1. **heterogenious geometry type selectors** (point, line, polygon, raster) eg: `geom_type_selector.classname { ... }` or `.classname _geom_type_selector { ... }` (which is less CSS like, but more like the sketches below)  Advanced: tests per feature for feature type, important for GeoJSON etc. **THIS IS THE ONLY SHOW STOPPER.**
    1. **building symbolizer**


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