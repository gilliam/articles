# Quick Intro using Vagrant

This is a quick intro guide for trying out Gilliam in a Vagrant box.

**Note**: Gilliam is very much work in progress.  Please don't expect
too much.  It might break at any time.  Reports are appreciated.

## Step 1: Install Vagrant

Go to [Vagrants homepage](http://www.vagrantup.com) and install the
latest release.

## Step 2: Launch Vagrant

This will bootstrap your Gilliam installation. When done (it may take a while,
it needs to download some large Docker images) everything is up and running:

    $ git clone https://github.com/gilliam/gilliam-vagrant.git
    $ cd gilliam-vagrant
    $ vagrant up

## Step 3: Create Your First Formation

For this quick intro guide we'll use a python example application.
SSH into the machine using `vagrant ssh` and issue the following
commands:

    $ git clone https://github.com/gilliam/python-example.git
    $ cd python-example
    $ gilliam create example

## Step 4: Build and Deploy

In this step we will build the example application (without pushing
the resulting image to the registry) and scale it:

    $ gilliam deploy --no-push
    start building image vagrant/example:e0b74777 ...
     |        Python app detected
    ...
    release 1
    $ gilliam scale www=1

You can inspect the created instance using `gilliam ps` if you like.
It might take a few seconds before it reaches state `running`.

## Step 5: Route Traffic to the Service

Now we will instruct the router to send traffic to the service for a
specific path:

    $ gilliam route /hello www.example.service/

## Step 6: Profit.

Let's make sure it works:

    $ curl localhost:8080/hello
    Hello World!
