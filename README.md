## Cheatsheet

- [Installing the OS](#installing-the-os)
    - [Setting up SSH keys](#ssh-keys)

<a name='installing-the-os'></a>

#### Installing the OS

I've chosen Ubuntu 20.04 as the choice for VPS OS.

#### Setting up SSH keys:

You'll require SSH keys in order to prevent logging in to computer with a password everytime.

**In your local computer:**

```bash
$ ssh-keygen
# Follow the onscreen directions in order to generate the public and the private pair
$ cat YOUR_KEY.pub
# The public key will appear
# Copy the public key.
```

**In cloudcone web dashboard**

Now go to the profile section in the dashboard of cloudcone and select SSH keys. _Paste the content that you've copied earlier._

Now it is time to login to the server.

Open the terminal and type the following:

    ssh root@YOUR_IPV4_IP

It will ask for your password which will be provided to you in the registered mail. Enter the password. Now it is time to install the SSH keys.

    # STEP 2: INSTALL THE SSH KEY
    $ curl -o cc-ikey -L web.cloudc.one/sh/key && sh cc-ikey some-random-key #it will be different for you.

Also, if you want to get information about the RAM usage in the system, then execute the following in the terminal.

    $ wget -O install stats.cloudcone.sh && bash install some-random-string
    # It will be different on every instances. The random string will be provided to you by cloudcone.

