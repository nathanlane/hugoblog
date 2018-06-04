--- author: Nathaniel categories: - Tutorials date:
"2014-10-19T16:16:08Z" meta: null published: true status: publish tags:
- digital history - ocr - pdf - python - tutorails title: 'Tutorial:
Manipulating PDFs in Python (to Scrape Them).' type: post ---

When digitizing old data, we often start with a pile of scanned
documents we must reorganize. Much time is spent manually trudging
through scans, deducing what variables exist, and selecting the tables
we eventually wish to turn into machine-readable data. When you have
hundreds of multi-page PDFS, this can be a painful experience. However,
automating PDF manipulation with Python can save major time.

### The Problem

We start with scans of old, provincial statistical yearbooks for a
Southeast Asian country.

Each yearbook page corresponds to a variable we want: page 1 has land
statistics, page 2 has rice statistics, page 3 irrigation, etc..

We want to reorganize this stack of yearbooks into something that is
easier to digitize and organized by variable, not province. In essence
(to the data scientist) we need to "reshape" our scanned PDF data.

To restate the problem:

-   #### Have: Province x Variable PDFs

    Most old historical data comes in the following format: hard copy
    volumes organized by province, state, region, etc., each with the
    same set of tables (*cough* variables).

-   #### Need: Variable x Province PDFs

    And most of the time we want to pull certain pages from each
    geographic volume and create a new document for each variable.

### The Code

The following is written for Python 3.\* and uses the **[PyPDF2
package](https://pypi.python.org/pypi/PyPDF2 "pyPDF2")** (which is a
**[fork of the original PyPdf
package](http://pybrary.net/pyPdf/ "PyPdf")**), as well as the `OS`
module for directory manipulation.

The code starts with a directory containing our multi-page PDFs and
creates a sub-folder to store individual pages, `/splits.`

{{&lt; highlight Python &gt;}} \#The only two modules you need: import
os from PyPDF2 import PdfFileReader, PdfFileWriter, PdfFileMerger \#The
directory of your (multipage) PDF files. start\_dir =
"/our/working/path" \# Main working directory with PDFs to chop/clean
\#Make the following dirs if it doesn't exist. splits =
os.path.join(start\_dir, "splits1") if not os.path.exists(splits):
os.makedirs(splits) {{&lt; / highlight &gt;}}

Second, our stack-o-PDFs are read, chopped, and their pages are placed
in (page) numbered folders.

The following code chunk begins at the `/start_dir`, the file containing
our original PDF files. We read each scan and then loop over its pages
with the line, `for i in xrange(in_file_pdf.numPages)`. Each page `i` is
saved to a variable folder, corresponding to its page number: first
pages are saved in `start_dir/splits/1`; second pages, into
`start_dir/splits/2` folder, etc..

{{&lt; highlight Python &gt;}} for filename in os.listdir(start\_dir):
\#Run the following on PDFs only. if filename.endswith('.pdf'): \#Show
current multi-page PDF. print("Splitting "+filename) \#Define input
files, paths. in\_file = os.path.join(start\_dir,filename) in\_file\_pdf
= PdfFileReader(file(in\_file, "rb")) \#(be explicit about binary file)
for i in xrange(in\_file\_pdf.numPages): output = PdfFileWriter() \#Make
subfolder for each page, but only once. num\_path =
os.path.join(splits,str(i)) if not os.path.exists(num\_path):
os.makedirs(num\_path) \#Add i page to output, define output path, save,
close outputstream. output.addPage(in\_file\_pdf.getPage(i))
out\_file\_pdf = os.path.splitext(filename)\[0\]+str(i)+".pdf" \#Add i
number to new name. out\_file =
os.path.join(splits,str(i),out\_file\_pdf) print("Saving "+out\_file)
outputStream = file(out\_file, "wb") output.write(outputStream)
outputStream.close() {{&lt; / highlight &gt;}}

Third, after chopping and saving, we combine the separated pages into
variable-based PDFs.

The following code loops over each page folder (`/splits/1`,
`/splits/2`,`...`). Using pyPDF2's `PdfFilerMerge `function, we combine
pages within each folder into a single PDF file.

Hence, the first page of each provincial yearbook is combined into a new
file (i.e. the pages in `/splits/1` become 1.pdf), which we can then
scrape/digitize/pre-process/whatever.

{{&lt; highlight Python &gt;}} for root, dirs, filenames in
os.walk(splits): for dir in dirs: merger = PdfFileMerger() dirname =
os.path.join(splits, dir) print(dirname) for filename in
os.listdir(dirname): print(filename) in\_file\_pdf =
os.path.join(splits, dir, filename) print(in\_file\_pdf)
merger.append(PdfFileReader(file(in\_file\_pdf, "rb"))) out\_file\_pdf =
str(dir)+".pdf" out\_file = os.path.join(splits, out\_file\_pdf)
outputStream = file(out\_file, "wb") merger.write(outputStream)
outputStream.close() {{&lt; / highlight &gt;}}

Importantly, your project will probably look much different from this,
but combining the `OS module` with the `pyPDF2 package` in Python can
make many splitting/merging tasks trivial. Digitizing old data often
entails mind-numbing file manipulation, so a little Python can go a long
way.
