--- author: Nathaniel categories: - OCR for Economists - Tutorials date:
"2014-10-05T02:05:28Z" published: true status: publish tags: -
finereader - ocr - python - regex title: 'Tutorial: A Beginner''s Guide
to Scraping Historic Table Data' type: post ---

This is a simple introduction to scraping tables from historic (scanned)
documents. It is by no means definitive. Instead, this is a broad
overview aimed at researchers with minimal programming experience
tackling smaller digitization projects--say, nothing more than
200 pages.  I focus on OCRing material with ABBYY FineReader, a popular
*commercial* program for OCRing. ABBYY has a relatively gentle learning
curve and, importantly, straightforward table functionality.

 

For those more comfortable with the command line and programming, or for
open source advocates, I suggest free programmatic alternatives for each
tutorial step. Larger complex digitization projects often entail more
technical elbow grease and advanced use of such tools.

 

[![](http://s3-us-west-2.amazonaws.com/landlaborcapital/blogposts/teletype2.jpg){width="636"
height="742"}](http://www.nathanlane.info/?p=281) Oh, the
enraging heritage of old data. (From the Mad Men Mondays "Data's First
Class Economy Set" Repository: Hartman Center, Rubenstein Library, Duke
University.)

 

### First, Some OCRing Tips.

-   Try to work with scans that are at least 300 DPI, saved in TIFF
    format. PDFs are often unavoidable, but use less "lossy" formats
    when possible.

<!-- -->

-   The older the text, the harder OCRing will be. This is especially
    true for text from the early 1900s and prior.

<!-- -->

-   Cleaning images improves text recognition. Pre-process scans to
    remove stains and borders; fix page orientation; deskew; and
    normalize page illumination.

<!-- -->

-   The straighter your page, the more likely programs are to
    recognize tabular content.

<!-- -->

-   Experiment with different color settings for your project. People
    debate the efficacy of binarized (black-white) versus color
    formats. Explore what works best for your project, the benefits are
    high.

<!-- -->

-   OCR quality suffers at very low and very high resolutions. If you
    scan at a higher resolution, drop the resolution before OCRing.

<!-- -->

-   OCR software is poor at reading small text.

<!-- -->

-   OCR software needs your help, especially for weird type faces:
    invest in training software prior to OCRing.

### 1. Convert Scans: split PDFs & convert to TIFF.

Most digitization projects don't start with clean TIFF files. They start
with a nasty, multi-page PDFs produced from clumsy library scanning
sessions. Before OCRing our scans, we must pre-process (batch clean) our
images. But even before we can clean our documents, we must we
must break apart our multi-page PDF and convert it to TIFF format. This
is the format used by our pre-processing software, as well as by other
OCRing tools:

#### Using Adobe Acrobat Pro.

If you have the luxury of Adobe Acrobat Pro, you can easily convert
multi-page scans or a set of combined PDF images into TIFFS. (File:
**Save As &gt; Image &gt; TIFFs**). Better yet, you can use their GUI to
extract and export subsets of the PDF document to TIFF format.

<div class="media image">

![](http://s3-us-west-2.amazonaws.com/landlaborcapital/blogposts/adobe.png){width="920"
height="555"}

</div>

Splitting and converting a multi-page PDF to TIFF in Acrobat Pro.

#### Open source alternatives:

If you don't have access to Acrobat Pro, you're in luck. There are
oodles of open source tools for breaking apart PDFs and/or batch
conversion. **[ImageMagick](http://www.imagemagick.org/))** is the
workhorse open source tool for command line-based image manipulation and
is incorporated into many digitization projects. It is cross-platform
and can be integrated into most major programming languages. For
OSX, **[Pdf-Splitter](https://github.com/thejefflarson/pdf-splitter)** by
Pro-Publica's Jeff Larson is a simple command line tool that utilizes
native OSX libraries. Born from the frustration of dealing with PDFs,
there are many Python packages for deconstructing documents. For
instance,
the **[pdfserenity](https://pypi.python.org/pypi/pdfserenitynow/0.6.7)**
package converts multi-page PDFs into TIFFs.

The [**pyPDF2**](https://pythonhosted.org/PyPDF2/) package in Python is
especially useful at manipulating PDFs, which I cover in **[this post
here](http://www.nathanlane.info/?p=369)**. Also, it's pretty darn fast!

### 2. Pre-process TIFFS with ScanTailor.

Pre-processing images--straightening documents, splitting pages,
removing distortions, de-staining pages, etc.--can make or break
character recognition. In the preceding step we converted our files so
that we could pre-process our images using
[**ScanTailor**,](http://scantailor.org/) an awesome open source
tool *specifically* for batch cleaning scanned documents. It only
accepts TIFF and does a limited number of basic automated tasks, but it
does them well. Hence, it has become a staple of the [digitization and
hacker/text scraping community](http://www.diybookscanner.org/).

While many OCRing suites, like ABBYY FineReader, also have solid
pre-processing tools, ScanTailor has key advantages. I have found it
is much better at straightening pages than most tools. Again, straight
pages are crucial for recognizing table structure. ScanTailor also comes
with a command line version that be used to script larger tasks.

<div class="media image">

![](http://s3-us-west-2.amazonaws.com/landlaborcapital/blogposts/scantailor.png){width="923"
height="501"} Batch pre-processing images in ScanTailor's GUI-based
version.

</div>

#### Other open source and programmatic tools for pre-processing.

While most people use ImageMagick for basic image conversion tasks, many
people utilize its powerful features for **[batch document cleaning
scripts](http://www.fmwconcepts.com/imagemagick/textcleaner/index.php)**. **[GIMP](www.gimp.org/)**,
the popular open source graphic suite, also has promising batch
pre-processing capabilities: people have had success with **[Nuvola
tools for cleaning up greyscale
scans](http://gimper.net/resources/nuvola-tools.582/)**.

### 3. OCRing with ABBYY FineReader 12.

Now that we have a pile of cleaned TIFFs, we load the files into ABBYY
FineReader for further  1) pre-processing, 2) training, 3) table
analysis/OCRing, and 4) error verification:

#### First, use ABBYY's pre-processing tools to further clean and select the optimal OCR resolution.

Once loaded into FineReader, you will likely want to further clean the
scans using the built-in pre-processing tools (**"Edit Image"**). One
tool that is particular useful is the FineReader's optimal resolution
tool, which scales the resolution of the image to maximize recognition.

#### But before you OCR, train.

Training is the next crucial step. With historic data, you will likely
get poor results if you neglect to train the OCR software and jump
straight into OCRing. The ability to fully train OCR
engines distinguishes professional software from less
sophisticated OCRing tools.

In general, training improves the ability of OCR algorithms to correctly
classify characters by "tuning" the algorithm on a sample of your
document. In FineReader, training is a trivial task, where you walk the
program through recognizing a sample set of characters from your
document. You can easily append and save these training files, called
"**User Patterns**."

<div class="media image">

![](http://s3-us-west-2.amazonaws.com/landlaborcapital/blogposts/abbytraining.png){width="776"
height="546"}

</div>

Training ABBYY FineReader's OCR engine on a sample document.

#### "Analyzing" & "Reading" - Table recognition & OCRing in FineReader.

In FineReader, layout recognition and OCR are known as "analyzing" and
"reading", respectively. Unlike straight forward digitization of textual
material, we want to make sure FineReader recognizes our table layouts
and correct mishaps before it reads the content of individual table
cells: First, the** Analyze Selected Pages** command detects the content
of our pages (i.e. finds our tables). We then confirm that FineReader
has recognized tables and table cells correctly, adjusting mistakes "by
hand" with the built-in table editing tools. Second, we OCR the table
contents with the **Read Selected Pages** command.

<div class="media image">

![](http://s3-us-west-2.amazonaws.com/landlaborcapital/blogposts/abbyytable.png){width="873"
height="519"} A properly recognized table in ABBYY FineReader with OCRd
content.

</div>

#### Check for mistakes, tweak, & repeat.

OCRing is never perfect. Once FineReader has "read" your document, you
will want to check for errors. For each page, FineReader gives a rough
error rate, reporting the number of characters it is uncertain about on
each document page.  The "Verify Text" tool allows us to easily
check and correct each uncertain character.

After the first OCR session it is best to get a sense of how successful
character recognition was and the types of errors that occur. Often,
exploring pre-processing and improved training can improve
text recognition.

### 5. Post-processing.

Ultimately, FineReader will spit out .csv or .xlsx files, but
newly digitized content still needs to be tidied up.

Especially if you’re working with old documents, OCRing produces some
junk output. Dust, scratches, and page discoloration can get picked up
as weird symbols: \*, \^, \\, etc.. You can easily correct these
blemishes [[using regular
expressions]{style="font-weight: bold;"}](http://en.wikipedia.org/wiki/Regular_expression)[ in your
preferred scripting language
(using ]{style="color: #666666;"}[sub/gsub]{style="font-weight: bold; color: #666666;"}[ commands
in
R,]{style="color: #666666;"}[ re.sub]{style="font-weight: bold; color: #666666;"}[ type
commands in Python). Better
yet,]{style="color: #666666;"}[ [OpenRefine](http://http//openrefine.org/) ]{style="font-weight: bold; color: #666666;"}provides
some extremely flexible tools for wrangling OCRd output, making most
cleaning tasks trivial while also supporting advanced regular expression
use.

[![](http://s3-us-west-2.amazonaws.com/landlaborcapital/blogposts/openrefinelogo.png){width="519"
height="125"}](http://openrefine.org/)

Clean up weird OCR output using OpenRefine and regular expressions.

 

### Ending Note. A bit more on OCR software and getting advanced.

Why ABBYY FineReader? First, the learning curve is lower than other open
source options. The closest open source competition comes
from [**Google’s tesseract OCR
program**](https://code.google.com/p/tesseract-ocr/), which is powerful
and useful for those comfortable with the command line or OCRing from a
preferred programming language. For advanced projects, tesseract offers
unmatched flexibility and customization. However in my experience, the
tesseract is frustrating to train and has poor table recognition
ability.

Although it is relatively easy, ABBYY FineReader has downsides. For
instance, the Mac version isn't as functional as the
complete "Professional" PC version. Moreover, multi-core support is
limited for both Professional and basic Corporate versions, making large
projects slow and unwieldy (in my experience).

While FineReader provides tools for table area recognition, other times
we have to pursue more programmatic methods of extracting table. Common
approaches can be seen in [**Dan Nugyen’s ProPublica
guide**](http://www.propublica.org/nerds/item/image-to-text-ocr-and-imagemagick)
and **[Dr. Rex Douglass’ (UCSD Polisci)
method](http://rexdouglass.com/extracting-data-from-printed-tables-in-historical-documents/),**
who use computer vision techniques to "cut up" tables, OCRing individual
cells before reassembling the table. I recommend taking a peak at both
to understand alternative workflows for table scraping.

Some users have opted to detect tables after OCRing: first, recognizing
text in PDF files and then stripping the OCRd content using [**PDF table
extraction tools like Tabula**](tabula.nerdpower.org). These methods
hint to the growing hacker community interested in scraping PDF content.
The recent [**PDF Liberation Hackathon
website**](http://pdfliberation.wordpress.com/) features some great
tools to this end.

Feel free to shoot me any feedback or share your experiences with
digitizing historic data: <nathaniel.lane@iies.su.se>.
