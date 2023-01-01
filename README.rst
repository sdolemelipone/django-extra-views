|build| |codecov| |docs-status|

Django Extra Views - The missing class-based generic views for Django
========================================================================

Django-extra-views is a Django package which introduces additional class-based views
in order to simplify common design patterns such as those found in the Django
admin interface.

Supported Python and Django versions: Python 3.5+, Django 2.2-4.1,
see `tox.ini <https://github.com/AndrewIngram/django-extra-views/blob/master/tox.ini>`_ for an up-to-date list.

Full documentation is available at `read the docs`_.

.. _read the docs: https://django-extra-views.readthedocs.io/

.. |build| image:: https://github.com/AndrewIngram/django-extra-views/workflows/Tests/badge.svg
    :target: https://github.com/AndrewIngram/django-extra-views/
    :alt: Build Status

.. |codecov| image:: https://codecov.io/github/AndrewIngram/django-extra-views/coverage.svg?branch=master
    :target: https://codecov.io/github/AndrewIngram/django-extra-views?branch=master
    :alt: Coverage Status

.. |docs-status| image:: https://readthedocs.org/projects/django-extra-views/badge/?version=latest
    :target: https://django-extra-views.readthedocs.io/
    :alt: Documentation Status

.. installation-start

Installation
------------

Install the stable release from pypi (using pip):

.. code-block:: sh

    pip install django-extra-views

Or install the current master branch from github:

.. code-block:: sh

    pip install -e git://github.com/AndrewIngram/django-extra-views.git#egg=django-extra-views

Then add ``'extra_views'`` to your ``INSTALLED_APPS``:

.. code-block:: python

    INSTALLED_APPS = [
        ...
        'extra_views',
        ...
    ]

.. installation-end

.. features-start

Features
--------

- ``FormSet`` and ``ModelFormSet`` views - The formset equivalents of
  ``FormView`` and ``ModelFormView``.
- ``InlineFormSetView`` - Lets you edit a formset related to a model (using
  Django's ``inlineformset_factory``).
- ``CreateWithInlinesView`` and ``UpdateWithInlinesView`` - Lets you edit a
  model and multiple inline formsets all in one view.
- ``GenericInlineFormSetView``, the equivalent of ``InlineFormSetView`` but for
  ``GenericForeignKeys``.
- Support for generic inlines in ``CreateWithInlinesView`` and
  ``UpdateWithInlinesView``.
- Support for naming each inline or formset in the template context with
  ``NamedFormsetsMixin``.
- ``SortableListMixin`` - Generic mixin for sorting functionality in your views.
- ``SearchableListMixin`` - Generic mixin for search functionality in your views.
- ``SuccessMessageMixin`` and ``FormSetSuccessMessageMixin`` - Generic mixins
  to display success messages after form submission.

.. features-end

Still to do
-----------

Add support for pagination in ModelFormSetView and its derivatives, the goal
being to be able to mimic the change_list view in Django's admin. Currently this
is proving difficult because of how Django's MultipleObjectMixin handles pagination.

.. quick-examples-start

Quick Examples
--------------

FormSetView
^^^^^^^^^^^^^^^^^^^^^^^

Define a :code:`FormSetView`, a view which creates a single formset from
:code:`django.forms.formset_factory` and adds it to the context.

.. code-block:: python

    from extra_views import FormSetView
    from my_forms import AddressForm

    class AddressFormSet(FormSetView):
        form_class = AddressForm
        template_name = 'address_formset.html'

Then within ``address_formset.html``, render the formset like this:

.. code-block:: html

    <form method="post">
      ...
      {{ formset }}
      ...
      <input type="submit" value="Submit" />
    </form>

ModelFormSetView
^^^^^^^^^^^^^^^^^^^^

Define a :code:`ModelFormSetView`, a view which works as :code:`FormSetView`
but instead renders a model formset using
:code:`django.forms.modelformset_factory`.

.. code-block:: python

    from extra_views import ModelFormSetView


    class ItemFormSetView(ModelFormSetView):
        model = Item
        fields = ['name', 'sku']
        template_name = 'item_formset.html'

CreateWithInlinesView or UpdateWithInlinesView
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Define :code:`CreateWithInlinesView` and :code:`UpdateWithInlinesView`,
views which render a form to create/update a model instance and its related
inline formsets. Each of the :code:`InlineFormSetFactory` classes use similar
class definitions as the :code:`ModelFormSetView`.

.. code-block:: python

    from extra_views import CreateWithInlinesView, UpdateWithInlinesView, InlineFormSetFactory


    class ItemInline(InlineFormSetFactory):
        model = Item
        fields = ['sku', 'price', 'name']


    class ContactInline(InlineFormSetFactory):
        model = Contact
        fields = ['name', 'email']


    class CreateOrderView(CreateWithInlinesView):
        model = Order
        inlines = [ItemInline, ContactInline]
        fields = ['customer', 'name']
        template_name = 'order_and_items.html'


    class UpdateOrderView(UpdateWithInlinesView):
        model = Order
        inlines = [ItemInline, ContactInline]
        fields = ['customer', 'name']
        template_name = 'order_and_items.html'


Then within ``order_and_items.html``, render the formset like this:

.. code-block:: html

    <form method="post">
      ...
      {{ form }}

      {% for formset in inlines %}
        {{ formset }}
      {% endfor %}
      ...
      <input type="submit" value="Submit" />
    </form>

.. quick-examples-end


Contributing
--------------

The project welcomes PR's to fix bugs and extend functionality.

Fork the repository, check out locally and create a local virtual environment.

Run the tests first to ensure that they pass.

Please add your details to the ``AUTHORS.rst`` file along with your changes.

Running tests
^^^^^^^^^^^^^

To run tests install tox using

.. code-block::

    pip install tox

..

Then run tests against all support Python versions:

.. code-block::

    tox

..

To run only a subset of the tests, e.g. only Python 3.10, run:

.. code-block::

    tox -e py310

..

See the ``tox.ini`` file for details of specific test suite names.

Tests will fail for any versions of Python which are not installed on the local machine.

Previewing documentation
^^^^^^^^^^^^^^^^^^^^^^^^

Documentation is generated when running the full tox test suite. The output can
be previewed in ``docs/_build``.

To build the documentation separately, run

.. code-block::

    tox -e docs

..


Linting & pre-commit
^^^^^^^^^^^^^^^^^^^^

The test suite will fail if any of the ``black``, ``isort`` or ``flake8``
formatter/linter tasks find errors.

Optionally, you can install ``pre-commit`` to run them automatically before committing.

In your local environment, run the commands below:

.. code-block::

    pip install pre-commit
    pre-commit install

..

Your code will now automatically be re-formatted before the commit is made.
