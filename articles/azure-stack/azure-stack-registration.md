---
title: "Azure-registrering för Azure-stacken integrerat system | Microsoft Docs"
description: "Beskriver Azure registreringsprocessen för flera noder Azure Stack Azure-anslutna distributioner."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 03a6ce77eed16cbc5d2fe46094b0b6ac7fbc022e
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure
Du kan registrera Azure stacken med Azure att hämta marketplace-objekt från Azure och Ställ in commerce rapporterar tillbaka till Microsoft. När du har registrerat Azure Stack rapporterat användning till Azure handel. Du kan se den under den prenumeration som du använde för registrering.

> [!IMPORTANT]
> Registrering är obligatoriskt om du väljer lön-som-du-Använd fakturering modellen. Annars kommer du att i överträdelse av licensvillkoren för Azure Stack-distribution som användning annars inte rapporteras.

## <a name="before-you-register-azure-stack-with-azure"></a>Innan du registrerar Azure stacken med Azure
Innan du registrerar Azure stacken med Azure måste du ha:

- Prenumerations-ID för en Azure-prenumeration. Om du vill hämta ID, logga in på Azure, klickar du på **fler tjänster** > **prenumerationer**, klickar du på den prenumeration som du vill använda, och under **Essentials** hittar du i Prenumerations-ID. 

  > [!NOTE]
  > Kina, Tyskland och US government molnprenumerationer stöds inte för närvarande. 

- Användarnamn och lösenord för ett konto som är en ägare till prenumerationen (MSA/2FA konton stöds)
- *Inte obligatoriskt från och med Azure-stacken 1712 Uppdateringsversion (180106.1)*: Azure AD för Azure-prenumerationen. Du hittar den här katalogen i Azure genom att hovra över din avatar i det övre högra hörnet i Azure-portalen. 
- Registrerad resursprovider Azure Stack (se registrera Azure Stack-Resursprovidern nedan för information)

Om du inte har en Azure-prenumeration som uppfyller dessa krav, kan du [skapa ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/free/?b=17.06). Registrera Azure Stack ådrar sig utan kostnad på din Azure-prenumeration.

### <a name="bkmk_powershell"></a>Installera PowerShell för Azure-stacken
Du måste använda den senaste PowerShell för Azure-stacken registreras i systemet med Azure.

