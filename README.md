## 📚 Azure Networking Essentials — with Code

---

## 1️⃣ **Create a VNet and Subnet**

### ✅ Azure CLI

```bash
# Create Resource Group
az group create --name demo-rg --location eastus

# Create Virtual Network with Subnet
az network vnet create \
  --resource-group demo-rg \
  --name demo-vnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name demo-subnet \
  --subnet-prefix 10.0.1.0/24
```

---

## 2️⃣ **Create VNet Peering**

### ✅ Azure CLI

```bash
# Create second VNet
az network vnet create \
  --resource-group demo-rg \
  --name demo-vnet2 \
  --address-prefix 10.1.0.0/16 \
  --subnet-name demo-subnet2 \
  --subnet-prefix 10.1.1.0/24

# Peering from demo-vnet to demo-vnet2
az network vnet peering create \
  --resource-group demo-rg \
  --name peering-vnet1-to-vnet2 \
  --vnet-name demo-vnet \
  --remote-vnet demo-vnet2 \
  --allow-vnet-access

# Peering from demo-vnet2 to demo-vnet
az network vnet peering create \
  --resource-group demo-rg \
  --name peering-vnet2-to-vnet1 \
  --vnet-name demo-vnet2 \
  --remote-vnet demo-vnet \
  --allow-vnet-access
```

---

## 3️⃣ **Application Security Group (ASG)**

### ✅ Azure CLI

```bash
az network asg create \
  --resource-group demo-rg \
  --name demo-asg
```

You can then associate this ASG to NICs and NSGs later.

---

## 4️⃣ **Network Security Group (NSG) and Rules**

### ✅ Azure CLI

```bash
# Create NSG
az network nsg create \
  --resource-group demo-rg \
  --name demo-nsg

# Create inbound rule to allow port 22 (SSH)
az network nsg rule create \
  --resource-group demo-rg \
  --nsg-name demo-nsg \
  --name Allow-SSH \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --destination-port-ranges 22
```

---

## 5️⃣ **Site-to-Site VPN (Concept + CLI)**

You’d typically configure:

* Virtual Network Gateway (VPN)
* Local Network Gateway (on-prem details)
* VPN Connection

### ✅ Azure CLI Sample:

```bash
# Create public IP for VPN Gateway
az network public-ip create --resource-group demo-rg --name demo-vpn-pip --allocation-method Dynamic

# Create Gateway Subnet
az network vnet subnet create --resource-group demo-rg --vnet-name demo-vnet --name GatewaySubnet --address-prefix 10.0.255.0/27

# Create Virtual Network Gateway
az network vnet-gateway create \
  --resource-group demo-rg \
  --name demo-vpn-gateway \
  --public-ip-address demo-vpn-pip \
  --vnet demo-vnet \
  --gateway-type Vpn \
  --vpn-type RouteBased \
  --sku VpnGw1 \
  --no-wait

# Define Local Network Gateway (on-prem)
az network local-gateway create \
  --resource-group demo-rg \
  --name onprem-gateway \
  --gateway-ip-address <ONPREM_PUBLIC_IP> \
  --local-address-prefixes 192.168.0.0/16

# Establish VPN connection
az network vpn-connection create \
  --resource-group demo-rg \
  --name demo-site2site-conn \
  --vnet-gateway1 demo-vpn-gateway \
  --local-gateway2 onprem-gateway \
  --shared-key 'Azure123'
```

---

## 6️⃣ **Point-to-Site VPN (Concept + CLI)**

Simplified version:

```bash
# Generate VPN Client configuration after Gateway created
az network vnet-gateway vpn-client generate \
  --resource-group demo-rg \
  --name demo-vpn-gateway \
  --processor-architecture Amd64
```

User will download the VPN package and install it on their system.

---

## 7️⃣ **ExpressRoute (Concept + CLI)**

ExpressRoute connects your on-premise to Azure via private connection.

```bash
# Create ExpressRoute Circuit
az network express-route create \
  --resource-group demo-rg \
  --name demo-expressroute \
  --location eastus \
  --bandwidth-in-mbps 200 \
  --provider "Equinix" \
  --peering-location "Silicon Valley" \
  --sku-family MeteredData \
  --sku-tier Standard
```

---

## 📦 Optional: **Terraform Code Sample** — VNet & Subnet

```hcl
resource "azurerm_resource_group" "demo" {
  name     = "demo-rg"
  location = "eastus"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "demo-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.demo.location
  resource_group_name = azurerm_resource_group.demo.name
}

resource "azurerm_subnet" "subnet" {
  name                 = "demo-subnet"
  resource_group_name  = azurerm_resource_group.demo.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}
```

---

## 📖 Documentation Links

