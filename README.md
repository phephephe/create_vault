# Create Vault

## Summary

This project aims to automate the Ansible Vault password content generation mainly for the MyFoE_inventory project.

Manually change all the password in the Ansible Vault file is very easily not done and default values are used.

The Ansible Vault password is generated and stored to the defined password file on '~/'. Optionally it can be GPG encrypted.

## Usage

Make backup the vault.yml and password file before running the following play, if needed.

When using GPG encryption on the password file, make sure that GPG is ready to be used. E.g. add key info to the local inventory/group_vars/all/mail.yml 'gpg_encryption_recipient_list' variable.

ansible-playbook playbook

## More information

GPG usage is documented nicely at https://github.com/harobed/ansible-vault-tutorial and https://www.techrepublic.com/article/how-to-encrypt-files-on-your-linux-servers-with-gocryptfs/.


GPG private key should be on hardware e.g. Yubikey.

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

