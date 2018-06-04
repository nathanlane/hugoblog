--- author: Nathaniel categories: - Tutorial date:
"2016-01-02T00:00:00Z" published: true title: 'Tutorial: Big GIS Data in
R & Functional Programming' type: post ---
[![](%7B%7B%20site.baseurl%20%7D%7D/assets/thermalmap.jpg){width="600px"}](%7B%7B%20site.baseurl%20%7D%7D/2016/01/02/gisfunctional.html)\

Caption: "Thermal Map of North America. Delineating the Isothermal
Zodiac, the Isothermal Axis of Identity, and its expansions up and down
the 'Plateau' " From William Gilpin’s "Mission of the North American
People (1873)." Via [the "Making Maps: DIY Cartography"
blog.](http://makingmaps.net/2014/09/30/gilpins-map-of-the-isothermal-zodiac-and-axis-of-intensity-round-the-world-calcareous-plain-maritime-selvage-etc-etc-maps-1873/)

### The Question

How do I take over 100 NetCDF files, each containing thousands of layers
of hourly raster data, and translate into dataset?

I'll flesh out the problem more,

-   **NetCDF files.** We have over 100 NetCDF datasets: *1850weather.nc,
    1851weather.nc , 1851weather , ... , 1971weather, ... etc.*.

-   **Raster layers.** Each NetCDF is a stack of hourly raster layers:
    1850weather.nc: *layer.hour1 , layer.hour2 , ... , layer.hour2000,
    ... etc*.

-   **Shapefiles.** We also have a shapefile countaining country
    boundaries.

-   **Our goal.** Extract a giant panel dataset, containing average
    hourly weather data for each country.

In other words, we need a way to cycle through each NetCDF file and for
every layer within a file, retrieve the average raster stats
corresponding to each country in our shapefile. This adds up.

This problem is interesting because normal approaches to processing
piles of raster data (using nested loops) takes forever. A more
"functional" approach to the problem can be amazingly more efficient.

In this post I consider the ways in which we can tackle this problem
(Part I.) and then motive a solution (Part 2.) for comfortably
extracting statistics from gigs of GIS weather data.

### Part 1. - A comparison of Two Approaches.

Consider two approaches to our problem, a conventional loop-based
approach and an optimized approach.

**Conventional Dead Ends with Loops.**

Most of us would attack the problem using nexted loops. The first loop
reads and prepares the NetCDF file; a second deals with the raster
layers within each file.

{{&lt; highlight R &gt;}} for(i in 1:number\_of\_netcdffiles) { \# Load
file\[i\] \# Do preliminary stuff to file\[i\] for(l in
1:number\_of\_rasterlayers) { \# Extract geographic statistics from
layer\[l\] \# Add statistics from layer\[l\] to a data set } \# Save
giant file for each NetCDF file } {{&lt; / highlight &gt;}}

We're tempted to iterate over the raster layers ( `layer[l]` ) and use
standard R GIS libraries to, say, extract everage values from the
rasters over a country boundary shapefile (e.g. hourly weather readings
over the borders of Finland). Above all, we're tempted to "harvest" the
geographic statistics, taking the values we extract and collating them
in a giant file.

Straight up, this is a bad idea.

Loops can be abysmal for these tasks. When iterating through objects
with a `for()` loop, we're actually calling many tiny functions ...
*over and over again*. Not only is the `for()` a function, but so is the
":", and so are the brackets "\[ \]".

To make matters worse, when we manipulate a vector or data.frame with a
for-loop, we're also making many internal copies of our objects.
Unbeknownst to us, mundane data transormations can quickly fill out
memory with repeated copies.

Moreover, embedded in our first attempt is the agony loop-based
"data-harvesting." Unless we're carefull, using a loop to incrementally
"grow" a dataset will bring your computer to its knees.

**Consider an Alternative Approach.**

Instead of loops, the following template - and specifically the full
program in Part 2 - considers a solution more suited to R.

By combining functional style with the use of the `raster()` library, we
eliminate the need for nested loops and boil the problem down to a
streamlined "apply+function" structure.

{{&lt; highlight R &gt;}} generate\_statistics\_from\_netcdf &lt;-
function( input\_netcdffile ) { \# Turn input\_netcdffile into a "raster
brick" ... \# Get statistics from "raster brick" ... \# Save statistics
for input\_netcdffile ... } lapply( list\_of\_netcdffiles ,
generate\_statistics\_from\_netcdffile) {{&lt; / highlight &gt;}}

Instead of using a for-loop to iterate over NetCDF weather files, we
take a list of files `list_of_netcdffiles` and "apply" a big function,
`generate_statistics_from_netcdf`.

We eliminate the entire inner loop with the help of `RasterBrick`
manipulations. That is, instead of looping over the individual raster
layers within a NetCDF file, we transform the NetCDF into a
`RasterBrick` object and manipulate the collection of layers as a single
object.

Appealing to `RasterBrick` instead of cycling through individual layers
feels a lot of like the practice of "vectorization", where instead of
iteraring over individual members of a vector one-by-one, we work
directly with the vector. Stylistically, this is a common line of attack
for writing more efficient code.

### Part 2. The Program.

Let's expand the alternative template above into a full program.

The first part of the program defines our functions: the main
` generate_datatable_from_rasterbricks` function and a set of small
sub-functions used within it.

The `generate_datatable_from_rasterbricks` functon eats a raw NetCDF
file, and using a a team of smaller functions, reads it as a
RasterBrick, aligns it with our country shapefile, extracts the
country-level weather statistcs, then saves the output file.

The second part contains the core code. Here we define a list of NetCDF
raster files (`raster_file_list`) and the country shapefile
(`countryshape`) used in our calculations. An \*apply function feeds
NetCDF files through the `generate_datatable_from_rasterbricks`
function.

Instead of using `lapply` I use `mclapply`: the latter is a
multiprocessor version of list-apply provided by the `parallel()`
package. Conveniently, `mclapply` utilizes the power of out multi-core
processor (if we have one).

The third part of the program takes our saved files and assembles them
into a giant file via the amazingly useful functions provifed by the
`data.table()`package. With `lapply()`, our list of .csv files is opened
with the speedy `fread()` function. A list big list of opened .csv files
is then fed through `rbindlist()`, which combines them into a single
massive data.table.

{{&lt; highlight R &gt;}} \# ---- X. Header. \# The libraries we use.
library(rgdal) library(raster) library(ncdf4) library(RNetCDF)
library(sp) library(parallel) library(magrittr) library(data.table) \#
Detect cores automatically, I usually free one up. cores &lt;-
detectCores() - 1 \# Define your file paths here. weatherraster\_path
&lt;- "/path/to/weatherrasterfiles" countryshape\_path &lt;-
"/path/to/countryshapefile" output\_path &lt;- "/path/to/outputfiles" \#
---- 1. Define Functions. \# -- 1.A. Define Small Subfunctions. \# Small
function 1) Reads filename & explicitly opens it as a NetCDF file.
open\_netcdf\_as\_rasterbrick &lt;- function( ncdf\_filename\_input ) {
ncdf\_filename\_input %&gt;% file.path( weatherraster\_path , . ) %&gt;%
nc\_open( . ) %&gt;% \# Open path as NetCDF file. ncvar\_get( . ) %&gt;%
\# Get NetCDF file. \# Transform NetCDF into raster brick. \# NOTE: Your
dimensions and CRS will differ, \# so these should be replaced. brick( .
,ymn = -0, ymx = 360, xmn = -90, xmx = 90, crs = "the string for your
CRS" ) %&gt;% return( . ) } \# Small function 2) Transforms the raster
brick to our country shapefile. match\_rainbrick\_to\_countryshape &lt;-
function( brick\_input ) { \# NOTE: Depending on your setting and the
nature of the shapefile \# and NetCDF raster files you're using, you may
have to do many more \# manipulations to make sure the raster layers
align with the shapefile. brick\_input %&gt;% \# Reproject raster brick
to the shapefile's coordinate system. projectRaster( . , crs =
proj4string( countryshape ), method = "ngb" ) %&gt;% \# Crop to match
the size of my country shapefile. raster::crop( . , extent(
country\_shapefile ) ) %&gt;% return( . ) } \# Small function 3) Extract
data from a raster brick. generate\_data\_from\_rasterandshape &lt;-
function( brick\_input ) { brick\_input %&gt;% \# Take means according
to the countryshape. \# Make sure df = TRUE , so that output is a
dataframe. raster::extract( . , countryshape , f=TRUE, fun = mean, na.rm
= TRUE ) return( . ) } \# Small function 4) Grab 4-digit year from input
filename. grab\_year\_from\_inputfile &lt;- function(
ncdf\_filename\_input ) { ncdf\_filename\_input %&gt;%
regexpr("\[0-9\]+", . ) %&gt;% \# Match 4-digit year. regmatches(
ncdf\_filename\_input , . ) %&gt;% \# Get matched REGEX from input
string. return( . ) } \# --- 1.B. Define "BIG" Function That Extracts
Dataset From a NetCDF File. generate\_datatable\_from\_rasterbricks
&lt;- function( ncdf\_filename\_input ) { \# Note: the only argument is
a NetCDF filename. \# Start with file argument and process with the
sub-functions above. ncdf\_filename\_input %&gt;%
open\_netcdf\_as\_rasterbrick( . ) %&gt;%
match\_rainbrick\_to\_countryshape( . ) %&gt;%
generate\_data\_from\_rasterandshape( . ) -&gt;
country\_means\_dataframe \# Go back to the file input name, create
automatic names, and save. ncdf\_filename\_input %&gt;%
grab\_year\_from\_inputfile( ) %&gt;% write.csv(
country\_means\_dataframe , file = file.path( . , output\_path ) ) } \#
---- 2. Main Code: Setup Environment to Run Big Function. \# Start with
your name of the country shapefile we're referencing.
"country\_shapefile\_name.shp" %&gt;% file.path( countryshape\_path , )
%&gt;% readODG( den = . , layer = "countries" ) -&gt; countryshape \#
Generate list of NetCDF files automatically from our directory. \# Match
all files ending in ".nc" raster\_file\_list &lt;- list.files( path =
weatherraster\_path , pattern = ".nc" , all.files = FALSE , full.names =
FALSE ) \# Run our big function on the list of NetCDF files. mclapply(
raster\_file\_list , generate\_datatable\_from\_rasterbricks ) \# ----
3. Assemble .CSV Files using Data.Table and Lapply. \# Fetch all files
ending in .CSV in out output path. csv\_file\_list &lt;- list.files(
path = output\_path , pattern = ".csv", all.files = FALSE, full.names =
TRUE, recursive = FALSE ) \# Take the list of saved files & "fast read"
them into R. lapply( csv\_file\_list , fread , sep = "," ) %&gt;% \#
Transform the list of read files into a data.table: rbindlist( . ) -&gt;
big\_datatable \# Note: Before reassembling the data, or after, you may
want \# to manipulate the data so that it is in a more usable format. \#
Note: You may want to setkeys() for data.table here. \# Save the big
file. write.csv( big\_datatable, file = file.path( output\_path ,
"big\_file\_name.csv") ) {{&lt; / highlight &gt;}}

#### One thing to try.

**Compiling functions with R's byte-compiler**

Writing our own functions also allows us to easily use R's `compiler()`
on chunks of our code. The `cmpfun()` function allows us to generate
byte-compiled versions of our own functions,

{{&lt; highlight R &gt;}} c.myfunction &lt;-
compiler::cmpfun(myfunction) {{&lt; / highlight &gt;}}

Sometimes byte-compiled function can give our programs another
performance boost, often with minimal upfront costs, and can yield
surprising gains in big data projects without having to turn to
Rcpp/C++.
