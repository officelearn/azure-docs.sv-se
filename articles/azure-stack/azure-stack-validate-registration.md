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
ms.date: 06/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d6835f05666d66cc4f6aa937c4b85047ce3c2e93
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077077"
---
# <a name="validate-azure-registration"></a>Verifiera Azure-registrering 
Använd verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) för att verifiera att din Azure-prenumeration är redo att använda med Azure Stack. Verifiera registrering innan du påbörjar en Azure Stack-distributionen. Validerar beredskap för installation:
- Azure-prenumeration du använder är en typ som stöds. Prenumerationer måste vara en Cloud Service Provider (CSP) eller Enterprise Agreement (EA). 
- Kontot du använder för att registrera din prenumeration med Azure kan logga in på Azure och är en Prenumerationens ägare. 

Läs mer om Azure Stack-registrering, [registrera Azure Stack med Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget för diagnostisk för installation
Ladda ned den senaste versionen av verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) finns på [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Förutsättningar
Följande krav måste vara på plats.

**Den dator där verktyget körs:**
 - Windows 10 eller Windows Server 2016, internet-anslutning.
 - PowerShell 5.1 eller senare. Om du vill kontrollera vilken version du kör följande PowerShell-cmd och granska de *större* version och *mindre* versioner:  

    >`$PSVersionTable.PSVersion` 
 - Konfigurera [PowerShell för Azure Stack](azure-stack-powershell-install.md). 
 - Ladda ned den senaste versionen av [Microsoft Azure Stack-beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.  

**Azure Active Directory-miljö:**
 - Identifiera användarnamnet och lösenordet för ett konto som är ägare till den Azure-prenumeration du vill använda med Azure Stack.  
 - Identifiera prenumerations-ID för Azure-prenumeration du ska använda. 
 - Identifiera AzureEnvironment som du ska använda: *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Verifiera Azure-registrering
1. Öppna en administrativ PowerShell-kommandotolk och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Från PowerShell-Kommandotolken kör du följande för att ange *$registrationCredential* som det konto som är prenumerationsägaren.   Ersätt *subscriptionowner@contoso.onmicrosoft.com* med ditt konto och en klient. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. Från PowerShell-Kommandotolken kör du följande för att ange *$subscriptionID* som Azure-prenumeration du ska använda. Ersätt *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* med ditt eget prenumerations-ID.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. Från PowerShell-Kommandotolken kör du följande för att starta valideringen av din prenumeration 
   - Ange värdet för AzureEnvironment som *AzureCloud*, *AzureGermanCloud*, eller *AzureChinaCloud*.  
   - Ange din Azure Active Directory-administratör och namnet på din Azure Active Directory-klient. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Granska utdata när verktyget körs. Bekräfta att statusen är OK för både inloggning och kraven för enhetsregistrering. En lyckad validering visas som på följande bild:  
![Kör-verifiering](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Rapporten och loggfilen
Varje tidsverifiering körs, loggas resultaten till **AzsReadinessChecker.log** och **AzsReadinessCheckerReport.json**. Platsen för filerna visas med verifieringsresultat i PowerShell. 

Dessa filer kan hjälpa dig att dela verifieringsstatus innan du distribuerar Azure Stack eller undersöka problem med verifieringen. Båda filerna spara resultatet av varje efterföljande valideringskontrollen. Rapporten innehåller din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem. 

Som standard båda filerna skrivs till *C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan plats.   
 - Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information från *AzsReadinessCheckerReport.json*.  Om tidigare körningar av verktyget. Mer information [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel
Om en verifieringskontroll misslyckas, visas information om felet i PowerShell-fönstret. Verktyget loggar också information till AzsReadinessChecker.log.

I följande exempel ger vägledning om vanliga verifieringsfel.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Användaren måste vara ägare till prenumerationen   
![Prenumerationens ägare](./media/azure-stack-validate-registration/subscription-owner.png)
**orsak** -kontot inte är administratör för Azure-prenumerationen.   

**Lösning** – Använd ett konto som är administratör för den prenumeration som kommer att debiteras för användning från Azure Stack-distributioner.


### <a name="expired-or-temporary-password"></a>Har upphört att gälla eller tillfälliga lösenord 
![lösenordet har upphört att gälla](./media/azure-stack-validate-registration/expired-password.png)
**orsak** -kontot kan inte logga in eftersom lösenordet har upphört att gälla eller är tillfälligt.     

**Lösning** – i PowerShell kör och följ anvisningarna för att återställa lösenordet. 
  > `Login-AzureRMAccount` 

Du kan också logga in på https://portal.azure.com som kontot och användaren tvingas att ändra lösenordet.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Microsoft-konton stöds inte för registrering  
![stöds inte kontot](./media/azure-stack-validate-registration/unsupported-account.png)
**orsak** – en Microsoft-konto (till exempel Outlook.com eller Hotmail.com) har angetts.  Dessa konton stöds inte.

**Lösning** – Använd ett konto och prenumeration från en Cloud Service Provider (CSP) eller Enterprise Agreement (EA). 


### <a name="unknown-user-type"></a>Okänd användartyp  
![Okänd användare](./media/azure-stack-validate-registration/unknown-user.png)
**orsak** -kontot kan inte logga in på den angivna Azure Active Directory-miljön. I det här exemplet *AzureChinaCloud* har angetts som den *AzureEnvironment*.  

**Lösning** – Kontrollera att kontot är giltigt för den angivna Azure-miljön. I PowerShell kör du följande för att verifiera kontot är giltigt för en viss miljö.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Nästa steg
[Verifiera Azure identity](azure-stack-validate-identity.md)
[Visa rapport om distributionsberedskap](azure-stack-validation-report.md)
[överväganden för allmänna Azure Stack-integrering](azure-stack-datacenter-integration.md)

