# Neomutt 
Setup for `neomutt`. This only works with `fastmail` account.

## Initial Configuration

This section is to remind myself how to set things up.

### Setting up secrets with `pass`
To avoid having a plaintext password stored in the config I have created a vault with `pass`. First 

```bash
pacman -S gnupg pass
```

or 

```bash
brew install gnupg pass
```

### Create a GPG key

Create a GPG key. I choose RSA 4096-bit. This is the key to unlock `pass` itself.

```bash
gpg --full-generate-key
```

Check it was created with 

```bash
gpg --list-keys
```

Get the hex string from the output. Example output looks like

```bash
pub   rsa4096 2024-01-01 [SC]
      A1B2C3D4E5F6A1B2C3D4E5F6A1B2C3D4E5F6A1B2
uid           [ultimate] Your Name <you@fastmail.com>
```

We need the `A1B2C3D4E5F6A1B2C3D4E5F6A1B2C3D4E5F6A1B2` here to later initialise the pass store, which we do by invoking

### Initialise pass store

```bash
pass init A1B2C3D4E5F6A1B2C3D4E5F6A1B2C3D4E5F6A1B2
```

### Insert a password into the password store

I intended to use this with `fastmail` which generates an app-specific password to use. Take this password and insert it into the password store by doing

```bash
pass insert some/path
```

where `some/path` is an identifying string for the account. Whatever this is, it should match the corresponding password lines in `neomuttrc`, ie:

```bash
set imap_pass = `pass some/path`
set smtp_pass = `pass some/path`
```

To test, invoking 

```bash
pass some/path
```

and supplying the GPG key should print the plaintext password in the console. 


## Moving GPG keys 
I want to use this on more than one machine (as I am extravagent enough to utilise more than a single computer). Some ideas include 

```bash
# On machine 1 — export
gpg --export-secret-keys --armor you@fastmail.com > secret-key.asc

# Copy secret-key.asc to machine 2 (USB, scp, whatever)

# On machine 2 — import
gpg --import secret-key.asc
gpg --edit-key you@fastmail.com   # then type: trust → 5 → y → quit

# Clean up
shred -u secret-key.asc           # don't leave this sitting around
```

Or syncing to `git`, support for which is built into `pass` 

```bash
pass git init
pass git remote add origin git@github.com:you/pass-store.git
pass git push -u origin main
```
