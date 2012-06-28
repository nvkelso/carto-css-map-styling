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
    
1. **display:none** - like `!important`, but for not showing stuff, regardless of other rules. Speeds up stylesheets by ignoring unused elements in renderer specific export.

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

1. **Advanced Mappy CSS: raster colorizer**

    Seems like this should just be gradient stretches like described in **gradients on strokes** section far below.

    See [Mapnik's RasterColorizer](https://github.com/mapnik/mapnik/wiki/RasterColorizer). 
    
    3. RASTER-RAMP-MODE: linear|discrete - like in Mapnik.

    4. RASTER-RAMP-TYPE: ratio (stretch based on percent from MIN and MAX)|absolute (stretch based on real values, default in Mapnik)
        
            Ratio: lowest data value to highest data value, so the renderer
        needs to be able to do that basic stat. Stops at % in between with color
        chips (color + opacity) associated.
        
            Absolute values: user specified data values with color chips (color + 
            opacity) associated.
                    
    5. RASTER-RAMP-STOPS: array of positions either in value (Mapnik default) or % format.

            Default: 0%, 100%;      - starting at the first vertex, ending at the last vertex

    5. RASTER-RAMP-COLORS: array of color and optional opacity values.
    
            Default: #fff, #000;      - white to black at 100% opacity, starting at the first vertex, ending at the last vertex

            ~~Default: #fff 100% 0, #000 100% 100;      - white to black at 100% opacity, starting at the first vertex, ending at the last vertex~~
            
            Problem that opacity is % but position is either number or a percent. Ambiguity.
        
    5. RASTER-RAMP-OPACITY: array of color and optional opacity values.

            Default: 100%, 100%;     - at 100% opacity, starting at the first vertex, ending at the last vertex
                
    8. RASTER-REVERSED: true|false
            
            Default: false.
            
        a nice to have. Often data comes inverted, this is a quick fix around that. 
        More GISy less designy. But makes both happy in the end.