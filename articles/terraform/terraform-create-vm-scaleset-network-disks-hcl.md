---
title: Använd Terraform för att skapa en Azure VM-skalningsuppsättning ange
description: Självstudier om hur du använder Terraform att konfigurera och version Azure VM-skalningsuppsättning med ett virtuellt nätverk och hanteras anslutna diskar
keywords: terraform, devops, virtuella datorer, Azure, skala set, nätverk, lagring, moduler
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/04/2018
ms.topic: article
ms.openlocfilehash: 5922bad24c50a9d315aae42ce11a33801b9dbcaf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971841"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Använd Terraform för att skapa en Azure VM-skalningsuppsättning ange

[Azure VM-skalningsuppsättningar](/azure/virtual-machine-scale-sets) kan du skapa och hantera grupper med identiska, läsa in belastningsutjämnade virtuella datorer där antalet instanser av virtuella datorer kan automatiskt öka eller minska som svar på begäran eller ett definierat schema. 

I den här självstudien får du lära dig hur du använder [Azure Cloud Shell](/azure/cloud-shell/overview) att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera en Terraform-distribution
> * Använda variabler och utdata för Terraform-distribution 
> * Skapa och distribuera nätverksinfrastrukturen
> * Skapa och distribuera en virtuell dator och koppla den till nätverket
> * Skapa och distribuera en jumpbox att ansluta till de virtuella datorerna via SSH

