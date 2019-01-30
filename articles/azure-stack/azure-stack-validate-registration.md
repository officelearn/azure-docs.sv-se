---
title: Verifiera Azure-registrering för Azure Stack | Microsoft Docs
description: Verifiera Azure-registrering med hjälp av Azure Stack-beredskap undersöker.
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
ms.topic: get-started-article
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 0281186be4fae71c56b65327e0c67da3f252a33c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239527"
---
# <a name="validate-azure-registration"></a>Verifiera Azure-registrering
 
Använd verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) för att verifiera att din Azure-prenumeration är redo att använda med Azure Stack. Verifiera registrering innan du påbörjar en Azure Stack-distributionen. Beredskap undersöker kontrollerar att:

- Azure-prenumeration du använder är en typ som stöds. Prenumerationer måste vara en Cloud Service Provider (CSP) eller Enterprise Agreement (EA). 
- Kontot du använder för att registrera din prenumeration med Azure kan logga in på Azure och är en Prenumerationens ägare. 

Läs mer om Azure Stack-registrering, [registrera Azure Stack med Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget för diagnostisk för installation

Ladda ned den senaste versionen av verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) från den [PowerShell-galleriet](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara uppfyllda:

**Den dator där verktyget körs:**
 - Windows 10 eller Windows Server 2016, internet-anslutning.
 - PowerShell 5.1 eller senare. Du kan kontrollera din version genom att köra följande PowerShell-cmdlet och granska de *större* och *mindre* versioner:  

    ```powershell
    $PSVersionTable.PSVersion
    ``` 
 - Konfigurera [PowerShell för Azure Stack](azure-stack-powershell-install.md). 
 - Ladda ned den senaste versionen av den [Microsoft Azure Stack-beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.  

**Azure Active Directory-miljö:**
 - Identifiera användarnamnet och lösenordet för ett konto som är ägare till den Azure-prenumeration du vill använda med Azure Stack.  
 - Identifiera prenumerations-ID för Azure-prenumeration du ska använda. 
 - Identifiera den **AzureEnvironment** du ska använda. Värden som stöds för parametern environment namn är **AzureCloud**, **AzureChinaCloud** eller **azureusgovernment eller**, beroende på vilken Azure-prenumeration du använder.

## <a name="validate-azure-registration"></a>Verifiera Azure-registrering

1. På en dator som uppfyller kraven och öppna en administrativ PowerShell-kommandotolk och kör sedan följande kommando för att installera den **AzsReadinessChecker**.

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

2. Från PowerShell-Kommandotolken kör du följande för att ange `$registrationCredential` som det konto som är prenumerationsägaren. Ersätt `subscriptionowner@contoso.onmicrosoft.com` med ditt konto och en klient: 
   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

3. Från PowerShell-Kommandotolken kör du följande för att ange `$subscriptionID` som Azure-prenumeration du ska använda. Ersätt `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` med ditt eget prenumerations-ID:
   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ``` 

4. Kör följande om du vill starta verifieringen av prenumerationen från PowerShell-prompten: 
   - Ange värdet för AzureEnvironment som **AzureCloud**, **AzureGermanCloud**, eller **AzureChinaCloud**.  
   - Ange din Azure Active Directory-administratör och namnet på din Azure Active Directory-klientorganisation. 

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Granska utdata när verktyget körs. Bekräfta att statusen är OK för både inloggning och kraven för enhetsregistrering. En lyckad validering ser ut som i följande exempel:
  
   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Rapporten och loggfilen

Varje tidsverifiering körs, loggas resultaten till **AzsReadinessChecker.log** och **AzsReadinessCheckerReport.json**. Platsen för filerna visas med verifieringsresultat i PowerShell. 

Dessa filer kan hjälpa dig att dela verifieringsstatus innan du distribuerar Azure Stack eller undersöka problem med verifieringen. Båda filerna spara resultatet av varje efterföljande valideringskontrollen. Rapporten innehåller din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem. 

Som standard båda filerna skrivs till *C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan plats.   
 - Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information från *AzsReadinessCheckerReport.json*.  Om tidigare körningar av verktyget. Mer information [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel
Om en verifieringskontroll misslyckas, visas information om felet i PowerShell-fönstret. Verktyget loggar också information i filen AzsReadinessChecker.log.

I följande exempel ger vägledning om vanliga verifieringsfel:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Användaren måste vara ägare till prenumerationen   

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Orsak** -kontot inte är administratör för Azure-prenumerationen.   

**Lösning** – Använd ett konto som är administratör för den prenumeration som kommer att debiteras för användning från Azure Stack-distributioner.

### <a name="expired-or-temporary-password"></a>Har upphört att gälla eller tillfälliga lösenord
 
```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Orsak** -kontot kan inte logga in eftersom lösenordet har upphört att gälla eller är tillfälligt.     

**Lösning** – i PowerShell kör och följ anvisningarna för att återställa lösenordet. 

```powershell
Login-AzureRMAccount
``` 

Du kan också logga in på https://portal.azure.com som kontot och användaren tvingas att ändra lösenordet.

### <a name="unknown-user-type"></a>Okänd användartyp  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Orsak** -kontot kan inte logga in på den angivna Azure Active Directory-miljön. I det här exemplet *AzureChinaCloud* har angetts som den *AzureEnvironment*.  

**Lösning** – Kontrollera att kontot är giltigt för den angivna Azure-miljön. I PowerShell kör du följande för att verifiera kontot är giltigt för en viss miljö:
     
```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Nästa steg

- [Verifiera Azure identity](azure-stack-validate-identity.md)
- [Visa rapport om distributionsberedskap](azure-stack-validation-report.md)
- [Överväganden för allmänna Azure Stack-integrering](azure-stack-datacenter-integration.md)

