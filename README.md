## Cheatsheet

- [Installing the OS](#installing-the-os)
    - [Setting up SSH keys](#ssh-keys)

<a id="installing-the-os" name='installing-the-os'></a>

#### Installing the OS

I've chosen Ubuntu 20.04 as the choice for VPS OS.

#### Setting up SSH keys:

You'll require SSH keys in order to prevent logging in to computer with a password everytime.

**In your loca computer:**

```bash
$ ssh-keygen
# Follow the onscreen directions in order to generate the public and the private pair
$ cat YOUR_KEY.pub
# The public key will appear
# Copy the public key.
```

