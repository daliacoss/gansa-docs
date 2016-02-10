__block__: content
title: Pages
sections: Metadata
          How metadata is used
          Markdown extensions

Pages
=====

A **page** is a Markdown file that is used to fill template blocks. Markdown is an easy-to-read text format that is converted into HTML. You can consult the official Markdown documentation [here](https://daringfireball.net/projects/markdown/).

Pages must be stored in the project's page directory, which is called "pages" by default.

Metadata
--------

Gansa adds a feature to pages that is not in the original Markdown format. This feature is the ability to include metadata.

Metadata is written as a collection of `key: value` pairs. A pair takes up a single line of text unless the value is a list. If the value is a list, then each item in the list is written on a separate line, indented by 4 or more spaces.

    title: My Page
    author: Decky Coss
    sections: Section 1
              Section 2

If we were to serialize the metadata in the above example as a Python dictionary, it would look like this:

    {
        "title": "My Page",
        "author": "Decky Coss",
        "sections": ["Section 1", "Section 2"]
    }

Metadata must be written at the top of the page. The metadata section of a page ends no farther than the first blank line of the page. If the first line of the page is `---`, then the metadata will end at either the first blank line or the next instance of a YAML-style delimitor (`---` or `...`).

When in doubt, consult the [syntax documentation](https://pythonhosted.org/Markdown/extensions/meta_data.html#syntax) for Python Markdown's Meta-Data extension.

How metadata is used
--------------------

Each key-value pair in the page's metadata is parsed, with only one exception, as a context variable. As the ["Templates" chapter](templates.html) explains, a context variable is a variable that can be accessed from a template.

The exception to this is **__block__**, a special metadata key that specifies the name of the template block that the page's contents will be inserted into. If this key is omitted, then the default block as specified by the settings file will be used.

Because metadata is parsed as context variables, a few considerations need to be made:

1. A metadatum can only be used as a context variable if its key is a valid variable name. That means it must consist only of alphanumeric characters or underscores, and cannot begin with a numeral.
2. If a view uses multiple pages, and each page assigns a different value to the same metadata key, and that key is not the special "__block__" key, then each page will override the value assigned by the page listed before it.
3. Conversely, if multiple pages in the view assign the same value to the "__block__" key, then only the final page listed will be used to fill the block.
4. It is possible for metadata to override other pre-defined context variables, such as Python built-ins. The ["Templates" chapter](templates.html#override-order) goes into the ramifications of this in greater detail.

Markdown extensions
-------------------

The general settings file allows you to enable and configure extensions for Markdown pages. As is documented in the ["Site configuration"](configuration.html#settingsyaml) chapter, this is accomplished through the "pages.extensions" and "pages.extension_options" settings. A full list of available extensions and their options can be found in the [Python Markdown documentation](https://pythonhosted.org/Markdown/extensions/index.html).

Gansa uses the Meta-data extension to enable page metadata. This extension cannot be disabled.