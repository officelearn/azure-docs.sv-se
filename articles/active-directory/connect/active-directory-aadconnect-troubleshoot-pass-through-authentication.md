---
title: 'Azure AD Connect: Felsöka direktautentisering | Microsoft Docs'
description: Den här artikeln beskriver hur du felsöker direktautentisering i Azure Active Directory (AD Azure).
services: active-directory
keywords: Felsöka Azure AD Connect direktautentisering genom att installera Active Directory, komponenter som krävs för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2e7f3b0f01dbd6656413c233fcf64c46963d00ef
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917378"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Felsöka Azure Active Directory-direktautentisering

Den här artikeln beskriver hur du hittar felsökningsinformation om vanliga frågor om Azure AD-direktautentisering.

>[!IMPORTANT]
>Om du får användaren inloggningsproblem med direktautentisering inte inaktivera funktionen eller avinstallera Autentiseringsagenter för direkt utan en endast molnbaserad konto som Global administratör för att använda. Lär dig mer om [att lägga till ett globalt administratörskonto molnbaserad](../active-directory-users-create-azure-portal.md). Utför det här steget är mycket viktigt och ser till att du inte blir utelåst från klientorganisationen.

## <a name="general-issues"></a>Allmänna problem

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Kontrollera statusen för funktionen och Autentiseringsagenter

Kontrollera att funktionen direktautentisering är fortfarande **aktiverad** på din klient och status för Autentiseringsagenter visar **Active**, och inte **inaktiv**. Du kan kontrollera statusen genom att gå till den **Azure AD Connect** bladet på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter – Azure AD Connect-bladet](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory Administrationscenter - direktautentisering bladet](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Användarens inloggning felmeddelanden

Om användaren inte kan logga in med hjälp av direktautentisering, kan de se något av följande användarinriktade fel på skärmen för Azure AD: 

|Fel|Beskrivning|Lösning
| --- | --- | ---
|AADSTS80001|Det går inte att ansluta till Active Directory|Kontrollera att agentservrar är medlemmar i samma AD-skog som de användare vars lösenord behöver verifieras och kunna ansluta till Active Directory.  
|AADSTS8002|En timeout inträffade vid anslutning till Active Directory|Kontrollera att Active Directory är tillgänglig och svarar på förfrågningar från agenter.
|AADSTS80004|Användarnamnet som skickades till agenten var inte giltig|Se till att användaren försöker att logga in med rätt användarnamn.
|AADSTS80005|Verifiering påträffade oförutsägbara WebException|Ett tillfälligt fel. Gör om begäran. Om det fortfarande inte går att kontakta Microsoft support.
|AADSTS80007|Ett fel inträffade under kommunikation med Active Directory|Kontrollera agentloggarna för mer information och verifiera att Active Directory fungerar som förväntat.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Orsaker till felet logga in på Azure Active Directory Administrationscenter (kräver Premium-licens)

Om klienten har en associerad med den Azure AD Premium-licens, kan du också titta på den [inloggningsaktivitet rapporten](../active-directory-reporting-activity-sign-ins.md) på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter - rapporten inloggningar](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Gå till **Azure Active Directory** -> **inloggningar** på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) och klicka på en viss användares inloggningsaktivitet. Leta efter den **LOGGA IN FELKODEN** fält. Mappa fältets värde till en felorsak och en lösning med hjälp av följande tabell:

|Felkod för inloggning|Logga in felorsak|Lösning
| --- | --- | ---
| 50144 | Användarens Active Directory-lösenord har upphört att gälla. | Återställa användarens lösenord i din lokala Active Directory.
| 80001 | Ingen autentiseringsagent är tillgänglig. | Installera och registrera en Agent för autentisering.
| 80002 | Den tillåtna tiden för autentiseringsagentens lösenordsvalidering överskreds. | Kontrollera om din Active Directory kan nås från den Autentiseringsagenten.
| 80003 | Ogiltigt svar har tagits emot av autentiseringsagenten. | Om problemet är konsekvent reproducerbar för flera användare, kontrollerar du konfigurationen av Active Directory.
| 80004 | Felaktig UPN (User Principal Name) används i begäran om inloggning. | Be användaren att logga in med rätt användarnamn.
| 80005 | Autentiseringsagent: Fel uppstod. | Tillfälligt fel. Försök igen senare.
| 80007 | Det gick inte att ansluta autentiseringsagenten till Active Directory. | Kontrollera om din Active Directory kan nås från den Autentiseringsagenten.
| 80010 | Autentiseringsagenten kan inte dekryptera lösenordet. | Om problemet är konsekvent reproducerbar, installera och registrera en ny Agent för autentisering. Och avinstallera den aktuella artikeln. 
| 80011 | Autentiseringsagenten kunde inte hämta dekrypteringsnyckeln. | Om problemet är konsekvent reproducerbar, installera och registrera en ny Agent för autentisering. Och avinstallera den aktuella artikeln.

## <a name="authentication-agent-installation-issues"></a>Autentiseringsproblem Agent installation

### <a name="an-unexpected-error-occurred"></a>Det uppstod ett oväntat fel