Om du inte redan är installerat, [installera PowerShell för Azure-stacken](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Hämta Azure Stack-verktyg
Azure-stacken verktyg GitHub-lagringsplatsen innehåller PowerShell-moduler som stöder Azure Stack-funktionalitet. inklusive funktioner för registrering. Under registreringen hittades process som du behöver importera och använda RegisterWithAzure.psm1 PowerShell-modulen i Azure-stacken verktyg databasen, att registrera din Azure Stack-instans med Azure. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Registrera Azure Stack i anslutna miljöer
Anslutna miljöer kan komma åt internet och Azure. För dessa miljöer måste du registrera Azure Stack-resursprovidern med Azure och konfigurera faktureringsmodellen.

### <a name="register-the-azure-stack-resource-provider"></a>Registerresursleverantören Azure Stack
Starta Powershell ISE som administratör och kör följande PowerShell-kommandon för att registrera Azure Stack-resursprovidern med Azure. Dessa kommandon kommer att:
- Uppmanar dig att logga in som en ägare av Azure-prenumerationen ska användas och ange den `EnvironmentName` parameter till **AzureCloud**.
- Registrera Azure-resursprovider **Microsoft.AzureStack**.

PowerShell för att köra:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrera Azure stacken med Azure med hjälp av lön-som-du-Använd fakturering modellen
Använd dessa steg för att registrera Azure stacken med Azure med hjälp av lön-som-du-Använd fakturering modellen.

Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack verktyg master** directory skapas när du [ned Azure Stack verktyg](#bkmk_tools). Importera den **RegisterWithAzure.psm1** modul med PowerShell: 

PowerShell för att köra:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
I samma PowerShell-session kör den **Set AzsRegistration** cmdlet. När du tillfrågas om autentiseringsuppgifter, ange ägare av Azure-prenumerationen.  

PowerShell för att köra:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Parameter|Beskrivning|
|-----|-----|
|CloudAdminCredential|PowerShell-objekt som innehåller information om autentiseringsuppgifter (användarnamn och lösenord) för ägaren av Azure-prenumerationen.|
|PrivilegedEndpoint|En förkonfigurerad PowerShell fjärrkonsolen ger tillgång till funktioner som Logginsamling och andra post distributionsåtgärder. Mer information finns i den [med Privilegierade slutpunkten](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint) artikel.|
|BillingModel|Vilken faktureringsmodell som tillämpas med din prenumeration. Tillåtna värden för den här parametern är: kapacitet, PayAsYouUse och utveckling.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registrera Azure stacken med Azure med hjälp av kapacitet fakturering modellen
Följ samma anvisningar som användes för registrering med hjälp av fakturering modellen lön-som-du-Använd men lägga till avtalsnummer som har köpts för kapacitet och ändra den `BillingModel` parameter till **kapacitet**. Alla andra parametrar har inte ändrats.

PowerShell för att köra:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrera Azure Stack i frånkopplade miljöer 
*Informationen i det här avsnittet gäller som börjar med den uppdaterade versionen som Azure Stack 1712 (180106.1) och stöds inte med tidigare versioner.*

Om du registrerar Azure Stack i en frånkopplad miljö (utan internet-anslutning), måste du skaffa en registrering token från Azure Stack-miljö och sedan använda denna token på en dator som kan ansluta till Azure och har [PowerShell för Azure-Stack installerat](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Hämta en registrering token från Azure Stack-miljö
  1. Kör följande PowerShell-kommandon för att hämta token för registrering:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Registrering-token har sparats i den aktuella filen för *$env:SystemDrive\RegistrationToken.txt*.

  2. Ansluten dator spara denna registrering token för användning på Azure.


### <a name="connect-to-azure-and-register"></a>Ansluta till Azure och registrera
Starta PowerShell ISE som administratör och navigera till den **registrering** mapp i den **AzureStack verktyg master** directory skapas när du [ned Azure Stack verktyg](#bkmk_tools). Importera den **RegisterWithAzure.psm1** modulen: 

PowerShell för att köra:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
Ange sedan den registrering token för att registrera med Azure i samma PowerShell-session:

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
> Spara registreringen-resursnamnet eller registrering token för framtida bruk.

## <a name="verify-azure-stack-registration"></a>Verifiera Azure Stack-registrering
Följ dessa steg för att verifiera att Azure-stacken har registrerats med Azure.
1. Logga in på Azure-stacken [administratörsportal](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https &#58; / / adminportal. *&lt;region >. &lt;fqdn >*.
2. Klicka på **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**.

Om du ser en lista med objekt som är tillgängliga från Azure (till exempel WordPress) lyckades aktiveringen.

> [!NOTE]
> När registreringen är klar, visas inte längre den aktiva varningen för att registrera inte.

## <a name="renew-or-change-registration"></a>Förnya eller ändra registrering
Du behöver uppdatera eller förnya registreringen under följande omständigheter:
- När du förnyar prenumerationen kapacitet-baserade år.
- När du ändrar faktureringsmodellen.
- När du skalar ändringar (Lägg till/ta bort noder) för kapacitet-baserade fakturering.

### <a name="change-the-subscription-you-use"></a>Ändra den prenumeration som du använder
Om du vill ändra prenumerationen du använder, måste du först köra den **ta bort AzsRegistration** cmdlet, kontrollera att du är inloggad kontexten rätt Azure PowerShell och kör slutligen **Set AzsRegistration**  med alla parametrar har ändrats:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Ändra fakturering modell eller syndikering funktioner
Om du vill ändra faktureringsmodell som tillämpas eller syndikering funktioner för din installation kan du anropa registreringsfunktionen för att ange de nya värdena. Du behöver inte först ta bort den aktuella registreringen: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>Ta bort en registrerad resurs
Om du vill ta bort en registrering och sedan måste du använda **UnRegister-AzsEnvironment** cmdlet- och pass i registreringen-resursnamnet eller registreringen token som du använde i **Register-AzsEnvironment**.

Ta bort en registrering med hjälp av ett resursnamn:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
Ta bort en registrering med hjälp av en token för registrering:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

