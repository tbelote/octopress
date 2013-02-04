---
layout: post
title: ec2-bundle-vol doesn't bundle all files
---



To avoid including private keys in public AMIs ec2-budle-vol doesn't include any .pem or .gpg files unless you explicitly include them. This is very annoying becuase it doesn't bundle the public key ca certificates, any debian repositories you have trusted, and even the ec2-cert.pem needed by the ami tools to make ec2 images.

To get around this I've added an include argument that overrides the default exclude like the command below:
{% highlight sh %}

    ec2-bundle-vol -c /mnt/cert-XXX.pem \
    -k /mnt/pk-XXX.pem \
    -u XXX \
    -e /mnt/ \
    -i /home/,/etc/ec2/amitools/cert-ec2.pem,/etc/ssl/certs/,/etc/apt/trusted.gpg,/etc/apt/trusted.gpg~,/etc/apt/trustdb.gpg \
    -d /mnt/volume -p myappXXX
{% endhighlight %}