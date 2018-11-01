---
title: Använd Terraform för att skapa en VM-skalningsuppsättning för Azure
description: Självstudie för att använda Terraform för att konfigurera och versionshantera en VM-skalningsuppsättning för Azure komplett med ett virtuellt nätverk och hanterade anslutna diskar
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuell dator, Azure, skalningsuppsättning, nätverk, lagring, moduler
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2018
ms.openlocfilehash: 5fe85d74b247e890790ec577d813d42f15290cde
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140916"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Använd Terraform för att skapa en VM-skalningsuppsättning för Azure

Med [VM-skalningsuppsättningar i Azure](/azure/virtual-machine-scale-sets) kan du skapa och hantera en grupp med identiska, läsa in belastningsutjämnade virtuella datorer där antalet instanser av virtuella datorer automatiskt kan ökas eller minskas som svar på begäran eller ett definierat schema. 

I den här självstudien lär du dig använda [Azure Cloud Shell](/azure/cloud-shell/overview) för att utföra följande åtgärder:

> [!div class="checklist"]
> * Konfigurera en Terraform-distribution
> * Använda variabler och utdata för Terraform-distribution 
> * Skapa och distribuera nätverksinfrastruktur
> * Skapa och distribuera en VM-skalningsuppsättning och anslut den till nätverket
> * Skapa och distribuera en jumpbox för att ansluta till de virtuella datorerna via SSH

