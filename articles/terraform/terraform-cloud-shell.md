---
title: Självstudiekurs - Konfigurera Azure Cloud Shell för Terraform
description: I den här självstudien använder du Terraform med Azure Cloud Shell för att förenkla autentisering och mallkonfiguration.
keywords: azure devops terraform moln skal
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945335"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Självstudiekurs: Konfigurera Azure Cloud Shell för Terraform

Terraform fungerar bra från en Bash-kommandorad i macOS, Windows eller Linux. Att köra dina Terraform-konfigurationer i Bash-upplevelsen av [Azure Cloud Shell](/azure/cloud-shell/overview) har några unika fördelar. Den här självstudien visar hur du skriver Terraform-skript som distribueras till Azure med hjälp av Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatisk konfiguration av autentiseringsuppgifter

Terraform är installerat och direkt tillgängligt i Cloud Shell. Terraform-skript autentiserar med Azure när de loggas in i Cloud Shell för att hantera infrastruktur utan ytterligare konfiguration. Automatisk autentisering kringgår två manuella processer:
- Skapa ett huvudnamn för Active Directory-tjänsten
- Konfigurera variablerna för Azure Terraform-providern


## <a name="use-modules-and-providers"></a>Använda moduler och leverantörer

Azure Terraform-moduler kräver autentiseringsuppgifter för att komma åt och ändra Azure-resurser. Om du vill använda Terraform-moduler i Cloud Shell lägger du till följande kod:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell skickar obligatoriska `azurerm` värden för leverantören via miljövariabler när du använder något av `terraform` CLI-kommandona.

## <a name="other-cloud-shell-developer-tools"></a>Andra utvecklingsverktyg för Cloud Shell

Filer och shelltillstånd finns kvar i Azure Storage mellan Cloud Shell-sessioner. Använd [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) för att kopiera och ladda upp filer till Cloud Shell från din lokala dator.

Azure CLI är tillgängligt i Cloud Shell och är ett bra `terraform apply` verktyg `terraform destroy` för att testa konfigurationer och kontrollera ditt arbete efter eller avsluta.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett litet vm-kluster med hjälp av modulregistret](terraform-create-vm-cluster-module.md)