.. _page1:
Getting Started
============================

Make sure you have access to an arangodb database.

Installation
-------------

Install the package

::

    	pip install arango-orm
 
Connecting :ref:`Database <databaseapi>`
-------------------------------------------

.. code-block:: python

    from arango import ArangoClient
    from arango_orm import Database
    client = ArangoClient(hosts='http://localhost:8529')
    test_db = client.db('test', username='test', password='test')
    db = Database(test_db)
    

Working With :ref:`Collection <collectionapi>`
---------------------------------------------------

First we need to define data models (similar to SQLAlchemy’s models) to specify what data our collection will contain and how to marshal it.

.. code-block:: python

    from arango_orm import Collection
    from arango_orm.fields import String, Date
    class Student(Collection):
    
    __collection__ = 'students'

    _key = String(required=True)  # registration number
    name = String(required=True, allow_none=False)
    dob = Date()

Create :ref:`Collection <collectionapi>` in the :ref:`Database<databaseapi>`
------------------------------------------------------------------------------

.. code-block:: python

    db.create_collection(Student)

Add Records
------------

.. code-block:: python

    from datetime import date
    s = Student(name='test', _key='12312', dob=date(year=2016, month=9, day=12))
    db.add(s)
    print(s._id)  # students/12312

Get Total Records in the :ref:`Collection <collectionapi>`
-----------------------------------------------------------

.. code-block:: python

    db.query(Student).count()
    
Get Record By Key
-------------------

.. code-block:: python

    s = db.query(Student).by_key('12312')

Update a Record
-----------------

.. code-block:: python

    s = db.query(Student).by_key('12312')
    s.name = 'Anonymous'
    db.update(s)

Delete a Record
----------------
.. code-block:: python

    s = db.query(Student).by_key('12312')
    db.delete(s)

Get All Records in a :ref:`Collection <collectionapi>`
-------------------------------------------------------
.. code-block:: python

    students = db.query(Student).all()

Get First Record Matching the Query
-------------------------------------
.. code-block:: python

    first_student = db.query(Student).first()

Filter Records
---------------
Using bind parameters (recommended)

.. code-block:: python

    records = db.query(Student).filter("name==@name",name='Anonymous').all()

Using plain condition strings (not safe in case of unsanitized user supplied input)

.. code-block:: python

    records = db.query(Student).filter("name=='Anonymous'").all()

Filter Using OR
-----------------

.. code-block:: python

    # Get all documents where student name starts with A or B
    records = db.query(Student).filter("LIKE(rec.name, 'A%')",prepend_rec_name=False).filter("LIKE(rec.name, 'B%')",prepend_rec_name=False, _or=True).all()

Filter, Sort and Limit
------------------------

.. code-block:: python

    # Last 5 students with names starting with A
    records = db.query(Student).filter("LIKE(rec.name, 'A%')",prepend_rec_name=False).sort("name DESC").limit(5).all()

    # Query students with pagination (limit&offset)
    page_num, per_page = 2, 10
    page = db.query(Student).sort("name DESC").limit(per_page,start_from=(page_num - 1) * per_page)

Fetch Only Some :ref:`Fields <fieldsapi>`
------------------------------------------
.. code-block:: python

    c = db.query(Student).limit(2).returns('_key', 'name').first()

Update Multiple Records
------------------------
.. code-block:: python

    db.query(Student).filter("name==@name", name='Anonymous').update(name='Mr. Anonymous')

Delete Multiple Records
------------------------

.. code-block:: python

    db.query(Student).filter("LIKE(rec.name, 'test%')", prepend_rec_name=False).delete()

Delete All Records
--------------------

.. code-block:: python

    db.query(Student).delete()

Bulk Create Records
--------------------

.. code-block:: python

    s1 = Student(name='test1', _key='12345', dob=date(year=2016, month=9, day=12))
    s2 = Student(name='test2', _key='22346', dob=date(year=2015, month=9, day=12))
    car1 = Car(make="Honda", model="Fiat", year=2010)
    car2 = Car(make="Honda", model="Skoda", year=2015)
    db.bulk_add(entity_list=[p_ref_10, p_ref_11, car1, car2])

Bulk Update Records
--------------------

.. code-block:: python

    p_ref1 = db.query(Person).by_key("12312")
    p_ref2 = db.query(Person).by_key("12345")
    p_ref1.name = "Bruce"
    p_ref2.name = "Eliza"
    db.bulk_update(entity_list=[p_ref1, p_ref2])

Query Using AQL
----------------
.. code-block:: python

    db.add(Student(name='test1', _key='12345', dob=date(year=2016, month=9, day=12)))
    db.add(Student(name='test2', _key='22346', dob=date(year=2015, month=9, day=12)))
    students = [Student._load(s) for s in db.aql.execute("FOR st IN students RETURN st")]






	     







    

