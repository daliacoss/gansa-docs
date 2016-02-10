__block__: content
title: Basic usage
sections: Create a site
          Site anatomy
          Hello world
          Render the site

Basic usage
===========

The `gansa` command-line tool allows you to initialize and build your website.

Create a site
-------------

To create a new site, navigate to an empty directory and run the following command:

`gansa init`

The following files and folders will then be created in the directory:

    distribute/
    src/
        assets/
        pages/
        templates/
        settings.yaml
        user.yaml
        views.yaml

If settings.yaml, user.yaml, or views.yaml already exists before `gansa init` is run, it will be overwritten. 

Site anatomy
------------
The **src** directory contains all of the source code, asset files, and settings for the site.

* **assets** contains "static" asset files. An asset file can be anything that you want to include on your site, but is usually either an image, a CSS stylesheet, or a JavaScript script.
* **pages** contains Markdown files that are used to create the site's HTML pages.
* **templates** contains Jinja2 or Jade template files that are used to control the layouts of the site's HTML pages.
* **settings.yaml** is the general settings file for the site.
* **user.yaml** is the user settings file for the site. Because it might contain sensitive information such as passwords, it should not be version-controlled.
* **views.yaml** lists all of the HTML pages that should be created, along with various parameters to control how they are rendered.

The **distribute** directory will contain the rendered website. It should not be version-controlled.

Hello world
-----------

To turn your new site into a simple "Hello world" webpage, you will need to create a Markdown page and a template file. You will then need to edit your views.yaml file to link the page and the template together.

First, create the Markdown page. Save it in src/pages as **index.md**:

    title: My Page
    __block__: main

    Hello world!


Next, create the template. (For this example, we'll use the Jinja2 syntax.) Save it in src/templates as **base.html**:

    <html>
    <head>
        <title>{{title}}</title>
    </head>
    <body>
	    {% block main %}{% endblock %}
    </body>
    </html>


Notice that the name of the block in the template is identical to the value of the `__block__` variable in the Markdown page.

Finally, update your **views.yaml** file:

    - route: index.html
      template: base.html
      pages: index.md

This creates a single **view** object that will be rendered on our site as **index.html**.

If you omit the `pages` variable from your view, it will attempt to infer the name of the Markdown page from the name of the route. So, if the route is "index.html", it will use "index.md". Thus, you could actually make the views file a little shorter:

    - route: index.html
      template: base.html

Render the site
---------------

To render the site, run the following command from the top of the site directory:

`gansa build`

Your finished site will be located in the "distribute" directory. If you've been following the "Hello world" tutorial, it should look like [this](files/hello.html).
