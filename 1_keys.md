---
layout: page
title: Keys
permalink: /keys/
---
Key hierarchy
=============
    $ gpg -K --fingerprint 0x376221C7FB620C0A

    sec#  rsa4096/0x376221C7FB620C0A 2016-10-13 [C] [expires: 2018-10-13]
          Key fingerprint = 572D 538F 55E2 991D 1F3E  0EE4 3762 21C7 FB62 0C0A
    uid                   [ultimate] Carlos Melero Bargues
    uid                   [ultimate] Carlos Melero Bargues <carlosmelero@live.com>
    ssb>  rsa4096/0x490431E1398756EC 2016-10-13 [S] [expires: 2017-10-13]
          Key fingerprint = 71EB 8B2F FED2 21E5 78BC  3CAC 4904 31E1 3987 56EC
          Card serial no. = 0005 000041EC
    ssb>  rsa4096/0x11804CB7D30D321A 2016-10-13 [E] [expires: 2017-10-13]
          Key fingerprint = B2B4 B9C5 F0A6 BEC8 7EB3  2A2A 1180 4CB7 D30D 321A
          Card serial no. = 0005 000041EC

The email encryption and code/blog signing key are linked at the page footer. [Here's the master public key](carlosmelero-master.asc)

As you can see gpg is called with the `-K` argument which is an alias to `--list-secret-keys`:

    --list-secret-keys
       -K     List all keys from the secret keyrings, or just the ones given on the command line.
              A # after the letters sec means that the secret key is not usable  
              (for  example,  if  it  was  created  via --export-secret-subkeys).  See also --list-keys.

Since the master key is marked with the `#` you can tell I don't have the private key in this machine.
I have it backed up in a microSD card somewhere safe.

You can also tell that I don't have access to the private keys of my signing and encryption keys. They're stored on a smartcard.

Timestamps
==========
This repository's tags are also timestamped using the great {% include icon-github.html username="opentimestamps" %}/ [opentimestamps-client](https://github.com/opentimestamps/opentimestamps-client)
To learn more about the rationale behind using this timestamps I highly suggest that you read [Peter Todd's blog posts](https://petertodd.org/2016/opentimestamps-announcement) especially the one [focused on Git](https://petertodd.org/2016/opentimestamps-git-integration).
