Maintaining Your Sanity
=================================================

While Maintaining Your Open Source App
-------------------------------------------------

Mark Lavin

----

What This Talk is About
-------------------------------------------------

Things that you won't find in the Django docs like

- Packaging an app
- Documentating an app
- Testing an app

----

Who Am I?
-------------------------------------------------

- Developer at Caktus Group in Carrboro, NC

.. image:: ./images/caktus-logo.png
    :align: center

- Founder/Developer for brewedbyus.com

.. image:: ./images/bbus-logo.png
    :align: center

----

Apps I've Open Sourced
-------------------------------------------------

- django-selectable
- django-email-bandit
- django-ad-code
- django-all-access
- django-scribbler
- django-responsive
- django-hilbert
- django-lastfm-auth
- django-meetup-auth

----

Non-Work Me
-------------------------------------------------

- Husband/Dad
- Runner/Triathlete
- Homebrewer

Presenter Notes
---------------

- This is also about how to do it all

----

Packaging
-------------------------------------------------

.. image:: ./images/1411998505_20c38def70_z.jpg
    :align: center

----

Packaging
-------------------------------------------------

- It can seem complicated the first time
- It doesn't need to be perfect
- It just needs to work

----

Setup.py
-------------------------------------------------

.. code-block:: python

    from setuptools import setup, find_packages

    setup(
        name='django-something',
        version=__import__('something').__version__,
        author='Mark Lavin',
        author_email='mlavin@caktusgroup.com',
        packages=find_packages(),
        include_package_data=True,
        url='https://github.com/mlavin/django-something',
        license='BSD',
        description=u' '.join(__import__('something').__doc__.splitlines()).strip(),
        classifiers=[
            'Topic :: Internet :: WWW/HTTP :: Dynamic Content',
            'Intended Audience :: Developers',
            'License :: OSI Approved :: BSD License',
            'Programming Language :: Python',
            'Programming Language :: Python :: 2.6',
            'Programming Language :: Python :: 2.7',
            'Framework :: Django',
            'Topic :: Software Development :: Libraries :: Python Modules',
            'Development Status :: 4 - Beta',
            'Operating System :: OS Independent',
        ],
        long_description=open('README.rst').read(),
        zip_safe=False, # If you have static resources
    )

----

Application Version
-------------------------------------------------

- Follow PEP386
- Try to be consistent in what your versions mean (new features/bug fixes)

.. code-block:: python

    # something.__init__.py
    "Short description of django-something."

    __version__ = '1.0.0'

----

MANIFEST.in
-------------------------------------------------

- Includes non-Python resources in your source distribution

.. code-block:: python

    include README.rst
    recursive-include something/static *
    recursive-include something/templates *

----

Register and Upload Your Package
-------------------------------------------------

.. code-block:: bash

    python setup.py register
    python setup.py sdist upload
    # Write more code
    # Write more docs
    # Update version
    python setup.py sdist upload

That's it!

----

Documentation
-------------------------------------------------

.. image:: ./images/4294079_e959b6104d.jpg
    :align: center

----

Getting Started with Sphinx
-------------------------------------------------

- A README is not documentation
- Auto-docs are not documentation
- Also don't need to be perfect but you need a starting point

Presenter Notes
---------------

- People will help you write better docs but they won't start them for you

----

Laying Out Your Docs
-------------------------------------------------

``sphinx-quickstart`` has fairly sane defaults

.. code-block:: bash

    $ sphinx-quickstart
    ...
    Enter the root path for documentation.
    > Root path for the documentation [.]: docs
    ...

----

Things to Document
-------------------------------------------------

- A description of the project and its goals
- How to install including requirements
- How to configure
- Release notes

----

Hosting Your Docs on Read the Docs
-------------------------------------------------

- Create an account
- Link to your repo
- Setup post-commit hook
- Pulls your tags/branches for different versions

Presenter Notes
---------------

- Django's docs are built here too

----

Testing
-------------------------------------------------

.. image:: ./images/6946913449_e8ac6ff7d7_z.jpg
    :align: center

----

Running Your App Tests
-------------------------------------------------

- Running tests needs to be easy
- Running tests needs to be fast

Presenter Notes
---------------

Or else no one (including you) will run them

----

Sample Project Anti-Pattern
-------------------------------------------------

- Tests should ship with your app
- An example project should not

...Therefore your tests must run without it

Presenter Notes
---------------

- Therefore your tests must run without it
- People seem to do this because they need models

----

Test Only Models
-------------------------------------------------

