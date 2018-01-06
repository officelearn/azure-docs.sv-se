---
title: Registrera Azure Stack | Microsoft Docs
description: Registrera Azure stacken med din Azure-prenumeration.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/5/2018
ms.author: jeffgilb
ms.openlocfilehash: c2edafbf483692d5a11771268a1755c11b74521f
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registrera Azure stacken med din Azure-prenumeration

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan registrera [Azure Stack](azure-stack-poc.md) med Azure att hämta marketplace-objekt från Azure och Ställ in commerce rapporterar tillbaka till Microsoft.

> [!NOTE]
>Registrering rekommenderas eftersom du kan testa viktiga Azure Stack-funktioner, t.ex marketplace syndikering och användningsrapportering. När du har registrerat Azure Stack rapporterat användning till Azure handel. Du kan se den under den prenumeration som du använde för registrering. Azure-stacken Development Kit användare debiteras inte för användning rapporterar.


## <a name="get-azure-subscription"></a>Skaffa Azure-prenumeration

Innan du registrerar Azure stacken med Azure måste du ha:

- Prenumerations-ID för en Azure-prenumeration. Om du vill hämta ID, logga in på Azure, klickar du på **fler tjänster** > **prenumerationer**, klickar du på den prenumeration som du vill använda, och under **Essentials** hittar du i **Prenumerations-ID**. Kina, Tyskland och US government molnprenumerationer stöds inte för närvarande.
- Användarnamn och lösenord för ett konto som är en ägare till prenumerationen (MSA/2FA konton stöds).
- *Inte obligatoriskt från och med Azure-stacken 1712 Uppdateringsversion (1.0.180103.2):* Azure Active Directory för Azure-prenumerationen. Du hittar den här katalogen i Azure genom att hovra över din avatar i det övre högra hörnet i Azure-portalen.

Om du inte har en Azure-prenumeration som uppfyller dessa krav, kan du [skapa ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/en-us/free/?b=17.06). Registrera Azure Stack ådrar sig utan kostnad på din Azure-prenumeration.

## <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure  
> [!NOTE]
> De här stegen måste köras från en dator som har åtkomst till Privilegierade slutpunkten. Det skulle vara värddatorn för Azure-stacken Development Kit. Kontakta Azure Stack-operatorn om du använder ett integrerat system.
>

1. Öppna ett PowerShell-konsolen som administratör och [installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).  

2. Lägg till Azure-konto som används för att registrera Azure stacken. Lägg till kontot genom att köra den `Add-AzureRmAccount` cmdlet med parametern EnvironmentName inställd på **AzureCloud**. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto och du kan behöva använda 2-faktor-autentisering baserat på konfigurationen för ditt konto.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Om du har flera prenumerationer kör du följande kommando för att välja den du vill använda:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Kör följande kommando för att registrera Azure Stack-resursprovidern i din Azure-prenumeration:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Ta bort alla befintliga versioner av PowerShell-moduler som motsvarar registrering och [ladda ned den senaste versionen av den från GitHub](azure-stack-powershell-download.md).  

