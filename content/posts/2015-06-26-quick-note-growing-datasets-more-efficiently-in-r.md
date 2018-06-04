--- author: Nathaniel categories: - Tutorial date:
"2015-06-26T11:57:06Z" published: true status: publish tags: - R -
programming title: 'Tutorial: Growing Datasets in R' type: post ---

<div class="media image">

![From the Field Museum
collection](%7B%7B%20site.baseurl%20%7D%7D/assets/pushingworkers_fieldmuseum.jpg)

</div>

Figure: [From the Field Museum archives, 1920, Photographer Herbert P.
Burtch, Oriental Institute. "Men moving Totem Pole outside Field Museum
by train."](http://fieldmuseumphotoarchives.tumblr.com/)

`Note: This was originally some notes to RAs but I figured it may be useful for other people out there.`

I've had some discussion with econ folks and RAs who are working with
giant datasets in R for the first time. In particular, those having to
*"harvest"* or *"grow"* unweildy datasets. R is notoriously slow when it
comes to expanding datasets, such as when you want to increntally append
rows to a file with results from a scraping API, or combine a giant
stack of raw text files from another text mining project.

The usual "good" method for concatination uses a `do.call` function with
the rbind function. This method essentially takes a list of stuff and
passes them as arguments all at once to `rbind`. In other words, you can
take a list of *data.frame* names and bind the rows together in one
motion: `do.call("rbind", <<<A list of data.frame names>>>)`.

### A Usual Approach

A common task I encounter is grabbing a chunk of files from a directory
and combining them into a dataset. Such a task requires three steps.
First, generating a list of files from a directory that match a pattern
(e.g. all the .csv files in a directory) using the `list.files()`
function. Next, looping over this list of files and loading them into R
with with `lapply`, applying the `read.csv()` function to a list of
files. Then, finally, using `do.call()` to `rbind`, or stack, all the
loaded *.csv* files into a single dataset.

{{&lt; highlight R &gt;}} \# Grab the list of files in the directory
"/home/user/foo" that end in ".csv" csvlist &lt;- list.files( path =
"/home/user/foo", pattern = ".csv", all.files = FALSE, full.names =
TRUE, recursive = FALSE ) \# "Apply" the read.csv function to the list
of csv files. csvloaded &lt;- lapply( csvlist, read.csv ) \# Append the
loaded .csv files into a list. dataset1 &lt;-do.call( "rbind" ,
csvloaded ) {{&lt; / highlight &gt;}}

This is all great, but it can still take a ton of time. Note, I condense
the `lapply` function and the `do.call`+`rbind` line into one.

{{&lt; highlight R &gt;}} ptm &lt;- proc.time() dataset1 &lt;- do.call(
"rbind", lapply( csvlist, read.csv) ) proc.time() - ptm &gt; user system
elapsed &gt; 48.840 0.148 50.241 {{&lt; / highlight &gt;}}

If you're doing more complicated tasks or working with large sets of
data, processing time can balloon.

### A faster method.

Using [the `data.table`
package](https://github.com/Rdatatable/data.table/wiki) can speed things
along if we're trying to get big data into R efficiently ([I highly
recommend checking out the github for the
project](I%20highly%20recommend%20checking%20out%20the%20github%20for%20the%20project)).\
The `rbindlist` function included in the package is incredibly fast and
written in C. In addition the `fread` function is built to efficiently
read data into R.

Below I replace the normal `read.csv` function with `fread()`, and
replace `do.call()`+`rbind()` with `rbindlist()`.

{{&lt; highlight R &gt;}} library(data.table) dataset2 &lt;- rbindlist(
lapply( csvlist, fread ) ) proc.time() - ptm &gt; user system elapsed
&gt; 4.044 0.084 4.144 {{&lt; / highlight &gt;}}

Both methods deliver identical datasets but there are some real
efficiency gains when using `fread` and `rbindlist` from the super
useful `data.table` package.

{{&lt; highlight R &gt;}} identical( dataset1, dataset2 ) &gt; TRUE
{{&lt; / highlight &gt;}}

This can have pretty amazing payoffs when working trying to load massive
data sets into R to process.
