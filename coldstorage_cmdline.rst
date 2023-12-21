Using Cold Storage with the Command Line
========================================

This page will show you how to sign a transaction with
an offline Electrum-Dime wallet, using the Command line.

Create an Unsigned Transaction
------------------------------

With your online (watching-only) wallet, create an
unsigned transaction:

.. code-block:: bash

   electrum payto 1Cpf9zb5Rm5Z5qmmGezn6ERxFWvwuZ6UCx 0.1 --unsigned > unsigned.txn

The unsigned transaction is stored in a file named 'unsigned.txn'.
Note that the --unsigned option is not needed if you use a
watching-only wallet.

You may view it using:

.. code-block:: bash

   cat unsigned.txn | electrum deserialize -

Sign the Transaction
--------------------

The serialization format of Electrum-Dime contains the master
public key needed and key derivation, used by the offline
wallet to sign the transaction.

Thus we only need to pass the serialized transaction to
the offline wallet:

.. code-block:: bash

   cat unsigned.txn | electrum signtransaction - > signed.txn

The command will ask for your password, and save the
signed transaction in 'signed.txn'

Broadcast the Transaction
-------------------------

Send your transaction to the Dimecoin network, using broadcast:

.. code-block:: bash

   cat signed.txn | electrum broadcast -

If successful, the command will return the ID of the
transaction.
