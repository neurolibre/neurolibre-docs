+++++++++++++++
🧪 Step 1 - Test
+++++++++++++++

Meet ``RoboNeuro``! Our preprint submission bot is at your service 24/7 to help you create a NeuroLibre preprint.

.. image:: https://github.com/neurolibre/brand/blob/main/png/preview_magn.png?raw=true
  :width: 500
  :align: center

We would like to ensure that all the submissions we receive meet certain requirements. To that end, we created a `test page <https://roboneuro.herokuapp.com>`_, 
where you point RoboNeuro to a public GitHub repository, enter your email address, then sit back and wait for the results.

.. note:: RoboNeuro book build process has two stages. First, **it creates a virtual environment** based on your runtime descriptions. If this stage is successful, then it proceeds to 
          build a Jupyter Book by **re-executing your code** in that environment. 

- On a successful book build, we will return you a preprint that is beyond PDF!
- If the build fails, we will send two log files for your inspection.

.. warning:: **A successful book build on RoboNeuro preview service is a prerequisite for submission.**

            Please note that RoboNeuro book preview is provided as a public service with limited computational resources. Therefore we encourage you to build your book locally before
            requesting our online service. Instructions are available `here <#testing-book-build-locally>`_.

⏩ Quickstart: Preprint templates
**********************

To give you a head-start, we created preprint template repositories:

.. list-table::
   :widths: 33 33 33
   :header-rows: 1

   * - Preprint
     - Programming language
     - GitHub repository
   * - Link
     - Python
     - `neurolibre/template <https://github.com/neurolibre/template>`_
   * - Link
     - C++
     - `neurolibre/cpp <https://github.com/neurolibre/binder-cpp>`_

1. Choose your template from the list below and create a new repository into your (or to an organization) account.
2. Follow the instructions in the ``README`` file.
3. Test your book build using `RoboNeuro preview service <https://roboneuro.herokuapp.com>`_.

The following section provides further detail about the structure of a NeuroLibre preprint repository. 

🗂 Preprint repository structure
**********************

We expect to find all the submission material in a **public** GitHub repository that has the following structure:

| neurolibre_submission
| ├── **/binder**
| │   ├── data_requirement.json
| │   ├── requirements.txt
| │   └── ...
| ├── **/content**
| │   ├── _toc.yml
| │   ├── _config.yml
| │   └── ...
| ├── paper.md
| └── paper.bib

📁 The ``binder`` folder
""""""""""""""""""""""""""

.. image:: https://github.com/neurolibre/brand/blob/main/png/binder_folder.png?raw=true
  :width: 800
  :align: left
                  

**⚙️Runtime**
----

.. topic:: 1 - Preprint-specific runtime dependencies

  The execution runtime can be based on any of the (non-proprietary) programming languages supported by Jupyter. NeuroLibre looks at the
  ``binder`` folder to find some configuration files such as a ``requirements.txt`` (Python), ``R.install`` (R), ``Project.toml`` (Julia)
  or a ``Dockerfile``.

.. seealso:: 
  The full list of supported configuration files is available `here <https://mybinder.readthedocs.io/en/latest/using/config_files.html>`_.

.. topic:: 2 - Environment configuration for NeuroLibre

  You should try to make your environment clean and concize, that is why the prefered configuration file for NeuroLibre are the
  ``requirements.txt``.

  It should be small (to keep environment building and loading as short as possible), and versionnized (so your
  environment is fully reproducible, and cache-able).

  For example this requirement is bad because it has lot of unnecessary dependencies:

  .. code-block:: text

    numpy
    scipy
    jupyter
    matplotlib
    Pillow
    scikit-learn
    tensorflow

  On the other hand, this one is concise, reproducible and will take much less time to build:

  .. code-block:: text

    tensorflow==2.4.0

.. topic:: 3 - NeuroLibre dependencies

  Our test server creates a virtual environment in which your content is re-executed to build a Jupyter Book. To enable this, we need some 
  Python packages.

  - If you are using configuration files, we need the following in a ``requirements.txt`` file:

  .. code-block:: text

    jupyter-book
    jupytext

  - If your environment is described by a ``Dockerfile`` you can use our base image: 

  .. code-block:: docker
    :emphasize-lines: 1

    FROM neurolibre/book:latest
    ...

**💽 Data**
----

NeuroLibre offers generous data storage and caching to supercharge your preprint. If your executable content consumes input data, please read this section carefully.

