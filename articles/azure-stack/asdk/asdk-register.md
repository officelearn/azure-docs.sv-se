---
title: Registrera ASDK med Azure | Microsoft Docs
description: Beskriver hur du registrerar Azure Stack med Azure för att möjliggöra marketplace-syndikering och användningsrapportering.
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
ms.date: 01/16/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: d5463b495620e441ad5371b5859c9ab543e0e7c6
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874672"
---
# <a name="azure-stack-registration"></a>Azure Stack-registrering
Du kan registrera din Azure Stack Development Kit (ASDK)-installation med Azure kan du hämta marketplace från Azure och du ställer in handelsdata rapporterar tillbaka till Microsoft. Registrering krävs för att stödja fullständig Azure Stack-funktioner, inklusive marketplace syndikering. Registrering krävs så att du kan testa viktiga Azure Stack-funktioner som marketplace-syndikering och användningsrapportering. När du har registrerat Azure Stack rapporteras användning till Azure commerce. Du kan se den prenumeration som du använde för registrering. ASDK användare debiteras dock inte för eventuell användning av rapporterar.

Om du inte registrerar din ASDK, kan du se en **aktivering krävs** varning om att registrera din Azure Stack Development Kit. Det här beteendet är förväntat.

## <a name="prerequisites"></a>Förutsättningar
Innan du använder dessa instruktioner för att registrera ASDK med Azure, se till att du har installerat Azure Stack PowerShell och ned Azure Stack-verktyg som beskrivs i den [konfigurationen efter distribution](asdk-post-deploy.md) artikeln.

Dessutom språkläge PowerShell måste anges till **FullLanguageMode** på den dator som används för att registrera ASDK med Azure. För att verifiera att det aktuella språkläget har angetts till full, öppna en upphöjd PowerShell-fönster och kör följande PowerShell-kommandon:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Kontrollera utdata returnerar **FullLanguageMode**. Om något annat språkläge returneras, registreringen måste köras på en annan dator eller språkläget måste anges till **FullLanguageMode** innan du fortsätter.

Azure AD-kontot som används för registreringen måste ha åtkomst till Azure-prenumerationen och har behörighet att skapa program med identiteter och tjänstens huvudnamn i katalogen som är associerade med den aktuella prenumerationen. Vi rekommenderar att du registrerar Azure Stack med Azure med lägsta behörighet administration av [skapar ett tjänstkonto som ska användas för registrering](..\azure-stack-registration-role.md) i stället för autentiseringsuppgifterna för global administratör.

## <a name="register-azure-stack-with-azure"></a>Registrera Azure Stack med Azure
Följ dessa steg för att registrera ASDK med Azure.

> [!NOTE]
> De här stegen måste köras från en dator som har åtkomst till privilegierad slutpunkt. För ASDK är som värddator för development kit.

1. Öppna en PowerShell-konsol som administratör.  