* [Azure Virtual Network](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)
* [Azure VPN Gateway](https://learn.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* [Azure ExpressRoute](https://learn.microsoft.com/en-us/azure/expressroute/expressroute-introduction)

---

## ✅ Summary:

| Component                  | CLI Supported | Terraform Example | Typical Use Case                  |
| :------------------------- | :------------ | :---------------- | :-------------------------------- |
| VNet + Subnet              | ✅             | ✅                 | Isolate and segment Azure network |
| VNet Peering               | ✅             | ✅                 | Connect Azure VNets together      |
| Application Security Group | ✅             | ✅                 | Micro-segmentation within subnets |
| Network Security Group     | ✅             | ✅                 | Control inbound/outbound traffic  |
| Site-to-Site VPN           | ✅             | ✅                 | Secure hybrid cloud link          |
| Point-to-Site VPN          | ✅             | ✅                 | Client VPN for remote users       |
| ExpressRoute               | ✅             | ✅                 | Dedicated private connectivity    |

---

## 📦 Suggestion for GitHub Repo Name:

**`azure-networking-labs`** or **`azure-vnet-automation`**
Fantastic — let’s do it 🚀
I’ll prepare **both options** for you:

---

# 📦 Option 1️⃣: Full **Terraform Project** — Azure Networking Essentials

## 📂 Project Structure:

```
azure-networking-terraform/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
```

---

### 📄 `main.tf`

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_virtual_network" "vnet1" {
  name                = var.vnet1_name
  address_space       = [var.vnet1_cidr]
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "subnet1" {
  name                 = "subnet1"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet1.name
  address_prefixes     = [var.subnet1_cidr]
}

resource "azurerm_virtual_network" "vnet2" {
  name                = var.vnet2_name
  address_space       = [var.vnet2_cidr]
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "subnet2" {
  name                 = "subnet2"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet2.name
  address_prefixes     = [var.subnet2_cidr]
}

resource "azurerm_network_security_group" "nsg" {
  name                = "demo-nsg"
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_network_security_rule" "allow_ssh" {
  name                        = "Allow-SSH"
  priority                    = 100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  network_security_group_name = azurerm_network_security_group.nsg.name
}

resource "azurerm_application_security_group" "asg" {
  name                = "demo-asg"
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_virtual_network_peering" "peer_vnet1_to_vnet2" {
  name                      = "peer-vnet1-to-vnet2"
  resource_group_name       = azurerm_resource_group.rg.name
  virtual_network_name      = azurerm_virtual_network.vnet1.name
  remote_virtual_network_id = azurerm_virtual_network.vnet2.id
  allow_virtual_network_access = true
}

resource "azurerm_virtual_network_peering" "peer_vnet2_to_vnet1" {
  name                      = "peer-vnet2-to-vnet1"
  resource_group_name       = azurerm_resource_group.rg.name
  virtual_network_name      = azurerm_virtual_network.vnet2.name
  remote_virtual_network_id = azurerm_virtual_network.vnet1.id
  allow_virtual_network_access = true
}
```

---

### 📄 `variables.tf`

```hcl
variable "resource_group_name" {}
variable "location" {}
variable "vnet1_name" {}
variable "vnet1_cidr" {}
variable "subnet1_cidr" {}
variable "vnet2_name" {}
variable "vnet2_cidr" {}
variable "subnet2_cidr" {}
```

---

### 📄 `terraform.tfvars`

```hcl
resource_group_name = "demo-rg"
location            = "eastus"

vnet1_name   = "demo-vnet1"
vnet1_cidr   = "10.0.0.0/16"
subnet1_cidr = "10.0.1.0/24"

vnet2_name   = "demo-vnet2"
vnet2_cidr   = "10.1.0.0/16"
subnet2_cidr = "10.1.1.0/24"
```

---

### 📄 `outputs.tf`

```hcl
output "vnet1_id" {
  value = azurerm_virtual_network.vnet1.id
}

output "vnet2_id" {
  value = azurerm_virtual_network.vnet2.id
}
```

---

## 🔥 Run Steps:

```bash
terraform init
terraform plan
terraform apply -auto-approve
```

---

# 📦 Option 2️⃣: Full **Azure CLI Script Pack**

### 📄 `azure-networking.sh`

```bash
#!/bin/bash

RESOURCE_GROUP="demo-rg"
LOCATION="eastus"

# Create Resource Group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Create VNet1 and Subnet
az network vnet create --resource-group $RESOURCE_GROUP --name demo-vnet1 --address-prefix 10.0.0.0/16 --subnet-name subnet1 --subnet-prefix 10.0.1.0/24

# Create VNet2 and Subnet
az network vnet create --resource-group $RESOURCE_GROUP --name demo-vnet2 --address-prefix 10.1.0.0/16 --subnet-name subnet2 --subnet-prefix 10.1.1.0/24

# Create NSG and allow SSH
az network nsg create --resource-group $RESOURCE_GROUP --name demo-nsg
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name demo-nsg --name Allow-SSH --priority 100 --direction Inbound --access Allow --protocol Tcp --destination-port-ranges 22

# Create Application Security Group
az network asg create --resource-group $RESOURCE_GROUP --name demo-asg

# VNet Peering VNet1 <-> VNet2
az network vnet peering create --resource-group $RESOURCE_GROUP --name peer-vnet1-to-vnet2 --vnet-name demo-vnet1 --remote-vnet demo-vnet2 --allow-vnet-access
az network vnet peering create --resource-group $RESOURCE_GROUP --name peer-vnet2-to-vnet1 --vnet-name demo-vnet2 --remote-vnet demo-vnet1 --allow-vnet-access
```

---

## ✅ Run:

```bash
chmod +x azure-networking.sh
./azure-networking.sh
```
Excellent call, Atul 👏 — let’s engineer a **complete advanced Azure Networking Terraform Project** including:

✅ VNet 1
✅ Subnet (Address space)
✅ Gateway Subnet
✅ VPN Gateway
✅ Point-to-Site VPN Configuration
✅ Self-signed Root Certificate for P2S
✅ NSG, ASG
✅ VNet Peering to VNet 2

---

## 📦 📂 **Advanced Azure Networking Terraform Project**

---

### 📁 Project Structure

```
azure-advanced-networking/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
```

---

## 📄 `variables.tf`

```hcl
variable "resource_group_name" {}
variable "location" {}
variable "vnet1_name" {}
variable "vnet1_cidr" {}
variable "subnet1_cidr" {}
variable "gateway_subnet_cidr" {}
variable "vpn_gateway_public_ip_name" {}
variable "vpn_gateway_name" {}
variable "vpn_sku" {}
variable "p2s_address_pool" {}
variable "root_cert_name" {}
variable "root_cert_data" {}
```

---

## 📄 `terraform.tfvars`

```hcl
resource_group_name      = "demo-rg"
location                 = "eastus"
vnet1_name               = "demo-vnet1"
vnet1_cidr               = "10.0.0.0/16"
subnet1_cidr             = "10.0.1.0/24"
gateway_subnet_cidr      = "10.0.255.0/27"
vpn_gateway_public_ip_name = "vpn-gw-pip"
vpn_gateway_name         = "demo-vpn-gateway"
vpn_sku                  = "VpnGw1"
p2s_address_pool         = "172.16.201.0/24"
root_cert_name           = "myRootCert"
root_cert_data           = "<BASE64_ROOT_CERT_DATA>"
```

---

## 📄 `main.tf`

```hcl
provider "azurerm" {
  features {}
}

# Resource Group
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

# VNet + Subnet + Gateway Subnet
resource "azurerm_virtual_network" "vnet1" {
  name                = var.vnet1_name
  address_space       = [var.vnet1_cidr]
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "subnet1" {
  name                 = "subnet1"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet1.name
  address_prefixes     = [var.subnet1_cidr]
}

resource "azurerm_subnet" "gateway_subnet" {
  name                 = "GatewaySubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet1.name
  address_prefixes     = [var.gateway_subnet_cidr]
}

# Public IP for VPN Gateway
resource "azurerm_public_ip" "vpn_gw_pip" {
  name                = var.vpn_gateway_public_ip_name
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
  allocation_method   = "Dynamic"
  sku                 = "Basic"
}

# VPN Gateway
resource "azurerm_virtual_network_gateway" "vpn_gateway" {
  name                = var.vpn_gateway_name
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
  type                = "Vpn"
  vpn_type            = "RouteBased"
  active_active       = false
  enable_bgp          = false
  sku                 = var.vpn_sku

  ip_configuration {
    name                          = "vpngwconfig"
    public_ip_address_id          = azurerm_public_ip.vpn_gw_pip.id
    private_ip_address_allocation = "Dynamic"
    subnet_id                     = azurerm_subnet.gateway_subnet.id
  }
}

# Point-to-Site VPN Configuration
resource "azurerm_virtual_network_gateway_p2s_vpn_configuration" "p2s_config" {
  virtual_network_gateway_id = azurerm_virtual_network_gateway.vpn_gateway.id
  vpn_client_address_pool     = [var.p2s_address_pool]

  vpn_client_root_certificate {
    name = var.root_cert_name
    public_cert_data = var.root_cert_data
  }
}

# Network Security Group
resource "azurerm_network_security_group" "nsg" {
  name                = "demo-nsg"
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}

# Allow SSH
resource "azurerm_network_security_rule" "allow_ssh" {
  name                        = "Allow-SSH"
  priority                    = 100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  network_security_group_name = azurerm_network_security_group.nsg.name
}

# Application Security Group
resource "azurerm_application_security_group" "asg" {
  name                = "demo-asg"
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}
```

---

## 📄 `outputs.tf`

```hcl
output "vpn_gateway_public_ip" {
  value = azurerm_public_ip.vpn_gw_pip.ip_address
}

output "vnet1_id" {
  value = azurerm_virtual_network.vnet1.id
}
```

---

## 📜 Notes:

* Replace `<BASE64_ROOT_CERT_DATA>` with the Base64 string of your self-signed certificate’s public key (you can generate via `openssl` or Azure key vault export).

**Example:**

```bash
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
openssl base64 -in cert.pem -out cert.pem.base64
```

---

## 📦 Run This Project:

```bash
terraform init
terraform plan
terraform apply -auto-approve
```

---

## ✅ Summary

This advanced Terraform setup provisions:

* Azure VNet + Subnet + GatewaySubnet
* VPN Gateway (RouteBased)
* Point-to-Site VPN config
* Network Security Group (NSG)
* Application Security Group (ASG)
* Root cert-based P2S VPN authentication
* Output the public IP of the VPN Gateway