6. Navigera till mappen ”registrering” från katalogen ”AzureStack-verktyg-master” som har skapats i föregående steg, och importera modulen ”.\RegisterWithAzure.psm1”:  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. Kör följande skript i samma PowerShell-session: när du tillfrågas om autentiseringsuppgifter, ange `azurestack\cloudadmin` som användarnamn och lösenord är detsamma som som användes för den lokala administratören under distributionen.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | Parameter | Beskrivning |  
   |--------|-------------|
   | CloudAdminCredential | Moln-domänautentiseringsuppgifter som används för att [komma åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Användarnamnet är i formatet  **\<Azure Stack domän\>\cloudadmin**. Development kit användarnamnet anges till **azurestack\cloudadmin**. Om du använder ett integrerat system kontaktar du din Azure Stack-operatorn för att få det här värdet.|  
   | PrivilegedEndpoint | En förkonfigurerad PowerShell fjärrkonsolen ger tillgång till funktioner som Logginsamling och andra post distributionsåtgärder. För development kit finns Privilegierade slutpunkten på den virtuella datorn ”AzS ERCS01”. Om du använder ett integrerat system kontaktar du din Azure Stack-operatorn för att få det här värdet. Mer information finns i den [med Privilegierade slutpunkten](azure-stack-privileged-endpoint.md) artikel.|  
   | BillingModel | Vilken faktureringsmodell som tillämpas med din prenumeration. Tillåtna värden för den här parametern är - **kapacitet**, **PayAsYouUse**, och **Development**. För SDK-paket, det här värdet anges **Development**. Om du använder ett integrerat system kontaktar du din Azure Stack-operatorn för att få det här värdet. |

8. När skriptet har slutförts visas ett meddelande om ”Aktivera Azure Stack (det här steget kan ta upp till 10 minuter att slutföra)”.




## <a name="verify-the-registration"></a>Verifiera registrering  

1. Logga in på administratörsportalen (https://adminportal.local.azurestack.external).

2. Klicka på **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**.

3. Om du ser en lista med objekt som är tillgängliga från Azure (till exempel WordPress) lyckades aktiveringen.

> [!NOTE]
> När registreringen är klar, visas inte längre den aktiva varningen för att registrera inte.


## <a name="modify-the-registration"></a>Ändra registreringen

### <a name="change-the-subscription-you-use"></a>Ändra den prenumeration som du använder
Om du vill ändra den prenumeration som du använder måste du först kör Remove-AzsRegistration, se till att du är inloggad kontexten rätt Azure PowerShell och kör sedan ange AzsRegistration med några ändrade parametrar.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Ändra fakturering modell eller syndikering funktioner
Om du vill ändra faktureringsmodell som tillämpas eller syndikering funktioner för din installation kan du anropa registreringsfunktionen för att ange de nya värdena. Du behöver inte först ta bort den aktuella registreringen.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Frånkopplade registrering
*Informationen i det här avsnittet gäller som börjar med den uppdaterade versionen som Azure Stack 1712 (1.0.180103.2) och stöds inte med tidigare versioner.*

Om du registrerar Azure Stack i en frånkopplad miljö måste du skaffa en registrering token från Azure Stack-miljö och sedan använda denna token på en dator som kan ansluta till Azure för registrering.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Hämta en registrering token från Azure Stack-miljö
  1. För att få en token för registrering, kör du följande:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Registrering-token har sparats i den aktuella filen för *$env:SystemDrive\RegistrationToken.txt*.

  2. Ansluten dator spara denna registrering token för användning på Azure.


### <a name="connect-to-azure-and-register"></a>Ansluta till Azure och registrera
Kör stegen för den här proceduren på en dator som kan ansluta till Azure.

  1. [Hämta de senaste PowerShell-moduler som är kopplade till registrering från GitHub](azure-stack-powershell-download.md).  

  2. Navigera till mappen ”registrering” från katalogen ”AzureStack-verktyg-master” som har skapats i föregående steg, och importera modulen ”.\RegisterWithAzure.psm1”:  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Kopiera [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) till en mapp som *C:\Temp*.

  4. Starta PowerShell ISE som administratör och sedan importera modulen RegisterWithAzure.  

  5. Se till att du är inloggad i kontexten för rätt Azure PowerShell som du vill använda för att registrera din Azure Stack-miljö:  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Ange den registrering token för att registrera med Azure:

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    Du kan även använda cmdlet Get-innehåll för att peka till en fil som innehåller din token för registrering:

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Spara registreringen-resursnamnet eller registrering token för framtida bruk.

### <a name="remove-a-registered-resource"></a>Ta bort en registrerad resurs
Om du vill ta bort resursen registrering måste du använda UnRegister-AzsEnvironment och ange resursnamnet registrering eller registrering token som du använde för att registrera AzsEnvironment.
- **Resursnamnet för registrering:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Token för registrering:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>Nästa steg
[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)
