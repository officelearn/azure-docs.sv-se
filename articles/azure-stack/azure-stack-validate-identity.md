---
title: Verifiera Azure identitet för Azure Stack | Microsoft Docs
description: Verifiera Azure identitet med hjälp av Azure Stack-beredskap undersöker.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 880c44530a33918f9526e4a7d805f094cfbf0c90
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772661"
---
# <a name="validate-azure-identity"></a>Verifiera Azure identity 
Använd verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) för att verifiera att din Azure Active Directory (Azure AD) är klart att användas med Azure Stack. Verifiera din för Azure-identitetslösning innan du påbörjar en Azure Stack-distributionen.  

Validerar beredskap för installation:
 - Azure Active Directory (Azure AD) som en identitetsleverantör för Azure Stack.
 - Azure AD-kontot som du planerar att använda kan logga in som global administratör för Azure Active Directory. 

Valideringen garanterar att din miljö är redo för Azure Stack att lagra information om användare, program, grupper och tjänstens huvudnamn från Azure Stack i Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget för diagnostisk för installation
Ladda ned den senaste versionen av verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) från den [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Förutsättningar
Följande krav måste vara på plats.

**Den dator där verktyget körs:**
 - Windows 10 eller Windows Server 2016, internet-anslutning.
 - PowerShell 5.1 eller senare. Om du vill kontrollera vilken version du kör följande PowerShell-cmd och granska de *större* version och *mindre* versioner:  

   > `$PSVersionTable.PSVersion`
 - Konfigurera [PowerShell för Azure Stack](azure-stack-powershell-install.md). 
 - Den senaste versionen av [Microsoft Azure Stack-beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.

**Azure Active Directory-miljö:**
 - Identifiera Azure AD-konto du vill använda för Azure Stack och kontrollera att det är en Azure Active Directory Global administratör.
 - Identifiera namnet på din Azure AD-klient. Innehavarens namn måste vara den *primära* domännamn för din Azure Active Directory. Till exempel *contoso.onmicrosoft.com*. 
 - Identifiera AzureEnvironment som du ska använda. Värden som stöds för miljön name-parametern är AzureCloud, AzureChinaCloud eller azureusgovernment eller beroende på vilken Azure-prenumeration du använder.

## <a name="validate-azure-identity"></a>Verifiera Azure identity 
1. Öppna en administrativ PowerShell-kommandotolk och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Från PowerShell-Kommandotolken kör du följande för att ange *$serviceAdminCredential* som tjänstadministratören för din Azure AD-klient.  Ersätt *serviceadmin@contoso.onmicrosoft.com* med ditt konto och en klient. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Från PowerShell-Kommandotolken kör du följande för att starta valideringen av din Azure AD. 
   - Ange namn-värde miljö för AzureEnvironment. Värden som stöds för miljön name-parametern är AzureCloud, AzureChinaCloud eller azureusgovernment eller beroende på vilken Azure-prenumeration du använder.  
   - Ange din Azure Active Directory klientnamnet för att ersätta *contoso.onmicrosoft.com*. 

   > `Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Granska utdata när verktyget körs. Bekräfta att statusen är **OK** för installationskraven. En lyckad validering visas som på följande bild: 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: OK

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```


## <a name="report-and-log-file"></a>Rapporten och loggfilen
Varje tidsverifiering körs, loggas resultaten till **AzsReadinessChecker.log** och **AzsReadinessCheckerReport.json**. Platsen för filerna visas med verifieringsresultat i PowerShell.

Dessa filer kan hjälpa dig att dela verifieringsstatus innan du distribuerar Azure Stack eller undersöka problem med verifieringen.  Båda filerna spara resultatet av varje efterföljande valideringskontrollen. Rapporten innehåller din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem. 

Som standard båda filerna skrivs till *C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan plats.   
 - Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information från *AzsReadinessCheckerReport.json*.  Om tidigare körningar av verktyget. 

Mer information [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel
Om en verifieringskontroll misslyckas, visas information om felet i PowerShell-fönstret. Verktyget loggar också information till AzsReadinessChecker.log.

I följande exempel ger vägledning om vanliga verifieringsfel.

### <a name="expired-or-temporary-password"></a>Har upphört att gälla eller tillfälliga lösenord 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**Orsak** -kontot kan inte logga in eftersom lösenordet har upphört att gälla eller är tillfälligt.     

**Lösning** – i PowerShell kör du följande och följer sedan anvisningarna för att återställa lösenordet.  
> `Login-AzureRMAccount`

Du kan också logga in på https://portal.azure.com som kontot och användaren tvingas att ändra lösenordet.
### <a name="unknown-user-type"></a>Okänd användartyp 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**Orsak** -kontot kan inte logga in på den angivna Azure Active Directory (AADDirectoryTenantName). I det här exemplet *AzureChinaCloud* har angetts som den *AzureEnvironment*.

**Lösning** – Kontrollera att kontot är giltigt för den angivna Azure-miljön. I PowerShell kör du följande för att verifiera kontot är giltigt för en viss miljö:   Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Kontot är inte en administratör 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Orsak** – även om konton som kan logga in, kontot inte är administratör för Azure Active Directory (AADDirectoryTenantName).  

**Lösning** -logga in på https://portal.azure.com som-kontot, går du till **Azure Active Directory** > **användare** > *Välj användare*  >  **Katalogroll**, och kontrollera att användaren är en **Global administratör**.  Om kontot är en användare, gå till **Azure Active Directory** > **Custom domain** namn och bekräfta att namnet du angav för *AADDirectoryTenantName* är markerad som primär domännamnet för den här katalogen.  I det här exemplet är *contoso.onmicrosoft.com*. 

Azure Stack kräver att domännamnet är namnet på primära domänen.

## <a name="next-steps"></a>Nästa steg
[Verifiera Azure-registrering](azure-stack-validate-registration.md)  
[Visa rapport om distributionsberedskap](azure-stack-validation-report.md)  
[Överväganden för allmänna Azure Stack-integrering](azure-stack-datacenter-integration.md)  

