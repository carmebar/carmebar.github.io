---
layout: page
title: How to verify
permalink: /verify/
---
## Motivation

As you may have noticed this blog is a git repository. From the many advantages this offers
the one I care the most is end-to-end integrity. You, the reader, can cryptographically check
that everything written in this blog was written by me (well, technically the person with access to the private signing key).

You can read more about the benefits and motivation of writting a git-based blog
on [this article](http://blog.invisiblethings.org/2015/02/09/my-new-git-based-blog.html) written by the always inspiring Joanna Rutkowska({% include icon-twitter.html username="rootkovska" %}), or at least someone who has access to her private keys.

## Getting the public keys

Before you can verify the signed commits/tags of this blog you first need to get my public keys.

You can download them from the [keys section]({{"/keys/" | prepend: site.baseurl}})
and import them to your keyring by file,

    $ gpg --import carlosmelero-key.asc

Or make gpg download them for you,

    $ gpg --fetch-keys "{{"/keys/carlosmelero-key.asc" | prepend: site.baseurl | prepend: site.url}}"

Or you can also fetch them from a keyserver,

    $ gpg --keyserver pool.sks-keyservers.net --recv-keys {{site.pgp_master_fp | replace: ' ', '' | prepend: '0x'}}

## Trusting the public keys [^1]

[^1]:This step is optional but will remove the warning `There is no indication that the signature belongs to the owner.`. Read more about GPG trust levels [here](https://www.gnupg.org/gph/en/manual.html#AEN346).


Once the master public key and its subkeys have been added to your key ring you can validate and trust them.
To validate the keys you should check that the fingerprint matches.

Here's the fingerprint of the master key: `{{site.pgp_master_fp}}`.
To retrieve the fingerprint of the imported key:

    $ gpg --fingerprint {{site.pgp_master_id}}
    pub   rsa4096 2016-10-13 [C] [expires: 2018-10-13]
          572D 538F 55E2 991D 1F3E  0EE4 3762 21C7 FB62 0C0A
    uid           [ unknown] Carlos Melero Bargues
    uid           [ unknown] Carlos Melero Bargues <carlosmelero@live.com>
    sub   rsa4096 2016-10-13 [S] [expires: 2017-10-13]
    sub   rsa4096 2016-10-13 [E] [expires: 2017-10-13]

Once you've checked that the fingerprint matches (ideally from multiple independent sources) you might want to trust the key:

    $ gpg --edit-key {{site.pgp_master_id}}
    gpg (GnuPG) 2.1.15; Copyright (C) 2016 Free Software Foundation, Inc.
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.


    pub  rsa4096/376221C7FB620C0A
         created: 2016-10-13  expires: 2018-10-13  usage: C   
         trust: unknown       validity: unknown
    sub  rsa4096/490431E1398756EC
         created: 2016-10-13  expires: 2017-10-13  usage: S   
    sub  rsa4096/11804CB7D30D321A
         created: 2016-10-13  expires: 2017-10-13  usage: E   
    [ unknown] (1). Carlos Melero Bargues
    [ unknown] (2)  Carlos Melero Bargues <carlosmelero@live.com>

    gpg> trust
    pub  rsa4096/376221C7FB620C0A
         created: 2016-10-13  expires: 2018-10-13  usage: C   
         trust: unknown       validity: unknown
    sub  rsa4096/490431E1398756EC
         created: 2016-10-13  expires: 2017-10-13  usage: S   
    sub  rsa4096/11804CB7D30D321A
         created: 2016-10-13  expires: 2017-10-13  usage: E   
    [ unknown] (1). Carlos Melero Bargues
    [ unknown] (2)  Carlos Melero Bargues <carlosmelero@live.com>

    Please decide how far you trust this user to correctly verify other users' keys
    (by looking at passports, checking fingerprints from different sources, etc.)

      1 = I don't know or won't say
      2 = I do NOT trust
      3 = I trust marginally
      4 = I trust fully
      5 = I trust ultimately
      m = back to the main menu

    Your decision? 5
    Do you really want to set this key to ultimate trust? (y/N) y

    pub  rsa4096/376221C7FB620C0A
         created: 2016-10-13  expires: 2018-10-13  usage: C   
         trust: ultimate      validity: unknown
    sub  rsa4096/490431E1398756EC
         created: 2016-10-13  expires: 2017-10-13  usage: S   
    sub  rsa4096/11804CB7D30D321A
         created: 2016-10-13  expires: 2017-10-13  usage: E   
    [ unknown] (1). Carlos Melero Bargues
    [ unknown] (2)  Carlos Melero Bargues <carlosmelero@live.com>
    Please note that the shown key validity is not necessarily correct
    unless you restart the program.

    gpg> quit

## Verifying commits

Now that the keys are imported into your keyring git is able to use gpg to check the validity of the singatures.
After downloading the repository you just need to select the commit you want to verify (p.e using `git log`) and run:

    $ git verify-commit $commit-sha1
    gpg: Signature made Tue 18 Oct 2016 21:59:21 CEST
    gpg:                using RSA key 490431E1398756EC
    gpg: Good signature from "Carlos Melero Bargues" [ultimate]
    gpg:                 aka "Carlos Melero Bargues <carlosmelero@live.com>" [ultimate]
    Primary key fingerprint: 572D 538F 55E2 991D 1F3E  0EE4 3762 21C7 FB62 0C0A
         Subkey fingerprint: 71EB 8B2F FED2 21E5 78BC  3CAC 4904 31E1 3987 56EC

You can see that the commit was signed with my signing key `71EB 8B2F FED2 21E5 78BC  3CAC 4904 31E1 3987 56EC`.

## Verifying tags

Signing every commit doesn't add any more security since a signed commit already covers all the previous ones[^2].
Signed tags give the same benefits as a signed commits but allow to group commits.

[^2]:You can read [Linus ranting about this](http://git.661346.n2.nabble.com/GPG-signing-for-git-commit-tt2582986.html#a2583316)

You can first tags using the `git tag` command and after selecting the tag to verify (usually it makes sense to verify the last one since it will cover the previous ones):

    $ git tag -v initial_blog
    object f10e934b9aabd95c6529c820a02ca4c4a69b3473
    type commit
    tag initial_blog
    tagger Carlos Melero <carlosmelero@live.com> 1476820817 +0200

    First tag, nothing really in the blog.
    gpg: Signature made Tue 18 Oct 2016 22:00:36 CEST
    gpg:                using RSA key 490431E1398756EC
    gpg: Good signature from "Carlos Melero Bargues" [ultimate]
    gpg:                 aka "Carlos Melero Bargues <carlosmelero@live.com>" [ultimate]
    Primary key fingerprint: 572D 538F 55E2 991D 1F3E  0EE4 3762 21C7 FB62 0C0A
         Subkey fingerprint: 71EB 8B2F FED2 21E5 78BC  3CAC 4904 31E1 3987 56EC

## What about the time?

As you can see the commands used to verify the commits and tags output a time in which the signature was _supposedly_ made.
Of course there's no easy way to make sure when the signature was made... __or is it?__

This repository's tags are timestamped using the great {% include icon-github.html username="opentimestamps" %}/ [opentimestamps-client](https://github.com/opentimestamps/opentimestamps-client).
Timestamping proves that the content of the repository existed prior to some point in time.

To learn more about the rationale behind using this timestamps I highly suggest that you read [Peter Todd's blog posts](https://petertodd.org/2016/opentimestamps-announcement) especially the one [focused on Git](https://petertodd.org/2016/opentimestamps-git-integration).

<!-- TODO: Explain how to verify timestamps -->