[Samla in agentloggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft Support med ditt problem.

## <a name="authentication-agent-registration-issues"></a>Autentiseringsproblem agenten registrering

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registrering av Autentiseringsagenten misslyckades på grund av blockerade portar

Kontrollera att servern där autentisering-agenten har installerats kan kommunicera med vår tjänst-URL: er och portar visas [här](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registrering av Autentiseringsagenten misslyckades på grund av token eller konto-auktoriseringsfel

Kontrollera att du använder ett globalt administratörskonto enbart i molnet för alla Azure AD Connect eller fristående autentiseringsagent installation och registreringsåtgärder. Det finns ett känt problem med MFA-aktiverade globala administratörskonton; inaktivera MFA tillfälligt (endast för att genomföra åtgärderna) som en tillfällig lösning.

### <a name="an-unexpected-error-occurred"></a>Det uppstod ett oväntat fel

[Samla in agentloggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft Support med ditt problem.

## <a name="authentication-agent-uninstallation-issues"></a>Autentiseringsproblem agenten avinstallation

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Varning vid avinstallera Azure AD Connect

Om du har direktautentisering har aktiverats på din klient och försök att avinstallera Azure AD Connect, den visar följande varning: ”användare kommer inte att kunna logga in på Azure AD om du inte har andra direktautentisering agenter som installerats på andra servrar ”.

Se till att din konfiguration är [hög tillgängliga](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) innan du avinstallerar den Azure AD Connect för att undvika att skada användarinloggning.

## <a name="issues-with-enabling-the-feature"></a>Problem med att aktivera funktionen

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Aktiverar funktionen misslyckades eftersom det fanns inga Autentiseringsagenter

Du måste ha minst en aktiv Agent för autentisering att aktivera direktautentisering på din klient. Du kan installera en Agent för autentisering genom att installera Azure AD Connect eller en fristående Autentiseringsagenten.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Det gick inte att aktivera funktionen på grund av blockerade portar

Kontrollera att servern där Azure AD Connect är installerad kan kommunicera med vår tjänst-URL: er och portar visas [här](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Det gick inte att aktivera funktionen på grund av token eller konto-auktoriseringsfel

Kontrollera att du använder ett globalt administratörskonto enbart i molnet när du aktiverar funktionen. Det finns ett känt problem med multifaktorautentisering (MFA)-aktiverad globala administratörskonton; inaktivera MFA tillfälligt (endast för att slutföra åtgärden) som en tillfällig lösning.

## <a name="exchange-activesync-configuration-issues"></a>Problem med konfigurationen av Exchange ActiveSync

Dessa är vanliga problem när du konfigurerar Exchange ActiveSync-stöd för direktautentisering.

### <a name="exchange-powershell-issue"></a>Exchange PowerShell-problem

Om du ser den ”**går inte att hitta en parameter som matchar parameternamnet på 'PerTenantSwitchToESTSEnabled'\.**” fel när du kör den `Set-OrganizationConfig` Exchange PowerShell kommandot, kontakta Microsoft Support.

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync fungerar inte

Konfigurationen tar lite tid att börja gälla – hur lång tid som beror på din miljö. Kontakta Microsoft Support om situationen kvarstår under en längre tid.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Samla in loggar för Autentiseringsagenten för direktautentisering

Beroende på vilken typ av problem som du kanske behöver du titta på olika platser för Autentiseringsagenten för direktautentisering loggar.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect-loggar

För fel som rör installation, titta på Azure AD Connect-loggfilerna på **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Händelseloggar för autentisering Agent

För fel som rör den autentiseringsagent, öppna Loggboken program på servern och kontrollerar du under **program och tjänsten Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Aktivera ”sessionsloggen” för detaljerad analys. Kör inte Autentiseringsagenten med den här loggfilen aktiveras under normal drift; Använd enbart för felsökning. Logginnehållet visas bara när loggen är inaktiverad igen.

### <a name="detailed-trace-logs"></a>Detaljerad spårningsloggar

Om du vill felsöka användaren inloggningar, leta efter spårningsloggar på **%ProgramData%\Microsoft\Azure AD ansluta autentisering Agent\Trace\\**. Dessa loggar innehåller orsaker till varför en viss användare logga in som misslyckats med att använda funktionen direktautentisering. De här felen också är mappade till inloggningsfel skälen visas i det föregående [tabell](#sign-in-failure-reasons-on-the-Azure-portal). Nedan följer ett exempel på post i loggen:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Du kan hämta beskrivande information om felet (”1328” i föregående exempel) genom att öppna Kommandotolken och kör följande kommando (Observera: Ersätt '1328' med faktiska felnumret som visas i loggarna):

`Net helpmsg 1328`

![Direktautentisering](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Domänkontrollanten loggar

Om granskning har aktiverats, finns mer information i säkerhetsloggar för domänkontrollanter. Ett enkelt sätt att fråga efter inloggning förfrågningar som skickas av direktautentisering Autentiseringsagenter är följande:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Prestandaräknare

Ett annat sätt att övervaka Autentiseringsagenter är att spåra specifika prestandaräknare på varje server där autentisering-agenten är installerad. Använd följande globala räknare (**# PTA autentiseringar**, **#PTA misslyckades autentiseringar** och **#PTA lyckade autentiseringar**) och fel räknare (**# PTA autentiseringsfel**):

![Räknare för prestandaövervakning för autentisering av direktautentisering](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Direktautentisering ger hög tillgänglighet med hjälp av flera Autentiseringsagenter och _inte_ belastningsutjämning. Beroende på din konfiguration _inte_ alla Autentiseringsagenter får ungefär _lika_ antal förfrågningar. Det är möjligt att en specifik autentiseringsagent får ingen trafik alls.
