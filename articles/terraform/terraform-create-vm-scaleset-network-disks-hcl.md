---
title: "Använd Terraform och skapa en Azure VM-skala med LISTAN."
description: "Använd Terraform för att konfigurera och version skala en virtuell dator i Azure anges med ett virtuellt nätverk och hanteras anslutna diskar."
keywords: "terraform, devops, skala ange, virtuella datorer, nätverk, lagring, moduler"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>Använda Terraform att planera och skapa en nätverksansluten Virtuella Azure-skala med hanterade lagringspoolen

I den här artikeln använder du [Terraform](https://www.terraform.io/) att skapa och distribuera en [virtuella Azure-datorn scaleset](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) med hanterade diskar med hjälp av den [Hashicorp Configuration språk](https://www.terraform.io/docs/configuration/syntax.html) (LISTAN).  

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera Terraform-distribution
> * Använda variabler och utdata för Terraform distribution 
> * Skapa och distribuera nätverksinfrastrukturen
> * Skapa och distribuera en skaluppsättning för virtuell dator och koppla den till nätverket
> * Skapa och distribuera en jumpbox att ansluta till de virtuella datorerna via SSH

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

- [Installera Terraform och konfigurera åtkomst till Azure](/azure/virtual-machines/linux/terraform-install-configure)
- [Skapa en SSH-nyckelpar](/azure/virtual-machines/linux/mac-create-ssh-keys) om du inte redan har ett.

## <a name="create-the-file-structure"></a>Skapa filstrukturen

Skapa tre nya filer i en tom katalog med följande namn:

- `variables.tf`Den här filen innehåller värdena för variabler som används i mallen.
- `output.tf`Den här filen beskriver de inställningar som ska visas efter distributionen.
- `vmss.tf`Den här koden för virtuella datorns skaluppsättning ange infrastruktur.

## <a name="create-the-variables-and-output-definitions"></a>Skapa variablerna och utdata definitioner

I det här steget kan definiera du variabler som anpassar de resurser som skapats av Terraform.

Redigera den `variables.tf` , kopiera följande kod och spara ändringarna.

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

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
  - Två offentliga IP-adresser. En som används av virtuella scale set belastningsutjämnaren, den andra används för att ansluta till SSH-jumpbox.


Redigera och kopiera följande kod i den `vmss.tf` filen: 

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
> Det är en bra idé att tagga resurser som distribueras i Azure för att underlätta deras identifiering i framtiden.

## <a name="create-the-network-infrastructure"></a>Skapa nätverkets infrastruktur

Initiera Terraform miljön genom att köra följande kommando i katalogen där du skapade den `.tf` filer:

```bash
terraform init 
```

Kör sedan följande kommando för att distribuera infrastrukturen i Azure.

```bash
terraform apply
```

Kontrollera att FQDN för den offentliga IP-adressen som motsvarar din konfiguration: ![VMSS terraform FQDN för offentlig IP-adress](./media/tf-create-vmss-step4-fqdn.png)


Resursgruppen ska ha följande resurser: ![VMSS terraform nätverksresurser](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Redigera infrastruktur för att lägga till virtuella datorns skaluppsättning

I det här steget kan du lägga till följande resurser i mallen:

- En Azure att läsa in belastningsutjämning och regler för att hantera programmet och koppla den till den offentliga IP-adressen som tidigare har konfigurerat.
- Azure backend-adresspool och tilldela den till loadbalancer 
- En hälsa avsökningsport som används av programmet och konfigurerats på loadbalancer 
- En virtuell dator skaluppsättningen placerad bakom belastningsutjämnaren som körs på vnet distribuerade tidigare
- [Nginx](http://nginx.org/) på noderna för virtuella datorns skaluppsättning använder ett tillägg för anpassat skript.

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
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
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

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
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

Resultatet av kommandot ska se ut ungefär så här.
![Terraform lägga till vmss plan](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

Distribuera ytterligare resurser i Azure: 

```bash
terraform apply 
```

Innehållet i resursgruppen bör se ut som:

![Terraform vm scaleset resursgruppen.](./media/tf-create-vmss-step6-apply.png)

Öppna en webbläsare och Anslut till FQDN som returnerades av kommandot. 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>Lägg till en SSH-jumpbox i det befintliga nätverket 

I det här steget konfigurerar du följande resurser:
- Ett nätverksgränssnitt som är anslutna till samma undernät som virtuella datorns skaluppsättning.
- En virtuell dator är ansluten till det här nätverksgränssnittet. Den här jumpbox är åtkomlig via fjärranslutning. När du är ansluten, kan du SSH till någon av de virtuella datorerna i skaluppsättning.



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

Redigera `outputs.tf` och Lägg till följande kod för att visa värdnamnet för jumpbox när installationen är klar:

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

När distributionen är klar ser ut innehållet i resursgruppen som:

![Terraform vm scaleset resursgruppen.](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> Logga in med ett lösenord har inaktiverats på jumpbox och virtuella datorns skaluppsättning ange att du har distribuerat. Logga in med SSH att komma åt de virtuella datorerna.

## <a name="clean-up-the-environment"></a>Rensa miljön

Följande kommandon för att ta bort de resurser som skapades i den här självstudiekursen:

```bash
terraform destroy
```

Typen `yes` när du ombeds bekräfta för borttagning av resurser. Destruktion processen tar några minuter att slutföra.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en virtuell dator skala på Azure med hjälp av Terraform i den här självstudiekursen. Du har lärt dig att:

> [!div class="checklist"]
> * Initiera Terraform distribution
> * Använda variabler och utdata för Terraform distribution 
> * Skapa och distribuera en nätverksinfrastruktur för
> * Skapa och distribuera en skaluppsättning för virtuell dator och koppla den till en befintlig miljö
> * Skapa och distribuera en jumpbox att ansluta till de virtuella datorerna via SSH 
