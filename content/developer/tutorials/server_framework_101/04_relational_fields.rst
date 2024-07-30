==================================
Chapter 4: Extend the model family
==================================

In Odoo, data rarely exists in isolation. The true power of an application lies in its ability to
connect and relate different pieces of information. In this chapter, we'll explore the three
fundamental types of model relationships in Odoo: Many2One, One2Many, and Many2Many. Mastering these
connections will allow us to create rich, interconnected data structures that will form the backbone
of our real estate application.

.. _tutorials/server_framework_101/module_structure:

Module structure
================

As our `real_estate` module grows, you may notice that we've already created a dozen files for just
one model, along with its menu items, actions and views. With more models on the horizon, our module
directory could quickly become cluttered. To address this potential issue, Odoo provides **module
structure guidelines** that offer several benefits:

- **Improved maintainability**: A well-organized directory structure makes it easier to navigate the
  module and locate specific files.
- **Scalability**: Proper organization prevents the module from becoming cluttered as it grows in
  complexity and size.
- **Collaboration**: A standardized structure facilitates easier understanding among contributors
  and ensures easier integration with the Odoo ecosystem.

.. seealso::
   :ref:`Coding guidelines on module directories
   <contributing/coding_guidelines/module_structure/directories>`

.. example::
   Let's consider a possible structure for our example `product` module:

   .. code-block:: text

      product/
      │
      ├── data/
      │   └── product_data.xml
      │
      ├── models/
      │   ├── __init__.py
      │   └── product.py
      │
      ├── security/
      │   └── ir.model.access.csv
      │
      ├── views/
      │   ├── actions.xml
      │   ├── menus.xml
      │   └── product_views.xml
      │
      ├── static/
      │   ├── description/
      │   │   └── icon.png
      │   │
      │   └── img/
      │       ├── coffee_table.png
      │       └── t_shirt.png
      │
      ├── __init__.py
      └── __manifest__.py

   .. note::

      - The :file:`models` directory contains its own :file:`__init__.py` file, simplifying Python
        imports. The root :file:`__init__.py` file imports the :file:`models` Python package, which
        in turns imports individual model files.
      - Security-related files, such as :file:`ir.model.access.csv`, are placed in the dedicated
        :file:`security` directory.
      - UI files (:file:`actions.xml`, :file:`menus.xml`, and view definitions) are organized within
        the :file:`views` directory.
      - The app icon has resides in :file:`static/description`, while other image assets are stored
        in :file:`static/img`.
      - The :file:`__init__.py` and :file:`__manifest__.py` files remain in the module's root
        directory.

.. exercise::

   Restructure the `real_estate` module according to the guidelines.

   .. tip::
      Pick `[CLN]` for your :ref:`commit message tag
      <contributing/git_guidelines/commit_tag_module>`.

.. spoiler:: Solution

   .. code-block:: text

      real_estate/
      │
      ├── data/
      │   └── real_estate_property_data.xml.xml
      │
      ├── models/
      │   ├── __init__.py
      │   └── real_estate_property.py
      │
      ├── security/
      │   └── ir.model.access.csv
      │
      ├── views/
      │   ├── actions.xml
      │   ├── menus.xml
      │   └── real_estate_property_views.xml
      │
      ├── static/
      │   ├── description/
      │   │   └── icon.png
      │   │
      │   └── img/
      │       ├── country_house.png.png
      │       ├── loft.png
      │       └── mixed_use_commercial.png.png
      │
      ├── __init__.py
      └── __manifest__.py

   .. code-block:: python
      :caption: `models/__init__.py`

      from . import real_estate_property

   .. code-block:: python
      :caption: `__init__.py`
      :emphasize-lines: 1

      from . import models

   .. code-block:: xml
      :caption: `data/real_estate_property_data.xml`
      :emphasize-lines: 3,9,15

      <record id="real_estate.country_house" model="real.estate.property">
          [...]
          <field name="image" type="base64" file="real_estate/static/img/country_house.png"/>
          [...]
      </record>

      <record id="real_estate.loft" model="real.estate.property">
          [...]
          <field name="image" type="base64" file="real_estate/static/img/loft.png"/>
          [...]
      </record>

      <record id="real_estate.mixed_use_commercial" model="real.estate.property">
          [...]
          <field name="image" type="base64" file="real_estate/static/img/mixed_use_commercial.png"/>
          [...]
      </record>

   .. code-block:: python
      :caption: `__manifest__.py`
      :emphasize-lines: 2-11

      'data': [
          # Model data
          'data/real_estate_property_data.xml',

          # Security
          'security/ir.model.access.csv',

          # Views
          'views/actions.xml',
          'views/menus.xml',  # Depends on `actions.xml`
          'views/real_estate_property_views.xml',
      ],

