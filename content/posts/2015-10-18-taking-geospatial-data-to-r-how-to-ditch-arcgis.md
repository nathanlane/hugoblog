--- author: Nathaniel categories: - Tutorials date:
"2015-10-18T00:00:00Z" meta: null published: true status: publish tags:
- Programming - R title: Geospatial Data in R type: post ---

### Taking Geospatial Data to R (& how to ditch ArcGIS)

For R users it's very straightforward to ditch ArcGIS (for most tasks)
in favor of doing everything through an R script. There are many reasons
to do this:

-   First, if you can do GIS work on your Linux system or Mac without
    having to run things through a lame emulator.
-   Second, you can cut yourself loose from dealing with the
    clunky ArcGIS licensing system.
-   Third, the GIS/R user community is pretty dang big, with a growing
    collection of resources and libraries.
-   Fourth, you can escape the mysterious, temperamental nature of
    ArcGIS and have full control over data outputs. Most quant folks I
    know try to minimize their time processing things on ArcGIS,
    outsourcing data as soon as possible to Stata or R. By working
    entirely in R lets you skip the murky black box of ArcGIS.

All this means there are many reasons to dump ArcGIS--something I should
have done before my pal called me out on twitter.

![Damnnnnnn](%7B%7B%20site.baseurl%20%7D%7D/assets/calledout.gif){width="500px"}

Here are just some aspects on working with raster and vector data in R
for those wanting to migrate from ArcGIS. Plus some tools that helped me
with scripts to manipulate "large" data sets--say a couple gigs of
raster data, etc..

