---
title: "How Pandoc Saved Me Hours"
date: 2021-04-25T15:08:17+09:00
Description: "Use Pandoc to Convert Document Formats"
Tags: [Markdown,Wiki,PDF]
Categories: [Tutorials,Linux]
Comments: false
draft: false
---
I have become pretty comfortable using markdown for multiple tasks. This blog is written in markdown, and most of my notes I've taken while studying have also used markdown. Why would I take notes in a way that I also have to be comfortable with the syntax? For me, it was about having a standard output and formatting. I found it quicker for indentations that I like, and using the headers helps as well. 

Needless to say I have .md files all over. After creating my [self hosted WIKI](https://kalifornia909.info/post/media-wiki-docker/), I didn't realize a pretty big hurdle. Wiki has it's own formatting and that would be something else I have to cram in my head. The goal being to transfer all my scattered notes and markdown files into this wiki would mean replacing the markdown syntax to wiki. This seemed like an impossible task, and would take hour upon hours. 

Once I was ready to embark on this nails on a chalk board journey I decided that there is no way I was the only person interested in doing something like this. Off to google! This brought me to a solution I didn't already know I was familiar with. Enter Pandoc! I have used pandoc before to convert documents to PDF, and well now its going to convert markdown to wiki syntax for me automatically. To say I was overjoyed is the understatement of year. Lets dive in and see how to do it.

## Converting Markdown to WIKI with PANDOC 

To convert the existing markdown to wiki format is a long but easy command:
```
pandoc -f commonmark markdownfile.md -t mediawiki -o wikifilename.wiki 
```
Let's see what we can breakdown from this. The -f flag is for the from format. Meaning this is the format of the origin file you are trying to convert from. -f commonmark is the format name for the markdown syntax. Putting this flag with the filename of the original document you are converting from being markdown is step one. 

Now, the -t flag is the to format. Meaning, what are you trying to format this file to. In this case we are formatting to mediawiki as a format. The -o flag being the output file you want the file to be named once its converted.

Just like that, the markdown file is now converted from markdown to wiki! This means no going line by line and changing syntax from markdown to wiki, and in my case, typos. Pandoc can also help with other conversions.

## Converting Markdown to PDF

I figured that including the PDF conversion is something a little more common use for most people. With that said, to convert to PDF requires more than just pandoc by itself. I needed the [texlive package](https://en.wikipedia.org/wiki/TeX_Live) to include the pdflatex needed for the conversion. This is very much a distribution specific task. That being said most of the formatting is similar to the breakdown we had earlier for the other conversion.

```
pandoc -f commonmark markdownfile.md -t latex -o pdfname.pdf
```
The latex package will use pdflatex to convert the file. 

I hope that you can find [Pandoc](https://pandoc.org/) useful. There are numerous formats that it can convert to and from. These conversion are just the tip of the iceberg. Also, the [latex](https://www.latex-project.org/) document preparation system is very powerful. A future project of mine is to use latex to format my resume with a template that you can easily find. If you enjoy writing but don't want to worry about keeping track of things like formatting I hope you find something in this article helpful.