To download data, NeuroLibre looks for a `repo2data <https://github.com/SIMEXP/Repo2Data>`_ configuration file: ``data_requirement.json``. This file must point to a **publicly available
dataset**, so the data will be available at the ``data`` folder during preprint runtime.

.. seealso:: **Repo2data** can download data from several resources including OSF, datalad, zenodo or aws. For details, please visit `repo2data <https://github.com/SIMEXP/Repo2Data>`_, where you can 
            also find the instructions to use ``repo2data`` on your local computer before requesting RoboNeuro preview service.

Example preprint templates using ``repo2data`` for caching data on NeuroLibre servers:

.. list-table::
   :widths: 50 50
   :header-rows: 1

   * - Download Resource
     - GitHub repository
   * - Nilearn
     - `neurolibre/repo2data-nilearn <https://github.com/neurolibre/repo2data-caching>`_
   * - OSF
     - `neurolibre/repo2data-osf <https://github.com/neurolibre/neurolibre-osf-test>`_



.. warning:: 
  RoboNeuro may fail downloading relatively large datasets (**exceeding 5GB**) as the book build process times out in 60 minutes.
  If you have data that exceed 5GB, please create an issue in your github repository so a Neurolibre admin can check it.

.. topic:: Help RoboNeuro find your data during book build

  `Repo2Data <https://github.com/SIMEXP/Repo2Data>`_ downloads your data to a folder named ``data/PROJECT_NAME``, which is created at the base of your repository.
  ``PROJECT_NAME`` is `the field <https://github.com/neurolibre/neurolibre-osf-test/blob/1edcdb82a5c3f52130572f5cd6e4f1386ae3d8e4/binder/data_requirement.json#L3>`_ that you configured in your ``data_requirement.json``.

  - A code cell in a ``content/my_notebook.ipynb`` would access data by:

    .. code-block:: python

      import nibabel as nib
      import os
      img = nib.load(os.path.join('..', 'data', 'PROJECT_NAME', 'my_brain.nii.gz'))

  - A code cell in a ``content/01/my_01_notebook.ipynb`` would access data by:

    .. code-block:: python

      import nibabel as nib
      img = nib.load(os.path.join('..', '..', 'data', 'PROJECT_NAME', 'my_brain.nii.gz')) # In this case, 2 upper directories

  If the data directories in your code cells are not following this convention, RoboNeuro will fail to re-execute your notebooks and interrupt the book build.

.. note:: We suggest testing repo2data locally before you request a RoboNeuro preview service.
          Instructions are available `here <#testing-book-locally>`_. 
          Matching your data loading convention with that of RoboNeuro will increase your chances of having a successful NeuroLibre preprint build.

.. warning:: If you are a Windows user, manually defined paths (e.g. ``.\data\my_data.txt``) won't be recognized by the preprint runtime.
             Please use an operating system agnostic convention to define file paths or file separators. For example use ``os.path.join`` in Python.
        
