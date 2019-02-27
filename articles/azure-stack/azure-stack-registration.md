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
ms.date: 02/14/2019
ms.author: jeffgilb
ms.reviewer: brbartle
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 1fb9917e30a88cf5a68c9e170d66eeaa1d477deb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877154"
---
# <a name="register-azure-stack-with-azure"></a>Registrera Azure Stack med Azure

Registrera Azure Stack med Azure kan du hämta marketplace-objekt från Azure och konfigurera handelsdata rapporterar tillbaka till Microsoft. När du har registrerat Azure Stack användning rapporteras till Azure commerce och du kan se dem under Azure fakturering prenumerations-ID används för registrering.

I den här artikeln beskrivs registrera integrerade Azure Stack-system med Azure. Information om hur du registrerar ASDK med Azure finns i [Azure Stack-registrering](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register) i ASDK-dokumentationen.

> [!IMPORTANT]  
> Registrering krävs för att stödja fullständig Azure Stack-funktioner, inklusive erbjuder objekt i marketplace. Du kan dessutom strider mot Azure Stack licensvillkoren om du inte registrerar när du använder faktureringsmodellen betalning som du-användning. Läs mer om Azure Stack licensiering modeller i den [köpa sidan](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för på plats innan du registrerar:

 - Ange autentiseringsuppgifterna
 - Ange språkläge PowerShell
 - Installera PowerShell för Azure Stack
 - Ladda ned Azure Stack-verktyg
 - Fastställa din registrering-scenario

### <a name="verify-your-credentials"></a>Ange autentiseringsuppgifterna

Innan du registrerar Azure Stack med Azure, måste du ha:

- Prenumerations-ID för en Azure-prenumeration. Endast EA, CSP eller CSP delade tjänster som prenumerationer stöds för registrering. CSP: er måste du bestämma om du vill [använder en CSP eller APSS prenumeration](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Logga in på Azure för att hämta ID, klickar du på **alla tjänster**. Sedan, under den **Allmänt** kategori, väljer **prenumerationer**, klicka på den prenumeration som du vill använda, och under **Essentials** du hittar prenumerations-ID.

  > [!Note]  
  > Tyskland cloud-prenumerationer stöds inte för närvarande.

- Användarnamn och lösenord för ett konto som är ägare till prenumerationen.

- Användarkontot måste ha åtkomst till Azure-prenumerationen och har behörighet att skapa program med identiteter och tjänstens huvudnamn i katalogen som är associerade med den aktuella prenumerationen. Vi rekommenderar att du registrerar Azure Stack med Azure lägsta behörighet administration. Läs mer om hur du skapar en anpassad rolldefinition som begränsar åtkomsten till din prenumeration för registrering, [skapa en roll för registrering för Azure Stack](azure-stack-registration-role.md).

- Registrerad resursprovider för Azure Stack (se avsnittet nedan registrera Azure Stack Resource Provider för mer information).

Efter registreringen krävs inte behörighet för Azure Active Directory global administratör. Vissa åtgärder kan dock kräva autentiseringsuppgifter för global administratör. Till exempel ett resource provider installer skript eller en ny funktion som kräver en behörighet som ska beviljas. Du kan tillfälligt återställa kontots behörigheter som global administratör eller använda ett separat globalt administratörskonto som äger den *standard providerprenumeration*.

Den användare som registrerar Azure Stack är ägare till tjänstens huvudnamn i Azure Active Directory. Endast den användare som registrerade Azure Stack kan ändra Azure Stack-registrering. Om en icke-administratörsanvändare som inte är ägare till registreringen tjänstens huvudnamn försöker registrera eller registrera Azure Stack, de kan stöta på ett 403-svar. Ett 403 svaret anger att användaren har inte behörighet att slutföra åtgärden.

Om du inte har en Azure-prenumeration som uppfyller dessa krav, kan du [skapa ett kostnadsfritt konto här](https://azure.microsoft.com/free/?b=17.06). Registrera Azure Stack medför utan kostnad på din Azure-prenumeration.

### <a name="powershell-language-mode"></a>PowerShell-läget för språk

För att kunna registrera Azure Stack, PowerShell-läget för språk måste anges till **FullLanguageMode**.  För att verifiera att det aktuella språkläget har angetts till full, öppna en upphöjd PowerShell-fönster och kör följande PowerShell-cmdletar:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Kontrollera utdata returnerar **FullLanguageMode**. Om något annat språkläge returneras, registreringen måste köras på en annan dator eller språkläget måste anges till **FullLanguageMode** innan du fortsätter.

### <a name="install-powershell-for-azure-stack"></a>Installera PowerShell för Azure Stack

Använd den senaste versionen av PowerShell för Azure Stack för att registrera med Azure.

Om den senaste versionen inte redan är installerat, se [installera PowerShell för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Ladda ned Azure Stack-verktyg

Verktyg för GitHub-lagringsplatsen för Azure Stack innehåller PowerShell-moduler som har stöd för Azure Stack-funktionalitet. inklusive registrering-funktionerna. Under registreringen, måste du importera och använda den **RegisterWithAzure.psm1** PowerShell-modulen, finns i lagringsplatsen Azure Stack-verktyg för att registrera din Azure Stack-instans med Azure.

För att säkerställa att du använder den senaste versionen, bör du ta bort eventuella befintliga versioner av Azure Stack-verktyg och [ladda ned den senaste versionen från GitHub](azure-stack-powershell-download.md) innan du registrerar med Azure.

### <a name="determine-your-registration-scenario"></a>Fastställa din registrering-scenario

Azure Stack-distributioner kanske *anslutna* eller *frånkopplad*.

 - **Ansluten**  
 Ansluten innebär att du har distribuerat Azure Stack så att den kan ansluta till Internet och till Azure. Du har Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) för din identitet butik. Med en ansluten distribution som du kan välja mellan två faktureringsmodeller: betalning som du-användning eller kapacitetsbaserad.
    - [Registrera en ansluten Azure Stack med Azure med hjälp av den **betalning som du-användning** faktureringsmodell](#register-connected-with-pay-as-you-go-billing)
    - [Registrera en ansluten Azure Stack med Azure med hjälp av den **kapacitet** faktureringsmodell](#register-connected-with-capacity-billing)

 - **Frånkopplad**  
 Med den frånkopplade från Azure distributionsalternativ, du kan distribuera och använda Azure Stack utan en anslutning till Internet. Men med en frånkopplad distribution är du begränsad till en AD FS-Identitetslagret och kapacitetsbaserad faktureringsmodell.
    - [Registrera en frånkopplad Azure Stack med den **kapacitet** faktureringsmodell ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Fastställa ett unika registrerings-namn 
När du registrerar Azure Stack med Azure måste du ange ett unika registrerings-namn. Ett enkelt sätt att associera Azure Stack-prenumeration med en Azure-registrering är att använda Azure Stack **moln-ID**. 

> [!NOTE]
> Azure Stack-registreringar med kapacitetsbaserad faktureringsmodellen måste du ändra det unika namnet när omregistrering när dessa årliga prenumerationer upphör att gälla, såvida inte du [ta bort utgångna registreringen](azure-stack-registration.md#change-the-subscription-you-use) och registrera med Azure.

Öppna PowerShell som administratör på en dator som kan komma åt den privilegierade slutpunkten, kör du följande kommandon för att fastställa moln-ID för Azure Stack-distribution och registrera den **CloudID** värde: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registrera kontakten med användningsbaserad fakturering

Följ dessa steg för att registrera Azure Stack med Azure med hjälp av faktureringsmodellen betalning som du-användning.

> [!Note]  
> De här stegen måste köras från en dator som har åtkomst till privilegierad slutpunkt (program). Mer information om detta program finns i [med hjälp av privilegierad slutpunkt i Azure Stack](azure-stack-privileged-endpoint.md).

Anslutna miljöer kan komma åt internet och Azure. För dessa miljöer måste du registrera Azure Stack-resursprovidern med Azure och sedan konfigurera faktureringsmodellen.

1. För att registrera resursprovidern Azure Stack med Azure, starta PowerShell ISE som administratör och använder följande PowerShell-cmdlets med den **EnvironmentName** parameteruppsättning till typen lämplig Azure-prenumeration (se nedanstående parametrar).

2. Lägg till Azure-konto som används för att registrera Azure Stack. Om du vill lägga till kontot, kör den **Add-AzureRmAccount** cmdlet. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto och du kan behöva använda 2-faktor autentisering baserat på konfigurationen för ditt konto.

   ```PowerShell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parameter | Beskrivning |  
   |-----|-----|
   | EnvironmentName | Azure-molnet miljö prenumerationsnamnet. Miljö som stöds är **AzureCloud**, **azureusgovernment eller**, eller om du använder en Kina Azure-prenumeration **AzureChinaCloud**.  |

3. Om du har flera prenumerationer kör du följande kommando för att välja den du vill använda:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Kör följande kommando för att registrera resursprovidern Azure Stack i Azure-prenumerationen:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack-Tools-master** katalog som skapades när du har laddat ned Azure Stack-verktyg. Importera den **RegisterWithAzure.psm1** modulen med hjälp av PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Kontrollera sedan du är inloggad rätt kontext i Azure PowerShell i samma PowerShell-session. Det här är den Azure-konto som används för att registrera resursprovidern Azure Stack tidigare. PowerShell för att köra:

   ```PowerShell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parameter | Beskrivning |  
   |-----|-----|
   | EnvironmentName | Azure-molnet miljö prenumerationsnamnet. Miljö som stöds är **AzureCloud**, **azureusgovernment eller**, eller om du använder en Kina Azure-prenumeration **AzureChinaCloud**.  |

7. I samma PowerShell-session kör du den **Set-AzsRegistration** cmdlet. PowerShell för att köra:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Mer information om cmdlet Set-AzsRegistration finns [referens för registrering av](#registration-reference).

  Processen tar mellan 10 och 15 minuter. När kommandot har slutförts visas meddelandet **”miljön är nu registrerad och aktiverad med hjälp av de angivna parametrarna”.**

## <a name="register-connected-with-capacity-billing"></a>Registrera kontakten med kapacitet fakturering

Följ dessa steg för att registrera Azure Stack med Azure med hjälp av faktureringsmodellen betalning som du-användning.

> [!Note]  
> De här stegen måste köras från en dator som har åtkomst till privilegierad slutpunkt (program). Mer information om detta program finns i [med hjälp av privilegierad slutpunkt i Azure Stack](azure-stack-privileged-endpoint.md).

Anslutna miljöer kan komma åt internet och Azure. För dessa miljöer måste du registrera Azure Stack-resursprovidern med Azure och sedan konfigurera faktureringsmodellen.

1. För att registrera resursprovidern Azure Stack med Azure, starta PowerShell ISE som administratör och använder följande PowerShell-cmdlets med den **EnvironmentName** parameteruppsättning till typen lämplig Azure-prenumeration (se nedanstående parametrar).

2. Lägg till Azure-konto som används för att registrera Azure Stack. Om du vill lägga till kontot, kör den **Add-AzureRmAccount** cmdlet. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto och du kan behöva använda 2-faktor autentisering baserat på konfigurationen för ditt konto.

   ```PowerShell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parameter | Beskrivning |  
   |-----|-----|
   | EnvironmentName | Azure-molnet miljö prenumerationsnamnet. Miljö som stöds är **AzureCloud**, **azureusgovernment eller**, eller om du använder en Kina Azure-prenumeration **AzureChinaCloud**.  |

3. Om du har flera prenumerationer kör du följande kommando för att välja den du vill använda:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Kör följande kommando för att registrera resursprovidern Azure Stack i Azure-prenumerationen:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack-Tools-master** katalog som skapades när du har laddat ned Azure Stack-verktyg. Importera den **RegisterWithAzure.psm1** modulen med hjälp av PowerShell:

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
   > [!Note]  
   > Du kan inaktivera rapportering med parametern UsageReportingEnabled för användning i **Set-AzsRegistration** cmdlet genom att ange parametern till false. 
   
  Mer information om cmdlet Set-AzsRegistration finns [referens för registrering av](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrera frånkopplade med kapacitet fakturering

Om du registrerar Azure Stack i en frånkopplad miljö (som saknar Internetanslutning), måste du skaffa en registrering token från Azure Stack-miljön och sedan använda denna token på en dator som kan ansluta till Azure med PowerShell för Azure Stack installerad.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Hämta en registrering åtkomsttoken från Azure Stack-miljön

1. Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack-Tools-master** katalog som skapades när du har laddat ned Azure Stack-verktyg. Importera den **RegisterWithAzure.psm1** modulen:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Kör följande PowerShell-cmdletar för att hämta registreringstoken:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Mer information om cmdleten Get-AzsRegistrationToken finns [referens för registrering av](#registration-reference).

   > [!Tip]  
   > Fcm sparas i filen som angetts för *$FilePathForRegistrationToken*. Du kan ändra filepath eller filnamn passar dig bäst.

3. Ansluten datorn om du vill spara den här registreringstoken för användning på Azure. Du kan kopiera filen eller texten från $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Ansluta till Azure och registrera

På den dator som är ansluten till Internet och utföra samma steg för att importera modulen RegisterWithAzure.psm1 och logga in på rätt Azure Powershell-kontexten. Anropa sedan registrera AzsEnvironment. Ange registreringstoken för att registrera med Azure. Om du registrerar fler än en instans av Azure Stack med samma Azure-prenumerationens ID, ange ett unika registrerings-namn. Kör följande cmdlet:

  ```PowerShell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

Du kan även använda cmdleten Get-innehåll för att den pekar på en fil som innehåller din registreringstoken:

  ```PowerShell  
  $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Spara resursnamnet registrering och registreringstoken för framtida bruk.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Hämta en aktiveringsnyckeln från Azure-registrering resurs

Därefter måste du hämta en aktiveringsnyckeln från registrering resursen skapades registrera AzsEnvironment i Azure.

Kör följande PowerShell-cmdletar för att hämta aktiveringsnyckeln:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Aktiveringsnyckeln sparas i filen som angetts för *$KeyOutputFilePath*. Du kan ändra filepath eller filnamn passar dig bäst.

### <a name="create-an-activation-resource-in-azure-stack"></a>Skapa en resurs för aktivering i Azure Stack

Gå tillbaka till Azure Stack-miljön med filen eller text från aktiveringsnyckeln som skapats från Get-AzsActivationKey. Därefter skapar du en resurs för aktivering i Azure Stack med hjälp av denna aktiveringsnyckel. Kör följande PowerShell-cmdletar för att skapa en resurs för aktivering: 

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

Du kan använda den **regionshantering** panelen för att kontrollera att Azure Stack-registreringen har lyckats. Den här panelen är tillgänglig på standardinstrumentpanelen i administratörsportalen. Status kan vara registrerad eller inte registrerad. Om registrerat, visar den även Azure prenumerations-ID som användes för att registrera din Azure-Stack tillsammans med resursgruppen för registrering och namn.

1. Logga in på den [Azure Stack-administrationsportalen](https://adminportal.local.azurestack.external).

2. Från instrumentpanelen väljer **regionshantering**.

3. Välj **egenskaper**. Det här bladet visar status och information om din miljö. Statusen kan vara **registrerad** eller **inte registrerad**.

    [![Panelen för hantering av region](media/azure-stack-registration/admin1sm.png "panelen för hantering av Region")](media/azure-stack-registration/admin1.png#lightbox)

    Om är registrerad ingår i egenskaperna:
    
    - **Prenumerations-ID för registrering**: Azure-prenumerations-ID som har registrerats och som är kopplad till Azure Stack
    - **Resursgrupp för registrering**: Den Azure-resursgruppen i samma prenumeration som innehåller Azure Stack-resurser.

4. Använda Azure-portalen för att visa Azure Stack-app-registreringar. Logga in på Azure-portalen med ett konto som är kopplad till prenumerationen du använde för att registrera Azure Stack. Växla till innehavaren som associeras med Azure Stack.
5. Gå till **Azure Active Directory > appregistreringar > Visa alla program**.

    ![Appregistreringar](media/azure-stack-registration/app-registrations.png)

    Azure Stack-app-registreringar har prefixet **Azure Stack**.

Du kan också kontrollera om din registreringen har lyckats genom att använda funktionen för hantering av Marketplace. Om du ser en lista med marketplace-objekt i bladet Marketplace hantering har registreringen slutförts. Men i frånkopplade miljöer kommer du inte att kunna se marketplace-objekt i Marketplace-hantering. Du kan dock använda verktyget offline för att verifiera registrering.

> [!NOTE]
> När registreringen är klar, visas inte längre den aktiva varningen för att inte registrera. I scenarier med frånkopplade visas ett meddelande i Marketplace management där du uppmanas att registrera och aktivera din Azure Stack, även om du har registrerats.

## <a name="renew-or-change-registration"></a>Förnya eller ändra registrering

### <a name="renew-or-change-registration-in-connected-environments"></a>Förnya eller ändra registrering i anslutna miljöer

Du behöver uppdatera eller förnya din registrering under följande omständigheter:

- När du förnyar prenumerationen kapacitetsbaserad årliga.
- När du har ändrat faktureringsmodellen.
- När du skalar ändringar (Lägg till/ta bort noder) för kapacitetsbaserad fakturering.

#### <a name="change-the-subscription-you-use"></a>Ändra den prenumeration som du använder

Om du vill ändra prenumerationen du använder, måste du först köra den **Remove-AzsRegistration** cmdlet, kontrollera att du är inloggad rätt sammanhang för Azure PowerShell och kör slutligen **Set-AzsRegistration**  med ändrade parametrar, inklusive \<faktureringsmodellen\>:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Ändra faktureringsmodellen eller hur du erbjuder funktioner

Om du vill ändra faktureringsmodellen eller hur du erbjuder funktioner för din installation kan du anropa registreringsfunktionen för att ställa in de nya värdena. Du behöver inte först ta bort den aktuella registreringen:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Förnya eller ändra registrering i frånkopplade miljöer

Du behöver uppdatera eller förnya din registrering under följande omständigheter:

- När du förnyar prenumerationen kapacitetsbaserad årliga.
- När du har ändrat faktureringsmodellen.
- När du skalar ändringar (Lägg till/ta bort noder) för kapacitetsbaserad fakturering.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Ta bort resursen aktivering från Azure Stack

Du måste först ta bort resursen aktivering från Azure Stack och registrerings-resurs i Azure.  

Om du vill ta bort resursen aktivering i Azure Stack, kör du följande PowerShell-cmdletar i Azure Stack-miljön:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Därefter för att ta bort resursen för registrering i Azure, kontrollera att du i en Azure anslutna datorn, logga in på rätt sammanhang för Azure PowerShell och kör lämpliga PowerShell-cmdlets som beskrivs nedan.

Du kan använda registreringstoken som används för att skapa resursen:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Du kan också använda namnet på registrering:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Registrera med frånkopplade steg

Du har nu helt avregistreras i ett scenario med frånkopplade och upprepa stegen för att registrera en Azure Stack-miljö i ett scenario med frånkopplade.

### <a name="disable-or-enable-usage-reporting"></a>Inaktivera eller aktivera användningsrapportering

Inaktivera för Azure Stack-miljöer som använder ett prispaket av fakturering, användning rapportering med den **UsageReportingEnabled** parametern med antingen den **Set-AzsRegistration** eller  **Get-AzsRegistrationToken** cmdletar. Azure Stack rapporterar användningsstatistik som standard. Operatörer med kapacitet använder eller stöd för en frånkopplad miljö måste du inaktivera användningsrapportering.

#### <a name="with-a-connected-azure-stack"></a>Med en ansluten Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Med en frånkopplad Azure Stack

1. Om du vill ändra fcm, kör du följande PowerShell-cmdletar:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Fcm sparas i filen som angetts för *$FilePathForRegistrationToken*. Du kan ändra filepath eller filnamn passar dig bäst.

2. Ansluten datorn om du vill spara den här registreringstoken för användning på Azure. Du kan kopiera filen eller texten från $FilePathForRegistrationToken.

## <a name="move-a-registration-resource"></a>Flytta en resurs för registrering
En registrering resurs flyttas mellan resursgrupper i samma prenumeration **är** stöds för alla miljöer. Men stöds en registrering resurs flyttas mellan prenumerationer endast för CSP: er när båda prenumerationerna matcha till samma Partner-ID. Läs mer om hur du flyttar resurser till en ny resursgrupp, [flytta resurser till ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Referens för registrering

### <a name="set-azsregistration"></a>Set-AzsRegistration

Du kan använda Set-AzsRegistration för att registrera Azure Stack med Azure och aktivera eller inaktivera erbjudandet om objekt i marketplace och användningsrapportering.

Om du vill köra cmdleten, behöver du:
- En global Azure-prenumeration av valfri typ.
- Du måste också vara inloggad på Azure PowerShell med ett konto som är ägare eller deltagare till den prenumerationen.

```PowerShell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Parameter | Type | Beskrivning |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Autentiseringsuppgifterna som används för [åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Användarnamnet är i formatet **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String | En förkonfigurerad PowerShell fjärrkonsolen ger tillgång till funktioner som loggar in och andra post distributionsuppgifter. Mer information finns i [med hjälp av privilegierad slutpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artikeln. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Faktureringsmodellen som använder din prenumeration. Tillåtna värden för den här parametern är: Kapacitet, PayAsYouUse och utveckling. |
| MarketplaceSyndicationEnabled | SANT/FALSKT | Anger huruvida hanteringsfunktionen marketplace är tillgängligt i portalen. Ange som SANT om registrering med Internetanslutning. Inställt på falskt om registrering i frånkopplade miljöer. För frånkopplade registreringar den [offline syndikering verktyget](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) kan användas för att ladda ned marketplace-objekt. |
| UsageReportingEnabled | SANT/FALSKT | Azure Stack rapporterar användningsstatistik som standard. Operatörer med kapacitet använder eller stöd för en frånkopplad miljö måste du inaktivera användningsrapportering. Tillåtna värden för den här parametern är: True, False. |
| AgreementNumber | String |  |
| RegistrationName | String | Ange ett unikt namn för registrering om du kör skriptet registrering på fler än en instans av Azure Stack med hjälp av den samma Azure prenumerations-ID. Parametern har ett standardvärde på **AzureStackRegistration**. Men om du använder samma namn på fler än en instans av Azure Stack kan misslyckas skriptet. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken genererar en registreringstoken från indataparametrarna.

```PowerShell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parameter | Type | Beskrivning |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Autentiseringsuppgifterna som används för [åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Användarnamnet är i formatet **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String |  En förkonfigurerad PowerShell fjärrkonsolen ger tillgång till funktioner som loggar in och andra post distributionsuppgifter. Mer information finns i [med hjälp av privilegierad slutpunkt](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artikeln. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Faktureringsmodellen som använder din prenumeration. Tillåtna värden för den här parametern är: Kapacitet, PayAsYouUse och utveckling. |
| MarketplaceSyndicationEnabled | SANT/FALSKT |  |
| UsageReportingEnabled | SANT/FALSKT | Azure Stack rapporterar användningsstatistik som standard. Operatörer med kapacitet använder eller stöd för en frånkopplad miljö måste du inaktivera användningsrapportering. Tillåtna värden för den här parametern är: True, False. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Registreringsfel

En av nedanstående felmeddelanden kan visas vid försök registrering av Azure Stack:
1. Kunde inte hämta obligatorisk maskinvara info för $hostName. Kontrollera fysisk värd och anslutning och försök köra registreringen.
2. Det går inte att ansluta till $hostName att få information om maskinvara – Kontrollera fysisk värd och anslutning och sedan försöka köra registreringen.

Orsak: Detta är vanligtvis eftersom vi försöker hämta information om maskinvara, till exempel UUID, Bios och processor från värdarna att försöka aktivera och kunde inte på grund av att det inte går att ansluta till den fysiska värden.

## <a name="next-steps"></a>Nästa steg

[Hämta marketplace-objekt från Azure](azure-stack-download-azure-marketplace-item.md)
