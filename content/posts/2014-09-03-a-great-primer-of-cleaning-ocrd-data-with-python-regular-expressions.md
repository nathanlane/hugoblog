--- author: Nathaniel categories: - OCR for Economists - Python -
Tutorials date: "2014-09-03T06:04:44Z" meta: \_edit\_last: "1"
pe\_theme\_meta:
O:8:"stdClass":2:{s:7:"gallery";O:8:"stdClass":3:{s:2:"id";s:2:"89";s:5:"width";s:0:"";s:6:"height";s:0:"";}s:5:"video";O:8:"stdClass":1:{s:2:"id";s:2:"63";}}
published: true status: publish tags: - links - programming - python -
regex - tutorials title: A great primer on cleaning OCRd data with
Python & Regular Expressions. type: post ---

[\
](http://programminghistorian.org/lessons/cleaning-ocrd-text-with-regular-expressions "Cleaning OCR’d text with Regular Expressions")

#### Link: Cleaning OCR’d text with Regular Expressions

Often the pain of [optical character
recognition](http://en.wikipedia.org/wiki/Optical_character_recognition)
isn't the OCRing procedure itself, it is cleaning the tiny, little
inconsistencies that plague OCRd content. This is especially true when
we OCR historical material: even high quality scans can have a speckle
or two that get recognized as gibberish.

Adept use of [Regular
Expressions](http://nikic.github.io/2012/06/15/The-true-power-of-regular-expressions.html)
(regex) coupled with simple Python (or Ruby scripts--or heck, even
Notepad++) can be a powerful means of removing nasty errors from OCRd
text/CSV files.

[Here's an awesome little
primer](http://programminghistorian.org/lessons/cleaning-ocrd-text-with-regular-expressions)
from Laura O'hare at [The Programming
Historian](http://programminghistorian.org/ "The Programming Historian blog")
on using Python to clean nasty OCRd content using regexs. Great sample
(verbose) code for helping turning mush into data. Importantly, they
break down a lot of the regex components, which is helpful for those
getting started with this brand of data cleaning.

Of course, regex+Python won't be perfect. While there are [preferred
ways](http://en.wikipedia.org/wiki/Grep) of using regex to wrangle text,
Python gives most of us quick, programmatic means of cleaning nasty OCRd
spreadsheets and the like. Most importantly, however, errors in our OCRd
content are seldom systematic, which makes completely automating OCRd
data cleaning tricky. There will be hand polishing involved. But as the
primer notes, the point is isn't perfection; it's to let regex+Python
"**do the heavy lifting**."
