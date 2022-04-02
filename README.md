# Create Vault

## Summary

This project aims to automates the Ansible Vault password content generation.

The Ansible Vault file is stored to a directory, which is asked from user during the play.

The Ansible Vault password file is generated and stored to the directory. Optionally it can be GPG encrypted.

## Usage - Generating the Ansible Vault content

During the play following items are asked from the user: 
* Target directory for the results.
  * vault.yml
  * password.txt / password.txt.gpg
  * ansible-vault.sh

* Source configuration file containing at least following variables:
  * vault_content_general
  * vault_content_host

Make backup the vault.yml and password file before running the following play.

When using GPG encryption on the password file, make sure that GPG is ready to be used and needed keys have been imported to gpg keyring.
E.g. add key info to the 'gpg_encryption_recipient_list' variable. GPG private key should be on hardware e.g. Yubikey.

Following command is used to run the play.
```
ansible-playbook playbook.yml
```

Current version does not support updating existing Ansible vaults.

## Usage during normal operation


```
cd < to your project >
ansible-playbook -i < path to inventory >/hosts.yml --vault-password-file=<path>/ansible-vault.sh playbook.yml
```
or following if you have added vault_password_file = <path>/ansible-vault.sh' to ansible.cfg
```
cd < to your project >
ansible-playbook -i < path to inventory >/hosts.yml playbook.yml
```

# Details


The generated passwords are defined using two variables:
* vault_content_general
* vault_content_host

```
vault_content_general:
    < Informative name>:`
    comment: '< comment that will be stored to the vault>'
    name: '< variable name to store the password >' 
    # Password type possible values: 'simple', 'normal', 'human', 'unix-hash-sha256', 'unix-hash-sha512', 'static'
    # These values are used on vault.yml.j2 to select different type of password quality
    password_type: 'normal'
    static_value: <static content>
```

Generated content on the Ansible Vault is like:
```
 # comment
 <name>: <random password>
``` 
```
 Host specific values.
  < Informative name>:
    comment: '< comment that will be stored to the vault>'
    name: '< variable name to store the password >' 
    # Password type possible values: 'simple', 'normal', 'human', 'unix-hash-sha256', 'unix-hash-sha512', 'static'
    # These values are used on vault.yml.j2 to select different type of password quality
    password_type: 'normal'
    static_value: <static content>
    # List of host that normally would be ansible_hostnames and will be added end of the name parameter
    host:
      - <ansible hostnames 1>
      - <ansible hostnames 2>
      - <ansible hostnames n>
```


Please, note that using static type is not secure since the password are in clear text in the configuration file. This type could also be also used to store some other values like pregenerated keys.


Generated content on the Ansible Vault is like:
```
 # comment
 <name><host>: <random password>
```

**The project that used these generated password needs to add 'vault_password_file = <path>/ansible-vault.sh' to ansible.cfg.**

## More information

This play is tested using Ansible version 2.10.

GPG usage is documented nicely at https://github.com/harobed/ansible-vault-tutorial and https://www.techrepublic.com/article/how-to-encrypt-files-on-your-linux-servers-with-gocryptfs/.

Following steps should be also done in order to the the Yubikey to function correctly.
```
* gpg --card-edit
* gpg/card> admin
* gpg/card> passwd

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

* key-attr
  * Change to 4096bit RSA
* generate
```


Supported password format and their complexities:
* normal (default and fallback option): 62 characters, random mix of upper and lowercase ASCII letters, the numbers 0-9 and full punctuation.
* simple: 62 characters, random mix of upper and lowercase ASCII letters, the numbers 0-9 and limited punctuation (". , : - _"). 
* human: 22 characters, ascii lower random mix of upper and lowercase ASCII letters, the numbers 0-9 and limited punctuation (". , : - _").
* unix-hash-sha256: `human` + salted sha256 hash (both password and hash provided)
* unix-hash-sha512: `human` + salted sha512 hash (both password and hash provided)
