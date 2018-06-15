---
title: Verifiera Azure registrering för Azure-Stack | Microsoft Docs
description: Verifiera Azure registrering med hjälp av Azure-stacken beredskap layout.
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
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937844"
---
# <a name="validate-azure-registration"></a>Verifiera Azure registrering 
Verktyget Azure Stack beredskap Checker (AzsReadinessChecker) att verifiera att din Azure-prenumeration är redo att användas med Azure-stacken. Verifiera registrering innan du påbörjar en Azure-Stack-distributionen. Validerar beredskap layout:
- Azure-prenumerationen du använder är en typ som stöds. Prenumerationer måste vara en molntjänst-providers (CSP) eller Enterprise-avtal (EA). 
- Det konto som används för att registrera prenumerationen med Azure kan logga in till Azure och är en prenumeration ägare. 

Mer information om Azure Stack-registreringen finns [registrera Azure stacken med Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget beredskap för installation
Ladda ned den senaste versionen av verktyget Azure Stack beredskap Checker (AzsReadinessChecker) finns på [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Förutsättningar
Följande krav måste vara på plats.

**Den dator där verktyget körs:**
 - Windows 10 eller Windows Server 2016 med internet-anslutning.
 - PowerShell 5.1 eller senare. Kör följande PowerShell-kommando för att kontrollera din version och granska sedan de *större* version och *mindre* versioner:  

    >`$PSVersionTable.PSVersion` 
 - Konfigurera [PowerShell för Azure-stacken](azure-stack-powershell-install.md). 
 - Hämta den senaste versionen av [Microsoft Azure-stacken beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.  

**Azure Active Directory-miljö:**
 - Identifiera användarnamnet och lösenordet för ett konto som är en ägare för Azure-prenumerationen kommer att använda med Azure-stacken.  
 - Identifiera prenumerations-ID för Azure-prenumerationen används. 
 - Identifiera AzureEnvironment som du vill använda: *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Verifiera Azure registrering
1. Öppna en administrativ PowerShell-Kommandotolken och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Från PowerShell-Kommandotolken kör du följande för att ange *$registrationCredential* som det konto som är prenumerationsägaren.   Ersätt *subscriptionowner@contoso.onmicrosoft.com* med ditt konto och din klient. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. Från PowerShell-Kommandotolken kör du följande för att ange *$subscriptionID* som Azure-prenumerationen används. Ersätt *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* med din egen prenumerations-ID.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. Kör följande om du vill starta verifieringen av prenumerationen från PowerShell-Kommandotolken 
   - Ange värdet för AzureEnvironment som *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.  
   - Ange din Azure Active Directory-administratör och Azure Active Directory-klient-namn. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Granska utdata när verktyget körs. Bekräfta status är OK för både inloggning och Registreringskrav för. En lyckad validering visas som i följande bild:  
![Kör-validering](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Rapporten och loggfil
Varje gång verifieringen körs, loggas resultaten till **AzsReadinessChecker.log** och **AzsReadinessCheckerReport.json**. Platsen för filerna visas med verifieringsresultat i PowerShell. 

Dessa filer kan hjälpa dig att dela verifieringsstatus innan du distribuerar Azure Stack eller undersöka problem för verifiering. Båda filerna sparas resultaten för varje efterföljande verifieringen. Den här rapporten innehåller din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem. 

Som standard skrivs båda filerna till *C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan rapport-plats.   
 - Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information från *AzsReadinessCheckerReport.json*.  Om föregående körningar av verktyget. Mer information [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel
Om en verifieringen misslyckas, visas information om felet i PowerShell-fönstret. Verktyget loggar också information till AzsReadinessChecker.log.

Följande exempel ger riktlinjer för vanliga verifieringsfel.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Användaren måste vara ägare till prenumerationen   
![prenumerationsägaren](./media/azure-stack-validate-registration/subscription-owner.png)
**orsak** -kontot inte är administratör för Azure-prenumerationen.   

**Lösning** -använder ett konto som är administratör för Azure-prenumeration som kommer att debiteras för användning från Azure Stack-distribution.


### <a name="expired-or-temporary-password"></a>Har upphört att gälla eller tillfälliga lösenord 
![utgångna lösenord](./media/azure-stack-validate-registration/expired-password.png)
**orsak** -kontot kan inte logga in eftersom lösenordet har antingen upphört att gälla eller är tillfällig.     

**Lösning** – i PowerShell kör och följ anvisningarna för att återställa lösenordet. 
  > `Login-AzureRMAccount` 

Du kan också logga in på https://portal.azure.com som kontot och användaren tvingas att ändra lösenordet.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Microsoft-konton stöds inte för registrering  
![stöds inte kontot](./media/azure-stack-validate-registration/unsupported-account.png)
**orsak** -ett Microsoft-konto (till exempel Outlook.com eller Hotmail.com) har angetts.  Dessa konton stöds inte.

**Lösning** -använder ett konto och prenumeration från en molntjänst-providers (CSP) eller Enterprise-avtal (EA). 


### <a name="unknown-user-type"></a>Okänd typ  
![Okänd användare](./media/azure-stack-validate-registration/unknown-user.png)
**orsak** -kontot kan inte logga in på den angivna Azure Active Directory-miljön. I det här exemplet *AzureChinaCloud* har angetts som den *AzureEnvironment*.  

**Lösning** -bekräfta att kontot är giltigt för den angivna Azure-miljön. I PowerShell kör du följande för att verifiera kontot är giltigt för en viss miljö.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Nästa steg
[Verifiera Azure identitet](azure-stack-validate-identity.md)
[visa beredskapsrapporten](azure-stack-validation-report.md)
[allmänna Azure Stack integration överväganden](azure-stack-datacenter-integration.md)

