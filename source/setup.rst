.. _setup:

Download & Setup
================

There are two ways to install oTree that you can choose from:

*   The "plain install" lets you run oTree from the command line.
*   The "launcher install" provides a graphical interface for running the oTree
    server.

If you encounter any error during installation, please email chris@otree.org
with the error message.

Prerequisite: Python 2.7 (not 3.x)
----------------------------------

*   On Windows: download and install
    `Python 2.7 <https://www.python.org/downloads/>`__. (oTree does not work with Python 3.)
    Then add Python to
    your ``Path`` environment variable:

    *   Open the Windows Start menu
    *   Search for "Edit the system environment variables", and then click it.
    *   Click ``Environment Variables``
    *   Select ``Path`` in the ``System variables`` section
    *   Click ``Edit``
    *   Add ``;C:\Python27;C:\Python27\Scripts`` to the end of the list
        (the paths are separated by semicolons). For example:
        ``C:\Windows;C:\Windows\System32;C:\Python27;C:\Python27\Scripts``
    *   (This assumes that Python was installed to ``C:\Python27``.)

*   On Mac/Unix, it is very likely that Python is already installed. You can
    check by opening the Terminal and writing ``python`` and hit Enter.

    If you get something like ``-bash: python: command not found`` you
    will have to install it yourself.

.. note::

    **Windows/Mac:** Verify that it worked by opening your command prompt and
    entering ``python``. You should see the "``>>>``" prompt.


Launcher install
----------------

-   Download the launcher from this link:

    .. only:: html

        .. raw:: html

            <div id="otree-launcher-dynamic-link">
                <img src="_static/loading.gif">
            </div>

    .. only:: not html

        `oTree_launcher-stable.zip <https://github.com/oTree-org/otree-launcher/archive/master.zip>`_

-   Unzip it to an easy-to-access location, like your "Documents" folder.
-   Run ``otree.py`` (or, in older versions, ``otree.sh`` (Mac) or ``otree.bat`` (Windows)).
-   Initial setup may take 5-10 minutes.
-   When the app window launches, click the buttons to create a new deploy and
    choose a location to store your project files.
-   Click the "run server" button
-   Note: the oTree launcher is not installed as an app in your Windows start
    menu or Mac Applications. To reopen the launcher, simply double click
    otree.py again.


Plain install
-------------

This is an alternative to the "launcher install".

*   From your command line, run the command ``pip`` to check if Pip is installed.
    If not, you can download it
    `here <https://pip.pypa.io/en/latest/installing.html>`__.
*   Download `oTree <https://github.com/oTree-org/oTree/archive/master.zip>`__
    and unzip it to a convenient location (such as your "Documents" folder).
    (Or better yet, use Git to clone
    `this repo <https://github.com/oTree-org/otree>`__.)
*   In your command line, go to the root directory of the unzipped folder
    where ``requirements_base.txt`` is

    .. note::

        if you cannot find ``requirements_base.txt``
        make sure you have downloaded ``oTree-master.zip``, not
        ``otree-launcher-master.zip``, which is a different download.

*   Run these commands (you may need administrator permissions):

.. code-block:: bash

    $ pip install -r requirements_base.txt

On Mac, you may need to use ``sudo``:

.. code-block:: bash

    $ sudo pip install -r requirements_base.txt

Then run:

    $ otree resetdb
    $ otree runserver


Explanation: oTree & Django
---------------------------

oTree is built on top of Django.

The ``oTree`` folder is a Django project, as explained
`here <https://docs.djangoproject.com/en/1.8/intro/tutorial01/#creating-a-project>`__.

It comes pre-configured with all the files,
settings and dependencies so that it works right away.
You should create your apps inside this folder.

If you want, you can delete all the existing example games
(like ``asset_market``, ``bargaining``, etc).
Just delete the folders and the corresponding entries in ``SESSION_CONFIGS``.
Just keep the directories ``_static`` and ``_templates``.

When you install oTree (either using the launcher or running
``pip install -r requirements_base.txt``),
``otree-core`` gets automatically installed as a dependency.

Upgrading/reinstalling oTree
----------------------------

There are several alternatives for upgrading or reinstalling oTree.

(TODO: when to use which)

.. _upgrade-otree-core:

Upgrade oTree core libraries
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the launcher, click "Version select" and select the most recent version in
the menu.

If you are using the "plain install", change the ``otree-core`` version number
in ``requirements_base.txt`` and then run:

.. code-block:: bash

    $ pip install -r requirements_base.txt


From-scratch reinstallation
~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  On Windows: Browse to ``%APPDATA%`` and delete the folder
   ``otree-launcher``
-  On Mac/Linux: Delete the folder ``~/.otree-launcher``
-  Re-download the launcher
