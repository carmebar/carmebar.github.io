---
layout: post
title: "Making a mirror of a git-based blog"
date: "2016-10-30 00:18:38 +0200"
---
The greatest thing of a git-based blog is how easy is to create a mirror. You
could simple do `git clone`. That's enougth to be able to [verify](/verify/)
it's authenticity if it's properly signed.
But that's not enough to serve a mirror of the blog, you'll need to run a
web server and most likely [Jekyll](https://jekyllrb.com), an static blogging platform.
[GitHub](https://github.com/) blogs usually use it.

Keeping the mirror up to date will require to pull the repository from time to
time. You don't have to trust the source if you trust the blog signatures.
[Caddy](https://caddyserver.com/), a webserver written in Go, and its git extension
will make our life easier.

Setting up Jekyll
-----------------

[Jekyll](https://jekyllrb.com) requires [Ruby](https://www.ruby-lang.org) v2.0
above. I installed it from source:

    $ wget --https-only https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz
    $ tar xfz ruby-2.3.1.tar.gz
    $ cd ruby-2.3.1
    $ ./configure --disable-install-doc
    $ make -j2
    # make install

This takes a while on a modest VPS, about 20min on a [VC1S from Scaleway](https://www.scaleway.com/).

Once ruby is installed you may need to update your PATH `source ~/.bashrc` and then
you can use gem to install jekyll and the required theme, in my case [minima](https://github.com/jekyll/minima):

    $ gem install jekyll minima --no-rdoc --no-ri

Preparing Caddy
---------------

[Caddy](https://caddyserver.com/) is a modern web server written in Go which has
*many* interesting features like HTTP/2 support, HTTPS by default and seamless
support with [Let'sEncrypt CA](https://letsencrypt.org/).

I'm gonna be using its [git middleware](https://github.com/abiosoft/caddy-git) to
keep the blog updated and run the verify hooks.

Here's the [Caddyfile](https://caddyserver.com/docs/caddyfile):

    domain.com {
      gzip
      root blog/_site
      git https://github.com/carmebar/carmebar.github.io.git ../ {
        then bash -c ../verify_and_build.sh
      }
    }

Caddy will try to get you a Let's Encrypt certificate if you don't disable
explicitly HTTPS.

Notice that the root of the website is `blog/_site` __blog__ is where the repository
will be cloned and __site__ is where Jekyll saves the generated website.

By default the git middleware will check for updates every hour, read
[the documentation](https://caddyserver.com/docs/git) if you want to set other interval.
Note the bash script being run after updating the repository.

Blog authenticity
-----------------
`verify_and_build.sh` will try to verify the repository authenticity using PGP.
For it to be able to verify you need to have imported the public keys into the keyring.
Read about it in the [verify section](/verify/#getting-the-public-keys).

It will first get the last commit and see if it's verifiable, if not it will
check the tag that covers it for a valid signature.

{% highlight bash %}
#!/bin/bash

#Get last commit
last=$(git rev-parse --verify HEAD)

#Get tag that includes it
tag=$(git tag --contains "$last" | head -1)

#Try to verify commit
if ! git verify-commit "$last"; then
  if ! git tag -v "$tag"; then
    echo "No tag covered the commit. Won't accept it to rebuild the blog."
    exit 1
  fi;
fi;

#Finally update blog
jekyll build --destination _site \
 || { echo "Error building website";exit 1; }
{% endhighlight %}
