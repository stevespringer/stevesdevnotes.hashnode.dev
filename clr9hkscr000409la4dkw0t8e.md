---
title: "Mining PDFs for tabular data, part 1"
datePublished: Thu Jan 11 2024 17:31:28 GMT+0000 (Coordinated Universal Time)
cuid: clr9hkscr000409la4dkw0t8e
slug: mining-pdfs-for-tabular-data-part-1
tags: python, pdf, data-engineering

---

Recently, I've had several use cases that lead me to use the excellent python library [pdfplumber](https://github.com/jsvine/pdfplumber).

<details data-node-type="hn-details-summary"><summary>Quick note regarding pdfs with a very high number of pages.</summary><div data-type="detailsContent">Memory leaks can occur, <a target="_blank" rel="noopener noreferrer nofollow" href="https://github.com/jsvine/pdfplumber/issues/193" style="pointer-events: none">but there are easy workarounds</a>. For most of my work, however, this was never a problem.</div></details>

By using python for document\\textual analysis, you can take advantage of the language's expressiveness, conciseness and flexibility.

### Extracting tables is not always straightforward...

While pdfplumber has functions for [extracting tables](https://github.com/jsvine/pdfplumber#extracting-tables), I found that it did not work with the default settings for all but the simplest of examples.

The library *does* allow you to tweak all manner of table-extraction parameters, and recommends cropping pages before you begin extracting.

### ... but it doesn't have to be complicated.

However, I haven't yet found a need to get buried in the details of table identification, but have used the following rules of thumb in extracting financial data from bank and other statements:

> If the structure of the documents and the rows and columns is predictable and simple enough, **just use regex and string manipulation**.

pdfplumber's `extract_text` function does a pretty good job of representing many documents as a series of lines which you can then parse. To get the entire document's text in a string, do something like:

```python
pdf = pdfplumber.open(file_path)
text = "".join(p.extract_text() for p in pdf.pages)
```

[I have written up a recent example of a regex-based approach in part 2](https://stevesdevnotes.hashnode.dev/extracting-pdf-statements-from-your-gmail-inbox).

> If you cannot deduce which field a piece of text belongs to from its context alone, you will have deal with the object model which gives you information about the horizontal and vertical position of every word.

But for tables with just a few well-defined columns, I still think this can be simpler and more reliable than using a dynamic, parameter-tweaked approach to table-identification.

[For more information on this technique, see part 3.](https://stevesdevnotes.hashnode.dev/extracting-usable-data-from-pdf-bank-statements)

> For financial statements, don't make the mistake of assuming that every transaction will occupy exactly one line.

That might be true for one statement pdf, but you may later encounter another in which they wrap across several lines. In my experience, the date and amount columns were never the cause of this, always the description which sometimes gets wordy.

Wrapped lines makes delineating one transaction from the next a little trickier than just `text.split('\n')`.