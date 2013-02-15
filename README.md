HtmlPageDom
===========

Wa72\HtmlPageDom is a PHP library for easy manipulation of HTML documents using DOM.
It requires [DomCrawler from Symfony2 components](https://github.com/symfony/DomCrawler) for traversing 
the DOM tree and extends it by adding methods for manipulating the DOM tree of HTML documents.

It's useful when you need to not just extract information from an HTML file (what DomCrawler does) but
also to modify HTML pages. It is usable as a template engine: load your HTML template file, set new
HTML content on certain elements such as the page title, "div#content" or "ul#menu" and print out
the modified page.

Wa72\HtmlPageDom consists of two classes:

-   *HtmlPageCrawler* extends Symfony\Components\DomCrawler by adding jQuery inspired, HTML specific 
    DOM *manipulation* functions such as html(), before(), append(), wrap(), addClass() or css().
    It's like jQuery for PHP: simply select elements of an HTML page using CSS selectors and change their attributes and content.

-   *HtmlPage* represents one complete HTML document and offers convenience functions like setTitle($title),
    setMeta('description', $description), getBody(). It uses HtmlPageCrawler for filtering and manipulation DOM Elements.
 

Requirements
------------

-   PHP 5.3+
-   [Symfony\Components\DomCrawler](https://github.com/symfony/DomCrawler)
-   [Symfony\Components\CssSelector](https://github.com/symfony/CssSelector)

Installation
------------

-   using composer: simply add "wa72/htmlpagedom": "dev-master" to the "require" section of your composer.json

-   using other PSR-0 compliant autoloader: clone this project to where your vendor libraries are 
    and point your autoloader to look for the "\Wa72\HtmlPageDom" namespace in the "src" 
    directory of this project

Usage
-----

HtmlPageCrawler is a wrapper around DOMNodes. HtmlPageCrawler objects can be created using "new" or the static function
HtmlPageCrawler::create(), which accepts an HTML string or a DOMNode (or an array of DOMNodes or a DOMNodeList)
as arguments.

Afterwards you can select nodes from the added DOM tree by calling filter() (equivalent to find() in jQuery) and alter
the selected elements using the following jQuery-like manipulation functions:

-   addClass(), hasClass(), removeClass(), toggleClass()
-   after()
-   append()
-   appendTo()
-   before()
-   css() (same as getStyle() / setStyle())
-   html() (same as getInnerHtml() / setInnerHtml())
-   insertAfter()
-   insertBefore()
-   makeEmpty() (equivalent to emtpy() in jQuery)
-   prepend()
-   prependTo()
-   remove()
-   removeAttribute(), getAttribute(), setAttribute()
-   replaceAll()
-   replaceWith()
-   unwrap()
-   wrap()
-   wrapInner()
-   wrapAll()

To get the modified DOM as HTML code use html() (returns innerHTML of the first node in your crawler object)
or saveHTML() (returns combined "outer" HTML code of all elements in the list).

Example:

```php

// create an object from a fragment of HTML code as you would do with jQuery's $() function
$c = \Wa72\HtmlPageDom\HtmlPageCrawler::create('<div id="content"><h1>Title</h1></div>');

// the above is the same as calling:
$c = new \Wa72\HtmlPageDom\HtmlPageCrawler('<div id="content"><h1>Title</h1></div>');

// filter for h1 elements and wrap them with an HTML structure
$c->filter('h1')->wrap('<div class="innercontent">');

// return the modified HTML
echo $c->saveHTML();
// or simply:
echo $c; // implicit __toString() calls saveHTML()
// will output: <div id="content"><div class="innercontent"><h1>Title</h1></div></div>

```

Usage examples for HtmlPage:

```php
// create a new HtmlPage object with an empty HTML skeleton
$page = new \Wa72\HtmlPageDom\HtmlPage();

// or create a HtmlPage object from an existing page
$page = new \Wa72\HtmlPageDom\HtmlPage(file_get_contents('http://www.heise.de'));

// get or set page title
echo $page->getTitle();
$page->setTitle('New page title');
echo $page->getTitle();


// add HTML content
$page->filter('body')->setInnerHtml('<div id="#content"><h1>This is the headline</h1><p class="text">This is a paragraph</p></div>');

// select elements by css selector
$h1 = $page->filter('#content h1');
$p = $page->filter('p.text');

// change attributes and content of an element
$h1->addClass('headline')->css('margin-top', '10px')->setInnerHtml('This is the <em>new</em> headline');

$p->removeClass('text')->append('<br>There is more than one line in this paragraph');

// add a new paragraph to div#content
$page->filter('#content')->append('<p>This is a new paragraph.</p>');

// add a class and some attribute to all paragraphs
$page->filter('p')->addClass('newclass')->setAttribute('data-foo', 'bar');


// get HTML content of an element
echo $page->filter('#content')->saveHTML();

// output the whole HTML page
echo $page->save();
// or simply:
echo $page;
```

History
-------

When I discovered how easy it was to modify HTML documents using jQuery I looked for a PHP library providing similar
possibilities for PHP.

Googling around I found [SimpleHtmlDom] (http://simplehtmldom.sourceforge.net)
and later [Ganon] (http://code.google.com/p/ganon) but both turned out to be very slow. Nevertheless I used both
libraries in my projects.

When Symfony2 appeared with it's DomCrawler and CssSelector components I thought:
the functions for traversing the DOM tree and selecting elements by CSS selectors are already there, only the
manipulation functions are missing. Let's implement them! So the HtmlPageDom project was born.

It turned out that it was a good choice to build on PHP's DOM functions: Compared to SimpleHtmlDom and Ganon, HmtlPageDom
is lightning fast. In one of my projects, I have a PHP script that takes a huge HTML page containing several hundreds
of article elements and extracts them into individual HTML files (that are later on demand loaded by AJAX back into the
original HTML page). Using SimpleHtmlDom it took the script 3 minutes (right, minutes!) to run (and I needed to raise
PHP's memory limit to over 500MB). Using Ganon as HTML parsing and manipulation engine it took even longer,
about 5 minutes. After switching to HtmlPageDom the same script doing the same processing tasks is running only about
one second (all on the same server). HtmlPageDom is really fast.





