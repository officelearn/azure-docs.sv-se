---
title: "Använd Terraform för att skapa en virtuell dator i Azure-skala ange från en anpassad avbildning förpackaren"
description: "Använd Terraform för att konfigurera och version Ange skalan för en virtuell Azure-dator från en anpassad avbildning som genererats av förpackaren (slutförd med ett virtuellt nätverk och hanterade anslutna diskar)."
keywords: "terraform, devops, skala ange, virtuella datorer, nätverk, lagring, moduler, anpassade avbildningar, förpackaren"
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Använd Terraform för att skapa en virtuell dator i Azure-skala ange från en anpassad avbildning förpackaren

I den här kursen använder du [Terraform](https://www.terraform.io/) att skapa och distribuera en [skaluppsättning för virtuell dator i Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) skapats med en anpassad avbildning som skapas med [förpackaren](https://www.packer.io/intro/index.html) med hanterade diskar med hjälp av den [HashiCorp Configuration språk](https://www.terraform.io/docs/configuration/syntax.html) (LISTAN).  

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera Terraform-distribution
> * Använda variabler och utdata för Terraform distribution 
> * Skapa och distribuera en nätverksinfrastruktur för
> * Skapa en avbildning för anpassade virtuella datorn med hjälp av förpackaren
> * Skapa och distribuera en virtuell dator skala som anges med den anpassade avbildningen
> * Skapa och distribuera en jumpbox 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar
> * [Installera Terraform och konfigurera åtkomst till Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Skapa en SSH-nyckelpar](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) om du inte redan har ett
> * [Installera förpackaren](https://www.packer.io/docs/install/index.html) om du inte redan har förpackaren som installerats på den lokala datorn


## <a name="create-the-file-structure"></a>Skapa filstrukturen

Skapa tre nya filer i en tom katalog med följande namn:

- ```variables.tf```Den här filen innehåller värdena för variabler som används i mallen.
- ```output.tf```Den här filen beskriver de inställningar som visas efter distributionen.
- ```vmss.tf```Den här filen innehåller koden för den infrastruktur som du distribuerar.

##  <a name="create-the-variables"></a>Skapa variablerna 

I det här steget kan definiera du variabler som anpassar de resurser som skapats av Terraform.

Redigera den `variables.tf` , kopiera följande kod och spara ändringarna.

```tf 
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
> Standardvärdet för variabeln resource_group_name är att, definiera ett eget värde.

Spara filen.

När du distribuerar mallen Terraform som du vill hämta det fullständigt kvalificerade domännamnet som används för att få åtkomst till programmet. Använd den ```output``` resurstypen för Terraform och får den ```fqdn``` egenskapen för resursen. 

Redigera den `output.tf` filen och kopiera följande kod för att visa det fullständigt kvalificerade domännamnet för virtuella datorer. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definiera nätverkets infrastruktur i en mall 

I det här steget skapar du följande nätverksinfrastruktur i en ny Azure resursgrupp: 
  - Ett VNET med 10.0.0.0/16 adressutrymme 
  - Ett undernät med 10.0.2.0/24 adressutrymme
  - Två offentliga IP-adresser. Används av den virtuella datorn skala set belastningsutjämnaren. den andra används för att ansluta till SSH-jumpbox

Du måste också en resursgrupp där alla resurser skapas. 

Redigera och kopiera följande kod i den ```vmss.tf``` filen: 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Vi rekommenderar att tagga resurser som distribueras i Azure för att underlätta deras identifiering i framtiden.

## <a name="create-the-network-infrastructure"></a>Skapa nätverkets infrastruktur

Initiera Terraform miljön genom att köra följande kommando i katalogen där du skapade den `.tf` filer:

```bash
terraform init 
```
 
Providern plugin-program hämtas från registret Terraform till den ```.terraform``` mappen i katalogen där du körde kommandot.

Kör följande kommando för att distribuera infrastrukturen i Azure.

```bash
terraform apply
```

Kontrollera att fullständigt kvalificerade domännamnet för den offentliga IP-adressen som motsvarar din konfiguration.

![Terraform fullständigt kvalificerade domännamnet för offentlig IP-adress för skaluppsättning för virtuell dator](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Resursgruppen innehåller följande resurser:

![Terraform nätverksresurser för skaluppsättning för virtuell dator](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Skapa en Azure-avbildning med hjälp av förpackaren
Skapa en anpassad avbildning för Linux med hjälp av stegen som beskrivs i självstudierna [använda förpackaren för att skapa avbildningar av Linux virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Följ självstudiekursen för att skapa en avetablerade Ubuntu-avbildning med NGINX installerad.

![När du skapar förpackaren avbildningen kan ha du en bild](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Ett kommando körs till installerar nginx för den här kursen förpackaren-bild. Du kan också köra egna skript när du skapar.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Redigera infrastruktur för att lägga till virtuella datorns skaluppsättning

I det här steget skapar du följande resurser i nätverket som redan har distribuerats:
- Azure belastningsutjämnare för att hantera programmet och koppla den till den offentliga IP-adressen som har distribuerats i steg 4
- En Azure att läsa in belastningsutjämning och regler för att hantera programmet och koppla den till den offentliga IP-adressen som tidigare har konfigurerat.
- Azure backend-adresspoolen och tilldelar den belastningsutjämnaren 
- En hälsa avsökningsport som används av programmet och konfigurerats på belastningsutjämnaren 
- En virtuell dator skaluppsättningen placerad bakom belastningsutjämnaren som körs på vnet distribuerade tidigare
- [Nginx](http://nginx.org/) på noderna för virtuella datorns skaluppsättning installeras från anpassad avbildning


Lägg till följande kod i slutet av den `vmss.tf` filen.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
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
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Anpassa distributionen genom att lägga till följande kod `variables.tf`:

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Distribuera skaluppsättningen för virtuell dator i Azure

Kör följande kommando för att visualisera den virtuella datorn skala distributionen:

```bash
terraform plan
```

Utdata från kommandot ser ut som följande bild:

![Terraform Lägg till plan för skaluppsättning för virtuell dator](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Distribuera ytterligare resurser i Azure: 

```bash
terraform apply 
```

Innehållet i resursgruppen ser ut som följande bild:

![Skaluppsättning för Terraform virtuella resursgruppen.](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Öppna en webbläsare och Anslut till det fullständigt kvalificerade domännamnet som returnerades av kommandot. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Lägg till en jumpbox i det befintliga nätverket 

Det här valfria steget gör det möjligt för SSH-åtkomst till instanser av skaluppsättningen för virtuell dator med hjälp av en jumpbox.

Lägg till följande resurser i den befintliga distributionen:
- Ett nätverksgränssnitt som är anslutna till samma undernät än virtuella datorns skaluppsättning
- En virtuell dator med det här nätverksgränssnittet

Lägg till följande kod i slutet av den `vmss.tf` filen:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
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
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Redigera `outputs.tf` att lägga till följande kod som visar värdnamnet för jumpbox när distributionen är klar:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Distribuera jumpbox

Distribuera jumpbox.

```bash
terraform apply 
```

När distributionen är klar innehållet i resursgruppen som ser ut som följande bild:

![Skaluppsättning för Terraform virtuella resursgruppen.](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Logga in med ett lösenord har inaktiverats på jumpbox och virtuella datorns skaluppsättning ange att du har distribuerat. Logga in med SSH att komma åt de virtuella datorerna.

## <a name="clean-up-the-environment"></a>Rensa miljön

Följande kommandon för att ta bort de resurser som skapades i den här självstudiekursen:

```bash
terraform destroy
```

Typen `yes` när du ombeds bekräfta för borttagning av resurser. Destruktion processen kan ta några minuter att slutföra.

## <a name="next-steps"></a>Nästa steg

I kursen får du har distribuerat en skaluppsättning för virtuell dator och en jumpbox på Azure med hjälp av Terraform. Du har lärt dig att:

> [!div class="checklist"]
> * Initiera Terraform distribution
> * Använda variabler och utdata för Terraform distribution 
> * Skapa och distribuera en nätverksinfrastruktur för
> * Skapa en avbildning för anpassade virtuella datorn med hjälp av förpackaren
> * Skapa och distribuera en virtuell dator skala som anges med den anpassade avbildningen
> * Skapa och distribuera en jumpbox 