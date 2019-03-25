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
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 3dfb87e5f6c231831cd9c007b19ad001e1fce326
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403195"
---
# <a name="validate-azure-identity"></a>Verifiera Azure identity

Använd verktyget för Azure Stack-beredskap för installation (**AzsReadinessChecker**) att verifiera att din Azure Active Directory (Azure AD) är klart att användas med Azure Stack. Verifiera din för Azure-identitetslösning innan du påbörjar en Azure Stack-distributionen.  

Validerar beredskap för installation:

- Azure Active Directory (Azure AD) som en identitetsleverantör för Azure Stack.
- Azure AD-kontot som du planerar att använda kan logga in som global administratör för Azure Active Directory.

Valideringen garanterar att din miljö är redo för Azure Stack att lagra information om användare, program, grupper och tjänstens huvudnamn från Azure Stack i Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget för diagnostisk för installation

Ladda ned den senaste versionen av verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) från den [PowerShell-galleriet](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Förutsättningar

Följande installationskrav gäller:

**Den dator där verktyget körs:**

- Windows 10 eller Windows Server 2016, internet-anslutning.
- PowerShell 5.1 eller senare. Kör följande PowerShell-kommando för att kontrollera vilken version och granska sedan de **större** version och **mindre** versioner:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell som konfigurerats för Azure Stack](azure-stack-powershell-install.md).
- Den senaste versionen av [Microsoft Azure Stack-beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.

**Azure Active Directory-miljö:**

- Identifiera Azure AD-konto om du vill använda för Azure Stack och kontrollera att det är en global administratör för Azure Active Directory.
- Identifiera namnet på din Azure AD-klientorganisation. Innehavarens namn måste vara det primära domännamnet för din Azure Active Directory. till exempel **contoso.onmicrosoft.com**.
- Identifiera Azure-miljön du ska använda. Värden som stöds för parametern environment namn är **AzureCloud**, **AzureChinaCloud**, eller **azureusgovernment eller**, beroende på vilken Azure-prenumeration du använder.

## <a name="steps-to-validate-azure-identity"></a>Anvisningar för att verifiera Azure identity

1. Öppna en upphöjd PowerShell-kommandotolk på en dator som uppfyller kraven, och kör sedan följande kommando för att installera **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Från PowerShell-Kommandotolken kör du följande kommando för att ange **$serviceAdminCredential** som tjänstadministratör för Azure AD-klienten.  Ersätt **serviceadmin\@contoso.onmicrosoft.com** med namnet på ditt konto och klient:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. Kör följande kommando för att starta valideringen av din Azure AD från PowerShell-Kommandotolken:

   - Ange miljö namn-värde för **AzureEnvironment**. Värden som stöds för parametern environment namn är **AzureCloud**, **AzureChinaCloud**, eller **azureusgovernment eller**, beroende på vilken Azure-prenumeration du använder.
   - Ersätt **contoso.onmicrosoft.com** med namnet på din Azure Active Directory-klientorganisation.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Granska utdata när verktyget körs. Bekräfta att statusen är **OK** för installationskraven. En lyckad validering visas som på följande bild:

   ```shell
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

Dessa filer kan hjälpa dig att dela verifieringsstatus innan du distribuerar Azure Stack eller undersöka problem med verifieringen. Båda filerna spara resultatet av varje efterföljande valideringskontrollen. Rapporten innehåller din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem.

Som standard båda filerna skrivs till **C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan plats.
- Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information om tidigare körningar av verktyget från **AzsReadinessCheckerReport.json**.

Mer information finns i [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel

Om en verifieringskontroll misslyckas, visas information om felet i PowerShell-fönstret. Verktyget loggar också information i filen AzsReadinessChecker.log.

I följande exempel ger vägledning om vanliga verifieringsfel.

### <a name="expired-or-temporary-password"></a>Har upphört att gälla eller tillfälliga lösenord

```shell
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

**Lösning** – kör följande kommando i PowerShell och följer sedan anvisningarna för att återställa lösenordet:

```powershell
Login-AzureRMAccount
```

Du kan också logga in på den [Azure-portalen](https://portal.azure.com) som ägare och användaren tvingas att ändra lösenordet.

### <a name="unknown-user-type"></a>Okänd användartyp

```shell
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

**Orsak** -kontot kan inte logga in till den angivna Azure Active Directory (**AADDirectoryTenantName**). I det här exemplet **AzureChinaCloud** har angetts som den **AzureEnvironment**.

**Lösning** -bekräfta att kontot är giltigt för den angivna Azure-miljön. I PowerShell kör du följande kommando för att verifiera kontot är giltigt för en viss miljö:

```powershell
Login-AzureRmAccount –EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Kontot är inte en administratör

```shell
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

**Orsak** – även om konton som kan logga in, kontot inte är administratör för Azure Active Directory (**AADDirectoryTenantName**).  

**Lösning** -logga in på den [Azure-portalen](https://portal.azure.com) som ägare, gå till **Azure Active Directory**, sedan **användare**, sedan **väljer den Användaren**, sedan **Katalogroll**, och kontrollera att användaren är en **Global administratör**. Om kontot är en **användaren**går du till **Azure Active Directory** > **anpassade domännamn**, och bekräfta att namnet du angav för  **AADDirectoryTenantName** har markerats som primär domännamn för den här katalogen. I det här exemplet är **contoso.onmicrosoft.com**.

Azure Stack kräver att domännamnet är namnet på primära domänen.

## <a name="next-steps"></a>Nästa steg

[Verifiera Azure-registrering](azure-stack-validate-registration.md)  
[Visa rapport om distributionsberedskap](azure-stack-validation-report.md)  
[Överväganden för allmänna Azure Stack-integrering](azure-stack-datacenter-integration.md)  
