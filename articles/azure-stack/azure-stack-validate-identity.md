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
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 9c7ac89d1f12e8ec033b201f2c2dd845c11486e2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077825"
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
 - Identifiera AzureEnvironement som du ska använda: *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Verifiera Azure identity 
1. Öppna en administrativ PowerShell-kommandotolk och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Från PowerShell-Kommandotolken kör du följande för att ange *$serviceAdminCredential* som tjänstadministratören för din Azure AD-klient.  Ersätt *serviceadmin@contoso.onmicrosoft.com* med ditt konto och en klient. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Från PowerShell-Kommandotolken kör du följande för att starta valideringen av din Azure AD. 
   - Ange värdet för AzureEnvironment som *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.  
   - Ange din Azure Active Directory klientnamnet för att ersätta *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Granska utdata när verktyget körs. Bekräfta att statusen är **OK** för både inloggning och installationskraven. En lyckad validering visas som på följande bild: 
 
![Kör-verifiering](./media/azure-stack-validate-identity/validation.png)


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
 
![har upphört att gälla lösenord](./media/azure-stack-validate-identity/expired-password.png)
**orsak** -kontot kan inte logga in eftersom lösenordet har upphört att gälla eller är tillfälligt.     

**Lösning** – i PowerShell kör du följande och följer sedan anvisningarna för att återställa lösenordet.  
> `Login-AzureRMAccount`

Du kan också logga in på https://portal.azure.com som kontot och användaren tvingas att ändra lösenordet.
### <a name="unknown-user-type"></a>Okänd användartyp 
 
![Okänd användare](./media/azure-stack-validate-identity/unknown-user.png)
**orsak** -kontot kan inte logga in på den angivna Azure Active Directory (AADDirectoryTenantName). I det här exemplet *AzureChinaCloud* har angetts som den *AzureEnvironment*.

**Lösning** – Kontrollera att kontot är giltigt för den angivna Azure-miljön. I PowerShell kör du följande för att verifiera kontot är giltigt för en viss miljö: Login-AzureRmAccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Kontot är inte en administratör 
 
![inte administratör](./media/azure-stack-validate-identity/not-admin.png)

**Orsak** – även om konton som kan logga in, kontot inte är administratör för Azure Active Directory (AADDirectoryTenantName).  

**Lösning** -logga in på https://portal.azure.com som-kontot, går du till **Azure Active Directory** > **användare** > *Välj användare*  >  **Katalogroll**, och kontrollera att användaren är en **Global administratör**.  Om kontot är en användare, gå till **Azure Active Directory** > **Custom domain** namn och bekräfta att namnet du angav för *AADDirectoryTenantName* är markerad som primär domännamnet för den här katalogen.  I det här exemplet är *contoso.onmicrosoft.com*. 

Azure Stack kräver att domännamnet är namnet på primära domänen.

## <a name="next-steps"></a>Nästa steg
[Verifiera Azure-registrering](azure-stack-validate-registration.md)  
[Visa rapport om distributionsberedskap](azure-stack-validation-report.md)  
[Överväganden för allmänna Azure Stack-integrering](azure-stack-datacenter-integration.md)  

