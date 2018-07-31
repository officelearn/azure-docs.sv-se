---
title: Integrerade Azure-registrering för Azure Stack-system | Microsoft Docs
description: Beskriver hur Azure-registrering för Azure Stack Azure-ansluten distribution av flera noder.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: e73cd24064f2b0d6197a69251b55639d41e3212c
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39357756"
---
# <a name="register-azure-stack-with-azure"></a>Registrera Azure Stack med Azure

Registrera [Azure Stack](azure-stack-poc.md) med Azure kan du hämta marketplace-objekt från Azure och du ställer in handelsdata rapporterar tillbaka till Microsoft. När du har registrerat Azure Stack användning rapporteras till Azure commerce och du kan se dem under den prenumeration som användes för registrering.

> [!IMPORTANT]  
> Registrering krävs för att stödja fullständig Azure Stack-funktioner, inklusive marketplace syndikering. Du kan dessutom strider mot Azure Stack licensvillkoren om du inte registrerar när du använder faktureringsmodellen betalning som du-användning. Läs mer om Azure Stack licensiering modeller i den [köpa sidan](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Förutsättningar

Innan du registrerar Azure Stack med Azure, måste du ha:

- Prenumerations-ID för en Azure-prenumeration. Logga in på Azure för att hämta ID, klickar du på **fler tjänster** > **prenumerationer**, klicka på den prenumeration som du vill använda, och under **Essentials** du hittar den Prenumerations-ID.

  > [!NOTE]
  > Tyskland och US Government cloud-prenumerationer stöds inte för närvarande.

- Användarnamnet och lösenordet för ett konto som är ägare till prenumerationen (MSA/2FA-konton stöds).
- Registrerad resursprovider för Azure Stack (se registrera Resursprovidern för Azure Stack-avsnittet nedan för information).

Om du inte har en Azure-prenumeration som uppfyller dessa krav, kan du [skapa ett kostnadsfritt konto här](https://azure.microsoft.com/free/?b=17.06). Registrera Azure Stack medför utan kostnad på din Azure-prenumeration.

### <a name="powershell-language-mode"></a>PowerShell-läget för språk

För att kunna registrera Azure Stack, PowerShell-läget för språk måste anges till **FullLanguageMode**.  För att verifiera att det aktuella språkläget har angetts till full, öppna en upphöjd PowerShell-fönster och kör följande PowerShell-kommandon:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Kontrollera utdata returnerar **FullLanguageMode**. Om något annat språkläge returneras, registreringen måste köras på en annan dator eller språkläget måste anges till **FullLanguageMode** innan du fortsätter.

### <a name="bkmk_powershell"></a>Installera PowerShell för Azure Stack

Du måste använda den senaste versionen av PowerShell för Azure Stack för att registrera med Azure.

Om du inte redan är installerat [installera PowerShell för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Ladda ned Azure Stack-verktyg

Verktyg för GitHub-lagringsplatsen för Azure Stack innehåller PowerShell-moduler som har stöd för Azure Stack-funktionalitet. inklusive registrering-funktionerna. Under registreringen, måste du importera och använda RegisterWithAzure.psm1 PowerShell-modulen finns i lagringsplatsen Azure Stack-verktyg för att registrera din Azure Stack-instans med Azure.

För att säkerställa att du använder den senaste versionen, bör du ta bort eventuella befintliga versioner av Azure Stack-verktyg och [ladda ned den senaste versionen från GitHub](azure-stack-powershell-download.md) innan du registrerar med Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Registrera Azure Stack i anslutna miljöer

Anslutna miljöer kan komma åt internet och Azure. För dessa miljöer måste du registrera Azure Stack-resursprovidern med Azure och sedan konfigurera faktureringsmodellen.

> [!NOTE]
> De här stegen måste köras från en dator som har åtkomst till privilegierad slutpunkt.

### <a name="register-the-azure-stack-resource-provider"></a>Registrera resursprovidern Azure Stack

För att registrera resursprovidern Azure Stack med Azure, starta PowerShell ISE som administratör och använder följande PowerShell-kommandon med den **EnvironmentName** parameteruppsättning till typen lämplig Azure-prenumeration (se nedanstående parametrar).

1. Lägg till Azure-konto som används för att registrera Azure Stack. Om du vill lägga till kontot, kör den **Add-AzureRmAccount** cmdlet. Du uppmanas att ange dina autentiseringsuppgifter för global administratör för Azure-konto och du kan behöva använda 2-faktor autentisering baserat på konfigurationen för ditt konto.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parameter | Beskrivning |  
   |-----|-----|
   | EnvironmentName | Azure-molnet miljö prenumerationsnamnet. Miljö som stöds är **AzureCloud** eller, om du använder en Kina Azure-prenumeration **AzureChinaCloud**.  |
   |  |  |

2. Om du har flera prenumerationer kör du följande kommando för att välja den du vill använda:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Kör följande kommando för att registrera resursprovidern Azure Stack i Azure-prenumerationen:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrera Azure Stack med Azure med hjälp av faktureringsmodellen betalning som du-användning

Följ dessa steg för att registrera Azure Stack med Azure med hjälp av faktureringsmodellen betalning som du-användning.

1. Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack-Tools-master** directory skapade när du [ned Azure Stack-verktyg](#bkmk_tools). Importera den **RegisterWithAzure.psm1** modulen med hjälp av PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Kontrollera sedan du är inloggad rätt kontext i Azure PowerShell i samma PowerShell-session. Det här är den azure-konto som används för att registrera resursprovidern Azure Stack ovan. PowerShell för att köra:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. I samma PowerShell-session kör du den **Set-AzsRegistration** cmdlet. PowerShell för att köra:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```

  |Parameter|Beskrivning|
  |-----|-----|
  |PrivilegedEndpointCredential|Autentiseringsuppgifterna som används för [åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Användarnamnet är i formatet **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|En förkonfigurerad PowerShell fjärrkonsolen ger tillgång till funktioner som loggar in och andra post distributionsuppgifter. Mer information finns i [med hjälp av privilegierad slutpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artikeln.|
  |BillingModel|Faktureringsmodellen som använder din prenumeration. Tillåtna värden för den här parametern är: kapacitet, PayAsYouUse och utveckling.|
  | registrationName | Ange ett unikt namn för registrering om du kör skriptet registrering på fler än en instans av Azure Stack med hjälp av den samma Azure prenumerations-ID. Parametern har ett standardvärde på **AzureStackRegistration**. Om du använder samma namn på fler än en instans av Azure Stack kan misslyckas skriptet. |

  Processen tar mellan 10 och 15 minuter. När kommandot har slutförts visas meddelandet **”miljön är nu registrerad och aktiverad med hjälp av de angivna parametrarna”.**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registrera Azure Stack med Azure med hjälp av faktureringsmodellen kapacitet

Följ samma anvisningar som används för att registrera med faktureringsmodell betalning som du-användning, men Lägg till avtalsnummer som har köpts för kapacitet och ändra den **BillingModel** parameter **kapacitet**. Alla andra parametrar har inte ändrats.

PowerShell för att köra:

```PowerShell  
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
$RegistrationName = "<unique-registration-name>"
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity `
    -RegistrationName $RegistrationName
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrera Azure Stack i frånkopplade miljöer
Om du registrerar Azure Stack i en frånkopplad miljö (som saknar Internetanslutning), måste du skaffa en registrering token från Azure Stack-miljön och sedan använda denna token på en dator som kan ansluta till Azure och har [PowerShell för Azure Stack installerat](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Hämta en registrering åtkomsttoken från Azure Stack-miljön

1. Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack-Tools-master** directory skapade när du [ned Azure Stack-verktyg](#bkmk_tools). Importera den **RegisterWithAzure.psm1** modulen:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Kör följande PowerShell-kommandon för att hämta registreringstoken:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > Fcm sparas i filen som angetts för *$FilePathForRegistrationToken*. Du kan ändra filepath eller filnamn passar dig bäst.

3. Ansluten datorn om du vill spara den här registreringstoken för användning på Azure. Du kan kopiera filen eller texten från $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Ansluta till Azure och registrera

På den dator som är ansluten till Internet och utföra samma steg för att importera modulen RegisterWithAzure.psm1 och logga in på rätt Azure Powershell-kontexten. Anropa sedan registrera AzsEnvironment. Ange registreringstoken för att registrera med Azure. Om du registrerar fler än en instans av Azure Stack med samma Azure-prenumerationens ID, ange ett unika registrerings-namn. Kör följande cmdlet:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

Du kan även använda cmdleten Get-innehåll för att den pekar på en fil som innehåller din registreringstoken:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!NOTE]  
  > Spara resursnamnet registrering och registreringstoken för framtida bruk.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Hämta en aktiveringsnyckeln från Azure-registrering resurs

Därefter måste du hämta en aktiveringsnyckeln från registrering resursen skapades registrera AzsEnvironment i Azure.

Kör följande PowerShell-kommandon för att hämta aktiveringsnyckeln:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > Aktiveringsnyckeln sparas i filen som angetts för *$KeyOutputFilePath*. Du kan ändra filepath eller filnamn passar dig bäst.

### <a name="create-an-activation-resource-in-azure-stack"></a>Skapa en resurs för aktivering i Azure Stack

Gå tillbaka till Azure Stack-miljön med filen eller text från aktiveringsnyckeln som skapats från Get-AzsActivationKey. Därefter skapar du en resurs för aktivering i Azure Stack med hjälp av denna aktiveringsnyckel. Kör följande PowerShell-kommandon för att skapa en resurs för aktivering:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Du kan även använda cmdleten Get-innehåll för att den pekar på en fil som innehåller din registreringstoken:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Verifiera Azure Stack-registrering

Följ dessa steg för att verifiera att Azure Stack har registrerats med Azure.

1. Logga in på Azure Stack [administratörsportalen](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;region >. &lt;fqdn >*.
2. Välj **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**.

Om du ser en lista med objekt som är tillgängliga från Azure (till exempel WordPress) lyckades aktiveringen. Men i frånkopplade miljöer visas inte Azure marketplace-objekt i Azure Stack marketplace.

> [!NOTE]
> När registreringen är klar, visas inte längre den aktiva varningen för att inte registrera.

## <a name="renew-or-change-registration"></a>Förnya eller ändra registrering

### <a name="renew-or-change-registration-in-connected-environments"></a>Förnya eller ändra registrering i anslutna miljöer

Du behöver uppdatera eller förnya din registrering under följande omständigheter:

- När du förnyar prenumerationen kapacitetsbaserad årliga.
- När du har ändrat faktureringsmodellen.
- När du skalar ändringar (Lägg till/ta bort noder) för kapacitetsbaserad fakturering.

#### <a name="change-the-subscription-you-use"></a>Ändra den prenumeration som du använder

Om du vill ändra prenumerationen du använder, måste du först köra den **Remove-AzsRegistration** cmdlet, kontrollera att du är inloggad rätt sammanhang för Azure PowerShell och kör slutligen **Set-AzsRegistration**  med alla parametrar som ändrats:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Ändra faktureringsfunktionerna modell eller syndikering

Om du vill ändra faktureringsmodellen eller syndikering funktioner för din installation kan du anropa registreringsfunktionen för att ställa in de nya värdena. Du behöver inte först ta bort den aktuella registreringen:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Förnya eller ändra registrering i frånkopplade miljöer

Du behöver uppdatera eller förnya din registrering under följande omständigheter:

- När du förnyar prenumerationen kapacitetsbaserad årliga.
- När du har ändrat faktureringsmodellen.
- När du skalar ändringar (Lägg till/ta bort noder) för kapacitetsbaserad fakturering.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Ta bort resursen aktivering från Azure Stack

Du måste först ta bort resursen aktivering från Azure Stack och registrerings-resurs i Azure.  

Om du vill ta bort resursen aktivering i Azure Stack, kör du följande PowerShell-kommandon i Azure Stack-miljön:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Därefter för att ta bort resursen för registrering i Azure, kontrollera att du i en Azure anslutna datorn, logga in på rätt sammanhang för Azure PowerShell och kör lämpligt PowerShell-kommandon som beskrivs nedan.

Du kan använda registreringstoken som används för att skapa resursen:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Du kan också använda namnet på registrering:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Registrera med frånkopplade steg

Du har nu helt avregistreras i ett scenario med frånkopplade och upprepa stegen för att registrera en Azure Stack-miljö i ett scenario med frånkopplade.

## <a name="next-steps"></a>Nästa steg

[Hämta marketplace-objekt från Azure](azure-stack-download-azure-marketplace-item.md)
