---
title: Självstudie – konfigurera Azure Cloud Shell för terraform
description: Använd Terraform med Azure Cloud Shell för att förenkla autentisering och mallkonfiguration.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159137"
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
}
```

Cloud Shell överför de värden som krävs för `azurerm`-providern via miljövariabler när du använder något av `terraform` CLI-kommandona.

## <a name="other-cloud-shell-developer-tools"></a>Andra utvecklingsverktyg för Cloud Shell

Filer och shelltillstånd finns kvar i Azure Storage mellan Cloud Shell-sessioner. Använd [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) för att kopiera och ladda upp filer till Cloud Shell från den lokala datorn.

Azure CLI är tillgängligt i Cloud Shell och är ett utmärkt verktyg för att testa konfigurationer och kontrol lera ditt arbete när `terraform apply` eller `terraform destroy` har slutförts.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett litet virtuellt dator kluster med modul registret](terraform-create-vm-cluster-module.md)
