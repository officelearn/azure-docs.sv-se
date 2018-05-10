---
title: Verifiera Azure identitet för Azure-Stack | Microsoft Docs
description: Verifiera Azure identitet med hjälp av Azure-stacken beredskap layout.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="validate-azure-identity"></a>Verifiera Azure identitet 
Verktyget Azure Stack beredskap Checker (AzsReadinessChecker) att verifiera att din Azure Active Directory (AD Azure) är redo att användas med Azure-stacken. Verifiera din identitetslösning för Azure innan du påbörjar en Azure-Stack-distributionen.  

Validerar beredskap layout:
 - Azure Active Directory (AD Azure) som en identitetsleverantör för Azure-stacken.
 - Azure AD-kontot som du tänker använda kan logga in som global administratör för Azure Active Directory. 

Validering säkerställer att din miljö är redo för Azure stackutrymme för att lagra information om användare, program, grupper och tjänstens huvudnamn från Azure-stacken i din Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget beredskap för installation
Ladda ned den senaste versionen av verktyget Azure Stack beredskap Checker (AzsReadinessChecker) från den [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Förutsättningar
Följande krav måste vara på plats.

**Den dator där verktyget körs:**
 - Windows 10 eller Windows Server 2016 med internet-anslutning.
 - PowerShell 5.1 eller senare. Kör följande PowerShell-kommando för att kontrollera din version och granska sedan de *större* version och *mindre* versioner:  

   > `$PSVersionTable.PSVersion`
 - Konfigurera [PowerShell för Azure-stacken](azure-stack-powershell-install.md). 
 - Den senaste versionen av [Microsoft Azure-stacken beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.

**Azure Active Directory-miljö:**
 - Identifiera Azure AD-kontot du vill använda för Azure-stacken och se till att det är en Azure Active Directory Global administratör.
 - Identifiera dina Azure AD-klient-namn. Innehavarens namn måste vara den *primära* domännamn för din Azure Active Directory. Till exempel *contoso.onmicrosoft.com*. 
 - Identifiera AzureEnvironement som du vill använda: *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Verifiera Azure identitet 
1. Öppna en administrativ PowerShell-Kommandotolken och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Från PowerShell-Kommandotolken kör du följande för att ange *$serviceAdminCredential* som tjänstadministratör för din Azure AD-klient.  Ersätt *serviceadmin@contoso.onmicrosoft.com* med ditt konto och din klient. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Kör följande om du vill starta verifieringen av din Azure AD från PowerShell-Kommandotolken. 
   - Ange värdet för AzureEnvironment som *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.  
   - Ange din Azure Active Directory innehavarens namn att ersätta *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Granska utdata när verktyget körs. Bekräfta status är **OK** för både inloggning och installationskraven. En lyckad validering visas som i följande bild: 
 
![Kör-validering](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>Rapporten och loggfil
Varje gång verifieringen körs, loggas resultaten till **AzsReadinessChecker.log** och **AzsReadinessCheckerReport.json**. Platsen för filerna visas med verifieringsresultat i PowerShell.

Dessa filer kan hjälpa dig att dela verifieringsstatus innan du distribuerar Azure Stack eller undersöka problem för verifiering.  Båda filerna sparas resultaten för varje efterföljande verifieringen. Den här rapporten innehåller din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem. 

Som standard skrivs båda filerna till *C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan rapport-plats.   
 - Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information från *AzsReadinessCheckerReport.json*.  Om föregående körningar av verktyget. 

Mer information [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel
Om en verifieringen misslyckas, visas information om felet i PowerShell-fönstret. Verktyget loggar också information till AzsReadinessChecker.log.

Följande exempel ger riktlinjer för vanliga verifieringsfel.

### <a name="expired-or-temporary-password"></a>Har upphört att gälla eller tillfälliga lösenord 
 
![utgångna lösenord](./media/azure-stack-validate-identity/expired-password.png)
**orsak** -kontot kan inte logga in eftersom lösenordet har antingen upphört att gälla eller är tillfällig.     

**Lösning** – i PowerShell kör du följande och följer sedan anvisningarna för att återställa lösenordet.  
> `Login-AzureRMAccount`

Du kan också logga in på https://portal.azure.com som kontot och användaren tvingas att ändra lösenordet.
### <a name="unknown-user-type"></a>Okänd typ 
 
![Okänd användare](./media/azure-stack-validate-identity/unknown-user.png)
**orsak** -kontot kan inte logga in på den angivna Azure Active Directory (AADDirectoryTenantName). I det här exemplet *AzureChinaCloud* har angetts som den *AzureEnvironment*.

**Lösning** -bekräfta att kontot är giltigt för den angivna Azure-miljön. I PowerShell kör du följande för att verifiera kontot är giltigt för en viss miljö: Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Kontot är inte en administratör 
 
![inte administratör](./media/azure-stack-validate-identity/not-admin.png)

**Orsak** -även om kontot kan logga in, kontot inte är administratör för Azure Active Directory (AADDirectoryTenantName).  

**Lösning** -logga in på https://portal.azure.com som-konto, gå till **Azure Active Directory** > **användare** > *Välj användare*  >  **Directory rollen**, och sedan kontrollera att användaren är en **Global administratör**.  Om kontot är en användare måste du gå till **Azure Active Directory** > **anpassad domän** namn och bekräfta att namnet du angav för *AADDirectoryTenantName* är markerat som primär domännamnet för den här katalogen.  I det här exemplet som är *contoso.onmicrosoft.com*. 

Azure-stacken kräver att domännamnet är namnet på primära domänen.

## <a name="next-steps"></a>Nästa steg
[Verifiera Azure registrering](azure-stack-validate-registration.md)  
[Visa beredskapsrapporten](azure-stack-validation-report.md)  
[Överväganden för allmänna Azure Stack-integration](azure-stack-datacenter-integration.md)  

