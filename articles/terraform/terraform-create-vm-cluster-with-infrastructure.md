---
title: Skapa ett VM-kluster med Terraform och HCL
description: Använd Terraform och HashiCorp Configuration Language (HCL) för att skapa ett Linux VM-kluster med en belastningsutjämnare i Azure
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuell dator, nätverk, moduler
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/13/2017
ms.openlocfilehash: a53fee8ee492de4d9eaa8b45a8d4a88e692da02d
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410378"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Skapa ett VM-kluster med Terraform och HCL

I den här självstudien visas hur du skapar ett litet beräkningskluster med [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). Konfigurationen skapar en belastningsutjämnare, två virtuella Linux-datorer i en [tillgänglighetsuppsättning](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) och alla nödvändiga nätverksresurser.

I den här kursen för du göra följande:

> [!div class="checklist"]
> * Konfigurera Azure-autentisering
> * Skapa en Terraform-konfigurationsfil
> * Initiera Terraform
> * Skapa en Terraform-körningsplan
> * Använd Terraform-körningsplanen

## <a name="1-set-up-azure-authentication"></a>1. Konfigurera Azure-autentisering

> [!NOTE]
> Om du [använder Terraform-miljövariabler](/azure/virtual-machines/linux/terraform-install-configure), eller kör den här självstudiekursen i [Azure Cloud Shell](terraform-cloud-shell.md) kan du hoppa över det här avsnittet.

I det här avsnittet skapar du ett huvudnamn för Azure-tjänsten och två Terraform-konfigurationsfiler som innehåller autentiseringsuppgifterna från säkerhetsobjektetet.

1. [Konfigurera ett huvudnamn för Azure AD-tjänsten](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) för att se till att Terraform kan tillhandahålla resurser till Azure. När du skapar huvudnamnet antecknar du värdena för prenumerations-ID, klient, appId och lösenord.

2. Öppna en kommandotolk.

3. Skapa en tom katalog där du vill lagra dina Terraform-filer.

4. Skapa en ny fil som innehåller dina variabeldeklarationer. Du kan kalla den här filen vad du vill med tillägget `.tf`.

5. Kopiera följande kod till din variabeldeklarationsfil:

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

6. Skapa en ny fil som innehåller värden för dina Terraform-variabler. Det är vanligt att ge Terraform-variabelfilen namnet `terraform.tfvars` eftersom Terraform automatiskt läser in en fil som heter `terraform.tfvars` (eller enligt mönstret `*.auto.tfvars`) om den finns i den aktuella katalogen. 

7. Kopiera följande kod till din variabelfil. Se till att ersätta platshållarna så här: För `subscription_id` använder du Azure-prenumerationens ID som du angav för att köra `az account set`. För `tenant_id` använder du värdet `tenant` som returneras från `az ad sp create-for-rbac`. För `client_id` använder du värdet `appId` som returneras från `az ad sp create-for-rbac`. För `client_secret` använder du värdet `password` som returneras från `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Skapa en Terraform-konfigurationsfil

I det här avsnittet skapar du en fil som innehåller resursdefinitionerna för din infrastruktur.

1. Skapa en ny fil med namnet `main.tf`. 

2. Kopiera följande exempel på resursdefinitioner till den nyligen skapade filen `main.tf`: 

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

[Kommandot terraform init](https://www.terraform.io/docs/commands/init.html) används för att initiera en katalog som innehåller Terraform-konfigurationsfilerna – filerna som du skapade med föregående avsnitt. Det är en bra idé att alltid köra kommandot `terraform init` när du har skrivit en ny Terraform-konfiguration. 

> [!TIP]
> Kommandot `terraform init` är idempotent, vilket innebär att det kan anropas upprepade gånger och ge samma resultat. Om du arbetar i en samarbetsmiljö och du tror att konfigurationsfilerna kan ha ändrats, är det därför alltid en bra idé att anropa kommandot `terraform init` innan du kör eller tillämpar en plan.

Initiera Terraform genom att köra följande kommando:

  ```cmd
  terraform init
  ```

  ![Initiering av Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Skapa en Terraform-körningsplan

Kommandot [terraform plan](https://www.terraform.io/docs/commands/plan.html) används för att skapa en körningsplan. Terraform samlar alla `.tf`-filer i den aktuella katalogen för att skapa en körningsplan. 

Om du arbetar i en samarbetsmiljö där konfigurationen kan ändras mellan tidpunkten då du skapar körningsplanen och tidpunkten då du använder körningsplanen bör du använda [parametern -out för kommandot terraform plan](https://www.terraform.io/docs/commands/plan.html#out-path) för att spara körningsplanen i en fil. Om du arbetar i en miljö med en enda person kan du utesluta parametern `-out`.

Om namnet på Terraform-variabelfilen inte är `terraform.tfvars` och den inte följer mönstret `*.auto.tfvars`, måste du ange filnamnet med [parametern -var-file för kommandot terraform plan](https://www.terraform.io/docs/commands/plan.html#var-file-foo) när du kör kommandot `terraform plan`.

Vid bearbetning av kommandot `terraform plan` utför Terraform en uppdatering och avgör vilka åtgärder som krävs för att uppnå det önskade tillstånd som anges i konfigurationsfilerna.

Om du inte behöver spara din körningsplan kör du följande kommando:

  ```cmd
  terraform plan
  ```

Om du vill spara din körningsplan kör du följande kommando (ersätt platshållaren &lt;path> med önskad sökväg för utdata):

  ```cmd
  terraform plan -out=<path>
  ```

![Skapa en plan för Terraform-körning](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Använd Terraform-körningsplanen

Det sista steget i den här självstudien är att använda [terraform-kommandot apply](https://www.terraform.io/docs/commands/apply.html) för att tillämpa uppsättningen åtgärder som skapats av kommandot `terraform plan`.

Kör följande kommando om du vill tillämpa den senaste körningsplanen:

  ```cmd
  terraform apply
  ```

Om du vill tillämpa en tidigare sparad körningsplan kör du följande kommando (ersätt platshållaren &lt;path> med sökvägen som innehåller den sparade körningsplanen):

  ```cmd
  terraform apply <path>
  ```

![Tillämpa en Terraform-körningsplan](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Nästa steg

- Bläddra i listan över [Azure Terraform-moduler](https://registry.terraform.io/modules/Azure)
- Skapa en [VM-skalningsuppsättning för med Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
