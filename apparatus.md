# Apparatuses for Gilliam

> the technical equipment or machinery needed for a particular
  activity or purpose.

In the context of Gilliam an apparatus is something that adds extra
functionality to your process.  For example, it can be a statsd
daemon. It can be a HTTP proxy that collects statistics and enforces
concurrency limits. In general, it can be pretty much anything that
will make your life easier as a developer.

An apparatus runs alongside your process, in the same image, sharing
memory and CPU resources with your process.

# Using An Apparatus

To include an apparatus in your project, just add it to your
`gilliam.yml` file:

     processes:
       web:
         script: python web.py
     apparatuses:
       - https://github.com/gilliam/apparatus-statsd.git

That's it.  Next time you'll build an image (`gilliam deploy`) the
apparatus will be included and you'll have a statsd daemon running in
your instance accepting metrics on UDP port 127.0.0.1:2185.
Configuration for the apparatus, if needed, is sourced from the
environment just as for your processes.

# Providing An Apparatus

To implement and provide an apparatus yourself is really easy. All you
need is a `start` script that launches the apparatus.  There's also an
optional `build` script that is run during the build phase.

## The `start` script

This script lives in the root directory and is responsible for
starting the apparatus.  Whatever is started need to run as a
background process, otherwise the boot process of the instance is
halted.

Note that the script is executed in a sub-shell (not sourced) so any
environment changes won't be exported to the process.

## The `build` script

This script is run when the image is built.  This script can download
extra resources or install packages.

The script is optional.

## Distribution

Apparatuses can be distributed as gzip'ed tarballs over http and
https, or as git repositories.  For the tarball, all files should be
put in the "root" of the ball.
