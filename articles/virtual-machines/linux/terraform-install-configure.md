---
title: Installera och konfigurera terraform för att etablera Azure-resurser
description: Lär dig hur du installerar och konfigurerar terraform för att skapa Azure-resurser
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: 02b6ddf723e47e4608e631aef96f8a12d5f68698
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369073"
---
# <a name="install-and-configure-terraform-to-provision-azure-resources"></a>Installera och konfigurera terraform för att etablera Azure-resurser
 
Terraform är ett enkelt sätt att definiera, förhandsgranska och distribuera moln infrastruktur med hjälp av ett [enkelt mall-språk](https://www.terraform.io/docs/configuration/syntax.html). I den här artikeln beskrivs de steg som krävs för att etablera resurser i Azure med hjälp av terraform.

Om du vill veta mer om hur du använder terraform med Azure kan du gå till [terraform Hub](/azure/terraform).
> [!NOTE]
> Om du har terraform-stöd kan du kontakta terraform direkt med någon av deras community-kanaler:
>
>   • [Avsnittet terraform](https://discuss.hashicorp.com/c/terraform-core) i Community-portalen innehåller frågor, användnings fall och användbara mönster.
>
>   • För provider-relaterade frågor går du till avsnittet [terraform-providers](https://discuss.hashicorp.com/c/terraform-providers) i Community-portalen.



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform installeras som standard i [Cloud Shell](/azure/terraform/terraform-cloud-shell). Om du väljer att installera terraform lokalt slutför du nästa steg, annars fortsätter att [Konfigurera terraform-åtkomst till Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Installera terraform

Om du vill installera terraform [hämtar](https://www.terraform.io/downloads.html) du lämpligt paket för operativ systemet till en separat installations katalog. Hämtningen innehåller en enda körbar fil som du även bör definiera en global sökväg för. Instruktioner för hur du ställer in sökvägen på Linux och Mac finns på [den här webb sidan](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Instruktioner för hur du anger sökvägen för Windows finns på [den här webb sidan](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Verifiera din Sök vägs konfiguration med kommandot `terraform`. En lista över tillgängliga terraform-alternativ visas, som i följande exempel:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Konfigurera terraform-åtkomst till Azure

Om du vill aktivera terraform för att etablera resurser i Azure skapar du ett [tjänst huvud namn för Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). Tjänstens huvud namn ger dina terraform-skript för att etablera resurser i din Azure-prenumeration.

Om du har flera Azure-prenumerationer ska du först fråga ditt konto med [AZ-konto lista](/cli/azure/account#az-account-list) för att hämta en lista över prenumerations-ID och klient-ID-värden:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Om du vill använda en vald prenumeration ställer du in prenumerationen på den här sessionen med [AZ-kontot](/cli/azure/account#az-account-set). Ange `SUBSCRIPTION_ID`-miljövariabeln så att den innehåller värdet för fältet returnerat `id` från den prenumeration som du vill använda:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Nu kan du skapa ett huvud namn för tjänsten för användning med terraform. Använd [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)och ange *omfånget* till din prenumeration på följande sätt:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Din `appId`, `password`, `sp_name`och `tenant` returneras. Anteckna `appId` och `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurera terraform-miljövariabler

Om du vill konfigurera terraform för att använda Azure AD-tjänstens huvud namn anger du följande miljövariabler som sedan används av [Azure terraform-modulerna](https://registry.terraform.io/modules/Azure). Du kan också ställa in miljön om du arbetar med ett annat Azure-moln än Azure Public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Du kan använda följande exempel kommando skript för att ange variablerna:

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

## <a name="run-a-sample-script"></a>Kör ett exempel skript

Skapa en fil `test.tf` i en tom katalog och klistra in följande skript.

```hcl
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Spara filen och initiera terraform-distributionen. Det här steget laddar ned de Azure-moduler som krävs för att skapa en Azure-resurs grupp.

```bash
terraform init
```

Utdata ser ut ungefär så här:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Du kan förhandsgranska de åtgärder som ska utföras av terraform-skriptet med `terraform plan`. När du är redo att skapa resurs gruppen använder du terraform-planen på följande sätt:

```bash
terraform apply
```

Utdata ser ut ungefär så här:

```console
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

I den här artikeln installerade du terraform eller använde Cloud Shell för att konfigurera Azure-autentiseringsuppgifter och börja skapa resurser i din Azure-prenumeration. Information om hur du skapar en mer fullständig terraform-distribution i Azure finns i följande artikel:

> [!div class="nextstepaction"]
> [Skapa en virtuell Azure-dator med terraform](terraform-create-complete-vm.md)
