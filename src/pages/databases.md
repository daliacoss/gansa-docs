title: Databases
__block__: content
sections: Basics
          CSV
          MongoDB
          SQL
          YAML

Databases
=========

Each Gansa project may be connected to a database. Gansa supports a variety of data stores, from simple file formats like YAML and CSV to sophisticated database engines like PostgresQL and MongoDB.

Basics
------

Each view can specify its own database **query**. A query is an instruction to retrieve a slice of the database. As the ["Templates" chapter](templates.html#context-variables) states, the resulting slice will be stored in the context table as "db". If no query is specified, then "db" will be an object that represents the entire database.

The specifications of the query format depend on the database engine that it used. The remaining sections of this chapter will cover how to query each database engine.

Note: if you need to construct more advanced queries than view objects are capable of, you should benefit from using [context processors](callbacks.html#context-processors).

CSV
---

A project that uses CSV as its database engine is allowed to count multiple CSV files as its database. Each CSV file would correspond to a table. Each view can query one table.

As the ["Site configuration" chapter](configuration.html#user-settings) shows, the "database.store_row_as" option in your user settings file allows you to set whether table rows are stored as arrays or as dictionaries. This is important because it affects how certain parameters of your query will work.

Here is the query format specification:

* **query**:
    * **filter** ([Python expression](configuration.html#python-expression) or list of Python expressions, default=[]): a condition, or list of conditions, that must be satisfied in order for each row to be retrieved from the table. The **row** variable, which represents the row being tested, is accessible from each expression. The result of each expression should be a boolean value.
    * **order** (string or number or list, default=[]): the field, or list of fields, to order the results by. Each field can be suffixed with a space followed by the word "ascending" or "descending". If this suffix is omitted, "ascending" will be used.
    * **table** (string): the name of the CSV file to use. Include the file extension.

### An example ###

Let us imagine a project with a database that consists of a single CSV file:

**user.yaml**

    database:
        engine: csv
        uri:
            - presidents.txt
        store_row_as: dict
        convert_numbers_and_bools: true

**presidents.txt**

    id,name,year_start,year_end
    1,George Washington,1789,1797
    2,John Adams,1797,1801
    16,Abraham Lincoln,1861,1865
    26,Theodore Roosevelt,1901,1909

If we wanted to get an alphabetical list of every president who only completed one term, our query would look like this:

    query:
        table: presidents.txt
        order: name ascending
        filter: "row['year_end'] - row['year_start'] < 8"

The result would look like this:

    [
        {"id": 16, "name": "Abraham Lincoln", "year_start": 1861, "year_end": 1865},
        {"id": 2, "name": "John Adams", "year_start": 1797, "year_end": 1801}
    ]

If we wanted to get an alphabetical list of every president who only completed one term and left the presidency before 1840, we would amend our query like this:

    query:
        table: presidents.txt
        order: name ascending
        filter:
            - "row['year_end'] - row['year_start'] < 8"
            - "row['year_end'] < 1840"

And the result would look like this:

    [
        {"id": 2, "name": "John Adams", "year_start": 1797, "year_end": 1801}
    ]

There are two things to keep in mind. First, these particular filters only work because "database.convert_numbers_and_bools" is switched on in the user settings file. If it was switched off, then the years would be stored as strings and the comparison expressions wouldn't work. Second, if "database.store_row_as" was set to "array" (this is currently the default setting), then we would have to rewrite our query to use numeric indexes instead of string indexes:

    query:
        table: presidents.txt
        order: 1 ascending
        filter:
            - "row[3] - row[2] < 8"
            - "row[3] < 1840"

Our result would thus be a list of lists, instead of a list of dictionaries:

    [
        [2, "John Adams", 1797, 1801]
    ]

MongoDB
-------

Gansa uses MongoEngine, which provides an object-document mapper (ODM) for MongoDB. This means that each document in the database is associated with an instance of a schema class. These schema classes are known as **models**.

In order for MongoDB queries to work, a project must have at least one model defined in a Python module.

The query specification for MongoDB is as follows:

* **query**:
    * **filter** (dictionary, default={}): a table of key/value pairs to pass to MongoEngine's query filter function. See the [MongoEngine querying documentation](http://docs.mongoengine.org/guide/querying.html#filtering-queries) for more information.
    * **model** ([Python object](configuration.html#python-object)): a reference to the model object that will be used for the query.
    * **order** (string or list of strings, default=[]): the field, or list of fields, to order the results by. The field name can be prefixed with "-" to indicate descending order. See the [MongoEngine API reference](http://docs.mongoengine.org/apireference.html#mongoengine.queryset.QuerySet.order_by) for more information.

### An example ###

Here is a models file for a database of customers:

**models.py**
    from mongoengine import *

    class Address(EmbeddedDocument):

        name = StringField()
        street = StringField(required=True)
        street_extra = StringField()
        city = StringField(required=True)
        state_or_province = StringField()
        country = StringField(required=True)
        postal_code = StringField(required=True)

    class User(Document):

        name = StringField(required=True)
        email_address = StringField(required=True)
        date_joined = DateTimeField(required=True)
        shipping_address = EmbeddedDocumentField("Address")

If we wanted a list of all users with shipping addresses outside of the USA, and we wanted that list to be sorted by user names, our query would look like this:

    query:
      model: models_mdb:User
      order: name
      filter:
        shipping_address__country__ne: USA

SQL
---

Gansa uses SQLAlchemy, which provides an object-relational mapper (ORM) for MySQL, PostgresQL, and SQLite. This means that each table in the database is associated with a class, which is known as a **model**. Each record in a table is associated with an instance of the table's model class.

You are allowed to send either raw SQL queries or ORM queries. In order for ORM queries to work, a project must have at least one model defined in a Python module.

Here is the query specification for SQL (MySQL, PostgresQL, and SQLite):

* **query** (string or dictionary): either a raw SQL query string, or a table for defining an ORM query. The latter must conforms to the following schema:
    * **filter** ([Python expression](configuration.html#python-expression) or list of Python expressions, default=[]): a condition, or list of conditions, that must be satisfied in order for each record to be retrieved from the database. The **sqlalchemy** module, along with every module referenced in query.models, is available from each expression.
    * **join** ([Python object](configuration.html#python-object) or list of Python objects): an expression, or list of expressions, that will be used to create a query join. The **sqlalchemy** module, along with every module referenced in query.models, is available from each expression. See the [SQLAlchemy Query API](http://docs.sqlalchemy.org/en/latest/orm/query.html#sqlalchemy.orm.query.Query.join) for more information.
    * **models** ([Python object](configuration.html#python-object) or list of Python objects): a reference, or list of references, to the model objects that will be used for the query.
    * **order** ([Python expression](configuration.html#python-expression) or list of Python expressions, default=[]): an expression, or list of expressions, that determine how to sort the query results. The **sqlalchemy** module, along with every module referenced in query.models, is available from each expression. See the [SQLAlchemy documentation](http://docs.sqlalchemy.org/en/latest/core/tutorial.html#ordering-grouping-limiting-offset-ing) for more information.

### An example ###

Here is a models file for a database of musical instruments:

**models.py**

    from sqlalchemy import *
    from sqlalchemy.ext.declarative import declarative_base

    Base = declarative_base()

    class InstrumentFamily(Base):
      __tablename__ = "instrument_families"

      id = Column(Integer, primary_key=True)
      name = Column(String, nullable=False) # "woodwind", "string", "percussion"...
      description = Column(String)

    class InstrumentType(Base):
        __tablename__ = "instrument_types"

        id = Column(Integer, primary_key=True)
        name = Column(String, nullable=False) # "guitar", "piano", "kalimba"...
        instrument_family_id = Column(Integer, ForeignKey("instrument_families.id"), nullable=False)
        description = Column(String)

    class InstrumentModel(Base):
      __tablename__ = "instrument_models"

      id = Column(Integer, primary_key=True)
      name = Column(String, nullable=False)
      instrument_type_id = Column(Integer, ForeignKey("instrument_types.id"), nullable=False)
      creator = Column(String)
      date_of_origin = Column(DateTime)
      description = Column(String)

If we wanted to get an alphabetical list of every string instrument in the database, our query would look like this:

    query:
      models: models:InstrumentModel
      order: models.InstrumentModel.name
      join:
          - models.InstrumentType
          - models.InstrumentFamily
      filter: models.InstrumentFamily.name == "string"

Alternatively, we could write our query as raw SQL:

    query: >
      SELECT m.* FROM instrument_models m 
        INNER JOIN instrument_types t ON m.instrument_type_id=t.id
        INNER JOIN instrument_families f ON t.instrument_family_id=f.id
        WHERE f.name='string';

YAML
----

The query specification for YAML is very simple:

* **query** ([Python expression](configuration.html#python-expression)): an expression that returns a slice of the database. The **db** variable, which represents the entire database, is accessible from the expression.

### An example ###

Here is our database:

db.yaml

    boxers:
        - name: Joe Frazier
          weight_class:
              - heavyweight
          wins: 32
          losses: 4
          draws: 1

        - name: Laila Ali
          weight_class:
              - super middleweight
              - light heavyweight
          wins: 24
          losses: 0
          draws: 0

        - name: George Foreman
          weight_class:
              - heavyweight
          wins: 76
          losses: 5
          draws: 0

If we want a list of all boxers who have competed in more than one weight class, we can construct our query using Python built-ins like **filter** and **len**:

    query: "filter((lambda x: len(x["weight_class"]) > 1), db["boxers"])"

The result:

    [
        {
            'name': 'Laila Ali',
            'weight_class': ['super middleweight', 'light heavyweight'],
            'losses': 0,
            'draws': 0,
            'wins': 24
        }
    ]