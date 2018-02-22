---
title: "Distribuera med Terraform från Bash i Azure-molnet Shell | Microsoft Docs"
description: "Distribuera med Terraform från Bash i Azure-molnet Shell"
services: Azure
documentationcenter: 
author: tomarcher
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 6df6a3a5242e0a5fc5c03136e1cd20967a93487a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Distribuera med Terraform från Bash i Azure-molnet Shell
Den här artikeln vägleder dig genom att skapa en resursgrupp med det [Terraform AzureRM provider](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) är ett verktyg med öppen källkod som kodifieras API: er i deklarativ konfigurationsfiler som kan delas i gruppmedlemmarna kan redigeras, granska och en ny version. Microsoft AzureRM-providern används för att interagera med resurser som stöds av Azure Resource Manager via AzureRM APIs. 

## <a name="automatic-authentication"></a>Automatisk autentisering
Terraform installeras som standard i Bash i molnet Shell. Dessutom verifierar molnet Shell automatiskt prenumerationen standard Azure CLI 2.0 för att distribuera resurser via Terraform Azure-moduler.

Terraform använder Azure CLI 2.0 standardabonnemang som har angetts. Om du vill uppdatera standard prenumerationer kör du:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Genomgång
### <a name="launch-bash-in-cloud-shell"></a>Starta Bash i molnet Shell
1. Starta molnet Shell från din önskade plats
2. Kontrollera prenumerationen prioriterade anges

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Skapa en mall för Terraform
Skapa en ny Terraform mall som heter main.tf med din önskade textredigerare.

```
vim main.tf
```

Kopiera och klistra in följande kod i molnet Shell.

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

Den [terraform init kommandot](https://www.terraform.io/docs/commands/init.html) används för att initiera en arbetskatalog som innehåller Terraform konfigurationsfiler. Den `terraform init` kommandot är det första kommandot som ska köras när du skriver en ny konfiguration för Terraform eller klona en befintlig från versionskontroll. Det är säkert att köra det här kommandot flera gånger.

### <a name="terraform-plan"></a>Terraform plan
Förhandsgranska resurserna som ska skapas med mallen Terraform med `terraform plan`.

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

Den [terraform plan kommandot](https://www.terraform.io/docs/commands/plan.html) används för att skapa en Körningsplan. Terraform utför en uppdatering, såvida inte uttryckligen inaktiveras och sedan avgör vilka åtgärder som krävs för att uppnå det tillstånd som anges i konfigurationsfilerna. Planen kan sparas med-ut, och sedan att terraform tillämpa för att kontrollera endast före planerade åtgärder utförs.

### <a name="terraform-apply"></a>Tillämpa Terraform
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

Den [terraform gäller kommandot](https://www.terraform.io/docs/commands/apply.html) används för att tillämpa de ändringar som krävs för att nå det önskade tillståndet i konfigurationen.

### <a name="verify-deployment-with-azure-cli-20"></a>Kontrollera distributionen med Azure CLI 2.0
Kör `az group show -n myRgName` att verifiera att resursen har slutförts etablering.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Rensa med terraform förstör
Ta bort resursgruppen som skapats med den [Terraform förstör kommandot](https://www.terraform.io/docs/commands/destroy.html) att rensa Terraform-skapa infrastruktur.

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

Du har skapat en Azure-resurs via Terraform. Gå vidare om du vill fortsätta lära dig mer om molntjänster Shell.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Terraform Azure-providern](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash i molnet Shell-Snabbstart](quickstart.md)