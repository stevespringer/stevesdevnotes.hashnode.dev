---
title: "Extracting usable data from pdf bank statements"
datePublished: Thu Jan 11 2024 17:34:53 GMT+0000 (Coordinated Universal Time)
cuid: clr9hp78r000209kzcq5dg6py
slug: extracting-usable-data-from-pdf-bank-statements
tags: python, pdf, data-engineering

---

While modern banks have excellent financial reporting facilities, some old ones provide little more than regular pdf statements.

A client came to me after the one csv export that their bank provided was rendered useless by almost every transaction description being removed or redacted, apparently for data protection reasons.

Despite there being simple ways to derive tabular data from PDF documents ([see part 2](https://stevesdevnotes.hashnode.dev/extracting-pdf-statements-from-your-gmail-inbox)), a slightly more sophisticated approach was needed here.

In this instance, the table was structured so that it was impossible to tell if a monetary amount was a debit or credit without knowing it's horizontal position on the page.

As I mentioned in [part 1](https://stevesdevnotes.hashnode.dev/mining-pdfs-for-tabular-data-part-1), there are technical ways to fine-tune table-extraction, but even in this case, a simple approach was sufficient.

I found that a common-sense analysis of the document structure, combined with running python list comprehensions over the results of [pdfplubmer's](https://github.com/jsvine/pdfplumber) `extract_words` function, was sufficient to place each word within its relevant transaction and column.

Like in part 2, I used Google Colab to conveniently traverse a folder full of documents and produce a single export containing every transaction.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Pro tip: If the document supplies one or more headline "summary" figures, use these to check against the sum total of the values that you extracted, to ensure the correctness of your parsing code.</div>
</div>

Below is an example of how this kind of task can be approached:

%[https://gist.github.com/stevespringer/018ae0fe3f0208c59db3d5fb0f2b2af0]