2. Kör följande PowerShell-kommandon för att registrera din ASDK-installation med Azure. Du behöver att logga in på både Azure fakturering prenumerations-ID och den lokala ASDK-installationen. Om du inte har en Azure-fakturering prenumerations-ID än kan du [skapa ett kostnadsfritt konto här](https://azure.microsoft.com/free/?b=17.06). Registrera Azure Stack medför utan kostnad på din Azure-prenumeration.<br><br>Ange ett unikt namn för registrering när du kör den **Set-AzsRegistration** cmdlet. Den **RegistrationName** parametern har ett standardvärde på **AzureStackRegistration**. Om du använder samma namn på fler än en instans av Azure Stack kan misslyckas skriptet.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. När skriptet har slutförts bör du se det här meddelandet: **Din miljö är nu registrerad och aktiverad med hjälp av de angivna parametrarna.**

    ![Din miljö är nu registrerad](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Registrera i frånkopplade miljöer
Om du registrerar Azure Stack i en frånkopplad miljö (som saknar Internetanslutning), måste du skaffa en registrering token från Azure Stack-miljön och sedan använda denna token på en dator som kan ansluta till Azure för att registrera och skapa en aktivering resurs för miljön ASDK.
 
 > [!IMPORTANT]
 > Innan du använder dessa instruktioner för att registrera Azure Stack, se till att du har installerat PowerShell för Azure Stack och ned Azure Stack-verktyg som beskrivs i den [konfigurationen efter distribution](asdk-post-deploy.md) artikeln på båda ASDK värden datorn och datorn med Internetåtkomst som används för att ansluta till Azure och registrera dig.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Hämta en registrering åtkomsttoken från Azure Stack-miljön
Starta PowerShell som administratör på värddatorn ASDK och navigera till den **registrering** mapp i den **AzureStack-Tools-master** katalog som skapades när du har laddat ned Azure Stack-verktyg. Använd följande PowerShell-kommandon för att importera den **RegisterWithAzure.psm1** modulen och använder sedan den **Get-AzsRegistrationToken** cmdlet för att hämta registreringstoken:  

   ```PowerShell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

Spara den här registreringstoken för användning på den Internetanslutna datorn. Du kan kopiera filen eller texten från den fil som skapats av parametern $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Ansluta till Azure och registrera
På internet ansluta datorn använder följande PowerShell-kommandon för att importera den **RegisterWithAzure.psm1** modulen och använder sedan den **registrera AzsEnvironment** cmdlet för att registrera med Azure med hjälp av registreringstoken som du just har skapat och ett unika registrerings-namn:  

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Du kan också använda den **Get-innehåll** cmdlet för att peka på en fil som innehåller din registreringstoken:

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

När registreringen är klar, bör du se ett meddelande som liknar **Your Azure Stack-miljön är nu registrerad med Azure.**

> [!IMPORTANT]
> Stäng inte PowerShell-fönstret. 

Spara fcm och resursnamnet för registrering för framtida bruk.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Hämta en aktiveringsnyckeln från Azure-registrering-resurs

Fortfarande använder datorns internet-anslutna **och samma PowerShell-konsolfönster**, hämta en aktiveringsnyckeln från registrering resursen skapades när du registrerade med Azure.

Kör följande PowerShell-kommandon för att hämta aktiveringsnyckeln, använder du samma unika registrerings-namnvärdet som du angav när du registrerar med Azure i föregående steg:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Skapa en resurs för aktivering i Azure Stack

Gå tillbaka till Azure Stack-miljön med filen eller text från aktiveringsnyckeln skapas från **Get-AzsActivationKey**. Kör följande PowerShell-kommandon för att skapa en resurs för aktivering i Azure Stack med hjälp av denna aktiveringsnyckel:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Du kan också använda den **Get-innehåll** cmdlet för att peka på en fil som innehåller din registreringstoken:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

När aktiveringen är klar bör du se ett meddelande som liknar **miljön har slutfört registreringen och aktiveringen processen.**

## <a name="verify-the-registration-was-successful"></a>Kontrollera registreringen har lyckats

Du kan använda den **regionshantering** panelen för att kontrollera att Azure Stack-registreringen har lyckats. Den här panelen är tillgänglig på standardinstrumentpanelen i administratörsportalen.

1. Logga in på den [Azure Stack-administrationsportalen](https://adminportal.local.azurestack.external).

2. Från instrumentpanelen väljer **regionshantering**.

    [![Panelen för hantering av region](media/asdk-register/admin1sm.png "panelen för hantering av Region")](media/asdk-register/admin1.png#lightbox)

3. Välj **egenskaper**. Det här bladet visar status och information om din miljö. Statusen kan vara **registrerad** eller **inte registrerad**. Om registrerat, visar den även Azure prenumerations-ID som användes för att registrera din Azure Stack, tillsammans med resursgruppen för registrering och namn.

## <a name="move-a-registration-resource"></a>Flytta en resurs för registrering
En registrering resurs flyttas mellan resursgrupper i samma prenumeration **är** stöds. Läs mer om hur du flyttar resurser till en ny resursgrupp, [flytta resurser till ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Nästa steg

- [Lägg till ett Azure Stack marketplace-objekt](../azure-stack-marketplace.md)