> [!NOTE]
> Den senaste versionen av Terraform-konfigurationsfilerna som används i den här artikeln finns på [lagringsplatsen Awesome Terraform på Github](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Installera Terraform**: Följ anvisningarna i artikeln [Terraform and configure access to Azure](/azure/virtual-machines/linux/terraform-install-configure) (Terraform och konfigurera åtkomst till Azure)

- **Skapa ett SSH-nyckelpar**: Om du inte redan har ett SSH-nyckelpar så följer du instruktionerna i artikeln [How to create and use an SSH public and private key pair for Linux VMs in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) (Så här skapar och använder du ett offentligt och ett privat SSH-nyckelpar för virtuella Linux-datorer i Azure).

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

1. Bläddra till [Azure-portalen](http://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog som heter `vmss`.

    ```bash
    mkdir vmss
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Skapa filen med variabeldefinitioner
I det här avsnittet definierar du variablerna som anpassar resurserna som skapas av Terraform.

I Azure Cloud Shell utför du följande steg:

1. Skapa en fil som heter `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i redigeringsprogrammet:

  ```JSON
  variable "location" {
    description = "The location where resources will be created"
  }

  variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
  }

  variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
  }
  ```

1. Avsluta infogningsläget genom att trycka på tangenten Esc.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Skapa filen för utdatadefinitioner
I det här avsnittet skapar du filen som beskriver utdata efter distributionen.

I Azure Cloud Shell utför du följande steg:

1. Skapa en fil som heter `output.tf`.

    ```bash
    vi output.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i redigeringsprogrammet för att göra det fullständigt kvalificerade domännamnet (FQDN) tillgängligt för de virtuella datorerna. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Avsluta infogningsläget genom att trycka på tangenten Esc.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definiera nätverksinfrastrukturen i en mall
I det här avsnittet skapar du följande nätverksinfrastruktur i en ny Azure-resursgrupp: 

  - Ett virtuellt nätverk (VNET) med adressutrymmet 10.0.0.0/16 
  - Ett undernät med adressutrymmet 10.0.2.0/24
  - Två offentliga IP-adresser. En används av belastningsutjämnaren för VM-skalningsuppsättningen, och den andra används för att ansluta till SSH-jumpboxen.

I Azure Cloud Shell utför du följande steg:

1. Skapa en fil som heter `vmss.tf` för att beskriva VM-skalningsuppsättningens infrastruktur.

    ```bash
    vi vmss.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i slutet av filen för att göra det fullständigt kvalificerade domännamnet (FQDN) tillgängligt för de virtuella datorerna. 

  ```JSON
  resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
  }

  resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
  }

  resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
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
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
  }
  ```

1. Avsluta infogningsläget genom att trycka på tangenten Esc.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Distribuera nätverksinfrastrukturen
Använd Azure Cloud Shell i katalogen där du skapade konfigurationsfilerna (.tf) och utför följande steg:

1. Initiera Terraform.

  ```bash
  terraform init 
  ```

1. Distribuera den definierade infrastrukturen till Azure genom att köra följande kommando.

  ```bash
  terraform apply
  ```

  Terraform uppmanar dig att ange ett värde för plats eftersom variabeln **location** (plats) har definierats i `variables.tf`, men den har aldrig angetts. Du kan ange valfri giltig plats – till exempel West US (västra USA) och väljer sedan Retur. (Använd parenteser kring ett värde med blanksteg).

1. Terraform skriver utdata enligt definitionen i filen `output.tf`. I följande skärmbild visas det fullständigt kvalificerade domännamnet i formen &lt;id>.&lt;plats>.cloudapp.azure.com. Id-värdet är ett beräknat värde och plats är det värde som du anger när du kör Terraform.

  ![Det fullständigt kvalificerade domännamnet för offentlig IP-adress för VM-skalningsuppsättningen](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. I Azure-portalen väljer du **Resursgrupper** i huvudmenyn.

1. På fliken **Resursgrupper** väljer du **myResourceGroup** för att visa resurserna som skapades av Terraform.
  ![Nätverksresurser för VM-skalningsuppsättningar](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Lägg till en VM-skalningsuppsättning

I det här avsnittet lär du dig hur du lägger till följande resurser i mallen:

- En Azure-belastningsutjämnare och regler för att hantera programmet och koppla det till den offentliga IP-adress som konfigurerades tidigare i den här artikeln
- En adresspool för Azure-serverdelen och tilldela den till belastningsutjämnaren 
- En avsökningsport för hälsotillstånd som används av programmet och konfigureras i belastningsutjämnaren 
- En VM-skalningsuppsättning som finns bakom belastningsutjämnaren och körs på det virtuella nätverket som distribuerades tidigare i den här artikeln
- [Nginx](http://nginx.org/) på noderna i VM-skalningsuppsättningen som installerats med [cloud-init](http://cloudinit.readthedocs.io/en/latest/).

I Cloud Shell utför du följande steg:

1. Öppna konfigurationsfilen `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Gå till slutet av filen och använd läget för att lägga till genom att välja tangenten A.

1. Klistra in följande kod i slutet av filen:

  ```JSON
  resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
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
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = "${azurerm_subnet.vmss.id}"
        load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
        primary = true
      }
    }

    tags = "${var.tags}"
}
  ```

1. Avsluta infogningsläget genom att trycka på tangenten Esc.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Skapa en fil som heter `web.conf` som fungerar som konfiguration för cloud-init för de virtuella datorer som är en del av skalningsuppsättningen. 

    ```bash
    vi web.conf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i redigeringsprogrammet:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Avsluta infogningsläget genom att trycka på tangenten Esc.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Öppna konfigurationsfilen `variables.tf`.

  ```bash
  vi variables.tf
  ```

1. Gå till slutet av filen och använd läget för att lägga till genom att välja tangenten A.

1. Anpassa distributionen genom att klistra in följande kod i slutet av filen:

  ```JSON
  variable "application_port" {
      description = "The port that you want to expose to the external load balancer"
      default     = 80
  }

  variable "admin_user" {
      description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
      default     = "azureuser"
  }

  variable "admin_password" {
      description = "Default password for admin account"
  }
  ``` 

1. Avsluta infogningsläget genom att trycka på tangenten Esc.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Skapa en Terraform-plan för att visualisera distributionen av VM-skalningsuppsättningen. (Du måste ange ett lösenord som du väljer, samt platsen för dina resurser.)

  ```bash
  terraform plan
  ```

  Kommandots utdata ska se ut ungefär som följande skärmbild:

  ![Utdata från skapandet av VM-skalningsuppsättningen](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Distribuera de nya resurserna i Azure.

  ```bash
  terraform apply 
  ```

  Kommandots utdata ska se ut ungefär som följande skärmbild:

  ![Resursgrupp för VM-skalningsuppsättning med Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Öppna en webbläsare och anslut till det fullständigt kvalificerade domännamnet som returnerades av kommandot. 

  ![Resultat av att bläddra till det fullständigt kvalificerade domännamnet](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Lägg till en SSH-jumpbox
En *SSH-jumpbox* är en enskild server som du "hoppar" genom för att komma åt andra servrar i nätverket. I det här steget konfigurerar du följande resurser:

- Ett nätverksgränssnitt (eller jumpbox) som är anslutet till samma undernät som VM-skalningsuppsättningen.

- En virtuell dator som har anslutits med det här nätverksgränssnittet. Den här jumpboxen är tillgänglig via fjärranslutning. När du är ansluten kan du använda SSH för att nå någon av de virtuella datorerna i skalningsuppsättningen.

1. Öppna konfigurationsfilen `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Gå till slutet av filen och använd läget för att lägga till genom att välja tangenten A.

1. Klistra in följande kod i slutet av filen:

  ```JSON
  resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
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

    tags = "${var.tags}"
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
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
  }
  ```

1. Öppna konfigurationsfilen `output.tf`.

  ```bash
  vi output.tf
  ```

1. Gå till slutet av filen och använd läget för att lägga till genom att välja tangenten A.

1. Klistra in följande kod i slutet av filen för att visa jumpboxens värdnamn när distributionen är klar:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Avsluta infogningsläget genom att trycka på tangenten Esc.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Distribuera jumpboxen.

  ```bash
  terraform apply 
  ```

När distributionen är klar påminner innehållet i resursgruppen om följande skärmbild:

![Resursgrupp för VM-skalningsuppsättning med Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Möjligheten att logga in med ett lösenord är inaktiverat på jumpboxen och VM-skalningsuppsättningen som du distribuerade. Logga in med SSH för att få åtkomst till den virtuella datorn/de virtuella datorerna.

## <a name="environment-cleanup"></a>Miljörensning 

Ta bort Terraform-resurser som har skapats i den här självstudien genom att ange följande kommando i Cloud Shell:

```bash
terraform destroy
```

Destruktionsprocessen kan ta flera minuter att slutföra.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig använda Terraform för att skapa en VM-skalningsuppsättning för Azure. Här är några ytterligare resurser som du kan använda om du vill lära dig mer om Terraform på Azure: 

 [Terraform Hub på Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform-dokumentation för Azure-providrar](http://aka.ms/terraform)  
 [Terraform-källa för Azure-providrar](http://aka.ms/tfgit)  
 [Terraform-moduler för Azure](http://aka.ms/tfmodules)