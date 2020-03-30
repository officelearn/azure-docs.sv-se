---
title: Snabbstart - Använd Terraform för att skapa en komplett Linux-virtuell dator i Azure
description: I den här snabbstarten använder du Terraform för att skapa och hantera en komplett Linux-miljö för virtuella datorer i Azure
keywords: azure devops terraform linux vm virtuell maskin
ms.topic: quickstart
ms.date: 03/15/2020
ms.openlocfilehash: f262734cc16d97e4d73af371410403a4cbb8815e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79415469"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Snabbstart: Skapa en komplett Linux-infrastruktur för virtuella datorer i Azure med Terraform

Med Terraform kan du definiera och skapa fullständiga infrastrukturdistributioner i Azure. Du skapar Terraform-mallar i ett läsbart format som skapar och konfigurerar Azure-resurser på ett konsekvent, reproducerbart sätt. Den här artikeln visar hur du skapar en komplett Linux-miljö och stöder resurser med Terraform. Du kan också lära dig hur du [installerar och konfigurerar Terraform](terraform-install-configure.md).

> [!NOTE]
> För Terraform specifik support, vänligen kontakta Terraform direkt med hjälp av en av deras community kanaler:
>
>    * [Terraform-delen](https://discuss.hashicorp.com/c/terraform-core) av community-portalen innehåller frågor, användningsfall och användbara mönster.
>
>    * För leverantörsrelaterade frågor, besök [terraformsleverantörerna](https://discuss.hashicorp.com/c/terraform-providers) på communityportalen.


## <a name="create-azure-connection-and-resource-group"></a>Skapa Azure-anslutning och resursgrupp

Låt oss gå igenom varje avsnitt i en Terraform-mall. Du kan också se den fullständiga versionen av [Terraform-mallen](#complete-terraform-script) som du kan kopiera och klistra in.

Avsnittet `provider` talar om för Terraform att använda en Azure-provider. Hämta värden `subscription_id`för `client_id` `client_secret`, `tenant_id`, och se [Installera och konfigurera Terraform](terraform-install-configure.md). 

> [!TIP]
> Om du skapar miljövariabler för värdena eller använder [Azure Cloud Shell Bash-upplevelsen](/azure/cloud-shell/overview) behöver du inte inkludera variabeldeklarationerna i det här avsnittet.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

I följande avsnitt skapas `myResourceGroup` en `eastus` resursgrupp med namnet på platsen:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

I ytterligare avsnitt refererar du `${azurerm_resource_group.myterraformgroup.name}`till resursgruppen med .

## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
I följande avsnitt skapas `myVnet` ett `10.0.0.0/16` virtuellt nätverk med namnet i adressutrymmet:

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

I följande avsnitt skapas `mySubnet` ett `myVnet` undernät med namnet i det virtuella nätverket:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Skapa offentlig IP-adress
Om du vill komma åt resurser över Internet skapar och tilldelar du en offentlig IP-adress till den virtuella datorn. I följande avsnitt skapas en `myPublicIP`offentlig IP-adress med namnet :

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


## <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp
Nätverkssäkerhetsgrupper styr flödet av nätverkstrafik in och ut ur den virtuella datorn. I följande avsnitt skapas en `myNetworkSecurityGroup` nätverkssäkerhetsgrupp med namnet och en regel som tillåter SSH-trafik på TCP-port 22:

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


## <a name="create-virtual-network-interface-card"></a>Skapa gränssnittskort för virtuella nätverk
Ett virtuellt nätverkskort (NIC) ansluter den virtuella datorn till ett visst virtuellt nätverk, offentlig IP-adress och nätverkssäkerhetsgrupp. I följande avsnitt i en Terraform-mall `myNIC` skapas ett virtuellt nätverkskort med namnet som är anslutet till de virtuella nätverksresurser som du har skapat:

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


## <a name="create-storage-account-for-diagnostics"></a>Skapa lagringskonto för diagnostik
Om du vill lagra startdiagnostik för en virtuell dator behöver du ett lagringskonto. Dessa startdiagnostik kan hjälpa dig att felsöka problem och övervaka status för din virtuella dator. Lagringskontot som du skapar är bara att lagra startdiagnostikdata. Eftersom varje lagringskonto måste ha ett unikt namn genereras en del slumpmässig text i följande avsnitt:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Nu kan du skapa ett lagringskonto. I följande avsnitt skapas ett lagringskonto med namnet baserat på den slumpmässiga text som genereras i föregående steg:

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

Det sista steget är att skapa en virtuell dator och använda alla resurser som skapats. I följande avsnitt skapas `myVM` en virtuell dator med `myNIC`namnet och det virtuella nätverkskortet med namnet . Den `Ubuntu 16.04-LTS` senaste bilden används och `azureuser` en användare som heter skapas med lösenordsautentisering inaktiverad.

 SSH-nyckeldata finns `ssh_keys` i avsnittet. Ange en offentlig SSH-nyckel i `key_data` fältet .

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

## <a name="complete-terraform-script"></a>Komplett Terraform-skript

Om du vill samla alla dessa avsnitt och se `terraform_azure.tf` Terraform i aktion skapar du en fil som heter och klistrar in följande innehåll:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
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


## <a name="build-and-deploy-the-infrastructure"></a>Bygga och distribuera infrastrukturen
Med din Terraform-mall skapad är det första steget att initiera Terraform. Det här steget säkerställer att Terraform har alla förutsättningar för att skapa din mall i Azure.

```bash
terraform init
```

Nästa steg är att låta Terraform granska och validera mallen. I det här steget jämförs de begärda resurserna med tillståndsinformationen som sparats av Terraform och sedan utdata den planerade körningen. Azure-resurserna skapas inte just nu.

```bash
terraform plan
```

När du har körat föregående kommando bör du se något i stil med följande skärm:

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

Om allt ser korrekt ut och du är redo att bygga infrastrukturen i Azure använder du mallen i Terraform:

```bash
terraform apply
```

När Terraform är klar är din VM-infrastruktur klar. Hämta den offentliga IP-adressen för din virtuella dator med [az vm show:](/cli/azure/vm)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Du kan sedan SSH till din virtuella dator:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Läs mer om hur du använder Terraform i Azure](/azure/terraform)