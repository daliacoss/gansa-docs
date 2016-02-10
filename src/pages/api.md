title: API reference
block: __content__
sections: gansa
          gansa.six

API Reference
=============

The gansa Python library consists of a single module, ([gansa](#gansa)).

gansa
-----

Classes:

* **[Site](#site)**

Other globals:

* **TMP_TEMPLATE**: a string used to generate temporary template files.
* **SUPPORTED_DB_ENGINES**: a set of all of the supported values for the "database.engine" user setting.

### Site ###

This class represents a Gansa project and its environment.

#### Constructor ####

* **Site**(environment, load=*True*)
    * Arguments:
        * **environment**: the top level of the Gansa project (one level higher than the src directory).
        * **load**: attempt to load an existing Gansa environment into the object. Set to False if you are creating a Gansa project from scratch.
    * Returns: a Site object.

#### Class attributes ####

* **default_settings**: a table of fallback values used for any omitted options or sections in the project settings file.
* **default_user_settings**: a table of fallback values used for any omitted options or sections in the project user settings file.

#### Object attributes ####

* **db**: the project's database object.
* **db_engine**: the [SQLAlchemy engine object](http://docs.sqlalchemy.org/en/latest/core/engines.html) for the database. This attribute is only set if a SQL datbase is used for the project.
* **environment**: the absolute path of the top level of the Gansa project.
* **environment_src** (read-only): the absolute path of the source directory of the Gansa project.
* **environment_dist** (read-only): the absolute path of the default distribution directory of the Gansa project.
* **g**: a table that is empty by default. Gansa will never index this table on its own, so you are free to use it to share data between custom callback functions.
* **routes** (read-only): a list of all unique full routes in the project views.
* **settings**: the project's settings table.
* **templates**: the [template environment object](http://jinja.pocoo.org/docs/dev/api/#jinja2.Environment) for the project.
* **user_settings**: the project's user settings table.
* **views**: the project's tree of view objects.

#### Methods ####

* **build**(out=*""*)
    * Render the Gansa project.
    * Arguments:
        * **out**: the directory to store the output in. If set to the empty string or another value that can be coerced to False, *self.environment_src* will be used.
    * Returns: *None*
* **init_environment**()
    * Initialize the project files and directories. Use this to create a Gansa project from scratch.
    * Returns: *None*
* **load_db**()
    * Establish a connection to the project database, using parameters from the project user settings. Called by *load_environment*.
    * Returns: *None*
* **load_environment**()
    * Load the project environment files (settings, templates, database).
    * Returns: *None*
* **load_settings**(merge=*True*)
    * Load the project settings. Called by *load_environment*.
    * Returns: *None*
* **load_templates**()
    * Create the project's template environment object, using parameters from the project settings. Called by *load_environment*.
    * Returns: *None*
* **load_user_settings**()
    * Load the project user settings. Called by *load_environment*.
    * Returns: *None*
* **load_views**()
    * Load the project view object tree, using parameters from the project settings. Called by *load_environment*.
    * Returns: *None*
* **set_view_full_routes**(views=*None*, route_prefix=*"/"*)
    * Recursively set the full routes for some or all of the loaded views.
    * Arguments:
        * **views**: the list of view trees to set. If set to *None*, *self.views* will be used.
        * **route_prefix**: the prefix of each value to use as a route.
    * Returns: *None*
* **set_view_parameter**(views, key, default_value, value=*None*)
    * Recursively set a parameter for some or all of the loaded views. This method is what enables [view attribute inheritance](views.html#the-views-file).
    * Arguments:
        * **views**: the list of view trees to set. If set to *None*, *self.views* will be used.
        * **key**: the name of the parameter to set.
        * **default_value**: the value of the parameter to set if *value* is *None*.
        * **value**: the value of the parameter to set.
    * Returns: *None*
* **query_db**(query=*None*)
    * Query the project database.
    * Arguments:
        * **query**: the query statement or table. The proper format for the query depends on the database settings. See [the "Databases" chapter](databases.html) for more information.
    * Returns: the result of the query, or the database object itself if query is *None*.
