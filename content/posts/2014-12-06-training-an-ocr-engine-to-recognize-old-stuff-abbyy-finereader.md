--- author: Nathaniel categories: - OCR for Economists - Tutorials date:
"2014-12-06T13:42:46Z" meta: \_edit\_last: "1" pe\_theme\_meta:
O:8:"stdClass":2:{s:7:"gallery";O:8:"stdClass":3:{s:2:"id";s:2:"89";s:5:"width";s:0:"";s:6:"height";s:0:"";}s:5:"video";O:8:"stdClass":1:{s:2:"id";s:3:"262";}}
published: true status: publish tags: - ocr - tutorials title:
'Tutorial: Training an OCR Engine' type: post ---

**[In a previous tutorial I covered the basics of digitizing old
stats](http://www.nathanlane.info/?p=281 "Tutorial: A Beginner’s Guide to Scraping Historic Table Data") **with
ABBYY FineReader (& alternative digitization tools). Now, I dig into
some important digitization nitty gritty: training optical character
recognition software to properly read historical content.\
 \
Most historical digitization projects will entail training. Old
statistical documents often use long-gone proprietary typefaces. While
modern OCR software can easily read Arials and Times New Romans, it
needs help with more exotic typography; this is where training come in.
If you have ever worked with machine learning-type projects and/or text
analysis, training software to properly classify *stuff* is a familiar
concept. And luckily, training ABBYY FineReader's engine is pretty
easy.\
 \

\
[![Gotta train
'em.](%7B%7B%20site.baseurl%20%7D%7D/assets/robotguy.jpg){.aligncenter
width="395"
height="480"}](http://www.nathanlane.info/?p=421 "Tutorial: Training an OCR Engine")

 \
Say you're working with an old scanned document and you wish to extract
its tables. If you OCR the document using the default pattern
recognition settings you will likely be disappointed. Below is an
example of  historic document OCRd using FineReader's default
recognition schemes; many numbers have been replaced by letters or
strange characters.

![FineReader gone
wrong.](%7B%7B%20site.baseurl%20%7D%7D/assets/badex.png){.aligncenter}\
 \
Training is used to tell FineReader, "Hey, don't do that. That British
pound sign is really a number. Same with that W. Hey, don't do that."
Anyone who has ever had to get their hands dirty with machine learning
will immediately recognize the intuition. And if not, don't worry.\
 \
Using custom **User Patterns**, we train ABBYY FineReader's OCRing
algorithm to correctly classify characters in our historic data. Old
documents will have many peculiarities that will be missed by FineReader
unless we point the software in the right direction.\
 

*Note*: Somewhere buried in the sparse FineReader User Guide is a line
reading, "oh no, you seldom have to train FineReader" etc., etc.. This
left us scratching our heads. I have *never* worked with historical data
that did not entail some degree of training.

#### 1. Setup.

It is a good idea to select a handful (or more) representative pages
from the document you wish to analyze. We will use these pages to train
ABBYY's recognition capabilities.\
 \
Save your "training pages" as a distinct file and open them in
FineReader.\
 \
Pre-process these test pages, but do not "Read" them yet. Instead, click
the **Options** button on the main toolbar (or **Tools &gt; Options**),
and then click the **Read** tab.\
 \
![](%7B%7B%20site.baseurl%20%7D%7D/assets/ex2.png){.aligncenter}\
 \
From the **Training** section, select either **Use built-in** **and user
patterns** or **Use only user patterns**. I typically go with the
built-in and user patterns options. You can (and should) experiment to
see which is best for your job.\
 \
In the **Training **section, click the **Pattern Editor** button.
The **Pattern Editor** box will open. From the **Pattern Editor** box,
click **New**. The **Create Pattern **box will open; enter a name for
the **User Pattern**. Click **OK**.\
 \
Last, click the **Read with training** box. This will put ABBYY
FineReader in a type of "training mode." Click **OK**.\
 

#### 2. Reading in Pattern Training mode.

ABBYY is now in a zombie-like training mode!\
 \
Click the main **Read** button.\
 \
Instead of reading the document per usual, a **Pattern Training** mode
box appears and ABBYY FineReader asks your advice about characters it is
unsure about.\
 \
Adjust the green frame around each character FineReader selects, making
sure the box completely encompasses each character. The program is
usually good about selecting the full character, but sometimes it needs
some help.

 

![](%7B%7B%20site.baseurl%20%7D%7D/assets/ex3.png){.aligncenter}

In many old documents, printed letters may be misread using FineReader's
default OCRing pattern. In my document (seen in the image above), the
letter D is always poorly printed. Without direction, the OCR engine
systematically reads these letters as Us or Os.\
 \
Confirm or correct the highlighted character using the **Enter the
character enclosed by the frame** box and click **Train**.
FineReader saves the corrected character and moves to the next one.\
 \
Repeat the Pattern Training process for your training pages, until there
are no more characters left to train.\
 \
*Important*: If working with old data, FineReader often mistakes 1s for
Is, zeros and letter Os, etc.. Hence, it is worth making sure that
FineReader has been extensively trained to recognized your document's
digits. These are potentially tedious errors to correct in raw output,
so it is worth making sure that FineReader *really* differentiates
things.\
 \
People argue about the optimal amount of pages to train ABBYY on, but it
will probably take at least two pages to train FineReader to read the
document adequately. However, since we're dealing with a messy
environment, we may wish to train many additional pages. It's really an
iterative process.\
 \
There may be gross characters you don't want ABBYY FineRead to store.
For instance, in the image below the word "TABLE" is cut. Feeding ABBYY
bad examples can diminish recognition. If letters/numbers are cut, use
the **Skip** button.\
 \
![](%7B%7B%20site.baseurl%20%7D%7D/assets/ex4.png){.aligncenter}\
 \
Err on the side of caution and skip weird stuff. For example, if you are
uncertain what a letter/number is, also **Skip** it. It is better
to skip a letter you are unsure about versus training FineReader
incorrectly.\
 \
Once you're done, **Save** all your hard work. Click the **Option**
button and select the **Read** tab; click the **Save to File** under the
**Training and Language** section.\
 \
When you're finally done with training (and have checked your pattern,
see *Section 3.*), **Load** the document you wish to analyze. Now when
you press **Read**, ABBYY will use your **User Pattern** to in its
OCRing process. Recognition should improve!\
 

#### 3. Editing Patterns, Rooting Out Errors.

Just like ABBYY, we make mistakes. Before we implement the pattern we
trained, make your **User Pattern** is correct.\
 \
Return to the **Options** window ( **Options** button or **Tools &gt;
Options**) and click the **Read** tab. Then click the **Pattern
Editor...** button.\
 \
The **User Pattern** box will open. In the training process we may
accidentally mis-characterize letters. Browse the patterns we have
recorded during the training process and root out typos. These typos are
important to delete! We don't want ABBYY FinerReader's OCR engine to
interpret characters incorrectly.\
 \
If you find an incorrect character (for example, the L that has been
mis-characterized as E below), click the letter image and
press **Delete**. Be sure to *really* check for these types of errors,
or any matches that seem iffy.\
 \
Press **OK** when you are done, then save the corrected **User
Pattern**.

![](%7B%7B%20site.baseurl%20%7D%7D/assets/ex5.png){.aligncenter}
