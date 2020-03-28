---
title: Självstudiekurs - Skapa en Azure-skalningsuppsättning för virtuella datorer från en anpassad avbildning för Packer med terraform
description: Använd Terraform för att konfigurera och versionshantera en VM-skalningsuppsättning för Azure från en anpassad avbildning som skapats genom Packer (komplett med ett virtuellt nätverk och hanterade anslutna diskar).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472220"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Självstudiekurs: Skapa en Azure-skalningsuppsättning för virtuella datorer från en anpassad avbildning för Packer med terraform

I den här självstudien använder du [Terraform](https://www.terraform.io/) för att skapa och distribuera en [Azure-skalningsuppsättning för virtuella datorer](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) som skapats med en anpassad avbildning som produceras med [Packer](https://www.packer.io/intro/index.html) med hanterade diskar som använder [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera terraform-distributionen.
> * Använd variabler och utdata för Terraform-distribution.
> * Skapa och distribuera en nätverksinfrastruktur.
> * Skapa en anpassad avbildning för virtuella datorer med Packer.
> * Skapa och distribuera en skala för virtuella datorer med hjälp av den anpassade avbildningen.
> * Skapa och distribuera en jumpbox.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

- **Terraform**: [Installera Terraform och konfigurera åtkomst till Azure](terraform-install-configure.md).
- **SSH-nyckelpar:** [Skapa ett SSH-nyckelpar.](/azure/virtual-machines/linux/mac-create-ssh-keys)
- **Packer**: [Installera Packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>Skapa filstrukturen

Skapa tre nya filer i en tom katalog med följande namn:

- `variables.tf`: Den här filen innehåller värdena för de variabler som används i mallen.
- `output.tf`: Den här filen beskriver de inställningar som visas efter distributionen.
- `vmss.tf`: Den här filen innehåller koden för den infrastruktur som du distribuerar.

##  <a name="create-the-variables"></a>Skapa variablerna 

I det här steget definierar du variabler som anpassar resurserna som skapas av Terraform.

Redigera `variables.tf` filen, kopiera följande kod och spara ändringarna.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> Standardvärdet för variabeln resource_group_name är avkopplat. Definiera ditt eget värde.

Spara filen.

När du distribuerar terraform-mallen vill du få det fullständigt kvalificerade domännamnet som används för att komma åt programmet. Använd resurstypen `output` för Terraform och hämta egenskapen `fqdn` för resursen. 

Redigera filen `output.tf` och kopiera följande kod för att göra det fullständigt kvalificerade domännamnet tillgängligt för de virtuella datorerna. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definiera nätverksinfrastrukturen i en mall 

I det här steget skapar du följande nätverksinfrastruktur i en ny Azure-resursgrupp: 
  - Ett virtuellt nätverk med adressutrymmet 10.0.0.0/16.
  - Ett undernät med adressutrymmet 10.0.2.0/24.
  - Två offentliga IP-adresser. En används av den virtuella datorn skala uppsättning belastningsutjämnare. Den andra används för att ansluta till SSH jumpbox.

Du behöver även en resursgrupp där alla resurser skapas. 

Redigera och kopiera följande kod i filen `vmss.tf`: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Tagga de resurser som distribueras i Azure för att underlätta deras identifiering i framtiden.

## <a name="create-the-network-infrastructure"></a>Skapa nätverksinfrastrukturen

Initiera Terraform-miljön genom att köra följande kommando i katalogen där du skapade `.tf`-filerna:

```bash
terraform init 
```
 
Providern plug-ins hämta från Terraform `.terraform` registret till mappen i katalogen där du körde kommandot.

Distribuera infrastrukturen till Azure genom att köra följande kommando.

```bash
terraform apply
```

Kontrollera att det fullständigt kvalificerade domännamnet för den offentliga IP-adressen motsvarar din konfiguration.

![Skala för virtuell dator Terraform fullständigt kvalificerat domännamn för offentlig IP-adress](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Resursgruppen innehåller följande resurser:

![VM-skalningsuppsättningen för Terraform-nätverksresurser](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Skapa en Azure-avbildning med Packer
Skapa en anpassad Linux-avbildning genom att följa stegen i självstudien [Hur du använder Packer för att skapa Linux-avbildningar för virtuella datorer i Azure](/azure/virtual-machines/linux/build-image-with-packer).
 
Följ självstudien för att skapa en avetablerade Ubuntu-avbildning med Nginx installerad.

![När du har skapat Packer-avbildningen har du en bild](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> I den här självstudien körs ett kommando för att installera Nginx i Packer-avbildningen. Du kan även köra ditt egna skript när du skapar.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Redigera infrastrukturen för att lägga till VM-skalningsuppsättningen

I det här steget skapar du följande resurser i nätverket som distribuerades tidigare:
- En Azure belastningsutjämnare för att betjäna programmet. Bifoga den till den offentliga IP-adressen som distribuerades tidigare.
- En Azure belastningsutjämnare och regler för att betjäna programmet. Bifoga den till den offentliga IP-adressen som konfigurerades tidigare.
- En Azure-backend-adresspool. Tilldela den till belastningsutjämnaren.
- En hälsoavsökningsport som används av programmet och konfigurerad på belastningsutjämnaren.
- En skala för den virtuella datorn som sitter bakom belastningsutjämnaren och körs i det virtuella nätverket som distribuerades tidigare.
- [Nginx](https://nginx.org/) på noderna i den virtuella datorn skala installeras från en anpassad avbildning.


Lägg till följande kod i slutet av filen `vmss.tf`.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Anpassa distributionen genom att lägga till följande kod i `variables.tf`:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Distribuera VM-skalningsuppsättningen i Azure

Kör följande kommando för att visualisera distributionen av VM-skalningsuppsättningen:

```bash
terraform plan
```

Kommandots utdata ser ut som följande bild:

![Plan för att lägga till VM-skalningsuppsättning med Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Distribuera de ytterligare resurserna i Azure: 

```bash
terraform apply 
```

Innehållet i resursgruppen ser ut som i följande bild:

![Resursgrupp för VM-skalningsuppsättning med Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Öppna en webbläsare och anslut till det fullständigt kvalificerade domännamnet som returnerades av kommandot. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Lägg till en jumpbox i det befintliga nätverket 

Det här valfria steget aktiverar SSH-åtkomst till instanser av VM-skalningsuppsättningen med en jumpbox.

Lägg till följande resurser i din befintliga distribution:
- Ett nätverksgränssnitt som är anslutet till samma undernät som skaluppsättningen för den virtuella datorn
- En virtuell dator med det här nätverksgränssnittet

Lägg till följande kod i slutet av filen `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Redigera `outputs.tf` om du vill lägga till följande kod som visar startfältets värdnamn när distributionen är klar:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Distribuera jumpboxen

Distribuera jumpboxen.

```bash
terraform apply 
```

När distributionen har slutförts ser innehållet i resursgruppen ut som följande avbildning:

![Resursgrupp för VM-skalningsuppsättning med Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Inloggning med ett lösenord är inaktiverat i jumpboxen och den virtuella datorskalauppsättningen som du har distribuerat. Logga in med SSH för att komma åt de virtuella datorerna.

## <a name="clean-up-the-environment"></a>Rensa miljön

Följande kommandon tar bort resurserna som skapades i den här självstudien:

```bash
terraform destroy
```

Ange *ja* när du uppmanas att bekräfta borttagningen av resurserna. Destruktionsprocessen kan ta ett par minuter att slutföra.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Läs mer om hur du använder Terraform i Azure](/azure/terraform)
