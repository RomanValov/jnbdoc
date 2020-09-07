Task 1
======

Hi, Erik. In this document I will guide you step-by-step into process of creating and deploying docs example to RTD.
As you've requested the docs will have Jupyter Notebook.

Step 1. Overview
~~~~~~~~~~~~~~~~

The RTD service is a document-hosting service for GitHub-hosted projects.
It's free and it's only supposed to host documentation projects, not custom sites or files.

RTD is built on top of widely-used Sphinx project ( https://www.sphinx-doc.org/en/master/ ).
Sphinx is documentation generator, it produces documentation in various formats (primarily html) from templates files.

Step 2. Templates
~~~~~~~~~~~~~~~~~

Sphinx templates are just text files formatted using RST markup language.
RST is similar to Markdown (markup language used in GitHub readme files or StackOverflow posts).

To get the idea of RST and learn basic constructs please read following doc:

https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html

It's actually possible to use Markdown to write tempates for Sphinx however I've never used this possibility before.

Step 3. Installation
~~~~~~~~~~~~~~~~~~~~

At first you have to install the Sphinx document generator on your system. If you're using Ubuntu run::

    sudo apt-get install python3-sphinx

Alternatively you could use ``pip3`` command to install ``sphinx`` package from PyPI repository::

    sudo pip3 install sphinx

Please note that there is python2 and python3 version of Sphinx could be available in your repository.
You should prefer the same version as your primary project uses (Sphinx analyzes your python code to build documentation).
Also please ensure you have only one version installed because you could get conflicts in ohter case.

Once installation completed, go to your source code directory and run::

    sphinx-quickstart

This wizard will ask you several questions about your project and configuration options.
It's safe to keep with defaults for all the configuration options, just ensure to set proper project name, author and version.

Take a minute to consider the structure of your sources. You should keep your \*.ipynb files in the root of documentation.

Step 4. Build docs
~~~~~~~~~~~~~~~~~~

When wizard completes you should find following files in your repository::

     conf.py (configuration)
     index.rst (home page template)
     Makefile (build scripts for Linux)
     make.bat (build scripts for Windows)

Directory could contain other files and subdirectories but they're not importrant.

In order to build html version of your docs run following command::

     make html

This command will generate html docs in ``_build/html`` subdirectory (if you haven't changed the defaults)
Now you can navigate with your file explorer to this directory and open ``index.html`` file in your browser.
If you're on Ubuntu you could run following command without leaving the console::

     xdg-open _build/html/index.html

Step 5. Notebooks extension
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Hope everything is working fine and you were able to see your documentation stub in the browser.
You could freely try various formatting constructs according to previously mentioned RST primer.
But for the sake of simplicity I will continue to show you how to integrate Jupyter Notebooks.

Sphinx is extensible software and in order to render Jupyter Notebooks you have to install ``nbsphinx`` extension.

If you're on Ubuntu you should run::

    sudo apt-get install python3-nbsphinx

Alternatively you could install PyPI version of the package::

    sudo pip3 install nbsphinx

Now you should enable the extension in the ``conf.py``. Open the file with your favorite editor and find ``extensions`` stanza. Add ``'nbsphinx'`` to the list of extensions, i.e.::

    extensions = ['nbsphinx']

Step 6. Rendering notebooks
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now it's time to add your notebook to the docs. Make sure you have you \*.ipynb file in the same directory with ``index.rst``.
Open ``index.rst`` with your favorite editor.
By default the auto-generated file has Table of Contents and several standard links.
You should modify table of contents and add the name of your \*.ipynb file to the list.
The \.ipynb extension should be omitted. Also make sure your entry is idented on the same level as colon-marked stanzas::

    .. toctree::
        :maxdepth: 2
        :caption: Contents:

        maps

As you see in my case I've added ``maps`` entry to the list.
It's actually a copy of ``python_maps_example.ipynb`` from your repository renamed for the sake of convenience.

Once ready please run the build again and check results in your browser.
Based on the ``maps`` file contents found in your repository you will get index page with pair of links on it.
Each of the links will point to the sub-section in newly created ``maps.html`` file built from your notebook.

The same way you could freely use arbitrary \*.ipynb file instead of RST-file, even instead of ``index.rst``.
However you have to delete ``index.rst`` file in latter case because \*.rst files are prioritized by Sphinx.

Step 7. Moving to GitHub
~~~~~~~~~~~~~~~~~~~~~~~~

If everything is working fine locally it's time to move to public hosting.
In order to do that you should commit and upload your files to your GitHub repository.

The following files should be commited and pushed to the repository::

    index.rst <your-noteook-file>.ipynb conf.py

As of ``Makefile`` and ``make.bat`` -- they're just convenient wrappers for local builds and not required for RTD.

You could check that GitHub will render not only \*.ipynb files in it's web-interface, but also \*.rst files.

Step 8. Read the Docs
~~~~~~~~~~~~~~~~~~~~~

When your files are available on GitHub it's time to register an account on ReadTheDocs and link your GitHub repository.

Go to https://readthedocs.org/accounts/login/ and press the ``Sign in with GitHub`` button.

In the profile page of ReadTheDocs you will find ``Import project`` button, use it and select your repository from the list.

Once imported all the machinery should be set up by ReadTheDocs to start build and set up rebuild on each commit to your repo.

Please take a time to get familiar with ReadTheDocs interface.

In general it's usefull to be able to check the status of the last build and view the build logs.

Step 9. Notebooks on Read the Docs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default ReadTheDocs is not configured to use Notebooks extension previously used for local build.

In order to change the limitations you have to add pair of configuration files to your repository.

At first, add the ``requirements.txt`` file to the same dir where you have ``index.rst`` located and add following lines::

    ipykernel
    nbsphinx

These lines will instruct ReadTheDocs build to download packages from the PyPI archive.

On your local setup ``ipykernel`` is usually installed as a dependency for Jupyter
and ``nbsphinx`` was installed as a part of the tutorial.

At second, you have to add configuration file for ReadTheDocs service itself which relies on the ``requirements.txt`` defined.
Configuration file for ReadTheDocs should be named ``.readthedocs.yml`` and should be located in top dir of your repository::

    version: 2
    formats: all
    python:
      version: 3
      install:
      - requirements: docs/requirements.txt
    system_packages: true

As you see in my case the version of Python interpreter is set to 3 and ``requirements.txt`` is located in ``docs`` subdir.

Once files added do a commit and push to your repository, the ReadTheDocs will do the rebuild in a while.

Step 10. Themming
~~~~~~~~~~~~~~~~~

Sphinx supports themming. In my case Sphinx tools bundled with the distro are patched to use Alabaster theme by default.

In order to force your documentation to use particular theme it should be configured via ``html_theme`` paramater.

For example to use default ReadTheDocs theme you have to set ``html_theme='sphinx_rtd_theme'`` in your configuration file.

Being default for ReadTheDocs it will be handled automatically on ReadTheDocs service.
However if you wish to give it a try locally you have to install theme's python package::

    sudo pip3 install sphinx-rtd-theme
