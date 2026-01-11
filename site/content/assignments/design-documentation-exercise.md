---
title: 'Design Documentation Exercise'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## Design Documentation Exercise

### Introduction

This team exercise has you starting to create your design documentation.

### Step 1: Edit your design document

1. A Design Document Template has been provided in your github repository in the top level `/docs` directory.
2. The `Designdoc.md` template file is in markdown format. Check the Resources page for [tools to edit markdown files](/docs/resources#markdown-formatting).
3. Add the following information in your design document:
    * Complete the **Team Information** section.
    * Write an **Executive Summary** for the project.
    * Insert your domain model and write the high-level overview of the domain for the application.
    * Remove the comments from all sections where you have filled in information.

    {{% hint warning %}}

    Note: You must have the actual image files physically in your repository `/docs` directory. When the PDF generator runs, it will most likely not have the necessary permissions to access a remote resource holding an image file. This is also a good practice because it will allow you to track revisions of your documentation including which images were placed in revisions of the document.

    {{% /hint %}}

4. Add and commit this to your repository with an appropriate comment.
5. Merge this update into your `master` branch and push to the remote.

### Step 2: Create a PDF of your design document

A PDF (Portable Document Format) is a professional looking document which includes page numbers and figure captions. There are a few options avialable to create PDF from markdown files:

#### VSCode Extension

* The VSCode extension [Markdown PDF](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf) converts markdown files to pdf, html, png or jpeg files.
* After installing the extension, open your `DesignDoc.md` and right-click in the file area and choose *Markdown PDF: (Export pdf)*.
* The `DesignDoc.pdf` will be created in the `/docs` directory
* You can preview the rendered markdown version directly in VSCode.

#### Pandoc and MiKTex

* You can use the [pandoc](https://pandoc.org/) and [MiKTeX](https://miktex.org/) tool combination to create a PDF from your markdown design document. These tools are installed on the SE lab machines. There are Mac and Windows versions that you can download to your own machine.
* There is a maven execution id that you can run to generate the PDF file with pandoc. From the `/docs` directory, use the maven command: `mvn exec:exec@docs`. The PDF file will be in `DesignDoc.pdf` file.

{{% hint warning %}}

Notes

* For anybody trying to run the `mvn exec:exec@docs` and receiving errors on a mac, this may be caused by a missing dependency called `pdflatex`. It can be fixed by installing `mactex` via homebrew as described here: https://superuser.com/questions/1038612/where-do-i-get-the-pdflatex-program-for-mac
* If you want to generate the PDF outside of maven, use the command: `pandoc DesignDoc.md -o DesignDoc.pdf`

{{% /hint %}}

By the time specified on the schedule for your section, put *only* the generated `DesignDoc.pdf` file in the *Design documentation - team* in the MyCourses Assignments *Team Exercises* area.