Command Line
============


Electrum-Dime has a powerful command line. This page will show you a few basic principles.

Note that this page has been updated for Electrum-Dime 1.0.


Using the inline help
---------------------


To see the list of Electrum-Dime commands, type:

.. code-block:: bash

   electrum help


To see the documentation for a command, type:

.. code-block:: bash

   electrum help <command>


How to Use the Daemon
---------------------

By default, commands are sent to an Electrum-Dime daemon.
Here is how to start and stop the daemon:

.. code-block:: bash

   electrum daemon -d
   electrum getinfo
   electrum stop


Some commands require a wallet. Here is how to load a wallet in the daemon:

.. code-block:: bash

   electrum load_wallet  # this will load the default wallet
   electrum load_wallet -w /path/to/wallet/file
   electrum list_wallets


Once the wallet is loaded, wallet operations are possible, such as:

.. code-block:: bash

   electrum listaddresses
   electrum payto <address> <amount>


Some commands do not require network access, and can be executed without a running daemon.
This is done with the --offline flag:

.. code-block:: bash

   electrum -o listaddresses
   electrum -o payto <address> <amount>
   electrum -o -w /path/to/wallet/file listaddresses



Magic Words
-----------


The arguments passed to commands may be one of the following magic words: ! ? : and -.

- The exclamation mark ! is a shortcut that means 'the maximum amount
  available'.

  Example:

  .. code-block:: bash

     electrum payto 1JuiT4dM65d8vBt8qUYamnDmAMJ4MjjxRE !

  Note that the transaction fee will be computed and deducted from the
  amount.


- A question mark ? means that you want the parameter to be prompted.

  Example:

  .. code-block:: bash

     electrum signmessage 1JuiT4dM65d8vBt8qUYamnDmAMJ4MjjxRE ?

- Use a colon : if you want the prompted parameter to be hidden (not
  echoed in your terminal).

  .. code-block:: bash

     electrum importprivkey :

  Note that you will be prompted twice in this example, first for the
  private key, then for your wallet password.


- A parameter replaced by a dash - will be read from standard input
  (in a pipe)

  .. code-block:: bash

     cat LICENCE | electrum signmessage 1JuiT4dM65d8vBt8qUYamnDmAMJ4MjjxRE -

Aliases
-------

You can use DNS aliases in place of bitcoin addresses, in most
commands.

.. code-block:: bash

   electrum payto ecdsa.net !


Formatting Outputs Using 'jq'
---------------------------

Command outputs are either simple strings or json structured data. A
very useful utility is the 'jq' program.  Install it with:

.. code-block:: bash

   sudo apt-get install jq

The following examples use it.

Examples
--------

Sign and Verify Message
```````````````````````

We may use a variable to store the signature, and verify
it:

.. code-block:: bash

   sig=$(cat LICENCE| electrum signmessage 1JuiT4dM65d8vBt8qUYamnDmAMJ4MjjxRE -)
          
And:

.. code-block:: bash

   cat LICENCE | electrum verifymessage 1JuiT4dM65d8vBt8qUYamnDmAMJ4MjjxRE $sig -


Show Values Unspents
````````````````````````````````

The 'listunspent' command returns a list of dict objects,
with various fields. Suppose we want to extract the 'value'
field of each record. This can be achieved with the jq
command:

.. code-block:: bash

   electrum listunspent | jq 'map(.value)'
          

Select Only Incoming Transactions from History
``````````````````````````````````````````````

Incoming transactions have a positive 'value' field

.. code-block:: bash

   electrum history | jq '.[] | select(.value>0)'

Filter Transactions by Date
```````````````````````````

The following command selects transactions that were
timestamped after a given date:

.. code-block:: bash

   after=$(date -d '07/01/2015' +"%s")

   electrum history | jq --arg after $after '.[] | select(.timestamp>($after|tonumber))'
          

Similarly, we may export transactions for a given time
period:

.. code-block:: bash

   before=$(date -d '08/01/2015' +"%s")

   after=$(date -d '07/01/2015' +"%s")

   electrum history | jq --arg before $before --arg after $after '.[] | select(.timestamp&gt;($after|tonumber) and .timestamp&lt;($before|tonumber))'
          

Encrypt and Decrypt Messages
````````````````````````````

First we need the public key of a wallet address:

.. code-block:: bash

   pk=$(electrum getpubkeys 1JuiT4dM65d8vBt8qUYamnDmAMJ4MjjxRE| jq -r '.[0]')
          

Encrypt:

.. code-block:: bash

   cat | electrum encrypt $pk -

Decrypt:

.. code-block:: bash

   electrum decrypt $pk ?       

Note: this command will prompt for the encrypted message, then for the
wallet password

Export Private Keys and Sweep Coins
```````````````````````````````````

The following command will export the private keys of all wallet
addresses that hold some bitcoins:

.. code-block:: bash

   electrum listaddresses --funded | electrum getprivatekeys -

This will return a list of lists of private keys. In most
cases, you want to get a simple list. This can be done by
adding a jq filer, as follows:

.. code-block:: bash

   electrum listaddresses --funded | electrum getprivatekeys - | jq 'map(.[0])'
          
Finally, let us use this list of private keys as input to the sweep
command:

.. code-block:: bash

   electrum listaddresses --funded | electrum getprivatekeys - | jq 'map(.[0])' | electrum sweep - [destination address]