> [!NOTE]
> Den senaste versionen av Terraform konfigurationsfiler som används i den här artikeln finns i den [fantastiska Terraform-arkivet på Github](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Installera Terraform**: Följ anvisningarna i artikeln [Terraform och konfigurera åtkomst till Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Skapa ett SSH-nyckelpar**: Om du inte redan har en SSH nyckelpar, följer du instruktionerna i artikeln [hur du skapar och använder ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

1. Bläddra till den [Azure-portalen](http://portal.azure.com).

1. Öppna [Azure Cloudshell](/azure/cloud-shell/overview). Om du inte väljer en miljö tidigare, väljer **Bash** som din miljö.

    ![Cloud Shell-prompten](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till den `clouddrive` directory.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog med namnet `vmss`.

    ```bash
    mkdir vmss
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Skapa filen variabler definitioner
I det här avsnittet definierar du de variabler som anpassar resurser som skapas av Terraform.

I Azure Cloud Shell, utför du följande steg:

1. Skapa en fil med namnet `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i redigeraren:

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

1. Avsluta infogningsläge genom att välja på ESC.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Skapa definitioner för utdatafilen
I det här avsnittet skapar du den fil som beskriver utdata efter distributionen.

I Azure Cloud Shell, utför du följande steg:

1. Skapa en fil med namnet `output.tf`.

    ```bash
    vi output.tf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i Redigeraren för att exponera det fullständigt kvalificerade domännamnet (FQDN) för de virtuella datorerna. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Avsluta infogningsläge genom att välja på ESC.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definiera nätverkets infrastruktur i en mall
I det här avsnittet skapar du följande nätverksinfrastruktur i en ny Azure resursgrupp: 

  - Ett virtuellt nätverk (VNET) med adressutrymmet 10.0.0.0/16 
  - Ett undernät med adressutrymmet för 10.0.2.0/24
  - Två offentliga IP-adresser. En som används av VM scale set belastningsutjämnaren, den andra används för att ansluta till SSH-jumpbox.

I Azure Cloud Shell, utför du följande steg:

1. Skapa en fil med namnet `vmss.tf` ange infrastruktur för att beskriva VM-skalningsuppsättningen.

    ```bash
    vi vmss.tf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i slutet av filen för att exponera det fullständigt kvalificerade domännamnet (FQDN) för de virtuella datorerna. 

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

1. Avsluta infogningsläge genom att välja på ESC.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Etablera nätverksinfrastrukturen
Med hjälp av Azure Cloud Shell från katalogen där du skapade configuration-filer (.tf) utför följande steg:

1. Initiera Terraform.

  ```bash
  terraform init 
  ```

1. Kör följande kommando för att distribuera den definierade infrastrukturen i Azure.

  ```bash
  terraform apply
  ```

  Terraform uppmanar dig att ange ett värde för ”plats” som den **plats** variabeln har definierats i `variables.tf`, men det har aldrig angetts. Du kan ange valfri giltig plats – till exempel ”West US” följt av att välja RETUR. (Använd parenteser kring ett värde med blanksteg).

1. Terraform skriver ut utdata som definierats i den `output.tf` filen. I följande skärmbild visas det fullständiga Domännamnet har formen &lt;id >.&lt; plats >. cloudapp.azure.com. Id-värdet är ett beräknat värde och plats är det värde som du anger när du kör Terraform.

  ![Fullständigt domännamn för offentliga IP-adressen för VM-skalningsuppsättningen](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. I Azure portal-menyn, Välj **resursgrupper** på huvudmenyn.

1. På den **resursgrupper** fliken **myResourceGroup** att visa de resurser som skapades med Terraform.
  ![Nätverksresurser för VM-skalningsuppsättningen](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Lägg till en VM-skalningsuppsättning

I det här avsnittet ska du lära dig hur du lägger till följande resurser i mallen:

- En Azure load balancer och regler för att hantera programmet och koppla den till offentliga IP-adress som konfigurerats tidigare i den här artikeln
- En Azure backend-adresspoolen och tilldela den till belastningsutjämnaren 
- En avsökningsport för hälsotillstånd som används av programmet och konfigureras på belastningsutjämnaren 
- En skalningsuppsättning virtuell dator bakom belastningsutjämnaren som körs på det virtuella nätverket som distribuerats tidigare i den här artikeln
- [Nginx](http://nginx.org/) på noderna i den virtuella datorn skala med [cloud-init](http://cloudinit.readthedocs.io/en/latest/).

I Cloud Shell, utför du följande steg:

1. Öppna den `vmss.tf` konfigurationsfilen.

  ```bash
  vi vmss.tf
  ```

1. Gå till slutet av filen och ange lägga till genom att välja en nyckel.

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
      name              = "osdisk"
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
      }
    }

    tags = "${var.tags}"
}
  ```

1. Avsluta infogningsläge genom att välja på ESC.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Skapa en fil med namnet `web.conf` som fungerar som cloud-init-konfigurationen för virtuella datorer som är en del av skalningsuppsättningen. 

    ```bash
    vi web.conf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i redigeraren:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Avsluta infogningsläge genom att välja på ESC.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Öppna den `variables.tf` konfigurationsfilen.

  ```bash
  vi variables.tf
  ```

1. Gå till slutet av filen och ange lägga till genom att välja en nyckel.

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

1. Avsluta infogningsläge genom att välja på ESC.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Skapa en plan för Terraform att visualisera VM scale set distributionen. (Du måste ange ett lösenord som du väljer, samt platsen för dina resurser.)

  ```bash
  terraform plan
  ```

  Kommandots utdata bör likna följande skärmbild:

  ![Utdata från att skapa virtuella datorns skalningsuppsättning](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Distribuera nya resurser i Azure.

  ```bash
  terraform apply 
  ```

  Kommandots utdata bör likna följande skärmbild:

  ![Terraform VM-skalningsuppsättningen resursgrupp](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Öppna en webbläsare och Anslut till det fullständiga Domännamnet som returnerades av kommandot. 

  ![Resultatet av att bläddra till FQDN](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Lägg till en SSH-jumpbox
En SSH *jumpbox* är en enskild server som du ”gå” via för att komma åt andra servrar i nätverket. I det här steget konfigurerar du följande resurser:

- Ett nätverksgränssnitt (eller jumpbox) som är anslutna till samma undernät som virtuella datorns skalningsuppsättning.

- En virtuell dator som är ansluten till nätverksgränssnittet. Den här jumpbox är tillgänglig via fjärranslutning. När du är ansluten, kan du SSH till någon av de virtuella datorerna i skalningsuppsättningen.

1. Öppna den `vmss.tf` konfigurationsfilen.

  ```bash
  vi vmss.tf
  ```

1. Gå till slutet av filen och ange lägga till genom att välja en nyckel.

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

1. Öppna den `output.tf` konfigurationsfilen.

  ```bash
  vi output.tf
  ```

1. Gå till slutet av filen och ange lägga till genom att välja en nyckel.

1. Klistra in följande kod i slutet av filen för att visa värdnamnet för jumpbox när distributionen är klar:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Avsluta infogningsläge genom att välja på ESC.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Distribuera jumpbox.

  ```bash
  terraform apply 
  ```

När distributionen är klar, liknar innehållet i resursgruppen som visas i följande skärmbild:

![Terraform VM-skalningsuppsättningen resursgrupp](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Möjligheten att logga in med ett lösenord är inaktiverat på jumpboxen och på VM-skalningsuppsättningen att du har distribuerat. Logga in med SSH för att få åtkomst till virtuella datorer.

## <a name="environment-cleanup"></a>Miljö-rensning 

Ta bort Terraform-resurser som har skapats i den här självstudien genom att ange följande kommando i Cloud Shell:

```bash
terraform destroy
```

Destruktion-processen kan ta flera minuter att slutföra.

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder Terraform för att skapa en Azure VM scale Sets. Här följer några ytterligare resurser för att lära dig mer om Terraform på Azure: 

 [Terraform-hubb på Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentationen för Terraform Azure-provider](http://aka.ms/terraform)  
 [Terraform Azure-providern källa](http://aka.ms/tfgit)  
 [Terraform Azure-moduler](http://aka.ms/tfmodules)