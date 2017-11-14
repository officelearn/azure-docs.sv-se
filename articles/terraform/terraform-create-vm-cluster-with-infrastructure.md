---
title: Skapa ett VM-kluster med Terraform och LISTAN
description: "Använda Terraform och HashiCorp konfiguration språk (LISTAN) för att skapa en virtuell Linux-kluster med en belastningsutjämnare i Azure"
keywords: "terraform, devops, virtuella datorer, nätverk, moduler"
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Skapa ett VM-kluster med Terraform och LISTAN

Den här kursen visar hur du skapar ett litet beräkning med den [HashiCorp Configuration språk](https://www.terraform.io/docs/configuration/syntax.html) (LISTAN). Konfigurationen skapar en belastningsutjämnare, två virtuella Linux-datorer i en [tillgänglighetsuppsättning](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy), och alla nödvändiga nätverksresurser.

I den här kursen har du:

> [!div class="checklist"]
> * Konfigurera Azure-autentisering
> * Skapa en konfigurationsfil för Terraform
> * Initiera Terraform
> * Skapa en plan för körning av Terraform
> * Tillämpa åtgärdsplan Terraform

## <a name="1-set-up-azure-authentication"></a>1. Konfigurera Azure-autentisering

> [!NOTE]
> Om du [använda Terraform miljövariabler](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables), eller köra den här kursen i den [Azure Cloud Shell](terraform-cloud-shell.md), hoppa över det här avsnittet.

I det här avsnittet Skapa en Azure tjänstens huvudnamn, och två Terraform configuration-filer som innehåller autentiseringsuppgifter från säkerhetsobjektet.

1. [Konfigurera en Azure AD-tjänstens huvudnamn](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) att aktivera Terraform etablera resurser i Azure. När du skapar objektet anteckna värdena för prenumerations-ID, klient, appId och lösenord.

2. Öppna en kommandotolk.

3. Skapa en tom katalog som ska lagra Terraform filer.

4. Skapa en ny fil som innehåller variabler-deklarationer. Du kan kalla filen något annat med en `.tf` tillägg.

5. Kopiera följande kod i filen variabeldeklarationen:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Skapa en ny fil som innehåller värden för Terraform-variabler. Det är vanligt att namnge filen Terraform variabeln `terraform.tfvars` som Terraform laddas automatiskt en fil med namnet `terraform.tfvars` (eller ett mönster för `*.auto.tfvars`) om det finns i den aktuella katalogen. 

7. Kopiera följande kod i filen variabler. Ersätt platshållarna på följande sätt: för `subscription_id`, Använd Azure prenumerations-ID du angav när du kör `az account set`. För `tenant_id`, använda den `tenant` värdet som returneras från `az ad sp create-for-rbac`. För `client_id`, använda den `appId` värdet som returneras från `az ad sp create-for-rbac`. För `client_secret`, använda den `password` värdet som returneras från `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Skapa en konfigurationsfil för Terraform

I det här avsnittet skapar du en fil som innehåller definitioner för resursen för din infrastruktur.

1. Skapa en ny fil med namnet `main.tf`. 

2. Kopiera följande exempel resursdefinitionerna till den nyligen skapade `main.tf` fil: 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
  }
  ```

## <a name="3-initialize-terraform"></a>3. Initiera Terraform 

Den [terraform init kommandot](https://www.terraform.io/docs/commands/init.html) används för att initiera en katalog som innehåller Terraform konfigurationsfiler - filer som du skapade i föregående avsnitt. Du bör alltid köra den `terraform init` kommandot efter en ny Terraform konfiguration. 

> [!TIP]
> Den `terraform init` kommandot är idempotent vilket innebär att den kan anropas flera gånger när producerar samma resultat. Om du arbetar i en miljö med samarbete och du tror konfigurationsfilerna kan ha ändrats, det därför alltid en bra idé att anropa den `terraform init` kommandot innan körs eller använda en plan.

Om du vill initiera Terraform, kör du följande kommando:

  ```cmd
  terraform init
  ```

  ![Initierar Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Skapa en plan för körning av Terraform

Den [terraform plan kommandot](https://www.terraform.io/docs/commands/plan.html) används för att skapa en Körningsplan. Om du vill generera en åtgärdsplan Terraform aggregerar alla de `.tf` filer i den aktuella katalogen. 

Om du arbetar samarbeta där konfigurationen kan ändra mellan när du skapar körningsplanen och tidpunkten du tillämpa körningsplanen bör du använda den [terraform plan kommando-out-parameter](https://www.terraform.io/docs/commands/plan.html#out-path)spara körningsplanen i en fil. Annars, om du arbetar i en enda person miljö kan du utelämna den `-out` parameter.

Om namnet på filen Terraform variabler inte är `terraform.tfvars` och inte följer den `*.auto.tfvars` mönster, måste du ange namn på filen med den [terraform plan kommando - var filen parametern](https://www.terraform.io/docs/commands/plan.html#var-file-foo) när du kör `terraform plan`kommando.

Vid bearbetning av `terraform plan` kommandot Terraform utför en uppdatering och avgör vilka åtgärder som krävs för att uppnå det tillstånd som anges i konfigurationsfilerna.

Om du inte behöver spara din åtgärdsplan kör du följande kommando:

  ```cmd
  terraform plan
  ```

Om du måste spara din Körningsplan, kör du följande kommando (ersätter den &lt;sökväg > med önskad Utdatasökvägen):

  ```cmd
  terraform plan -out=<path>
  ```

![Skapa en plan för körning av Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Tillämpa åtgärdsplan Terraform

Det sista steget i den här kursen är att använda den [terraform gäller kommandot](https://www.terraform.io/docs/commands/apply.html) att tillämpa en uppsättning åtgärder som genererats av den `terraform plan` kommando.

Om du vill tillämpa den senaste körningsplanen, kör du följande kommando:

  ```cmd
  terraform apply
  ```

Om du vill tillämpa en tidigare sparade åtgärdsplan kör du följande kommando (ersätter den &lt;sökväg > med den sökväg som innehåller den sparade åtgärdsplan):

  ```cmd
  terraform apply <path>
  ```

![Tillämpa en plan för körning av Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Nästa steg

- Bläddra i listan över [Azure Terraform moduler](https://registry.terraform.io/modules/Azure)
- Skapa en [virtuella skaluppsättning med Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)