# Using Ansible Vault with Ansible CLI

Ansible Vault is a feature of Ansible that allows you to keep sensitive data such as passwords or keys in encrypted files, rather than as plaintext in your playbooks or roles.

## Setting Up Ansible Vault

1. **Creating an Encrypted Variables File**:

    To create a new encrypted file, use the following command:

    ```bash
    ansible-vault create vault_vars.yml
    ```

    This command will prompt you to enter a password. After confirming the password, a file editor will open where you can add your sensitive variables. For example:

    ```yaml
    secret_username: myuser
    secret_password: mypassword
    ```

2. **Editing an Encrypted File**:

    If you need to edit the encrypted file, use:

    ```bash
    ansible-vault edit vault_vars.yml
    ```

    You'll be prompted for the password you created the file with.

## Using Encrypted Variables in Playbooks

When running playbooks that use variables stored in an encrypted file, you have a few options for providing the Ansible Vault password:

1. **Interactive Prompt for Password**:

    You can prompt for the Vault password interactively:

    ```bash
    ansible-playbook playbook.yml --ask-vault-pass -i inventory_file
    ```

    This command will prompt you to enter the Vault password before executing.

2. **Using a Password File**:

    For automation, you might want to store the Vault password in a file (ensure this file is secured appropriately):

    First, create a file with the Vault password:

    ```bash
    echo "yourVaultPassword" > .vault_pass.txt
    chmod 0600 .vault_pass.txt
    ```

    Then, reference this file when running the playbook:

    ```bash
    ansible-playbook playbook.yml --vault-password-file .vault_pass.txt -i inventory_file
    ```

3. **Referencing the Vault File in Playbooks**:

    In your playbook, reference the variables file as you would with any other vars file. Ansible will handle the decryption based on the method you've used to provide the password:

    ```yaml
    ---
    - hosts: all
      vars_files:
        - vault_vars.yml
      tasks:
        - name: Print a variable
          debug:
            var: secret_username
    ```

## Best Practices

- **Security**: Keep the Vault password and any password files secure.
- **Version Control**: You can commit the encrypted file (`vault_vars.yml`) to version control safely, but never the plaintext password or the password file.
- **Multiple Vaults**: Consider using different vault files for different environments (e.g., staging vs production) or different types of secrets.
