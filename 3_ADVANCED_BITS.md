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