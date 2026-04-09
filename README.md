# Terraform + Ansible — Azure Web Server Deployment

> Provision cloud infrastructure with Terraform and configure it with Ansible. A complete Infrastructure as Code (IaC) workflow on Microsoft Azure.

---

## 📌 What this project does

- Provisions Azure infrastructure using Terraform (VNet, Subnet, NSG, Public IP, Linux VM)
- Configures the VM using Ansible (installs Nginx, creates a user, deploys a static website)
- Demonstrates a full IaC + Configuration Management workflow reproducible with two commands

---

## 🗂️ Project Structure

```
terraform-ansible-azure/
├── terraform/
│   ├── main.tf              # Azure resources
│   ├── variables.tf         # Input variables
│   ├── outputs.tf           # Public IP output
│   └── terraform.tfvars     # Variable values
└── ansible/
    ├── inventory.ini        # VM connection details
    ├── playbook.yml         # Configuration tasks
    └── files/
        └── index.html       # Static website
```

---

## ⚙️ Prerequisites

Make sure you have these installed before starting:

- [Terraform CLI](https://developer.hashicorp.com/terraform/install)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html)
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
- An active Azure subscription
- An SSH key pair

---

## 🚀 How to deploy

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/terraform-ansible-azure.git
cd terraform-ansible-azure
```

### 2. Login to Azure

```bash
az login
az account show
```

### 3. Generate SSH key (if you don't have one)

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/azure_vm_key
```

### 4. Provision infrastructure with Terraform

```bash
cd terraform
terraform init
terraform validate
terraform plan
terraform apply
```

Note the public IP address printed in the output.

### 5. Update Ansible inventory

Open `ansible/inventory.ini` and replace the IP with your VM's public IP:

```ini
[webservers]
<your-vm-public-ip> ansible_user=adminuser ansible_ssh_private_key_file=~/.ssh/azure_vm_key
```

### 6. Test Ansible connectivity

```bash
cd ../ansible
ansible -i inventory.ini webservers -m ping
```

Expected output:
```json
<ip> | SUCCESS => { "ping": "pong" }
```

### 7. Run the Ansible playbook

```bash
ansible-playbook -i inventory.ini playbook.yml
```

### 8. Verify

Open your browser and go to:
```
http://<your-vm-public-ip>
```

You should see the deployed webpage.

---

## 🏗️ Azure Resources Created

| Resource | Name |
|---|---|
| Resource Group | `example-project-rg` |
| Virtual Network | `example-project-vnet` |
| Subnet | `example-project-subnet` |
| Network Security Group | `example-project-nsg` |
| Public IP | `example-project-public-ip` |
| Network Interface | `example-project-nic` |
| Linux Virtual Machine | `example-project-vm` |

---

## 🔒 NSG Rules

| Rule | Port | Protocol | Direction |
|---|---|---|---|
| Allow SSH | 22 | TCP | Inbound |
| Allow HTTP | 80 | TCP | Inbound |

---

## 📋 Ansible Playbook Tasks

1. Update apt package cache
2. Install Nginx
3. Start and enable Nginx on boot
4. Create deployment user with sudo access
5. Deploy static website to `/var/www/html/`

---

## 🧹 Cleanup

To destroy all Azure resources and stop credit usage:

```bash
cd terraform
terraform destroy
```

---

## 💡 Key Concepts Demonstrated

| Concept | Tool | Description |
|---|---|---|
| Infrastructure as Code | Terraform | Infra defined in `.tf` files, version controlled |
| Configuration Management | Ansible | Server setup automated via playbook |
| Idempotency | Both | Safe to run multiple times — only changes what needs changing |
| SSH Key Authentication | Azure + Ansible | No passwords used anywhere |
| State Management | Terraform | `.tfstate` tracks what exists in the cloud |

---

## 🌐 Extending to other cloud providers

This same workflow applies to AWS and GCP by swapping the Terraform provider and resources. The Ansible playbook requires zero changes.

| Concept | Azure | AWS | GCP |
|---|---|---|---|
| Provider | `azurerm` | `aws` | `google` |
| VM | `azurerm_linux_virtual_machine` | `aws_instance` | `google_compute_instance` |
| Network | `azurerm_virtual_network` | `aws_vpc` | `google_compute_network` |
| Firewall | `azurerm_network_security_group` | `aws_security_group` | `google_compute_firewall` |
| Public IP | `azurerm_public_ip` | `aws_eip` | `google_compute_address` |

---

## 📚 References

- [Terraform Azure Provider Docs](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Ansible Module Index](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)
- [Azure CLI Reference](https://learn.microsoft.com/en-us/cli/azure/)

---

## 👤 Author

Built as part of a DevOps learning project covering Terraform, Ansible, and Azure.
