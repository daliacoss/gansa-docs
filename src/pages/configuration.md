__block__: content
title: Site configuration
sections: settings.yaml
          User settings (user.yaml)
          Special YAML types

Site configuration
==================

Configuring a Gansa project is done via the project's **settings** and **user settings** YAML files. The settings file, which is always named "settings.yaml", contains general, public settings for the site. The user settings file, which is named "user.yaml", contains user-specific, private settings for the site.

This documentation assumes basic familiarity with [YAML syntax](http://yaml.org/spec/1.2/spec.html).

settings.yaml
-------------

Here is the specification for settings.yaml:

* **environment**
    * **assets** (string, default="assets"): the name of the assets directory.
    * **pages** (string, default="pages"): the name of the pages directory.
    * **template** (string, default="template"): the name of the template directory.
    * **user** (string, default="user.yaml"): the name of the user settings file.
    * **views** (string, default="views.yaml"): the name of the views file.
* **pages**
    * **extensions** (list of strings, default=[]): a list of extensions to enable for Markdown pages. Refer to the [Python Markdown documentation](https://pythonhosted.org/Markdown/extensions/index.html) for a full list of supported extensions.
    * **extension_options** (dictionary, default={}): a nested table of options for the enabled Markdown extensions. For each key/value pair, the key should be the name of an extension, and the value should be a dictionary of options for that extension.
* **templates**
    * **default_block** (string, default="content"): the name of the default template block in which to insert content from Markdown pages. This value is only used if the "__block__" metadata variable is omitted from a page.
    * **builtins** (list of strings, default=[]): the names of all Python built-ins (e.g., "range", "len") to pass to templates as context variables.
* **callbacks**
    * **postrender** ([Python object](#python-object), default=""): a reference to a Python callable (that is, a function or an object that can be called like a function) that is called after the site is rendered. For more information, see the ["Callbacks"](callbacks.html#context-processors) chapter.

As an example, here is the settings.yaml file for the Gansa documentation site:

	environment:
      assets: assets
      pages: pages
      templates: templates
      user: user.yaml
      views: views.yaml
    pages:
      extensions:
        - markdown.extensions.toc
      extension_options:
        markdown.extensions.toc:
          baselevel: 1
    templates:
      default_block: content

User settings (user.yaml)
-------------------------

Here is the specification for the user settings file, which is named user.yaml by default:

* **database** (dictionary, default={}): set to {} (an empty table) if you do not want to use a database.
    * **convert_numbers_and_bools** (boolean, default=true): only used if the database engine is CSV. If true, Gansa will convert numeric values in each row to integers or floats, and boolean values ("true" and "false") to actual booleans. If false, all values will be stored as strings.
        * **Note:** if "store_row_as" is set to "dict", all elements in the header row will always be parsed as strings.
    * **engine** (string, default=""): if blank, Gansa will attempt to infer the engine from the value of **uri**. If not blank, the value must be one of:
        * csv
        * mongodb
        * mysql
        * postgresql
        * sqlite
        * yaml
    * **store_row_as** (string, default="array"): only used if the database engine is CSV. Must be either "array" or "dict". If "dict", each row in a CSV file is stored as a dictionary, with the top row used as keys.
    * **uri** (string or list of strings): the file name(s) or URI of the database.
        * For CSV, this may be the name of a single CSV file, or a list of names of multiple CSV files.
        * For MongoDB, this must be a valid MongoDB database URI. See the [MongoDB documentation](https://docs.mongodb.org/v2.6/reference/connection-string/) for details on the URI format.
        * For SQL (MySQL, PostgresQL, SQLite), this must be a valid database URI. See the [SQLAlchemy documentation](http://docs.sqlalchemy.org/en/latest/core/engines.html#database-urls) for details on the URI format.
        * For YAML, this must be the name of a single YAML file.

See the ["Databases"](databases.html) chapter for more detailed information on database configuration and usage.

Special YAML types
------------------

### Python expression ###

This is a string that is formatted such that it is a valid Python expression. Depending on the property this is used for, pre-defined variables may be accessible within the scope of the expression.

### Python object ###

This is a string that is formatted according to the following syntax:

`some.python.package:some_object`

For example, if you have a function called "render_page" in a Python file called "views.py", you would refer to it as "views:render_page".
