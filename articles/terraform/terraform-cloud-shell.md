---
title: Självstudie – konfigurera Azure Cloud Shell för terraform
description: Använd Terraform med Azure Cloud Shell för att förenkla autentisering och mallkonfiguration.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 693ed462fb1ba3dfed079e8ae97152732c771253
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969582"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Självstudie: Konfigurera Azure Cloud Shell för terraform

Terraform fungerar bra från en bash-kommando rad i macOS, Windows eller Linux. Att köra dina terraform-konfigurationer i bash-upplevelsen av [Azure Cloud Shell](/azure/cloud-shell/overview) har några unika fördelar. Den här självstudien visar hur du skriver terraform-skript som distribueras till Azure med hjälp av Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Terraform är installerat och direkt tillgängligt i Cloud Shell. Terraform-skript autentiseras med Azure när du har loggat in på Cloud Shell för att hantera infrastruktur utan ytterligare konfiguration. Automatisk autentisering kringgår två manuella processer:
- Skapar ett huvud namn för Active Directory tjänsten.
- Konfigurera Azure terraform-providerns variabler.


## <a name="using-modules-and-providers"></a>Använda moduler och providrar

Azure terraform-moduler kräver autentiseringsuppgifter för att komma åt och ändra Azure-resurser. Om du vill använda terraform-moduler i Cloud Shell lägger du till följande kod:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell skickar värden som krävs för `azurerm`-providern via miljövariabler när du använder något CLI-kommando för `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Andra utvecklingsverktyg för Cloud Shell

Filer och shelltillstånd finns kvar i Azure Storage mellan Cloud Shell-sessioner. Använd [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) för att kopiera och ladda upp filer till Cloud Shell från din lokala dator.

Azure CLI är tillgängligt i Cloud Shell och är ett bra verktyg för att testa konfigurationer och kontrollera ditt arbete när `terraform apply` eller `terraform destroy` har slutförts.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett litet virtuellt dator kluster med modul registret](terraform-create-vm-cluster-module.md)