---
title: Registrera ASDK med Azure | Microsoft Docs
description: Beskriver hur du registrerar Azure stacken med Azure för att aktivera marketplace syndikering och användningsrapportering.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 08a300d0e2d1565428f282a2073d91b5dd08c060
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017007"
---
# <a name="azure-stack-registration"></a>Azure Stack-registrering
Du kan registrera installationen av Azure Stack Development Kit (ASDK) med Azure att hämta marketplace-objekt från Azure och Ställ in commerce rapporterar tillbaka till Microsoft. Registrering krävs för att stödja fullständig Azure Stack-funktioner, inklusive marketplace-syndikeringsfeed. Registrering rekommenderas eftersom du kan testa viktiga Azure Stack-funktioner som marketplace syndikering och användningsrapportering. När du har registrerat Azure Stack rapporterat användning till Azure handel. Du kan se den under den prenumeration som du använde för registrering. Dock debiteras inte ASDK användare för användning rapporterar.

Om du inte registrerar din ASDK, kan du se en **-aktivering krävs** varning om att registrera din Azure-stacken Development Kit. Det är förväntat.

## <a name="prerequisites"></a>Förutsättningar
Innan du använder dessa instruktioner för att registrera ASDK med Azure, se till att du har installerat Azure Stack PowerShell och hämta Azure Stack-verktyg som beskrivs i den [efter distributionskonfiguration](asdk-post-deploy.md) artikel.

Dessutom PowerShell språk läge måste anges till **FullLanguageMode** på den dator som används för att registrera ASDK med Azure. Kontrollera att det aktuella läget för språket är inställd på fullständig, öppna ett upphöjt PowerShell-fönster och kör följande PowerShell-kommandon:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Kontrollera utdata returnerar **FullLanguageMode**. Om något annat språk läge returneras, registreringen måste köras på en annan dator eller språk-läge måste anges till **FullLanguageMode** innan du fortsätter.

## <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure
Följ dessa steg för att registrera ASDK med Azure.

> [!NOTE]
> De här stegen måste köras från en dator som har åtkomst till Privilegierade slutpunkten. För ASDK är som värddator för development kit.

1. Öppna ett PowerShell-konsolen som administratör.  

2. Kör följande PowerShell-kommandon för att registrera ASDK-installationen med Azure. Du måste logga in på både Azure-prenumerationen och den lokala ASDK-installationen. Om du inte har en Azure-prenumeration, men du kan [skapa ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/free/?b=17.06). Registrera Azure Stack ådrar sig utan kostnad på din Azure-prenumeration.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. När skriptet har slutförts ska du se meddelandet: **din miljö är nu registrerad och aktiverad med de angivna parametrarna.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Kontrollera registreringen lyckades
Följ dessa steg om du vill verifiera att registreringen ASDK med Azure lyckades.

1. Logga in på den [Azure Stack-administrationsportalen](https://adminportal.local.azurestack.external).

2. Klicka på **Marketplace Management** > **Lägg till från Azure**.

    ![](media/asdk-register/2.PNG)

3. Om du ser en lista med objekt som är tillgängliga från Azure lyckades aktiveringen.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>Nästa steg
[Lägg till ett Azure-stacken marketplace-objekt](.\.\azure-stack-marketplace.md)
