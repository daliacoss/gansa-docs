__block__: content
title: Templates
sections: Template files
          Blocks
          Context variables

Templates
=========

A **template** is a file that acts as a prototype for HTML files. Templates typically consist of a mix of markup, which may be HTML or an equivalent language, and macros, which are non-HTML control structures, expressions, or statements. A macro is like a "blank" in the HTML that contains instructions on how Gansa should fill that blank.

Templates have two primary benefits:

1. **Minimization of repetition**: if you want to produce many HTML files that all have an identical layout, you can define that layout in a single template file instead of rewriting it for every page. 
2. **Separation of concerns**: template macros allow you to separate content from form. Generally, creating website content requires different skills from creating website *layouts*. Without templates, one must store content and layout in the same file, making it harder to modify one without also modifying the other.

Template files
--------------

Template files must be stored in the project's template directory, which is called "templates" by default.

Each template file may be written in either the [Jinja2](http://jinja.pocoo.org/docs/dev/) or the [Jade](http://jade-lang.com/) language. If a template file's extension is ".jade", Gansa will assume it is a Jade file. Otherwise, Gansa will assume it is a Jinja2 file.

### A note for Jade users ###

Gansa uses the [PyJade](https://pypi.python.org/pypi/pyjade/3.1.0) implementation of Jade. Some Jade files written for standard Jade may not work with PyJade, and vice versa. In particular, any Jade macro that uses JavaScript syntax may need to be rewritten to use Python syntax.

Blocks
------

A major feature of templates is the **block**. A block is a gap in the template that can be filled with custom HTML whenever that template is used.

In Gansa, there are two ways to fill a template block: through Markdown pages, and through template inheritance.

### Filling blocks with Markdown ###

Here is an example using a Jinja2 template and a Markdown page:

**templates/base.html**

    <html>
    <head>
        <title>My Page</title>
    </head>
    <body>
        {% block content %}{% endblock %}
    </body>
    </html>

**pages/index.md**

    __block__: content

    Welcome to my web site!

The final output would look like this:

**index.html**

    <html>
    <head>
        <title>My Page</title>
    </head>
    <body>
        <p>Welcome to my web site!</p>
    </body>
    </html>

### Filling blocks through template inheritance ###

Here is an example using two Jinja2 templates:

**templates/base.html**

    <html>
    <head>
        <title>My Page</title>
    </head>
    <body>
        {% block content %}{% endblock %}
    </body>
    </html>

**templates/base_extended.html**

    {% extends "base.html" %}

    {% block content %}<p>Welcome to my web site!</p>{% endblock %}

The final output would look like this:

**index.html**

    <html>
    <head>
        <title>My Page</title>
    </head>
    <body>
        <p>Welcome to my web site!</p>
    </body>
    </html>

Context variables
-----------------

A context variable is a variable that can be accessed from a template. Unlike blocks, which can only be filled with Unicode strings of HTML, a context variable can be of any Python type.

Each view has its own table of context variables, which can be referred to as a **context table**. Context variables can be defined in the view object itself, a page's metadata, or a context processor. Python built-ins listed in the settings file under "templates.builtins" are also passed as context variables. In addition, Gansa always inserts the following variables into each context table:

* **route**: the view's route.
* **full_route**: the view's route concatenated with the routes of its ancestors, using "/" as a separator. For example, if View A's route is "post1.html", and View B's route is "blog", and View B is View A's sole ancestor, then View A's full route is "blog/post1.html".
* **db**: the database query result for the view.

### Override order ###

Because context variables can be defined in multiple places, it is possible to override the value of a context variable after it has already been defined. When a context variable is finally passed to a template, the value of the variable is always the *last* value that was assigned to it. Here is the order in which variables are added to the context table:

1. "route", "full_route", and Python built-ins
2. context variables defined by the view object
3. "db"
4. page metadata
5. anything returned by the context processor

This list can also be thought of as a reverse order of precedence. That is, the lower an item on the list is, the greater its say is in the final value of a context variable.