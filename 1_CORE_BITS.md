#Core bits (Mappy CSS 1.0):

_All these must be supported to meet 1.0 compliance_

1. **Basic CSS syntax: stroke, fill, marker** - marker was point, also talked about anchor

    Properties consist of name/value pairs separated by colons (:).
             
    They are grouped per selector by { ... }.
    
    To be visible:
        
        stroke needs **both** stroke-width and stroke-color defined.
        fill needs **only** fill defined.
        markers need either a) **both** stroke-width and stroke-color or b) **only** marker-image set.
        rasters are visible by default?

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

    **raster**

    `raster-opacity: 1.0;`  - ratio range between 0.0 and 1.0. Default is 1.0
    
     NOTE: Colorizing a raster outside of it's default RGB channel values is an advanced feature, see below.
     
     See: [Mapnik's RasterSymbolizer](https://github.com/mapnik/mapnik/wiki/RasterSymbolizer).
    
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