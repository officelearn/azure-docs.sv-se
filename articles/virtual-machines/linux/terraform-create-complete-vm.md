---
title: Använd Terraform för att skapa en fullständig Linux VM i Azure | Microsoft Docs
description: Lär dig hur du använder Terraform för att skapa och hantera en fullständig miljö med Linux virtuella datorer i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: timlt
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: aaa8235d5fe47e72963ef4942876ea9827a38ef6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Skapa en komplett infrastruktur för Linux virtuella datorer i Azure med Terraform

Terraform kan du definiera och skapa hela infrastrukturen distributioner i Azure. Du kan skapa Terraform mallar i ett läsbart format som kan skapa och konfigurera Azure-resurser på ett konsekvent och reproducerbara sätt. Den här artikeln visar hur du skapar en komplett Linux-miljö och ge support för resurser med Terraform. Du kan också lära dig hur du [installera och konfigurera Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Skapa Azure-anslutningen och resursgruppen.

Vi går igenom varje avsnitt i en mall för Terraform. Du kan också se den fullständiga versionen av den [Terraform mallen](#complete-terraform-script) som du kan kopiera och klistra in.

Den `provider` avsnittet visar Terraform att använda en Azure-provider. Att hämta värden för *PRENUMERATIONSID*, *client_id*, *client_secret*, och *tenant_id*, se [installera och Konfigurera Terraform](terraform-install-configure.md). 

> [!TIP]
> Om du skapar miljövariabler för värden eller använder den [Azure Cloud Shell Bash upplevelse](/azure/cloud-shell/overview) , du behöver inte inkludera variabeldeklarationer i det här avsnittet.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Följande avsnitt skapar en resursgrupp med namnet `myResourceGroup` i den `eastus` plats:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}
```

I följande avsnitt du refererar till resursgruppen med *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
Följande avsnitt skapar ett virtuellt nätverk med namnet *myVnet* i den *10.0.0.0/16* adressutrymmet:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```
: Nedan skapar ett undernät med namnet *mySubnet* i den *myVnet* virtuellt nätverk

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Skapa offentlig IP-adress
Skapa och tilldela en offentlig IP-adress till den virtuella datorn kan komma åt resurser via Internet. Följande avsnitt skapar en offentlig IP-adress med namnet *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Skapa Nätverkssäkerhetsgrupp
Nätverkssäkerhetsgrupper styra flödet i nätverkstrafiken till och från den virtuella datorn. Följande avsnitt skapar en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* och definierar en regel för att tillåta SSH-trafik på TCP-port 22:

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    ;
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Skapa virtuella nätverkskort
Ett virtuellt nätverkskort (NIC) ansluter den virtuella datorn till ett givet virtuellt nätverk, offentlig IP-adress och nätverkssäkerhetsgruppen. Följande avsnitt i en mall för Terraform skapar ett virtuellt nätverkskort med namnet *myNIC* anslutna till de virtuella nätverksresurser som du har skapat:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Skapa lagringskontot för diagnostik
Du behöver ett lagringskonto för att lagra startdiagnostikinställningar för en virtuell dator. Dessa startdiagnostikinställningar kan hjälpa dig att felsöka problem och övervaka statusen för den virtuella datorn. Storage-konto som du skapar är endast för att lagra diagnostikdata startavbildningen. Eftersom varje storage-konto måste ha ett unikt namn genererar slumpmässig text i följande avsnitt:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Nu kan du skapa ett lagringskonto. Följande avsnitt skapar ett lagringskonto med namnet baserat på slumpmässiga texten som genereras i föregående steg:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Det sista steget är att skapa en virtuell dator och använda de resurser som skapades. Följande avsnitt skapar en virtuell dator med namnet *myVM* och bifogar det virtuella nätverkskortet med namnet *myNIC*. Senast *Ubuntu 16.04-LTS* bilden används och en användare med namnet *azureuser* skapas med en lösenordsautentisering inaktiverad.

 SSH viktiga data har angetts i den *ssh_keys* avsnitt. Ange en giltig offentlig SSH-nyckel i den *key_data* fältet.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Slutföra Terraform skript

Samordnar dessa avsnitt och se hur Terraform fungerar genom att skapa en fil med namnet *terraform_azure.tf* och klistra in följande innehåll:

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Skapa och distribuera infrastrukturen
Det första steget är att initiera Terraform med mallen Terraform skapas. Det här steget säkerställer att Terraform har alla förutsättningar för att skapa mallen i Azure.

```bash
terraform init
```

Nästa steg är att ha Terraform granska och validera mallen. Det här steget jämför de begärda resurserna till statusinformation som sparas av Terraform och matar ut planerad körningen. Resurser som är *inte* skapas i Azure.

```bash
terraform plan
```

När du kör det föregående kommandot, bör du se något som liknar följande skärm:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Om allt verkar vara korrekta och du är redo att bygga infrastrukturer i Azure, tillämpa mallen i Terraform:

```bash
terraform apply
```

VM-infrastruktur är klar när Terraform har slutförts. Hämta den offentliga IP-adressen på den virtuella datorn med [az vm visa](/cli/azure/vm#az_vm_show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Du kan sedan SSH till den virtuella datorn:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Nästa steg
Du har skapat grundläggande infrastruktur i Azure med hjälp av Terraform. Mer komplicerade scenarier inklusive exempel Använd belastningsutjämning och virtuella skala uppsättningar, finns ett stort antal [Terraform exempel Azure](https://github.com/hashicorp/terraform/tree/master/examples). En aktuell lista över Azure providers som stöds finns i [Terraform dokumentationen](https://www.terraform.io/docs/providers/azurerm/index.html).
