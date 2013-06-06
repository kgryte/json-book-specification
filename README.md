json-book-specification
================

The project: create a BookJSON specification. Several specifications have arisen which impose schema atop of JSON. Examples include <a href="http://geojson.org/" target="_blank">GeoJSON</a> and <a href="https://github.com/mbostock/topojson" target="_blank">TopoJSON</a> for geographic data and <a href="http://www.bibjson.org/" target="_blank">BibJSON</a> for bibliographic data. This projects seeks to create a schema for book data.


---

### Motivation

Within academic science, particularly in fields which involve mathematics, <a href="http://www.latex-project.org/" target="_blank">LaTeX</a> is the language of choice for professional publication. LaTeX is a great language for typesetting and provides a powerful platform for creating professional documents. Nevertheless, the future is not static documents. The future is documents which live and breathe, which update and animate, which evolve and interact. 

Several initiatives have created interactive documentation: <a href="http://ipython.org/notebook.html" target="_blank">IPython notebooks</a> for Python, <a href="http://www.wolfram.com/cdf/" target="_blank">Computable Document Format</a> (CDF) for Mathematica, <a href="http://www.stat.uni-muenchen.de/~leisch/Sweave/" target="_blank">Sweave</a> and <a href="http://yihui.name/knitr/" target="_blank">knitr</a> for R, <a href="http://staffwww.dcs.shef.ac.uk/people/N.Lawrence/matweave.html" target="_blank">MATweave</a> for MATLAB/Octave, <a href="http://www.dexy.it" target="_blank">Dexy</a> for multiple languages, and Bret Victor's <a href="http://worrydream.com/ExplorableExplanations/" target="_blank">Explorable Explanations</a> for web documents. But alas, the vaunted ivory tower moves at a snail's pace; apart from early adopters (who are few and far between), these tools are not widely used. 

Unfortunately, I was not one of those early adopters. Only after I wrote my doctoral thesis did I become aware of interactive documents and their utility, not just in academia and computing, but across all fields and industries. Had I fully appreciated the need to go beyond static and lifeless documents several years ago, how I approached research, exploration, and communication would have been vastly different. Hindsight is always 20:20.

Now that I have recognized the potential power of interactive documentation, I first sought an easy solution: surely someone has created a TeX to, say, HTML/CSS conversion tool. Indeed, several have tried, some more successful than others. <a href="http://johnmacfarlane.net/pandoc/index.html" target="_blank">Pandoc</a> even allows conversion to and from several different formats. But after reviewing the various options, I did not find anything particularly satisfying, with each option having its limitations. Some options converted all formulas to images (a deal-breaker); others struggle with CSS; and others seemed okay for the job, but failed to provide the flexibility needed to realize each format's potential (e.g., one cannot have a LaTeX document with markup linking to data that, when converted to a web document, creates a document able to load and access that data, say, via JavaScript, unless some clever trickery is used.)

I realized, however, that the problem could be avoided, if we created a data format specification for books (and, more generally, documents). With such a specification, we would define a single data format from which any other presentation format could be derived. Currently, each format conversion is unique: a separate utility must address a single input-output format pair. Granted, some utilities transform several different input data types into an intermediary format which is then transformed to desired output. But this workflow is unnecessary (a middle man) and still fails to allow for tailored output. If we defined a single book specification, we could include a complete specification list from which various output formats could realize specification subsets, depending on platform ability. 

For instance, while writing my thesis, I found myself creating images in three different formats. I would first create all graphics in SVG to produce vector graphics. While generating document drafts on my computer, I would export the SVG files as low-resolution (90dpi) PNG files. And once I needed to generate a final print version, I exported the SVG files as high-resolution (300dpi) PNG files.

Each image format is suited for particular medium: SVG for web graphics, low-resolution (90dpi) for electronic documentation, and high-resolution (300dpi) for print graphics. While we could introduce conditionality into, say, LaTeX documents such that we could specify when to use each particular graphic and somehow ensure this is ported to converted output, this introduces unnecessary markup and overhead and is messy. Instead, if we linked all three images to a single data object and used a well-defined API, a format converter could choose the appropriate format for the task.

