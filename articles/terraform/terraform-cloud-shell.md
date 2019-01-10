---
title: Använd Terraform med Azure Cloud Shell
description: Använd Terraform med Azure Cloud Shell för att förenkla autentisering och mallkonfiguration.
services: terraform
ms.service: terraform
keywords: terraform, devops, skalningsuppsättning, virtuell dator, nätverk, lagring, moduler
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: af71cf07ea12e605f57d34b530fcbcd61838128b
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074559"
---
# <a name="terraform-cloud-shell-development"></a>Terraform Cloud Shell-utveckling 

Terraform fungerar bra från en kommandorad för Bash, till exempel Terminal på macOS eller Bash på Windows eller Linux. Om du kör dina Terraform-konfigurationer i Bash på [Azure Cloud Shell](/azure/cloud-shell/overview) så får du vissa unika fördelar för att påskynda utvecklingen.

Den här begreppsartikeln handlar om Cloud Shell-funktioner som hjälper dig skriva Terraform-skript som distribueras till Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Terraform är installerat och direkt tillgängligt i Cloud Shell. Terraform-skript autentiseras med Azure när du har loggat in på Cloud Shell för att hantera infrastruktur utan ytterligare konfiguration. Med automatisk autentisering kringgås behovet av att manuellt skapa ett huvudnamn för Active Directory-tjänsten och konfigurera Azure Terraform-providervariablerna.


## <a name="using-modules-and-providers"></a>Använda moduler och providrar

Azure Terraform-moduler kräver autentiseringsuppgifter för att komma åt och ändra resurserna i din Azure-prenumeration. När du arbetar i Cloud Shell lägger du till följande kod i dina skript för att använda Azure Terraform-moduler i Cloud Shell:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell skickar värden som krävs för `azurerm`-providern via miljövariabler när du använder något CLI-kommando för `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Andra utvecklingsverktyg för Cloud Shell

Filer och shelltillstånd finns kvar i Azure Storage mellan Cloud Shell-sessioner. Använd [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) för att kopiera och ladda upp filer till Cloud Shell från din lokala dator.

Azure CLI är tillgängligt i Cloud Shell och är ett bra verktyg för att testa konfigurationer och kontrollera ditt arbete när `terraform apply` eller `terraform destroy` har slutförts.


## <a name="next-steps"></a>Nästa steg

[Skapa ett litet VM-kluster med modulregistret](terraform-create-vm-cluster-module.md)
[Skapa ett litet VM-kluster med anpassat HCL](terraform-create-vm-cluster-with-infrastructure.md)
