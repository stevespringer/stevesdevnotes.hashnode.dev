---
title: "Extracting PDF statements from your gmail inbox"
datePublished: Thu Jan 11 2024 17:33:32 GMT+0000 (Coordinated Universal Time)
cuid: clr9hng1a000g09jm2wfg13tf
slug: extracting-pdf-statements-from-your-gmail-inbox
tags: python, pdf, data-engineering

---

For part 1, see [Mining PDFs for tabular data, part 1](https://stevesdevnotes.hashnode.dev/mining-pdfs-for-tabular-data-part-1)

A client of mine received monthly statements via email for several properties that he rented out, and wanted a quick way to extract the data from the attached PDF files, and get them into his financial spreadsheet.

In order to keep things simple we decided **not** to automate:

* Reading emails directly via the Gmail API.  
    *It was easy enough for the client to perform a search manually each year, and convert the relevant emails into a set of eml files for my code to process.*
    
* Placing the emails into the sheet directly via Google apps script.  
    *The client was happy to run my program annually, supplying it with its input, and copying the generated csv manually into sheets, leaving him with flexibility as to where the final data would end up within his own system.*
    

Therefore, I focussed on the task of scripting the extraction and parsing of attachments from a set of supplied eml files.

### Why Google Colab was the ideal tool

Using Colab meant I could leverage python and [pdfplumber](https://github.com/jsvine/pdfplumber), but it also offered:

* A clear way to describe the steps involved in the automation task
    
* A sweet-spot between scripting and UI
    
* A way to store the script in and run it from the client's Google Drive
    
* Access to input files stored in Google Drive, and the ability to write extracted attachments to another folder within Google Drive
    
* A way to get URLs for the extracted documents, which can then be richly linked from the Google Sheet automatically
    
* Rich data display and easy csv export via Colab's [Data Table Display](https://colab.research.google.com/notebooks/data_table.ipynb)
    

### Example code

This is essentially the Jupyter notebook we ended up with, with a couple of modifications and anonymisations (and permission to share here).

%[https://gist.github.com/stevespringer/2f849db71f275d26da0a623ef60a5556]