Using Electrum-Dime on Tails
========================================

Software installed on Tails cannot be permanently upgraded since it is a fixed (read-only) image. You can run the most recent version of Electrum-Dime with Tails by using the AppImage binary we distribute (a self-contained executable that should work on any x86_64 Linux including Tails).

These steps have been tested on Tails versions 3 and 4.

Steps to use AppImage
---------------------

1. Write down your wallet seed words and store them securely off the computer.
2. Enable and configure persistent storage. In Tails enter the Applications/Tails menu and select "Configure persistent volume". Ensure "Personal data" and "Dimecoin client" sliders are enabled. Reboot if necessary and make sure the persistent volume is unlocked.
3. Ensure your Tails is connected to a network and the onion icon at the top confirms Tor network is ready. 
4. Using Tor browser download the Linux Appimage file under "Sources and Binaries" near the top of the wallets page on dimecoinnetwork.com_  and save it to the default "Tor browser" folder. Tails and Tor are not as fast as your regular operating system, and the download may take much longer than normally expected especially if you have a slow computer or USB drive - Tor download speed depends entirely on the Tor network connections. Your AppImage will not start nor will it give any error message if you do not download the entire file.
5. Open Home/Tor browser folder and drag the appimage to the Persistent folder (lower left side of the window). Tails is very sensitive to user writeable file locations and Electrum-Dime may not work in another location.
6. Recommended: Check the PGP signature of the AppImage by following these_ instructions before using the AppImage.
7. Open Home/Persistent folder (where the appimage will now live), right click on the appimage and select Properties. Select the Permissions tab and click "Allow executing file as program" then close the dialog. More detailed instructions with screenshots are available here_.

.. _dimecoinnetwork.com: https://dimecoinnetwork.com/wallets
.. _here: https://docs.appimage.org/user-guide/run-appimages.html
.. _these: https://github.com/dime-coin/electrum-docs/blob/master/gpg-check.rst 

You can now simply click on the Electrum-Dime AppImage icon in your persistent folder to run the newest Electrum-Dime. Your new AppImage should use any previous Electrum-Dime data (if there is any) without difficulty and the wallet(s) will remain on your Tails USB drive if you've enabled persistent storage. If there is any question about your wallet(s) being corrupted, erase the files in ~/.electrum-dime/wallets and reinitialize from seed words. 

**Caution:** Do not use the other versions of Electrum-Dime if available on the Tails version 3 menus. 
