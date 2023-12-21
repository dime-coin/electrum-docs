libsecp256k1 on Linux
=========================

Notes about getting libsecp256k1 to work with Electrum-Dime on Linux.


1. Using Package Manager
~~~~~~~~~~~~~~~~~~~~~~~~

On recent versions of Ubuntu/Debian, libsecp256k1 should be available
through the package manager:

::

    sudo apt-get install libsecp256k1-0


Electrum-Dime should be able to find the ``.so`` file and use it.


2. How Library is Searched
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The command Electrum-Dime uses to find the library, on Linux, is the following:

::

    import ctypes; ctypes.cdll.LoadLibrary('libsecp256k1.so.0')


If the library does not seem to be picked up by Electrum-Dime, consider
opening a python interpreter to see for yourself.


3. Compiling libsecp256k1 Yourself
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you compile from source, the ``.so`` files are typically placed in
``/usr/local/lib``.

Unfortunately, ``ctypes.cdll.LoadLibrary`` does not seem to find them there.

Two Workarounds:

Set LD_LIBRARY_PATH env var
^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can set the LD_LIBRARY_PATH environment variable.
Start Electrum-Dime like this:

::

    LD_LIBRARY_PATH=/usr/local/lib electrum


Symlink .so file
^^^^^^^^^^^^^^^^

You can symlink the ``.so`` file. ``/usr/lib`` seems to work.

::

    sudo ln -s /usr/local/lib/libsecp256k1.so.0 /usr/lib/libsecp256k1.so.0

