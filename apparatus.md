# Apparatuses for Gilliam

> the technical equipment or machinery needed for a particular
  activity or purpose.

In the context of Gilliam an apparatus is something that adds extra
functionality to your process.  It can be a statsd daemon or some other
metrics gathering tool.  It can be pretty much anything that will make
your life easier as a developer.

An apparatus runs alongside your process, in the same image, sharing
memory and CPU resources.  It is important to point out that
apparatuses are isolated from other instances just as your process
instance.

# Using An Apparatus

To include an apparatus in your project, just add it to your
`gilliam.yml` file:

     processes:
       web:
         script: python web.py
     apparatuses:
       - https://github.com/gilliam/apparatus-statsd.git

That's it.  Next time you'll build an image (`gilliam deploy`) the
apparatus will be included.  Configuration for the apparatus, if
needed, is sourced from the environment just as for your processes.

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
