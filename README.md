wget-tor-mirror
===============

This should help you create a partial mirror of torproject.org. [1] A full
mirror will use much more than 8.0 GB of disk space. [2] This partial mirror 
requires about 1.5 GB (for en-US and zh-CN) depending on which torbrowser 
locales you choose to mirror. (see defaults.cfg)

Instead of using rsync, like recommended [2], wget is used with -N
(or -nc).

I started writing this script a few years ago, but I only used it for
my own needs. It looks like a group on the tor-mirrors mailing list has
recently (May'14) written a bash mirror setup script. [3] You might want
to use that instead.

Files:

* defaults.cfg - default configuration (e.g. set which torbrowser 
                 locales you want to mirror)
* wget-tor-mirror - downloads the dist directory
* check-signature - checks for valid gpg signatures [4]
* remove-old - removes "old" versions (i.e files that are no longer 
               served by the mirror)
* dist/.htaccess - noiice! pretty indexes served via apache 
* dist/README.html - copied from torproject.org (see COPYING)

Use:

To download a partial mirror of the main mirror (configure via defaults.cfg)

    $ ./wget-tor-mirror

To delete~ stale tor versions (~check out the file itself for more options)

    $ ./remove-old

To check cryptographic signatures (bang bang!), you'll first need to import keys:

    $ # import gpg keys [4] from keys.gnupg.net (or another server)
    $ gpg --keyserver hkp://keys.example.org --recv-keys 0x00000000 0x00000001 ...

Assuming you trust me, here's a one liner that'll add all gpg keyfingerprints 
from torproject.org:

    $ gpg --keyserver hkp://keys.gnupg.net/ --fingerprint --recv-key $(wget --quiet -O - https://www.torproject.org/docs/signing-keys.html.en | grep "Key\ fingerprint" | sed 's/.*\=\ //' | tr -d "\ "| tr '\n' '\ ')

Once you have all the gpg keys necessary, simply run check-signatures:

    $ ./check-signatures

The check-signatures script will only output if a signature could not be verified.
Full results can be seen in the $results file.

Run this every 6 hours by adding the following line to your crontab:

    <insert random number from 0-59> */6 * * * /path/to/wget-tor-mirror

1: https://www.torproject.org/dist/

2: https://www.torproject.org/docs/running-a-mirror.html.en

3: https://github.com/wpapper/tor-download-web

4: https://www.torproject.org/docs/signing-keys.html.en
