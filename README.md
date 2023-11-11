# Ansible Playbooks for Certificate Management

This repository contains two Ansible playbooks:

1. `asa_cert_management.yml` - Manages SSL certificates on a Cisco ASA device.
2. `letsencrypt_cert_creation.yml` - Creates Let's Encrypt certificates for specified domains.

## Prerequisites

Before you begin, ensure you have the following:

- Ansible installed on your control node.
- Access to an Ansible AWX or Tower server if you prefer a GUI-based approach.
- Necessary credentials and access permissions to the target devices and servers.

## Installation

### Installing Ansible

To install Ansible on your system, follow the official [Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/index.html).

### Cloning the Repository

Clone this repository to your control node:

```bash
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```

## Using the Playbooks

### With Ansible CLI

1. **Edit Variables**: Open each playbook and edit the variables under the `vars` section to match your environment.

2. **Run the Playbook**: Use the following command to run the playbook:

    ```bash
    ansible-playbook -i inventory_file_path playbook_name.yml -e "extra_variables"
    ```

   Replace `inventory_file_path` with the path to your inventory file, `playbook_name.yml` with the name of the playbook (`asa_cert_management.yml` or `letsencrypt_cert_creation.yml`), and `extra_variables` with any extra variables you need to pass.

### With Ansible AWX

1. **Create a Project**:
   - Log into AWX.
   - Navigate to **Resources > Projects**.
   - Click **Add** and provide the necessary details, including the SCM URL pointing to this Git repository.

2. **Create an Inventory**:
   - Navigate to **Resources > Inventories**.
   - Click **Add** and define your inventory, including hosts and necessary variables.

3. **Create a Job Template**:
   - Navigate to **Templates**.
   - Click **Add > Job Template**.
   - Select the project and inventory you created.
   - Choose the playbook (`asa_cert_management.yml` or `letsencrypt_cert_creation.yml`).
   - Save the template.

4. **Launch the Template**:
   - Once the template is configured, click the **Launch** button to start the job.

5. **Monitor Job Execution**:
   - You can monitor the progress and view the output of the job in real-time from the job's detail page.

## Additional Notes

- Ensure you have the correct permissions set for any credentials or SSH keys used by the playbooks.
- Always test playbooks in a non-production environment before using them in production.
- The playbooks may require specific Ansible collections or roles; install them using `ansible-galaxy` if needed.

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

Certainly! I'll add a section to the README explaining how to modify the playbooks to use variables from the Ansible Vault encrypted file.

---

## Modifying Playbooks to Use Variables from Ansible Vault

To utilize variables stored in an Ansible Vault encrypted file within your playbooks, follow these steps:

### 1. Store Necessary Variables in the Vault File

After creating your encrypted `vault_vars.yml` file with Ansible Vault, add your sensitive variables to this file. For example:

```yaml
# In vault_vars.yml
asa_username: "encrypted_username"
asa_password: "encrypted_password"
gitlab_username: "encrypted_gitlab_user"
gitlab_password: "encrypted_gitlab_pass"
```

### 2. Reference the Vault File in Your Playbooks

In your playbooks, reference the vault file under the `vars_files` section. This tells Ansible to load variables from this file. 

For instance, modify your `asa_cert_management.yml` playbook like so:

```yaml
---
- name: Cert Management on ASA
  hosts: platforms_cisco-asa
  become: true
  become_method: enable
  gather_facts: false
  vars_files:
    - vault_vars.yml  # Include the path to your encrypted var file here
  vars:
    files_loc: "~/cert-store"
    # other non-sensitive variables...
  tasks:
    # your tasks here...
```

Do the same for your `letsencrypt_cert_creation.yml` playbook.

### 3. Running the Playbook with Vault Variables

When running the playbook, you will need to provide the Ansible Vault password to decrypt the `vault_vars.yml` file:

- **Using the CLI**:

  Run the playbook and provide the vault password interactively:

  ```bash
  ansible-playbook -i inventory_file_path asa_cert_management.yml --ask-vault-pass
  ```

  Or use a password file:

  ```bash
  ansible-playbook -i inventory_file_path asa_cert_management.yml --vault-password-file .vault_pass.txt
  ```

- **Using Ansible AWX**:

  In AWX, you can add your Vault password under **Credentials** and use it within your job templates. This way, AWX will handle the decryption of your vault file automatically when the playbook runs.

### 4. Best Practices

- Do not store your vault password in plaintext or version control.
- Only grant access to the vault password to authorized individuals.
- Regularly rotate your vault password and encrypted credentials for security.

---

By following these instructions, you can securely manage sensitive variables in your Ansible playbooks using Ansible Vault. This method enhances the security of your automation processes, especially when dealing with credentials and private data.

## Contributing

Contributions to improve these playbooks are welcome. Please follow the standard Git workflow - fork this repository, make your changes, and submit a pull request.
