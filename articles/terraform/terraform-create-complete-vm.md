---
title: Snabb start – Använd terraform för att skapa en komplett virtuell Linux-dator i Azure
description: I den här snabb starten använder du terraform för att skapa och hantera en komplett Linux-miljö för virtuella Linux-datorer i Azure
keywords: virtuell Azure DevOps terraform Linux VM-dator
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 03974d68477855d4ff55b7179312c91ba7d0d055
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943526"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Snabb start: skapa en komplett Linux-infrastruktur för virtuella datorer i Azure med terraform

Med terraform kan du definiera och skapa fullständiga infrastruktur distributioner i Azure. Du skapar terraform-mallar i ett läsbart format som skapar och konfigurerar Azure-resurser på ett konsekvent och reproducerbart sätt. Den här artikeln visar hur du skapar en komplett Linux-miljö och stöd för resurser med terraform. Du kan också lära dig hur du [installerar och konfigurerar terraform](terraform-install-configure.md).

> [!NOTE]
> Om du har terraform-stöd kan du kontakta terraform direkt med någon av deras community-kanaler:
>
>    * [Avsnittet terraform](https://discuss.hashicorp.com/c/terraform-core) i Community-portalen innehåller frågor, användnings fall och användbara mönster.
>
>    * Om du har frågor om providrar går du till avsnittet [terraform-providers](https://discuss.hashicorp.com/c/terraform-providers) i Community-portalen.


## <a name="create-azure-connection-and-resource-group"></a>Skapa Azure-anslutning och resurs grupp

Vi går igenom varje avsnitt i en terraform-mall. Du kan också se den fullständiga versionen av [terraform-mallen](#complete-terraform-script) som du kan kopiera och klistra in.

I avsnittet `provider` instrueras terraform att använda en Azure-Provider. För att hämta värden för *subscription_id*, *client_id*, *Client_secret*och *tenant_id*, se [Installera och konfigurera terraform](terraform-install-configure.md). 

> [!TIP]
> Om du skapar miljövariabler för värdena eller använder [Azure Cloud Shell bash-upplevelsen](/azure/cloud-shell/overview) , behöver du inte inkludera variabel deklarationerna i det här avsnittet.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

I följande avsnitt skapas en resurs grupp med namnet `myResourceGroup` på `eastus` plats:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

I ytterligare avsnitt hänvisar du till resurs gruppen med *$ {azurerm_resource_group. myterraformgroup. name}* .

## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
I följande avsnitt skapas ett virtuellt nätverk med namnet *myVnet* i adress utrymmet *10.0.0.0/16* :

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

I följande avsnitt skapas ett undernät med namnet *mitt undernät* i det virtuella *myVnet* -nätverket:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Skapa offentlig IP-adress
Skapa och tilldela en offentlig IP-adress till den virtuella datorn för att få åtkomst till resurser via Internet. I följande avsnitt skapas en offentlig IP-adress med namnet *myPublicIP*:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Skapa nätverks säkerhets grupp
Nätverks säkerhets grupper styr flödet av nätverks trafik till och från den virtuella datorn. Följande avsnitt skapar en nätverks säkerhets grupp med namnet *myNetworkSecurityGroup* och definierar en regel för att tillåta SSH-trafik på TCP-port 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Skapa ett nätverkskort för virtuella nätverkskort
Ett virtuellt nätverks gränssnitts kort (NIC) ansluter den virtuella datorn till ett angivet virtuellt nätverk, en offentlig IP-adress och en nätverks säkerhets grupp. I följande avsnitt i en terraform-mall skapas ett virtuellt nätverkskort med namnet *myNIC* som är anslutet till de virtuella nätverks resurser som du har skapat:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Skapa lagrings konto för diagnostik
Du behöver ett lagrings konto för att kunna lagra startdiagnostik för en virtuell dator. Den här startdiagnostiken kan hjälpa dig att felsöka problem och övervaka statusen för den virtuella datorn. Det lagrings konto som du skapar är bara att lagra startdiagnostikens data. Eftersom varje lagrings konto måste ha ett unikt namn genererar följande avsnitt slumpmässig text:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Nu kan du skapa ett lagrings konto. I följande avsnitt skapas ett lagrings konto med namnet baserat på den slumpmässiga text som genererades i föregående steg:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Det sista steget är att skapa en virtuell dator och använda alla resurser som skapats. I följande avsnitt skapas en virtuell dator med namnet *myVM* och bifogas det virtuella nätverkskortet med namnet *myNIC*. Den senaste *Ubuntu 16,04-LTS-* avbildningen används och en användare med namnet *azureuser* skapas med lösenordsautentisering inaktive rad.

 Information om SSH-nycklar finns i avsnittet *ssh_keys* . Ange en giltig Offentlig SSH-nyckel i fältet *key_data* .

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Slutför terraform-skript

Om du vill samla alla dessa avsnitt och se terraform i praktiken skapar du en fil med namnet *terraform_azure. tf* och klistrar in följande innehåll:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Bygg och distribuera infrastrukturen
När din terraform-mall har skapats är det första steget att initiera terraform. Det här steget säkerställer att terraform har alla krav för att bygga din mall i Azure.

```bash
terraform init
```

Nästa steg är att låta terraform granska och validera mallen. I det här steget jämförs de begärda resurserna med den statusinformation som sparats av terraform och sedan genereras den planerade körningen. Resurser skapas *inte* i Azure.

```bash
terraform plan
```

När du har kört föregående kommando bör du se något som liknar följande skärm:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
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

Om allting ser korrekt ut och du är redo att bygga infrastrukturen i Azure, använder du mallen i terraform:

```bash
terraform apply
```

När terraform är klar är din infrastruktur för virtuell dator klar. Hämta den offentliga IP-adressen för den virtuella datorn med [AZ VM show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Du kan sedan använda SSH till den virtuella datorn:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lär dig mer om hur du använder terraform i Azure](/azure/terraform)