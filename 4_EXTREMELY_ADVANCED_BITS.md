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

1. **building symbolizer**

    _Supported by: Carto._
    
        building-fill: #ffffff;
        building-opacity: 1.0;
        building-height: 0;


        