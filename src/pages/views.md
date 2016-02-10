__block__: content
title: Views
sections: The views file
          Specification
          Special YAML types
          An example

Views
=====

A Gansa site consists of **views**. A view is an object that either fills an HTML template with content to produce an HTML file, or helps other view objects do so. The content may come from Markdown pages, context variables, and/or a database.

Views may be nested—that is, a view may have subviews. A view that has subviews is called a **parent view**. A parent view does not generate an HTML file on its own. Instead, it generates a folder in which its subviews will generate their HTML files (or folders). It also stores data that can be shared across all of its descendant views, allowing you to avoid repeating yourself.

From this point forward, a view that is *not* a parent view—that is, a view that *does* generate an HTML file—will be referred to as a **terminal view**.

The views file
--------------

View objects are defined in the project's views file, which is called views.yaml by default. The views file consists of a tree of view objects defined in YAML format.

Every view must have a **route**. The route is the base name of the HTML file or folder that the view generates. For example, the route of this documentation page is "views.html". Parent views are allowed to use the empty string ("") as their route; a parent view that does so will not generate a folder. This can be useful when you want multiple views to share the same properties without creating a new folder for them.

Every terminal view must have a **template**. This is the name of the template file that the view will fill with content.

Every parent view has, by definition, a list of its **subviews**.

As stated, a view can use a number of different sources of content. You can specify which sources to use by setting various optional properties. The **pages** property specifies one or more Markdown files that will be used to fill the template. The **context** property defines variables that can be referenced by the template. The **query** property allows you to pass a portion of a database to the template. The **context_processor** property specifies a Python function that can be used to programmatically generate further context variables.

All of the aforementioned properties may be explicitly set for each view, but most can be implicitly set through inheritance. (Exceptions are noted in the following section, "Specification".) This means that if a property for a view is not explicitly set, it will be set using the value provided by the nearest ancestor to explicitly set that property. If no suitable value can be found, the property will be left undefined. **Note that explicitly setting a property to an empty string is not the same as leaving it undefined.**

Most view properties are optional, with the exception of routes and templates. Every view must have an explicitly defined route. Every terminal view must have a template that is either explicitly defined or implicitly inherited from ancestor views.

Specification
-------------

Here is the specification for a view object:

* **context** (dictionary): a table of context variables.
* **context_processor** ([Python object](configuration.html#python-object)): a reference to a Python callable (that is, a function or an object that can be called like a function) that returns a table of context variables. For more information, see the ["Callbacks"](callbacks.html#context-processors) chapter.
* **pages** (string or list of strings): the names of Markdown files to fill the template with. If undefined for a terminal view, Gansa will attempt to find a page with the same name as the route, but with a .md extension instead of .html. **This property cannot be inherited and is ignored on parent views.**
* **query** (dictionary or [Python expression](configuration.html#python-expression)): a set of properties or expressions that retrieve a portion of the project's database, to be passed to the view template. For more information, see the ["Databases"](databases.html) chapter.
* **route** (string): the name of the file or folder that the view will generate. **This property cannot be inherited.**
* **subviews** (list): a list of the view's subviews. Specifying this parameter will automatically turn the view into a parent view. **This property cannot be inherited.**
* **template** (string): the name of the template file that the view will fill.

An example
----------

Here is what a views file might look like for a personal site with a simple blog:

    - route: ""
      template: base_home.jade
      subviews:
        - route: "index.html"
          pages:
            - index.md
            - index_alt_filler.md
        - route: "about.html"

    - route: "blog"
      template: base_blog.jade
      subviews:
        - route: post1.html
        - route: post2.html
        - route: post3.html

Here is what the structure of that site would look like:

    index.html
    about.html
    blog/
        post1.html
        post2.html
        post3.html

The first parent view uses the empty string as its route. Because of this, its subviews (index.html and about.html) are placed in the same directory as its sibling view (blog). The second parent view creates a new directory, "blog", that its subviews are generated in.

Most of the terminal views do not explicitly specify pages. That means that Gansa will try to use, for example, about.md for the about.html view, and blog/post1.md for the post1.html view. However, the index.html view specifies two Markdown pages to use. index.md is probably meant to fill the "main" content block of the view, whereas index_alt_filler.md might override the default content for a footer or sidebar.

None of the terminal views explicitly specify a template. Instead, their templates are inherited from their parent views.