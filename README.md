# Ansible Playbooks for Certificate and SSL Management

This repository contains three Ansible playbooks for managing SSL certificates:

1. `installTrustpoint.yml` - Manages SSL certificates on a Cisco ASA device.
2. `LetsEncryptCreateCert.yml` - Creates Let's Encrypt certificates for specified domain.
3. `LetsEncryptCertRenwal.yml` - Renews a list of LetsEncrypt certificates based on a list of domains from a text file.

## Prerequisites

Before using these playbooks, ensure you have:

- Ansible installed on your control node.
- Access to an Ansible AWX or Tower server for a GUI-based approach (optional).
- Necessary credentials and access permissions to the target devices and servers.

## Ansible Installation

### Installing Ansible

Follow the official [Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/index.html) to install Ansible on your system.

### Installing Ansible AWX

Follow the Official [AWX Oerator installation Guide](https://github.com/ansible/awx/blob/devel/INSTALL.md)

### Cloning the Repository

Clone this repository to your control node:

```bash
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```

### Installing Required Collections

The `requirements.yml` file is already created in the repository. It lists all the necessary Ansible collections:

```yaml
collections:
  - name: cisco.asa
  - name: community.crypto
  - name: community.general
  - name: community.dns
```

Install these collections by running:

```bash
ansible-galaxy collection install -r requirements.yml
```

#### Note for AWX/Tower Users:
AWX/Tower automatically installs all collections listed in the `requirements.yml` file. Ensure this file is present in your project repository, and AWX/Tower will handle the installation of these collections.

## Using the Playbooks

### With Ansible CLI

1. **Edit Variables**: Variables not defined directly in the playbook should be stored in a separate vars file and encrypted using Ansible Vault.
    #### Managing Sensitive Data with Ansible Vault
    
    Use Ansible Vault to securely manage sensitive variables that are not directly defined in the playbooks:
    
    #### Creating and Editing an Encrypted Vars File
    
    1. **Create an Encrypted File**:
    
       Create a new encrypted vars file using Ansible Vault:
    
       ```bash
       ansible-vault create encrypted_vars.yml
       ```
    
       This command will prompt you to create a new password.
    
    2. **Add Sensitive Variables**:
    
       Once the file is opened in your editor, you can add sensitive variables. For example:
    
       ```yaml
       gitlab_username: "your_username"
       gitlab_password: "your_password"
       API_key: "your_api_key"
       sn_username: "servicenow_username"
       sn_password: "servicenow_password"
       sn_instance: "your_servicenow_instance"
       ```
    
       Save and close the file after adding your variables.
    
    3. **Editing an Existing Encrypted File**:
    
       If you need to edit the encrypted file later, use:
    
       ```bash
       ansible-vault edit encrypted_vars.yml
       ```
    
       Enter the password you set when creating the file to open it in the editor.
    
    #### Using Encrypted Variables in Playbooks
    
    Reference the encrypted vars file in your playbooks to use the variables:
    
    ```yaml
    vars_files:
      - encrypted_vars.yml
    ```

2. **Run the Playbook**: Execute the playbook using the following command:

    ```bash
    ansible-playbook -i inventory_file_path playbook_name.yml --ask-vault-pass
    ```

    Replace `inventory_file_path` with your inventory file path and `playbook_name.yml` with the playbook name. The `--ask-vault-pass` flag prompts for the Vault password.

### With Ansible AWX

1. **Create a Project**: Set up a new project in AWX, pointing to this Git repository.

2. **Create an Inventory**: Define your inventory, including hosts and variables.

3. **Create a Job Template**: Set up a job template with the project and inventory you created. Choose the playbook you want to run.

4. **Launch the Template**: Execute the job from the AWX interface.

5. **Monitor Job Execution**: Track the job's progress in real-time.

## Best Practices and Notes

- Test playbooks in a non-production environment first.
- Securely manage sensitive data using Ansible Vault.
- Keep your Vault password and any password files secure.
- Consider using different vault files for different environments or secrets types.

## Contributing

Contributions to improve these playbooks are welcome. Please follow standard Git workflows: fork this repository, make your changes, and submit a pull request.
