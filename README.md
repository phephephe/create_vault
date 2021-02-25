# Create Vault

This project aims to automate the Ansible Vault password content generation mainly for the MyFoE_inventory project.

Manually change all the password in the Ansible Vault file is very easily not done and default values are used.

This project supports two vault files. One for all the normal variables and one for CA dir encryption key.

The Ansible Vault password is generated and stored to the defined password file on '~/'

The inventory project also uses GPG to encrypt the password file.

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

