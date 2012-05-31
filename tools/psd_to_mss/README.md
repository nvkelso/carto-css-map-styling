#PSD file

A layered Photoshop file with 4 zooms in three places (SF, ? and Tokyo). 

![PSD file preview image](https://github.com/nvkelso/carto-css-map-styling/raw/master/tools/psd_to_mss/MAP_colors.png)

#Layers

The following layers are included per map feature class:

* Paths
* Railways
* Minor Roads inline
* Major Roads inline
* Motorways (highways) inline
* Contours (not used often?)
* Minor Roads outline/casing
* Major Roads outline/casing
* Motorways (highways) outline/casing
* Buildings
* Civic
* Nature
* Water
* Ground

These conform to common OpenStreetMap and other geodata feature categories.

To change the **colors**, double click on the solid color chip in the layers panel. These are adjustment layers with masks. 

Changing the color values in the resulting dialog will affect the 4 zooms for the 3 places. 

Once you're happy with the design, run the script from the File > Scripts > Other script... menu command and the .mss file next to the .psd will update.

#Photoshop Script

Writen in JSX, Adobe's JavaScript based Extend Script which has hooks for the Photoshop API.

Script tk tk tk.

#Sample MSS output

tk tk tk

#MML

tk tk tk