---
title: Självstudie – Skapa ett Azure VM-kluster med terraform med hjälp av modulen register
description: Lär dig hur du använder Terraform-moduler för att skapa ett kluster med virtuella Windows-datorer i Azure
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba99f9cdc20448398b339041aeab41fb75495e5d
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969484"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Självstudie: skapa ett Azure VM-kluster med terraform med hjälp av modulen register

Den här artikeln ger dig instruktioner för att skapa ett litet kluster med virtuella datorer med Terraform-modulen [Azure Compute](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). I den här guiden får du lära du dig hur man: 

> [!div class="checklist"]
> * Konfigurera autentisering med Azure
> * Skapa Terraform-mallen
> * Visualisera ändringarna med planen
> * Tillämpa konfigurationen för att skapa klustret med virtuella datorer

Mer information om Terraform finns i [Terraform-dokumentationen](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Konfigurera autentisering med Azure

> [!TIP]
> Om du [använder Terraform-miljövariabler](/azure/virtual-machines/linux/terraform-install-configure), eller kör den här självstudiekursen i [Azure Cloud Shell](/azure/cloud-shell/overview) kan du hoppa över det här steget.

 Läs [Installera Terraform och konfigurera åtkomst till Azure](/azure/virtual-machines/linux/terraform-install-configure) för att skapa ett huvudnamn för Azure-tjänsten. Använd tjänstens huvudnamn för att fylla i följande kod i en ny fil `azureProviderAndCreds.tf` i en tom katalog:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Skapa mallen

Skapa en ny Terraform-mall som heter `main.tf` med följande kod:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

Kör `terraform init` i konfigurationskatalogen. Om du använder version 0.10.6 eller senare av Terraform visas följande utdata:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualisera ändringarna med planen

Kör `terraform plan` för att förhandsgranska infrastrukturen med virtuella datorer som skapas av mallen.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Skapa de virtuella datorerna med Apply

Kör `terraform apply` för etablera de virtuella datorerna på Azure.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Bläddra i listan över Azure terraform-moduler](https://registry.terraform.io/modules/Azure)