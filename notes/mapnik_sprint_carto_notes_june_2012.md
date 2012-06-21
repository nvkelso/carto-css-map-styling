June 17, 2012 - Google+ hangout to share visions, positions, and plans on css styling for maps

Attending:

david winslow, javier de la torre, tom macwright, artem pavlenko, nate kelso, lars ahlzen, aj ashton, dane springmeyer, josh livni

Idea stream captured:

* need small subset - simpler/smaller than all existing implementations, to start (jlivni) The "core" bits in MappyCSS here seem to satisfy that (nvkelso).
* soften transition and encourage sharing of styles between client/server, vector/raster (all)
* commenting on details on github changeset, nvkelso should split core off from the others, resubmit the documents so we can comment on new files (springmeyer, nvkelso) 
* versioning, semantics, critical (jatorre)
* note at top of file, what version and user agent (nvkelso)
* create a validator - flags functionality that is not standardized (dwins)
* is the language strict or non-strict (in terms of a validator)? (tmcw)
* need to focus on usefulness (and iteration) on breakouts of core vs non-core at https://github.com/nvkelso/carto-css-map-styling
* eval() for JS implementations? (jatorre) - nvk add to notes.
* security concerns around functionality expressions - don't try to standardize a language that allows the evaluation of another language (e.g. MapCSS eval) - as you'll be standardizing a second language (tmcw)
* css does not work well when you try to mix in a functional language - statelessnes vs functional expressions changing value at any time (tmcw)
* ^^ but isn't javascript (the second language here) standardized? (jatorre)
* ^^ supporting will help ability to show animations (simply by refreshing the page with new data) (jatorre)
* is animation part of cartography or an issue of the interaction with the cartography? (e.g. hover/click should be outside the language - like how tilemill does it currently) (jlivni)
* hover/click might be/sound very cool - but what about mobile clients - does it make sense into the future? (jlivni)
* part of the styling is how the cartography has motion - how things grow into place rather than just be rendered in place (motion vs static visual) (nvkelso)
* what is the plan at devseed in terms of making mapnik more dynamic (nvkelso)?
* ^^ general plan is: test out mapnik on more clients (like iOS), and support more dynamic expressions in symbolizers (dane)
* ^^ data variables/expansion aka `data-driven symbolizers` in Mapik speak (jatorre, nvkelso, dane)
* FPS (frames per second) for motion, OpenGL implementations? (jatorre) - nvk add to notes.
* thematic, hexagons, grids, animations, time-lapse, point clustering - what parts in or out? (jtorre) - some NVK add.
* ^^ probably all out for now (jlivni/tmcw)
* (nvkelso takes off....)
* geotrellis - applying domain specific framework for raster transformations - https://github.com/azavea/geotrellis (tmcw)
* carto.js needs to be able to throw warnings/show deprecations, rather than just errors (tmcw)
* there also should be a way to have extensions that are not interpreted by default (jtorre)
* in geoserver `-gt` prefix used to prefix (postfix?, nvkelso) extentions (dwins)
* **meeting ended**
* what is the mapnik development/release plan - how can we help (jatorre)
* will blog at mapnik.org about plan, to better enable points of help (dane)
* hey, if we agree on small/common subset, no need for versioning! (artem)
* should invest some time and describe it in a grammar (e.g. http://en.wikipedia.org/wiki/BNF or? http://en.wikipedia.org/wiki/Backus%E2%80%93Naur_Form) (artem)
* avoid pitfall of people adding things to an unstructured doc and the spec not reflecting it