To get started working with GIS data, a couple of R packages cover most
ArcGIS tasks. I'd install `sp, Raster, rgeostats, maptools,`and `rgdal`
packages, which cover a surprising number of bases (Also: [*this is
helpful to note if you're a Linux
user*](http://robinlovelace.net/r/2013/11/26/installing-rgdal-on-ubuntu.html "rgdal in linux")).

**Starting with Raster Data**

Let's consider working with raster files first. You can think of loading
GIS-based data just like you would any object, such as .csv file.
Specifically, `library( raster )` is enough to load raster-based images
directly into R.

{{&lt; highlight R &gt;}} library(raster) weatherfile &lt;-
"/home/user/population\_raster.tif" rasterweather &lt;- raster(
weatherfile ) popfile &lt;- "/home/user/population\_raster.tif"
rasterpop &lt;- raster( popfile )  {{&lt; / highlight &gt;}}

Crop to the size of Europe shapefile; the extent() function helps with
this.

{{&lt; highlight R &gt;}} rasterpop &lt;- crop( rasterpopfile , extent(
rasterweather ) ) {{&lt; / highlight &gt;}}

Above, I used the `extext()` function to automatically use the
dimensions of another file in our memory. Since we're in R, we can
easily save an extent to an object and re use it.

One thing that ArcGIS has over R, however, is that it is based
thoroughly on a graphical user interface and allows you to *see*
multiple layers seamlessly. Nonetheless, the `raster` package (as well
as staples such as `GGPLOT2`) allow you to eyeball and visualize GIS
tasks. To plot an individual R layer, `plot(rasterpopfile):`

![](%7B%7B%20site.baseurl%20%7D%7D/assets/Rplot04.jpg){.alignnone
width="521" height="614"}

Similarly, it is fairly easy to plot multiple layers. Of course there
are all sorts of wacky things you can do to visualize GIS objects, but
this is pretty much what you need to graphically verify nothing wacky is
going on. Hence, manipulating GIS data programmatically in R doesn't
mean flying blind.

{{&lt; highlight R &gt;}} \# Superimpose a rasters and vectors by using
"add=TRUE" plot(rasterpop) plot(countryshape, add=TRUE) {{&lt; /
highlight &gt;}}

Moreover, it's pretty straightforward to perform common manipulations of
raster data, such as changing the changing the CRS. One can change the
projection by using the `reprojectRaster()` function followed by the
`resample()` function.

{{&lt; highlight R &gt;}} \# Say we have another raster with a different
coordinate system. \# We can save this coordinate system using the
proj4string() function. target\_raster &lt;- raster(
"/home/user/target\_raster.tif" )  target\_crs &lt;- proj4string(
target\_raster ) \# Reproject using the projectRaster() function and the
target\_crs. re\_rasterpopfile &lt;- projectRaster( rasterpopfile, crs =
target\_crs , method = "bilinear") \# Reproject using the
projectRaster() function and the target\_crs. re\_rasterpopfile &lt;-
resample( re\_rasterpopfile, target\_raster , method = "bilinear")
{{&lt; / highlight &gt;}}

The first manipulation changes coordinate system of a current raster,
changing it to match the target coordinate system; the second function
is necessary so that the grid of the starting raster matches the grid of
the target raster.

Alternatively, you can easily specify nearest neighbor methods if you
are working with categorical raster data. Now, both the target and
starting raster layers should have the same resolution.

While `resample()` allows us to align the grids of the two files, if the
target raster is much more coarse--at a much lower resolution--we should
use the `aggregate()` function, which lets us aggregate the cells of the
fine raster to the larger raster; `disaggregate()` does just the
opposite.

**Shapefiles & Vector Manipulations**

The `rgdal` package is fantastic for reading vectorized data and
shapefiles into R. The package's `readOGR()` function is fantastic for
loading shapefiles directly into R.

Besides liberating yourself from ArcGIS wackiness, you can manipulate
shapefile objects similar to the way you manipulate dataframes. This is
because points, lines, and polygon shapes can be recognized as
special `SpatialPointsDataFrames` , `SpatialĹinesDataFrames`,
or `SpatialPolygonsDataFrames` classes. Each type, or *class*, of layer
contains an attributes table. An advantage of this is that you can use
these attributes to select parts of the shapefile as you would select a
subset of a dataset.

The `rgdal` assists in loading vector-based GIS data into R, and
comfortably handles ESRI shapefiles. The libary's `readOGR()` function
is enough to get started. Here we load a file a standard shapefile of
country polygons and create a shapefile layer for Sweden:

{{&lt; highlight R &gt;}} library( rgdal ) \# Read in with readOGR since
it preserves CRS projections. globeshape &lt;- readOGR( dsn=
"/home/user/countries.shp" , layer = "countries" ) plot( globeshape )
{{&lt; / highlight &gt;}}
![](%7B%7B%20site.baseurl%20%7D%7D/assets/globen.jpg){width="450px"}
{{&lt; highlight R &gt;}} \# Subset the European files. swedeshape &lt;-
globeshape\[ globeshape\$COUNTRY == "Sweden", \] plot( swedeshape )
{{&lt; / highlight &gt;}}
![](%7B%7B%20site.baseurl%20%7D%7D/assets/sweden.jpg){width="300px"}

**Ingredients to Manipulating GIS Data En Masse**

Sure R is a free, programmatic solution to working with spatial data.
Sure it also gives you transparent control over transforming spatial
data. However, a big benefit of using R is being able to manipulate
giant chunks of geographic data--and to do so in a way that is
reproducible via a script.

Work with *Brick* and *Stack* Objects

*RasterBrick*s and *RasterStack*s are your friend when working with big
datasets. For instance, weather data often comes in the compact NetCDF
format, where a common NetCDF file may contain hundreds of layers of
daily weather data, each dimension representing geocoded raster data for
a single day. RasterBricks are useful in this case, and store a
multi-layered raster file in a single object that can be manipulated.

With the `ncdf4` library, you can load a 365 layer NetCDF raster file
directly into R. Together with the `brick()` function (from the `raster`
package), you can work with large, multi-dimensional raster files as if
they were one single raster file. In other words, you can apply raster
manipulations to a block of raster data directly by defining it as a
brick (or `stack()` as well, though raster bricks and raster stacks are
treated a bit differently in memory). This is handy when you want to
resize, crop, or transpose an entire set of raster layers all at once
instead of looping through each individual raster layer.

For instance, using the `ncdf4` library I can load a giant NetCDF file
directly; together with the `raster` library's `brick()` function, you
get load an entire NetCDF file and recognize it as a RasterBrick with
minimal fuss

{{&lt; highlight R &gt;}} library( ncdf4 ) library( raster ) netdata
&lt;- nc\_open( "/home/user/rain.2015.nc" ) netdata &lt;- get( netdata )
dailyweather &lt;- brick( netdata ) {{&lt; / highlight &gt;}}

And we chop hundreds of layers to an appropriate size in one go,

{{&lt; highlight R &gt;}} cropsize &lt;- extent( eushape )
cropped\_dailyweather &lt;- crop( dailyweather , crop\_size ) {{&lt; /
highlight &gt;}}

We can also multiply a multi-dimensional brick object by a single raster
layer, effectively multiplying a hundred rasters contained in the brick
with the singleton layer. I find this extremely useful for calculation
population-based weights.

{{&lt; highlight R &gt;}} weatherXpop &lt;- overlay( crop\_dailyweather
, population\_raster ) {{&lt; / highlight &gt;}}

**Parallelization, foreach/plyr, apply, and data.table**

If you're trying to programmatically manipulate many files at once, you
can speed things up tremendously with a a number of R libraries and
features, especially those that support parallelization.

For instance, the omnipresent `plyr` package and the handy `foreach`
package allow you to parallalize time consuming manipulations of GIS
data, especially GIS tasks that you would normally loop, such as
computing repetitive calculations of zonal statistics and such. I'm not
sure what geoprocessing tools are supported by ArcGIS' own parallel
processing environment, but R certainly allows you to flexibly use the
power of your multi-core processors for intensive tasks.

Moreover, if you are manipulating GIS data and assembling the results
into a dataset--e.g. \`growing' a panel dataset of annual mean
temperature readings across municipalities--the `data.table` package can
be very helpful in speeding things along and reducing processes that are
usually quite inefficient in R.
