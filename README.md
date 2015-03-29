secretctl
---------

GPG wrapper to simplify multi-user encryption/decryption of secrets


workflow
--------

This is the basic workflow enabled by `secretctl`

```
$ echo "top secret stuff" > my_secret
$ gpg --gen-key
...interactive key generation...
gpg: key A59B011E marked as ultimately trusted
...snip...
$ secretctl export A59B011E
Complete. Exported A59B011E as anowell_olaf and added to the keylist.
$ secretctl encrypt my_secret
Complete. Encrypted to my_secret.gpg
$ rm my_secret
$ secretctl decrypt my_secret.gpg
Complete. Decrypted to my_secret
$ cat my_secret
top secret stuff

# After another user exports a GPG key using secretctl:
$ secretctl import
Importing /path/to/gpg/anowell_olaf.pub
...snip...
Importing /path/to/gpg/johndoe_serenity.pub
...snip...
Finished importing all public keys. Ready to encrypt.
$ secretctl decrypt my_secret.gpg
Complete. Decrypted to my_secret
$ secretctl encrypt my_secret
Complete. Encrypted to my_secret.gpg
$ rm my_secret

# Now my_secret can be decrypted by either user
```


detailed usage
--------------
Usage can be seen by running `secretctl` with no args

```
Usage: secretctl CMD [ARGS]

A simple gpg-based workflow for shared encryption of secrets.

secretctl works by maintaining a directory containing public keys
and a keyfile that maps the key ID to the key name (basename of the key file)

Generate a key with: gpg --gen-key

Commands:

  export KEYID [KEYNAME]  Adds a new key that will be used in future encryptions
                          KEYID is the hex ID of a public key which is output when
                              creating (gpg --gen-key) or listing keys (gpg --list-keys):
                              - given: pub rsa2048/A69BF163 2015-03-29
                              - KEYID is A69BF163
                          KEYNAME is a human-recognizable short name for the key
                              Defaults to ${user}_$(hostname)

  import                  Imports all public keys in the keydir into your keychain
                          This allows encryption of files that can be decrypted by others

  encrypt FILENAME        Encrypts a file with all the public keys in the keydir
                          Outputs the encrypted file as FILENAME.gpg

  decrypt FILENAME        Decrypts a file with your keyring
                          Outputs the decrypted file without the .gpg suffix
```
