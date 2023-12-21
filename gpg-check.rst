Verifying GPG Signature of Electrum-Dime Using Linux Command Line
============================================================

This can be used to verify the authenticity of Electrum-Dime binaries/sources.

Download only from dimecoinnetwork.com and remember to check the gpg signature again every time you download a new version

Obtain Public GPG Key for Douglas Hopping (Dhop14)
---------------------------------

In a terminal enter (or copy):

.. code-block:: bash

   gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "A3E6459E3707BC46849AC0AA964DA787DBC83054" 
   
You should be able to substitute any public GPG keyserver if pgp.mit.edu is (temporarily) not working

Download Electrum-Dime and Signature File (asc)
------------------------------------------

Download the Python Electrum-Dime-<version>.tar.gz or AppImage File 

Right click on the signature file and save it as well

Verify GPG Signature
--------------------

Run the following command from the same directory you saved the files replacing <electrum file> with the one actually downloaded:

.. code-block:: bash

   gpg --verify <electrum file>.asc <electrum file>

The message should say:

.. code-block:: bash

  Good signature from "DHop14" <douglas@dimecoinnetwork.com>

and 

.. code-block:: bash

  Primary key fingerprint: A3E6 459E 3707 BC46 849A C0AA 964D A787 DBC8 3054

You can ignore this:

.. code-block:: bash

  WARNING: This key is not certified with a trusted signature!
  gpg:          There is no indication that the signature belongs to the owner.

as it simply means you have not established a web of trust with other GPG users
