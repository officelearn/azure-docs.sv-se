---
title: Självstudie – konfigurera Azure Cloud Shell för terraform
description: I den här självstudien använder du terraform med Azure Cloud Shell för att förenkla autentisering och konfiguration av mallar.
keywords: Azure DevOps terraform Cloud Shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945335"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Självstudie: Konfigurera Azure Cloud Shell för terraform

Terraform fungerar bra från en bash-kommando rad i macOS, Windows eller Linux. Det finns vissa unika fördelar med att köra dina terraform-konfigurationer i bash-upplevelsen av [Azure Cloud Shell](/azure/cloud-shell/overview) . Den här självstudien visar hur du skriver terraform-skript som distribueras till Azure med hjälp av Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Terraform är installerat och direkt tillgängligt i Cloud Shell. Terraform-skript autentiseras med Azure när du är inloggad på Cloud Shell för att hantera infrastrukturen utan ytterligare konfiguration. Automatisk autentisering kringgår två manuella processer:
- Skapa ett huvud namn för Active Directory tjänsten
- Konfigurera Azure terraform-providerns variabler


## <a name="use-modules-and-providers"></a>Använda moduler och providers

Azure terraform-moduler kräver autentiseringsuppgifter för att komma åt och ändra Azure-resurser. Om du vill använda terraform-moduler i Cloud Shell lägger du till följande kod:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell överför de värden som krävs för `azurerm`-providern via miljövariabler när du använder något av `terraform` CLI-kommandona.

## <a name="other-cloud-shell-developer-tools"></a>Andra utvecklingsverktyg för Cloud Shell

Filer och shelltillstånd finns kvar i Azure Storage mellan Cloud Shell-sessioner. Använd [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) för att kopiera och ladda upp filer till Cloud Shell från den lokala datorn.

Azure CLI är tillgängligt i Cloud Shell och är ett utmärkt verktyg för att testa konfigurationer och kontrol lera ditt arbete när `terraform apply` eller `terraform destroy` har slutförts.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett litet virtuellt dator kluster med modul registret](terraform-create-vm-cluster-module.md)