📁 The ``content`` folder
""""""""""""""""""""""
.. image:: https://github.com/neurolibre/brand/blob/main/png/content_folder.png?raw=true
  :width: 800
  :align: left

**Executable & narrative content**
----

NeuroLibre accepts the following file types to create a preprint that is beyond PDF:

- ✅ Jupyter Notebooks, 
- ✅ `MyST <https://github.com/neurolibre/template/blob/main/content/02-simple-myst.md>`_ formatted markdown.
- ✅ Plain text markdown files.
- ✅ A mixture of all above

.. warning:: ❌  We don't accept markdown files with narrative content **only**, that is not really beyond PDF :)

.. note:: ✅  You can organize your content in sub-folders.

.. topic:: Writing narrative content
   
   Jupyter Book provides you with an arsenal of authoring tools to include citations, equations, figures, special content
   blocks and more into your notebooks or markdown files.
  
  .. seealso:: Please visit the corresponding Jupyter Book `documentation page <https://jupyterbook.org/content/index.html#write-narrative-content>`_ for guidelines.

.. topic:: Writing executable content

   Based on the powerful Jupyter ecosystem, NeuroLibre preprints allow you to interleave computational material with your narrative.
   You can add some directives and metadata to your code cell blocks for Jupyter Book to determine the format and behavior of the outputs,
   such as interactive data visualization.

  .. seealso:: Please visit the corresponding Jupyter Book `documentation page <https://jupyterbook.org/execute/index.html#write-executable-content>`_ for guidelines.

There are two **mandatory** files that we look for in the ``content`` folder: ``_config.yml`` and ``_toc.yml``. These files 
help RoboNeuro structure your book and configure some settings.

**⑆Table of contents**
----

The ``_toc.yml`` file determines the structure of your NeuroLibre preprint. It is a simple configuration file 
specifying a table of content from all the executable & narrative content found in the ``content`` folder (and in subfolders).

.. seealso:: The complete reference for the ``_toc.yml`` can be found `here <https://jupyterbook.org/customize/toc.html>`_.

**⚡︎Book configuration**
----

The ``_config.yml`` file governs all the configuration options for your Jupyter Book formatted preprint, such as adding a logo, 
enable/disable interactive buttons or control notebook execution and caching settings. Few important points:

- Please ensure that the title and the list of authors matches those specified in the ``paper.md``.

 .. code-block:: yaml

   title:  "NeuroLibre preprint template"  # Add your title
   author: John Doe, Jane Doe  # Add author names

- Please ensure that the repository address is accurate.

 .. code-block:: yaml

   repository:
     url: https://github.com/username/reponame  # The URL to your repository

.. seealso:: The complete reference for the ``_config.yml`` can be found `here <https://jupyterbook.org/customize/config.html>`_.

📝 Static summary
""""""""""""""""""""""""""
.. image:: https://github.com/neurolibre/brand/blob/main/png/paper.png?raw=true
  :width: 800
  :align: left

The front matter of ``paper.md`` is used to collect meta-information about your preprint:

.. code-block:: yaml

  ---
  title: 'White matter integrity of developing brain in everlasting childhood'
  tags:
    - Tag1
    - Tag2
  authors:
    - name: Peter Pan
      orcid: 0000-0000-0000-0000
      affiliation: "1, 2"
    - name: Tinker Bell
      affiliation: 2
  affiliations:
  - name: Fairy dust research lab, Everyoung state university, Nevermind, Neverland
    index: 1
  - name: Captain Hook's lantern, Pirate academy, Nevermind, Neverland
    index: 2
  date: 08 September 1991
  bibliography: paper.bib
  ---

The corpus of this static document is intended for a big picture summary of the preprint
generated by the executable and narrative content you provided (in the ``content``) folder. You can include citations
to this document from an accompanying BibTex bibliography file ``paper.bib``.

To check if your PDF compiles, visit RoboNeuro `preprint preview page <https://roboneuro.herokuapp.com>`_, select `NeuroLibre PDF` option and enter your repository address.

.. seealso:: For more information on how to format your paper, please `take a look at JOSS documentation <https://joss.readthedocs.io/en/latest/submitting.html#example-paper-and-bibliography>`_.


💻 Testing book build locally
******************************

Assuming that:

- you already installed all the dependencies to develop your notebooks locally 
- your preprint repository follows the `NeuroLibre preprint structure <#preprint-repository-structure>`_

you can easily test your preprint build locally.

**Step 1 - Install Jupyter Book**

 .. code-block:: shell

    pip install jupyter-book

**Step 2 - Data**

First, make sure that your data is available online and can be downloaded publicly.

You can now install `Repo2Data <https://github.com/SIMEXP/Repo2Data>`_, and configure the ``data_requirement.json`` with ``"dst": "./data"``.
Navigate into your repo and run repo2data, your data will download to the data folder.

Finally, modify respective code lines in your notebooks to set data (relative) path to the data folder.

.. warning:: Please make sure that you ignore your local ``./data`` folder and all of its contents from Git history, by adding the following in the ``.gitignore`` file:
               
            .. code-block:: text

                 data/

**Step 3 - Book build**

- Navigate to the repository location in a terminal

 .. code-block:: shell

    cd /your/repo/directory

- Trigger a jupyter book build

 .. code-block:: shell

    jupyter-book build ./content

.. seealso:: Please visit reference `documentation <https://jupyterbook.org/content/execute.html?highlight=execute#execute-and-cache-your-pages>`_ on executing and caching your outputs during a book build.

+++++++++++++++
💌 Step 2 - Submit
+++++++++++++++

.. warning:: **A successful book build on RoboNeuro preview service is a prerequisite for submission.**

           Your submission request will go through only if we can find a built preprint on our test server for your preprint repository.

Submission is as simple as:

- Filling in the short submission form at `NeuroLibre web page <https://neurolibre.herokuapp.com>`_
- Waiting for the managing editor to start a pre-review issue over in the NeuroLibre reviews repository: https://github.com/neurolibre/neurolibre-reviews

+++++++++++++++
🔎 Technical screening
+++++++++++++++

Our editorial team will start a technical screening process on GitHub to ensure the functionality of your preprint.