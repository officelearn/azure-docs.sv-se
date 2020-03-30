---
title: Distribuera med Terraform från Azure Cloud Shell | Microsoft-dokument
description: Distribuera med Terraform från Azure Cloud Shell
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
ms.openlocfilehash: 8bacadd8941131f608411e61cc15c120c1b2bc60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458162"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Distribuera med Terraform från Bash i Azure Cloud Shell
I den här artikeln får du hjälp med att skapa en resursgrupp med [Terraform AzureRM-providern](https://www.terraform.io/docs/providers/azurerm/index.html).

[Hashicorp Terraform](https://www.terraform.io/) är ett verktyg med öppen källkod som kodifierar API:er i deklarativa konfigurationsfiler som kan delas mellan gruppmedlemmar som ska redigeras, granskas och versionshanteras. Microsoft AzureRM-providern används för att interagera med resurser som stöds av Azure Resource Manager via AzureRM API:er.

## <a name="automatic-authentication"></a>Automatisk autentisering
Terraform installeras som standard i Bash i Cloud Shell. Dessutom autentiserar Cloud Shell automatiskt din standardprenumeration för Azure CLI för att distribuera resurser via Terraform Azure-modulerna.

Terraform använder standardprenumerationen för Azure CLI som har angetts. Om du vill uppdatera standardprenumerationer kör du:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Genomgång
### <a name="launch-bash-in-cloud-shell"></a>Starta Bash i Cloud Shell
1. Starta Cloud Shell från önskad plats
2. Verifiera att önskad prenumeration är inställd

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Skapa en Terraform-mall
Skapa en ny Terraform-mall med namnet main.tf med önskad textredigerare.

```
vim main.tf
```

Kopiera/klistra in följande kod i Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Spara filen och avsluta textredigeraren.

### <a name="terraform-init"></a>Terraform init
Börja med `terraform init`att köra .

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

Terraform [init-kommandot](https://www.terraform.io/docs/commands/init.html) används för att initiera en arbetskatalog som innehåller Terraform-konfigurationsfiler. Kommandot `terraform init` är det första kommandot som ska köras efter att ha skrivit en ny Terraform-konfiguration eller kloning av en befintlig från versionskontrollen. Det är säkert att köra det här kommandot flera gånger.

### <a name="terraform-plan"></a>Terraform-plan
Förhandsgranska de resurser som ska skapas `terraform plan`av Terraform-mallen med .

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

Kommandot [terraform plan](https://www.terraform.io/docs/commands/plan.html) används för att skapa en körningsplan. Terraform utför en uppdatering, om inte uttryckligen inaktiveras, och bestämmer sedan vilka åtgärder som är nödvändiga för att uppnå önskat tillstånd som anges i konfigurationsfilerna. Planen kan sparas med -out och sedan tillhandahållas för terraform gäller för att säkerställa att endast de förplanerade åtgärderna utförs.

### <a name="terraform-apply"></a>Terraform-kommandot apply
Etablera Azure-resurser `terraform apply`med .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Terraform apply-kommandot](https://www.terraform.io/docs/commands/apply.html) används för att tillämpa de ändringar som krävs för att nå önskat tillstånd för konfigurationen.

### <a name="verify-deployment-with-azure-cli"></a>Verifiera distribution med Azure CLI
Kör `az group show -n myRgName` för att verifiera att resursen har lyckats etablera.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Städa upp med terraform förstöra
Rensa resursgruppen som skapats med [kommandot Terraform destroy](https://www.terraform.io/docs/commands/destroy.html) för att rensa terraform-skapad infrastruktur.

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

Du har skapat en Azure-resurs via Terraform. Besök nästa steg för att fortsätta lära dig mer om Cloud Shell.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Terraform Azure-providern](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash i Cloud Shell snabbstart](quickstart.md)
