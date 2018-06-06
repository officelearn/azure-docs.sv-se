---
title: Azure-registrering för Azure-stacken integrerat system | Microsoft Docs
description: Beskriver Azure registreringsprocessen för flera noder Azure Stack Azure-anslutna distributioner.
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
ms.date: 06/01/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 4f1492180c31f69eb438b012cf489a5851189136
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714858"
---
# <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure
Registrera [Azure Stack](azure-stack-poc.md) med Azure kan du hämta marketplace-objekt från Azure och Ställ in commerce rapporterar tillbaka till Microsoft. När du har registrerat Azure Stack användning rapporteras till Azure handel och du kan se den under den prenumeration som används för registrering.

> [!IMPORTANT]
> Registrering krävs för att stödja fullständig Azure Stack-funktioner, inklusive marketplace-syndikeringsfeed. Du kan dessutom i överträdelse av Azure-stacken licensvillkoren om du inte registrerar när du använder lön-som-du-Använd fakturering modellen. Läs mer om Azure-stacken licensiering modeller i den [köpa sidan](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Förutsättningar
Innan du registrerar Azure stacken med Azure måste du ha:

- Prenumerations-ID för en Azure-prenumeration. Om du vill hämta ID, logga in på Azure, klickar du på **fler tjänster** > **prenumerationer**, klickar du på den prenumeration som du vill använda, och under **Essentials** hittar du i Prenumerations-ID.

  > [!NOTE]
  > Tyskland och som tillhör amerikanska myndigheter molnprenumerationer stöds inte för närvarande.

- Användarnamn och lösenord för ett konto som är en ägare till prenumerationen (MSA/2FA konton stöds).
- Registrerad resursprovider Azure Stack (se registrera Azure Stack-Resursprovidern nedan för information).

Om du inte har en Azure-prenumeration som uppfyller dessa krav, kan du [skapa ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/free/?b=17.06). Registrera Azure Stack ådrar sig utan kostnad på din Azure-prenumeration.

### <a name="powershell-language-mode"></a>PowerShell språk läge
Om du vill registrera har Azure-stacken, PowerShell språk läge måste anges till **FullLanguageMode**.  Kontrollera att det aktuella läget för språket är inställd på fullständig, öppna ett upphöjt PowerShell-fönster och kör följande PowerShell-kommandon:

```powershell
$ExecutionContext.SessionState.LanguageMode
```
Kontrollera utdata returnerar **FullLanguageMode**. Om något annat språk läge returneras, registreringen måste köras på en annan dator eller språk-läge måste anges till **FullLanguageMode** innan du fortsätter.


### <a name="bkmk_powershell"></a>Installera PowerShell för Azure-stacken
Du måste använda den senaste PowerShell för Azure-stacken registreras på Azure.

Om du inte redan är installerat, [installera PowerShell för Azure-stacken](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Hämta Azure Stack-verktyg
Azure-stacken verktyg GitHub-lagringsplatsen innehåller PowerShell-moduler som stöder Azure Stack-funktionalitet. inklusive funktioner för registrering. Under registreringen, måste du importera och använda RegisterWithAzure.psm1 PowerShell-modulen hittades i Azure-stacken verktyg databasen att registrera din Azure Stack-instans med Azure.

För att säkerställa att du använder den senaste versionen, bör du ta bort alla befintliga versioner av Azure Stack-verktyg och [hämta den senaste versionen från GitHub](azure-stack-powershell-download.md) innan du registrerar med Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Registrera Azure Stack i anslutna miljöer
Anslutna miljöer kan komma åt internet och Azure. För dessa miljöer måste du registrera Azure Stack-resursprovidern med Azure och konfigurera faktureringsmodellen.

> [!NOTE]
> De här stegen måste köras från en dator som har åtkomst till Privilegierade slutpunkten.

### <a name="register-the-azure-stack-resource-provider"></a>Registerresursleverantören Azure Stack
Registrera Azure Stack-resursprovidern med Azure, starta PowerShell ISE som administratör och kör följande PowerShell-kommandon med den **EnvironmentName** parameterinställning till lämplig Azure-prenumerationstyp (se parametrarna nedan).

1. Lägg till Azure-konto som används för att registrera Azure stacken. Lägg till kontot genom att köra den **Add-AzureRmAccount** cmdlet. Du uppmanas att ange dina autentiseringsuppgifter för global administratör för Azure-konto och du kan behöva använda 2-faktor-autentisering baserat på konfigurationen för ditt konto.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parameter | Beskrivning |  
   |-----|-----|
   | EnvironmentName | Azure-molnet miljö prenumerationsnamn. Miljö som stöds är **AzureCloud** eller, om du använder en Kina Azure-prenumeration **AzureChinaCloud**.  |
   |  |  |

2. Om du har flera prenumerationer kör du följande kommando för att välja den du vill använda:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Kör följande kommando för att registrera Azure Stack-resursprovidern i din Azure-prenumeration:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrera Azure stacken med Azure med hjälp av lön-som-du-Använd fakturering modellen
Följ dessa steg för att registrera Azure stacken med Azure med hjälp av lön-som-du-Använd fakturering modellen.

1. Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack verktyg master** directory skapas när du [ned Azure Stack verktyg](#bkmk_tools). Importera den **RegisterWithAzure.psm1** modul med PowerShell:

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Därefter samma PowerShell-session, se till att du har loggat in till rätt Azure PowerShell-kontexten. Detta är det azure-konto som användes för att registrera Azure Stack resursprovidern ovan. PowerShell för att köra:

  ```powershell
  Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
  ```

3. I samma PowerShell-session kör den **Set AzsRegistration** cmdlet. PowerShell för att köra:  

  ```powershell
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
  ```

  |Parameter|Beskrivning|
  |-----|-----|
  |PrivilegedEndpointCredential|Autentiseringsuppgifterna som används för [komma åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Användarnamnet är i formatet **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|En förkonfigurerad PowerShell fjärrkonsolen ger tillgång till funktioner som Logginsamling och andra post distributionsåtgärder. Mer information finns i den [med Privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artikel.|
  |BillingModel|Vilken faktureringsmodell som tillämpas med din prenumeration. Tillåtna värden för den här parametern är: kapacitet, PayAsYouUse och utveckling.|
  |  |  |

  Processen tar mellan 10 och 15 minuter. När kommandot har slutförts visas meddelandet **”din miljö är nu registrerad och aktiverad med de angivna parametrarna”.**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registrera Azure stacken med Azure med hjälp av kapacitet fakturering modellen
Följ samma anvisningar som användes för registrering med hjälp av fakturering modellen lön-som-du-Använd men lägga till avtalsnummer som har köpts för kapacitet och ändra den **BillingModel** parameter till **kapacitet**. Alla andra parametrar har inte ändrats.

PowerShell för att köra:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrera Azure Stack i frånkopplade miljöer
*Informationen i det här avsnittet gäller som börjar med den uppdaterade versionen som Azure Stack 1712 (180106.1) och stöds inte med tidigare versioner.*

Om du registrerar Azure Stack i en frånkopplad miljö (utan internet-anslutning), måste du skaffa en registrering token från Azure Stack-miljö och sedan använda denna token på en dator som kan ansluta till Azure och har [PowerShell för Azure-Stack installerat](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Hämta en registrering token från Azure Stack-miljö

1. Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack verktyg master** directory skapas när du [ned Azure Stack verktyg](#bkmk_tools). Importera den **RegisterWithAzure.psm1** modulen:  

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Kör följande PowerShell-kommandon för att hämta token för registrering:  

  ```Powershell
  $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
  $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
  ```

  > [!TIP]  
  > Registrering-token har sparats i den aktuella filen för *$FilePathForRegistrationToken*. Du kan ändra sökvägen eller filnamnet önskar.

3. Ansluten dator spara denna registrering token för användning på Azure. Du kan kopiera filen eller texten från $FilePathForRegistrationToken.


### <a name="connect-to-azure-and-register"></a>Ansluta till Azure och registrera
På den dator som är internet-ansluten utför du samma steg för att importera modulen RegisterWithAzure.psm1 och logga in till rätt Azure Powershell-kontexten. Sedan anropa registrera AzsEnvironment och ange registrering token registreras med Azure:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
Du kan även använda cmdlet Get-innehåll för att peka till en fil som innehåller din token för registrering:

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
  > [!NOTE]  
  > Spara registreringen-resursnamnet och registrering token för framtida bruk.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Hämta en aktiveringsnyckel från Azure registrering resurs
Därefter måste du hämta ett aktiveringsnyckeln från resursen registreringen skapas i Azure under registrera AzsEnvironment.

Kör följande PowerShell-kommandon för att hämta aktiveringsnyckeln:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```
  > [!TIP]   
  > Aktiveringsnyckeln sparas i filen som angetts för *$KeyOutputFilePath*. Du kan ändra sökvägen eller filnamnet önskar.

### <a name="create-an-activation-resource-in-azure-stack"></a>Skapa en resurs för aktivering i Azure-stacken
Gå tillbaka till Azure Stack-miljö med filen eller text från aktiveringsnyckeln skapas från Get-AzsActivationKey. Därefter skapar du en resurs för aktivering i Azure-stacken använder den aktiveringsnyckeln. Om du vill skapa en aktivering resurs för att köra följande PowerShell-kommandon:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Du kan även använda cmdlet Get-innehåll för att peka till en fil som innehåller din token för registrering:

  ```Powershell   
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Verifiera Azure Stack-registrering
Följ dessa steg för att verifiera att Azure-stacken har registrerats med Azure.
1. Logga in på Azure-stacken [administratörsportal](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;region >. &lt;fqdn >*.
2. Klicka på **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**.

Om du ser en lista med objekt som är tillgängliga från Azure (till exempel WordPress) lyckades aktiveringen. Men i frånkopplade miljöer visas inte Azure marketplace-objekt i stacken för Azure marketplace.

> [!NOTE]
> När registreringen är klar, visas inte längre den aktiva varningen för att registrera inte.

## <a name="renew-or-change-registration"></a>Förnya eller ändra registrering
### <a name="renew-or-change-registration-in-connected-environments"></a>Förnya eller ändra registrering i anslutna miljöer
Du behöver uppdatera eller förnya registreringen under följande omständigheter:
- När du förnyar prenumerationen kapacitet-baserade år.
- När du ändrar faktureringsmodellen.
- När du skalar ändringar (Lägg till/ta bort noder) för kapacitet-baserade fakturering.

#### <a name="change-the-subscription-you-use"></a>Ändra den prenumeration som du använder
Om du vill ändra prenumerationen du använder, måste du först köra den **ta bort AzsRegistration** cmdlet, kontrollera att du är inloggad kontexten rätt Azure PowerShell och kör slutligen **Set AzsRegistration**  med alla parametrar har ändrats:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Ändra fakturering modell eller syndikering funktioner
Om du vill ändra faktureringsmodell som tillämpas eller syndikering funktioner för din installation kan du anropa registreringsfunktionen för att ange de nya värdena. Du behöver inte först ta bort den aktuella registreringen:

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Förnya eller ändra registrering i frånkopplade miljöer
Du behöver uppdatera eller förnya registreringen under följande omständigheter:
- När du förnyar prenumerationen kapacitet-baserade år.
- När du ändrar faktureringsmodellen.
- När du skalar ändringar (Lägg till/ta bort noder) för kapacitet-baserade fakturering.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Ta bort resursen aktivering från Azure-grupp
Du måste först ta bort resursen aktivering från Azure-stacken och resursen registrering i Azure.  

Kör följande PowerShell-kommandon för att ta bort resursen aktivering i Azure-stacken i Azure Stack-miljö:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Sedan för att ta bort resursen registrering i Azure se till att du är på en Azure ansluten dator, logga in på rätt Azure PowerShell-kontexten och kör lämplig PowerShell-kommandon som beskrivs nedan.

Du kan använda registrering token som används för att skapa resursen:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Eller så kan du använda namnet för registrering:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Registrera med frånkopplade steg
Du har nu helt avregistreras i ett scenario med frånkopplade och måste upprepa stegen för att registrera en Azure-Stack-miljö i ett scenario med frånkopplade.

## <a name="next-steps"></a>Nästa steg

[Hämta marketplace-objekt från Azure](azure-stack-download-azure-marketplace-item.md)
