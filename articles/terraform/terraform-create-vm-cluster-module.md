---
title: Självstudie – Skapa ett Azure VM-kluster med terraform med hjälp av modulen register
description: I den här självstudien använder du terraform-moduler för att skapa ett Windows-kluster för virtuella datorer i Azure
keywords: registret DevOps terraform VM Virtual Machine Cluster module
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945279"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Självstudie: skapa ett Azure VM-kluster med terraform med hjälp av modulen register

Den här artikeln ger dig instruktioner för att skapa ett litet kluster med virtuella datorer med Terraform-modulen [Azure Compute](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). I den här självstudiekursen får du lära du dig att: 

> [!div class="checklist"]
> * Konfigurera autentisering med Azure
> * Skapa Terraform-mallen
> * Visualisera ändringarna med planen
> * Tillämpa konfigurationen för att skapa klustret med virtuella datorer

Mer information om Terraform finns i [Terraform-dokumentationen](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Konfigurera autentisering med Azure

> [!TIP]
> Om du [använder Terraform-miljövariabler](terraform-install-configure.md), eller kör den här självstudiekursen i [Azure Cloud Shell](/azure/cloud-shell/overview) kan du hoppa över det här steget.

 Läs [Installera Terraform och konfigurera åtkomst till Azure](terraform-install-configure.md) för att skapa ett huvudnamn för Azure-tjänsten. Använd tjänstens huvudnamn för att fylla i följande kod i en ny fil `azureProviderAndCreds.tf` i en tom katalog:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

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
    is_windows_image = "true"
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