.. _tutorials/server_framework_101/many2one:

Many-to-one
===========

Many2One relationships are the building blocks of data hierarchy in Odoo. They allow you to link a record in one model to a single record in another model. Let's start by adding a Many2One field to our real.estate.property model to connect each property to a property type.

By convention, many2one fields have the _id suffix.

.. note::
   In Odoo, one2one implemented as many2many

ondelete='restrict' or ondelete='cascade'


.. seealso::
   :ref:`Reference documentation for Many2one fields <reference/fields/many2one>`

The type field is not flexible as it prevents adding new property types. Let's replace the field
with a new model to manage property types.

.. exercise::

   #. add property type model with necessary access rights
   #. add menu item (replace Settings with Configuration > Property Types), action, and list-only view
   #. add M2O field on the `real.estate.property` model to the `real.estate.property.type` model
   #. add the field to the form, list and search views of properties

.. spoiler:: Solution

   .. code-block:: python
      :caption: `__manifest__.py`
      :emphasize-lines: 1

      todo

.. exercise::

   #. add M2O field on the `real.estate.property` model to the `res.users` model (salesman)
   #. add M2O field on the `real.estate.property` model to the `res.partner` model (seller/owner)
   #. add notebook page to display new fields

.. spoiler:: Solution

   .. code-block:: python
      :caption: `__manifest__.py`
      :emphasize-lines: 1

      todo

.. _tutorials/server_framework_101/one2many:

One-to-many
===========

One2Many fields are the inverse of Many2One fields. They allow you to display and manage multiple related records from the "one" side of the relationship. Let's add a One2Many field to our real.estate.property.type model to show all properties of a certain type:

By convention, one2many fields have the _ids suffix.

.. seealso::
   :ref:`Reference documentation for One2many fields <reference/fields/one2many>`

.. exercise::

   #. add `real.estate.offer` model with menu item, action, and list and form views (search?)
   #. no menu or action required
   #. add Many2one and One2many fields to connect to the `real.estate.property` model
   #. add the field to the form view of properties in a new Offers notebook page

.. spoiler:: Solution

   .. code-block:: python
      :caption: `__manifest__.py`
      :emphasize-lines: 1

      todo

.. exercise::

   #. add M2O field on the `real.estate.offer` model to the `res.partner` model

.. spoiler:: Solution

   .. code-block:: python
      :caption: `__manifest__.py`
      :emphasize-lines: 1

      todo

.. _tutorials/server_framework_101/many2many:

Many-to-many
============

Many2Many relationships allow for complex associations where multiple records from one model can be linked to multiple records from another model. Let's add a Many2Many field to our real.estate.property model to associate multiple tags with each property:

By convention, many2many fields have the _ids suffix.

.. seealso::
   :ref:`Reference documentation for Many2many fields <reference/fields/many2many>`

.. exercise::

   #. add a `real.estate.tag` model (cozy, renovated...) with `name` and `color` with menu item, action, and list-only views
   #. add M2M field to the `real.estate.property` and `real.estate.tag` models
   #. add the field to the form view of properties with many2many_tags widget

.. spoiler:: Solution

   .. code-block:: python
      :caption: `__manifest__.py`
      :emphasize-lines: 1

      todo

----

Congratulations! You've learned the art of forging connections between your Odoo models. You're now
well-equipped to build complex, interconnected data structures. In the next chapter, we'll
:doc:`add custom business logic to the models <05_business_logic>`, turning your application from a
simple data management tool into a smart, automated system that can handle complex business
processes.
