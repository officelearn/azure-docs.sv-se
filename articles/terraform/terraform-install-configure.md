---
title: Snabbstart – installera och konfigurera Terraform för att etablera Azure-resurser
description: I den här quicstarten installerar och konfigurerar du Terraform för att skapa Azure-resurser
keywords: azure devops terraform installera konfigurera
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943506"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Snabbstart: Installera och konfigurera Terraform för att etablera Azure-resurser
 
Terraform är ett enkelt sätt att definiera, förhandsgranska och distribuera molninfrastruktur med hjälp av ett [enkelt templatingspråk](https://www.terraform.io/docs/configuration/syntax.html). I den här artikeln beskrivs de nödvändiga stegen för att använda Terraform för att etablera resurser i Azure.

Mer information om hur du använder Terraform med Azure finns i [Terraform Hub](/azure/terraform).
> [!NOTE]
> För Terraform specifik support, vänligen kontakta Terraform direkt med hjälp av en av deras community kanaler:
>
>    * [Terraform-delen](https://discuss.hashicorp.com/c/terraform-core) av community-portalen innehåller frågor, användningsfall och användbara mönster.
>
>    * För leverantörsrelaterade frågor, besök [terraformsleverantörerna](https://discuss.hashicorp.com/c/terraform-providers) på communityportalen.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Terraform installeras som standard i [Cloud Shell](/azure/terraform/terraform-cloud-shell). Om du väljer att installera Terraform lokalt slutför du nästa steg och fortsätter annars att [konfigurera Terraform-åtkomst till Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Installera Terraform

Om du vill installera Terraform [laddar du ned](https://www.terraform.io/downloads.html) lämpligt paket för operativsystemet till en separat installationskatalog. Hämtningen innehåller en enda körbar fil, som du också bör definiera en global sökväg för. Instruktioner om hur du ställer in sökvägen på Linux och Mac finns på [den här webbsidan](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Instruktioner om hur du ställer in sökvägen i Windows finns på [den här webbsidan](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Verifiera sökvägskonfigurationen `terraform` med kommandot. En lista över tillgängliga Terraform-alternativ visas, som i följande exempelutdata:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Konfigurera Terraform-åtkomst till Azure

Skapa ett huvudnamn för [Azure AD-tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli)om du vill aktivera Terraform för att etablera resurser i Azure. Tjänstens huvudnamn ger dina Terraform-skript för att etablera resurser i din Azure-prenumeration.

Om du har flera Azure-prenumerationer frågar du först ditt konto med [az-kontolista](/cli/azure/account#az-account-list) för att få en lista över prenumerations-ID- och klient-ID-värden:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Om du vill använda en vald prenumeration anger du prenumerationen för den här sessionen med [az-kontouppsättningen](/cli/azure/account#az-account-set). Ange `SUBSCRIPTION_ID` att miljövariabeln ska innehålla `id` värdet för det returnerade fältet från den prenumeration som du vill använda:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Nu kan du skapa ett huvudnamn för tjänsten som kan användas med Terraform. Använd [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)och ange *omfattningen* till din prenumeration på följande sätt:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Din `appId` `password`, `sp_name`, `tenant` , och returneras. Anteckna `appId` och `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurera terraform-miljövariabler

Om du vill konfigurera Terraform så att ditt Azure AD-tjänsthuvudnamn används anger du följande miljövariabler, som sedan används av [Azure Terraform-modulerna](https://registry.terraform.io/modules/Azure). Du kan också ställa in miljön om du arbetar med ett Annat Azure-moln än Azure public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Du kan använda följande exempelskalskript för att ange dessa variabler:

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

## <a name="run-a-sample-script"></a>Köra ett exempelskript

Skapa en `test.tf` fil i en tom katalog och klistra in följande skript.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Spara filen och initiera sedan Terraform-distributionen. Det här steget hämtar de Azure-moduler som krävs för att skapa en Azure-resursgrupp.

```bash
terraform init
```

Utdata ser ut ungefär så här:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Du kan förhandsgranska de åtgärder som ska `terraform plan`slutföras av Terraform-skriptet med . När du är redo att skapa resursgruppen använder du Terraform-planen på följande sätt:

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

I den här artikeln har du installerat Terraform eller använt Cloud Shell för att konfigurera Azure-autentiseringsuppgifter och börja skapa resurser i din Azure-prenumeration. Information om hur du skapar en mer komplett Terraform-distribution i Azure finns i följande artikel:

> [!div class="nextstepaction"]
> [Skapa en virtuell Azure-dator med Terraform](terraform-create-complete-vm.md)