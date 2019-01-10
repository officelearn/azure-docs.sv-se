---
title: Distribuera med Terraform från Bash i Azure Cloudshell | Microsoft Docs
description: Distribuera med Terraform från Bash i Azure Cloudshell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: a08a4e7df6cf0493ab1aa6aced1abf888a61072a
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159272"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Distribuera med Terraform från Bash i Azure Cloudshell
Den här artikeln visar hur du skapar en resursgrupp med det [Terraform AzureRM-providern](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) är ett verktyg med öppen källkod som kodifieras API: er i deklarativa konfigurationsfiler som kan vara delas teammedlemmar kan redigeras, granskat och version. Microsoft AzureRM-providern används för att interagera med resurser som stöds av Azure Resource Manager via AzureRM APIs. 

## <a name="automatic-authentication"></a>Automatisk autentisering
Terraform är installerad i Bash i Cloud Shell som standard. Cloud Shell autentiserar dessutom automatiskt din Standardprenumeration för Azure CLI för att distribuera resurser via Terraform Azure-moduler.

Terraform använder du standardprenumerationen för Azure CLI som har angetts. Om du vill uppdatera standard prenumerationer kör du:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Genomgång
### <a name="launch-bash-in-cloud-shell"></a>Starta Bash i Cloudshell
1. Starta Cloud Shell från din primära plats
2. Kontrollera din önskade prenumeration har angetts

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Skapa en mall för Terraform
Skapa en ny Terraform-mall med namnet main.tf med dina önskade textredigerare.

```
vim main.tf
```

Kopiera och klistra in följande kod i Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Spara filen och avsluta din textredigerare.

### <a name="terraform-init"></a>Terraform init
Börja genom att köra `terraform init`.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Den [terraform init kommandot](https://www.terraform.io/docs/commands/init.html) används för att initiera en arbetskatalog som innehåller Terraform-konfigurationsfiler. Den `terraform init` kommandot är det första kommandot som ska köras när du skriver en ny konfiguration av Terraform eller klona en befintlig från versionskontroll. Det är säkert att köra det här kommandot flera gånger.

### <a name="terraform-plan"></a>Terraform-plan
Förhandsgranska resurserna som ska skapas av mallen med Terraform `terraform plan`.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

Kommandot [terraform plan](https://www.terraform.io/docs/commands/plan.html) används för att skapa en körningsplan. Terraform utför en uppdatering, såvida inte uttryckligen inaktiveras och sedan avgör vilka åtgärder som krävs för att uppnå önskat tillstånd som anges i konfigurationsfilerna. Planen kan sparas med-ut, och sedan levereras till terraform gäller för att säkerställa att bara inför planerat åtgärder utförs.

### <a name="terraform-apply"></a>Terraform-kommandot apply
Etablera Azure-resurser med `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Den [terraform gäller kommandot](https://www.terraform.io/docs/commands/apply.html) används för att tillämpa de ändringar som krävs för att nå det önskade tillståndet för konfigurationen.

### <a name="verify-deployment-with-azure-cli"></a>Kontrollera distributionen med Azure CLI
Kör `az group show -n myRgName` att verifiera resursen har slutförts etablering.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Rensa med terraform förstör
Rensa resursgruppen som skapades med den [Terraform förstör kommandot](https://www.terraform.io/docs/commands/destroy.html) att rensa Terraform skapade infrastruktur.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Du skapat har en Azure-resurs via Terraform. Gå till nästa steg för att lära dig mer om Cloud Shell.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Terraform Azure-providern](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash i Cloud Shell-Snabbstart](quickstart.md)
