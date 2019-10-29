---
title: Självstudie – Skapa ett Azure VM-kluster med terraform och HCL
description: Använd terraform och HCL för att skapa ett virtuellt Linux-dator kluster med en belastningsutjämnare i Azure
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 7adf3afe993a01357abcae846f19f602a49862bc
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969475"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Självstudie: skapa ett Azure VM-kluster med terraform och HCL

I den här självstudien får du se hur du skapar ett litet beräknings kluster med hjälp av [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Du får lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Konfigurera Azure-autentisering.
> * Skapa en konfigurations fil för terraform.
> * Använd en konfigurations fil för terraform för att skapa en belastningsutjämnare.
> * Använd en konfigurations fil för terraform för att distribuera två virtuella Linux-datorer i en tillgänglighets uppsättning.
> * Initiera Terraform.
> * Skapa en terraform körnings plan.
> * Använd terraform-körnings planen för att skapa Azure-resurserna.

## <a name="1-set-up-azure-authentication"></a>1. Konfigurera Azure-autentisering

> [!NOTE]
> Om du [använder Terraform-miljövariabler](/azure/virtual-machines/linux/terraform-install-configure), eller kör den här självstudiekursen i [Azure Cloud Shell](terraform-cloud-shell.md) kan du hoppa över det här avsnittet.

I det här avsnittet skapar du ett huvudnamn för Azure-tjänsten och två Terraform-konfigurationsfiler som innehåller autentiseringsuppgifterna från säkerhetsobjektetet.

1. [Konfigurera ett huvudnamn för Azure AD-tjänsten](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) för att se till att Terraform kan tillhandahålla resurser till Azure. När du skapar huvudnamnet antecknar du värdena för prenumerations-ID, klient, appId och lösenord.

2. Öppna en kommandotolk.

3. Skapa en tom katalog där du vill lagra dina Terraform-filer.

4. Skapa en ny fil som innehåller dina variabeldeklarationer. Du kan kalla den här filen vad du vill med tillägget `.tf`.

5. Kopiera följande kod till din variabeldeklarationsfil:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Skapa en ny fil som innehåller värden för dina Terraform-variabler. Det är vanligt att namnge variabel filen terraform `terraform.tfvars` eftersom terraform automatiskt läser in en fil med namnet `terraform.tfvars` (eller följer ett mönster i `*.auto.tfvars`) om den finns i den aktuella katalogen. 

7. Kopiera följande kod till din variabelfil. Se till att ersätta platshållarna på följande sätt: för `subscription_id` använder du ID:t för Azure-prenumeration som du angav när du körde `az account set`. För `tenant_id` använder du värdet `tenant` som returneras från `az ad sp create-for-rbac`. För `client_id` använder du värdet `appId` som returneras från `az ad sp create-for-rbac`. För `client_secret` använder du värdet `password` som returneras från `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. skapa en konfigurations fil för terraform

I det här avsnittet skapar du en fil som innehåller resursdefinitionerna för din infrastruktur.

1. Skapa en ny fil med namnet `main.tf`. 

2. Kopiera följande exempel på resursdefinitioner till den nyligen skapade filen `main.tf`: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
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
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
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

## <a name="3-initialize-terraform"></a>3. initiera terraform 

[Kommandot terraform init](https://www.terraform.io/docs/commands/init.html) används för att initiera en katalog som innehåller Terraform-konfigurationsfilerna – filerna som du skapade med föregående avsnitt. Det är en bra idé att alltid köra kommandot `terraform init` när du har skrivit en ny terraform-konfiguration. 

> [!TIP]
> Kommandot `terraform init` är idempotent, vilket innebär att det kan anropas upprepade gånger och ge samma resultat. Om du arbetar i en samarbetsmiljö och du tror att konfigurationsfilerna kan ha ändrats, är det därför alltid en bra idé att anropa kommandot `terraform init` innan du kör eller tillämpar en plan.

Initiera Terraform genom att köra följande kommando:

  ```bash
  terraform init
  ```

  ![Initiering av Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. skapa en terraform körnings plan

Kommandot [terraform plan](https://www.terraform.io/docs/commands/plan.html) används för att skapa en körningsplan. Terraform samlar alla `.tf`-filer i den aktuella katalogen för att skapa en körningsplan. 

[Parametern-out](https://www.terraform.io/docs/commands/plan.html#out-path) sparar körnings planen i en utdatafil. Den här funktionen löser samtidiga problem i flera dev-miljöer. Ett sådant problem som löses av utdatafilen är följande scenario:

1. Dev 1 skapar konfigurations filen.
1. Dev 2 ändrar konfigurations filen.
1. Dev 1 gäller (kör) konfigurations filen.
1. Dev 1 får oväntade resultat som inte vet att dev 2 ändrade konfigurationen.

Dev 1 om du anger en utdatafil hindras dev 2 från att påverka dev 1. 

Om du inte behöver spara din körnings plan kör du följande kommando:

  ```bash
  terraform plan
  ```

Om du behöver spara din körnings plan kör du följande kommando. Ersätt plats hållarna med lämpliga värden för din miljö.

  ```bash
  terraform plan -out=<path>
  ```

En annan användbar parameter är [-var-fil](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Som standard försöker terraform hitta din variabel fil på följande sätt:
- Fil med namnet `terraform.tfvars`
- Fil med namnet med hjälp av följande mönster: `*.auto.tfvars`

Din variabel fil behöver dock inte följa någon av de två föregående konventionerna. I så fall anger du variabelns fil namn med parametern `-var-file`. I följande exempel visas den här punkten:

```hcl
terraform plan -var-file <my-variables-file.tf>
```

Terraform avgör vilka åtgärder som krävs för att uppnå det tillstånd som anges i konfigurations filen.

![Skapa en plan för Terraform-körning](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. tillämpa terraform-körnings planen

Det sista steget i den här självstudien är att använda [terraform-kommandot apply](https://www.terraform.io/docs/commands/apply.html) för att tillämpa uppsättningen åtgärder som skapats av kommandot `terraform plan`.

Kör följande kommando om du vill tillämpa den senaste körningsplanen:

  ```bash
  terraform apply
  ```

Om du vill använda en tidigare sparad körnings plan kör du följande kommando. Ersätt plats hållarna med lämpliga värden för din miljö:

  ```bash
  terraform apply <path>
  ```

![Tillämpa en Terraform-körningsplan](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en skalnings uppsättning för virtuella Azure-datorer med terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
