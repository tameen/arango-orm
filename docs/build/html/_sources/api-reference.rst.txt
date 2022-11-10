.. _page2:
API Reference
==============


Collections
-------------

.. code-block:: python
   
   class arango_orm.collections.CollectionBase

Base class for Collections, Nodes and Links

.. code-block:: python
   
   classmethod schema(only: Optional[List[str]] = None)

schema caches Marshmellow Schemas on this class to preserve memory

.. code-block:: python
   
   class arango_orm.collections.Collection(collection_name=None, **kwargs)

Base class for representing collections (or vertices as called in AranogDB).

.. code-block:: python
    
    _dump(only=None, **kwargs)

Dump all object attributes into a dict.

.. code-block:: python

    property _id

    classmethod _load(in_dict, only=None, instance=None, db=None)

Create object from given dict.
        
.. code-block:: python
   
   class arango_orm.collections.Relation(collection_name=None, **kwargs)
