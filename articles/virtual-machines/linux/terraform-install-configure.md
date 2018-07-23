---
title: Installera och konfigurera Terraform för användning med Azure | Microsoft Docs
description: Lär dig att installera och konfigurera Terraform för att skapa Azure-resurser
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 1af96b686a1502d638b4335e22259b79169d1065
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173255"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installera och konfigurera Terraform för att etablera virtuella datorer och annan infrastruktur till Azure
 
Terraform ger ett enkelt sätt att definiera, förhandsgranska och distribuera moln-infrastruktur med hjälp av en [enkel mall språk](https://www.terraform.io/docs/configuration/syntax.html). Den här artikeln beskriver de steg som krävs för att använda Terraform att etablera resurser i Azure.

Mer information om hur du använder Terraform med Azure, på den [Terraform Hub](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform installeras som standard i den [Cloud Shell](/azure/terraform/terraform-cloud-shell). Om du väljer att installera Terraform lokalt, utföra nästa steg, Fortsätt annars till [konfigurera Terraform åtkomst till Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Installera Terraform

Installera Terraform, [hämta](https://www.terraform.io/downloads.html) lämpligt paket för ditt operativsystem i en separat installationskatalog. Den hämtade filen innehåller en enda körbar fil som ska du också definiera en global sökväg. Anvisningar om hur du anger sökvägen för Linux och Mac finns i [den här webbsidan](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Anvisningar för hur du anger sökvägen för Windows, går du till [den här webbsidan](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Kontrollera sökvägen konfigurationen med den `terraform` kommando. En lista över tillgängliga Terraform alternativ visas som i följande Exempelutdata:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Konfigurera Terraform åtkomst till Azure

För att aktivera Terraform att etablera resurser i Azure, skapa en [Azure AD-tjänstobjekt](/cli/azure/create-an-azure-service-principal-azure-cli). Tjänstens huvudnamn ger skripten Terraform att etablera resurser i Azure-prenumerationen.

Om du har flera Azure-prenumerationer först fråga ditt konto med [az konto show](/cli/azure/account#az-account-show) att hämta en lista över prenumerations-ID och klient-ID-värden:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Om du vill använda en vald prenumeration, Ställ in prenumerationen för den här sessionen med [az-kontogrupper](/cli/azure/account#az-account-set). Ange den `SUBSCRIPTION_ID` miljövariabeln för värdet för den returnerade `id` från den prenumeration som du vill använda:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Nu kan du skapa ett huvudnamn för tjänsten för användning med Terraform. Använd [az ad sp create-for-rbac]/cli/azure/ad/sp#az-ad-sp-create-for-rbac), och ange den *omfång* till din prenumeration på följande sätt:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Din *appId*, *lösenord*, *sp_name*, och *klient* returneras. Anteckna värdena för *appId* och *password*.

## <a name="configure-terraform-environment-variables"></a>Konfigurera Terraform miljövariabler

Om du vill konfigurera Terraform för att använda AD-tjänstens huvudnamn, ange följande miljövariabler, som sedan används av den [Azure Terraform-moduler](https://registry.terraform.io/modules/Azure). Du kan också ange miljön om du arbetar med en azuremolnet än Azure offentlig.

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Du kan använda följande exempelskript shell för att ange de variablerna:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Kör ett exempelskript

Skapa en fil `test.tf` i en tom katalog och klistra in följande skript.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Spara filen och sedan starta Terraform-distributionen. Det här steget laddar du ned Azure-moduler krävs för att skapa en Azure-resursgrupp.

```bash
terraform init
```

Utdata ser ut ungefär så här:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Du kan förhandsgranska åtgärderna som ska utföras med Terraform-skriptet med `terraform plan`. När du är klar att skapa resursgruppen gäller avtalet Terraform på följande sätt:

```bash
terraform apply
```

Utdata ser ut ungefär så här:

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du installerat Terraform eller används Cloud Shell för att konfigurera autentiseringsuppgifter för Azure och börja skapa resurser i Azure-prenumerationen. Om du vill skapa en mer komplett Terraform-distribution i Azure finns i följande artikel:

> [!div class="nextstepaction"]
> [Skapa en Azure virtuell dator med Terraform](terraform-create-complete-vm.md)