Moreover, if we explicitly defined the data content, we enable each format generator to encode the content according to its own conventions. For instance, if we defined a data selection as a 'paragraph', a tool to realize HTML would know to use paragraph tags to markup the data selection. In contrast, a tool to realize LaTeX would know to use additional linebreaks to indicate a new paragraph. The same applies for every other data type within documents.

And lastly, along similar lines to what is happening in data visualization and visualization grammars (e.g., <a href="http://trifacta.github.io/vega/" target="_blank">Vega</a>), we can create a 'book' (or more generally, document) grammar. By establishing a common data framework for documents, we eliminate translation inefficiencies and enable communication across formats and platforms. And by making the grammar lower-level, we refrain from imposing limits on the output format and, instead, enable fine-grain control of document design.

With this is mind, this is my attempt at a document grammar.



---

### Specification


BookJSON is a format for encoding document data structures. BookJSON supports the following types: chapter, section, paragraph, equation, figure, quote, ordered list, unordered list, and graph. Types are allowed to nest, such that a chapter may contain multiple sections with each sections containing multiple paragraphs and so on and so forth. 

In JSON terms, a complete BookJSON data structure is always an object. An object is a collection of key : value pairs. The key is always a string, and values may be either a string, number, object, array, or a literal ('true', 'false', 'null'). 


#### Examples

A BookJSON example of a book chapter:


```js
{
	"title": "My First Chapter",
	"type": "chapter",
	"id": "chapter-1",
	"author": [
		{
			"name": "Firstname Lastname",
			"affiliation": "Author Affiliation"
		}
	],
	"abstract": "This is the chapter abstract.",
	"content": [
		{
			"title": "Introduction",
			"type": "section",
			"id": "section-1",
			"desc": "An internal reference.",
			"content": [
				{
					"type": "paragraph",
					"content": "This is a paragraph. Here is a sentence with a citation &lt;cite&gt;AuthorYear&lt;/cite&gt;"
				},
				{
					"type": "paragraph",
					"content": "This is another paragraph with a footnote.&gt;footnote&lt;"
					"footnote": [
						{
							"content": [
								{
									"type": "equation",
									"id": "equation-1",
									"content": "@frac{a}{b}",
									"aside": "An aside explaining this equation with example code: &lt;code&gt;var x = a/b;&lt;/code&gt;." 
								}
							]
						}
					]
				},
				{
					"type": "figure",
					"id": "figure-1",
					"class": "full-width",
					"title": "Figure 1",
					"img": [
						{
							"url": "this/is/the/path/to/the/figure.png"
						}
					],
					"desc": "An internal note description about the figure.",
					"caption": "The figure caption with an endnote.&lt;endnote&gt;",
					"endnote": [
						{
							"content": [
								{
									"type": "paragraph",
									"content": "Endnote text with additional &lt;span class="term"&gt;markup&lt;/span&gt;."
								}
							]
						}
					]
				},
				{
					"type": "quote",
					"content": "This is a &lt;index&gt;quote&lt;/index&gt; with a term added to the index."
				},
				{
					"type": "ordered list",
					"content": [
						{
							"item": "Item 1."
						},
						{
							"item": "Item 2 which refers to &lt;a href="#figure-1"&gt;Figure 1&lt;/a&gt;"
						},
						{
							"item": "Item 3 with inline math: **@int_{-@infty}^{+@infty} e^{xy} dx**."
						}
					]
				},
				{
					"type": "graph*",
					"id": "graph-1",
					"class": "graphs",
					"title": "Graph 1",
					"desc": "An interactive visualization with an image fallback.",
					"url": "path/to/my/visualization/files.js",
					"img": [
						{
							"url": "path/to/my/image/file.png"
						}
					],
					"caption": "This is a graph/image caption. Notice that the type has an asterisk: in &lt;a href="http://example.com"&gt;LaTeX&lt;, this would refer to an un-numbered graph."
				}
			]
		}
	]
}

```




#### Types

Possible types.



#### Fields

Possible fields.


#### Classes

Possible defined classes. Others can include unsupported classes.


#### LaTeX Commands

Addressing equations.

