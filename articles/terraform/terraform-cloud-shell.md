---
title: "Använda Terraform med Azure-molnet Shell"
description: "Använd Terraform med Azure Cloud Shell för att förenkla autentisering och konfigureras."
keywords: "terraform, devops, skala ange, virtuella datorer, nätverk, lagring, moduler"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 253e5d341f93e61d851893eb05832fbf35707cfc
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="terraform-cloud-shell-development"></a>Terraform moln Shell-utveckling 

Terraform passar utmärkt från en kommandorad för Bash, till exempel macOS Terminal eller Bash på Windows- eller Linux. Kör ditt Terraform konfigurationer i Bash upplevelse av den [Azure Cloud Shell](/azure/cloud-shell/overview) har vissa unika fördelar påskynda utvecklingen.

Den här artikeln begrepp täcker molnet Shell funktioner som hjälper dig skriva Terraform skript som distribuerar till Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk behörighetskonfigurationen

Terraform är installerat och omedelbart tillgängliga i molnet Shell. Terraform skript autentisera med Azure när inloggad till molnet Shell att hantera infrastrukturen utan någon ytterligare konfiguration. Automatisk autentisering kringgår behovet av att manuellt skapa en Active Directory-tjänstens huvudnamn och konfigurera Azure Terraform providern variabler.


## <a name="using-modules-and-providers"></a>Med hjälp av moduler och Providers

Azure Terraform moduler kräver autentiseringsuppgifter för att komma åt och ändra till resurserna i din Azure-prenumeration. När du arbetar i molnet-gränssnittet, lägger du till följande kod för att använda Azure Terraform moduler i molnet Shell-skript:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Molnet Shell skickar värden som krävs den `azurerm` providern via miljövariabler när något av de `terraform` CLI-kommandona.

## <a name="other-cloud-shell-developer-tools"></a>Andra molntjänster Shell utvecklingsverktygen

Filer och shell tillstånd finns kvar i Azure Storage mellan moln Shell-sessioner. Använd [Azure Lagringsutforskaren](/azure/vs-azure-tools-storage-manage-with-storage-explorer) att kopiera och överföra filer till molnet Shell från den lokala datorn.

Azure CLI 2.0 finns i molnet-gränssnittet och är ett bra verktyg för att testa konfigurationer och kontroll av ditt arbete när en `terraform apply` eller `terraform destroy` har slutförts.


## <a name="next-steps"></a>Nästa steg

[Skapa en mindre VM-kluster med hjälp av modulen registret](terraform-create-vm-cluster-module.md)
[skapa en mindre VM-kluster med hjälp av anpassade LISTAN](terraform-create-vm-cluster-with-infrastructure.md)
