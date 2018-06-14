---
title: Använd Terraform moduler för att skapa ett VM-kluster i Azure
description: Lär dig hur du använder Terraform moduler för att skapa en virtuell Windows-kluster i Azure
keywords: terraform, devops, virtuella datorer, nätverk, moduler
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: e33aef252413eeb243b03543f171d5f1e2385b48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29952225"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Skapa ett VM-kluster med Terraform med hjälp av modulen registret

Den här artikeln vägleder dig genom att skapa ett litet VM-kluster med Terraform [Azure compute modulen](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). I den här självstudiekursen får du lära du dig att: 

> [!div class="checklist"]
> * Konfigurera autentisering med Azure
> * Skapa mall för Terraform
> * Visualisera ändringarna med planen
> * Tillämpa konfigurationen för att skapa virtuella datorns kluster

Mer information om Terraform finns i [Terraform dokumentationen](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Konfigurera autentisering med Azure

> [!TIP]
> Om du [använda Terraform miljövariabler](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) eller köra den här kursen i den [Azure Cloud Shell](/azure/cloud-shell/overview), hoppa över detta steg.

 Granska [Terraform installera och konfigurera åtkomst till Azure](/azure/virtual-machines/linux/terraform-install-configure) att skapa en Azure-tjänstens huvudnamn. Använd den här tjänstens huvudnamn för att fylla i en ny fil `azureProviderAndCreds.tf` i en tom katalog med följande kod:

```tf
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

Skapa en ny mall för Terraform med namnet `main.tf` med följande kod:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Kör `terraform init` i konfigurationskatalogen. Med en Terraform version av minst 0.10.6 visar följande utdata:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualisera ändringarna med planen

Kör `terraform plan` att förhandsgranska den virtuella infrastrukturen som skapas av mallen.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Skapa virtuella datorer med Verkställ

Kör `terraform apply` att etablera de virtuella datorerna på Azure.

![Tillämpa Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Nästa steg

- Bläddra i listan över [Azure Terraform moduler](https://registry.terraform.io/modules/Azure)
- Skapa en [virtuella skaluppsättning med Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
