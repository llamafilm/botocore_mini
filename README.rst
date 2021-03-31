botocore
========
A minified version of Amazon's botocore.

Purpose
-------
I like writing small Python apps to interact with AWS services using ``boto3``.  Unfortunately this package depends on ``botocore`` which is extremely large because it supports hundreds of services, as well as maintaining old API versions for backwards compatibility.  I only need a tiny portion of this module, so I created a "minified" version which reduces app size and launch time.  This is especially helpful when packaging a single file binary with PyInstaller because it has to unpack all the files every time you launch.

This build currently includes the following services which are useful to me:

- ec2
- iam
- pricing
- s3
- sesv2
- sns
- sts

Impact
------
Here is a simple program to compare performance of the minified package against the regular botocore.

.. code-block:: python

    import boto3
    s3 = boto3.client('s3')
    print('Finished!')

I package this as a single file binary with ``pyinstaller -F boto-speed.py``.  On Windows, the execution time is 67% faster and the binary is reduced from 9.6MB to 8.1MB.

.. code-block:: powershell

    (Measure-Command {boto-speed-mini.exe}).TotalSeconds
    3.3924868
    
    (Measure-Command {boto-speed.exe}).TotalSeconds
    10.4119532

On macOS the execution time is 45% faster and the binary is reduced from 16.4MB to 7.9MB.

.. code-block::

    $ /usr/bin/time boto-speed-mini 
    Finished!
            0.58 real         0.24 user         0.12 sys
    $ /usr/bin/time boto-speed
    Finished!
            1.05 real         0.39 user         0.46 sys

Usage
-----
I recommend installing inside a virtual environment, and also install ``PyInstaller`` there.  I kept the package name and version unchanged so you can install ``boto3`` the usual way and it will recognize this automatically.

.. code-block:: bash

    python3 -m venv env
    source env/bin/activate
    pip install git+https://github.com/llamafilm/botocore_mini.git
    pip install boto3

In Python, you can validate it's working

.. code-block:: python

    >>> import botocore.session
    >>> session = botocore.session.get_session()
    >>> print(session.get_available_services())

More details about the package itself are `upstream <https://github.com/boto/botocore>`__.
