# Create Vault

## Summary

This project aims to automate the Ansible Vault password content generation.

The Ansible Vault password file is generated and stored to the defined password file on '~/' directory. Optionally it can be GPG encrypted.

## Usage

Make backup the vault.yml and password file before running the following play, if needed.

When using GPG encryption on the password file, make sure that GPG is ready to be used. E.g. add key info to the local inventory/group_vars/all/mail.yml 'gpg_encryption_recipient_list' variable. GPG private key should be on hardware e.g. Yubikey.

Following command is used to run the play.
```
ansible-playbook playbook.yml
```
Also, following command can be used when real inventory file is elsewhere.
```
ansible-playbook -i INVENTORY playbook.yml
```

If vault file has already been created, you need to provide the vault password to rerun the playbook. ('ERROR! Attempting to decrypt but no vault secrets found')
Example below provides example with default settings:
```
ansible-playbook --vault-id VAULT_PASSWORD_FILE playbook.yml
```
or 
```
ansible-playbook --vault-password-file VAULT_PASSWORD_FILES playbook.yml
```
or
```
ansible-playbook -i INVENTORY --vault-password-file VAULT_PASSWORD_FILES playbook.yml
```

For example first run
```
ansible-playbook -i ../MyFoE_inventory/development/hosts playbook.yml
```

For example subsequent runs
```
ansible-playbook -i ../MyFoE_inventory/development/hosts --vault-password-file ~/.vault_password.txt playbook.yml
```
Or also when gpg is used (works also without gpg)
```
ansible-playbook -i ../MyFoE_inventory/development/hosts --vault-password-file ~/.ansible-vault.sh playbook.yml
```


This play will generated passwords to Ansible vault to:
* "{{ target_inventory }}/{{ target_vault }}"

The generated passwords are defined using two variables:
* vault_content_general
* vault_content_host

```
vault_content_general:
    < Informative name>:`
    comment: '< comment that will be stored to the vault>'
    name: '< variable name to store the password >' 
    # Password type possible values: 'simple', 'normal', 'human', 'unix-hash-sha256', 'unix-hash-sha512'
    # These values are used on vault.yml.j2 to select different type of password quality
    password_type: 'normal'
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
    # Password type possible values: 'simple', 'normal', 'human', 'unix-hash-sha256', 'unix-hash-sha512'
    # These values are used on vault.yml.j2 to select different type of password quality
    password_type: 'normal'
    # List of host that normally would be ansible_hostnames and will be added end of the name parameter
    host:
      - <ansible hostnames 1>
      - <ansible hostnames 2>
      - <ansible hostnames n>
```

Generated content on the Ansible Vault is like:
```
 # comment
 <name><host>: <random password>
```

**The project that used these generated password needs to add 'vault_password_file = ~/.ansible-vault.sh' to ansible.cfg.**

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