`Ticket #7835 <https://code.djangoproject.com/ticket/7835>`_

    ...it appears to me that we already have a pretty good working solution for test-only models in trunk (and I'm wondering why I never thought of it). Apparently you can simply define models directly in your tests.py. Syncdb never imports tests.py, so those models won't get synced to the normal db, but they will get synced to the test database, and can be used in tests.

    -- Carl Meyer (Comment #24)

This approach is already used for Django's own test suite in ``contrib.contenttypes``

Presenter Notes
---------------

- This is used by django-selectable
- If this changes you'll know because you're going to have a test suite

----

Running App Tests (runtests.py)
-------------------------------------------------

.. code-block:: python

    #!/usr/bin/env python
    import sys
    from django.conf import settings

    if not settings.configured:
        settings.configure(
            DATABASES={
                'default': {
                    'ENGINE': 'django.db.backends.sqlite3',
                    'NAME': ':memory:',
                }
            },
            INSTALLED_APPS=(
                'something', # Don't forget dependencies
            ),
            SECRET_KEY='something-secret',
            SITE_ID=1,
            ROOT_URLCONF='something.tests.urls', # If needed
        )

    from django.test.utils import get_runner

    def runtests():
        TestRunner = get_runner(settings)
        test_runner = TestRunner(verbosity=1, interactive=True, failfast=False)
        sys.exit(test_runner.run_tests(['something', ]))

    if __name__ == '__main__':
        runtests()

----

Supercharge Your Tests with Tox
-------------------------------------------------

Install tox

.. code-block:: bash

    pip install tox

- Tox uses virtualenv to run a test matrix
- Test different Python versions
- Test different Django versions
- Test different DB backends
- http://tox.readthedocs.org/

----

Basic Tox Configuration
-------------------------------------------------

Configure tox.ini

.. code-block:: guess

    [tox]
    downloadcache = {toxworkdir}/_download/
    envlist = py26-1.4.X,py26-1.3.X

    [testenv]
    commands = {envpython} runtests.py

    [testenv:py26-1.4.X]
    basepython = python2.6
    deps = django>=1.4,<1.5

    [testenv:py26-1.3.X]
    basepython = python2.6
    deps = django>=1.3,<1.4

----

Running Tox
-------------------------------------------------

.. code-block:: bash

    # All environments
    $ tox
    ...
    [TOX] py26-1.4.X: commands succeeded
    [TOX] py26-1.3.X: commands succeeded
    # Only 1.4 on Python 2.6
    $ tox -e py26-1.4.X


Presenter Notes
---------------

- You can also use tox to build your documentation

----

App Template
-------------------------------------------------

- Django 1.4 added app templates for ``startapp``
- I've created one using these ideas
- Use it/fork it for your next app
- https://github.com/mlavin/django-app-template

Presenter Notes
---------------

- I've made it easy for you so you have no excuse

----

Why Bother?
-------------------------------------------------

- These tools make it easy on you to write better code and docs
- And make it easier for others to help you
- Give contributors a starting point for more docs and tests

----

Non-Code This To Do
-------------------------------------------------

.. image:: ./images/3944131005_d0563d76ac.jpg
    :align: center

----

State Your Goals
-------------------------------------------------

- Let people know the problem you were trying to solve
- Let people know the problems you aren't interested in solving

----

Include a License
-------------------------------------------------

- There are plenty of good ones, just pick one
- Let people know what they can (and can't) do with the code

----

Prepare for The Future
-------------------------------------------------

- Be ready for new Django releases
- Be ready for Python 3
- Be ready to be replaced (yourself or your code)

----

Non-Code This To Avoid
-------------------------------------------------

.. image:: ./images/3415590901_b3fb25fcca.jpg
    :align: center

----

Rejecting Every Contribution
-------------------------------------------------

- Don't make it impossible for people to help you
- If you have to reject a request be nice

----

"This needs tests and docs"
-------------------------------------------------

.. image:: ./images/3qkg59.jpg
    :align: center

Presenter Notes
---------------

- Not everyone is good a writing tests or docs
- Might need help or direction not heckling

----

Accepting Every Contribution
-------------------------------------------------

- Adding features is easy but taking them away is hard
- If you accept it you should be prepared to maintain it

----

Developer Burnout
-------------------------------------------------

- It's ok to step away for awhile
- If you have these tools in place others can pick up in your place

Presenter Notes
---------------

- These things should make it easier
- Don't let open source feel like a burden

----

Info
-------------------------------------------------

Slides

- HTML: http://mlavin.github.com/sanity-talk/
- Source: https://github.com/mlavin/sanity-talk

Me

- Github: https://github.com/mlavin/
- Bitbucket: https://bitbucket.com/mlavin/

----

Photo Credits
-------------------------------------------------

- http://www.flickr.com/photos/feesta/1411998505/
- http://www.flickr.com/photos/plindberg/4294079/
- http://www.flickr.com/photos/snre/6946913449/
- http://www.flickr.com/photos/booleansplit/3944131005/
- http://www.flickr.com/photos/jmarty/3415590901/

