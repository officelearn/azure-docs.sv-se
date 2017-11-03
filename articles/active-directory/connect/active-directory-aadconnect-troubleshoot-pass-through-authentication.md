---
title: "Azure AD Connect: Felsöka direkt-autentisering | Microsoft Docs"
description: "Den här artikeln beskriver hur du felsöker Azure Active Directory (AD Azure) direkt-autentisering."
services: active-directory
keywords: "Felsöka Azure AD Connect direkt-autentisering, installera Active Directory, komponenter som krävs för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: d57235671389e02c7d397b1244cdddb7a20067cc
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2017
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Felsöka Azure Active Directory direkt-autentisering

Den här artikeln får du hittar felsökningsinformation om vanliga frågor om Azure AD direkt-autentisering.

>[!IMPORTANT]
>Om du inför användaren inloggningsproblem med direkt-autentisering inte inaktivera funktionen eller avinstallera agenter för direkt-autentisering utan att behöva en endast molnbaserad globala administratörskonto återställde. Lär dig mer om [att lägga till ett globalt administratörskonto endast molnbaserad](../active-directory-users-create-azure-portal.md). Gör det här steget är mycket viktigt och garanterar att du inte blir utelåst från din klient.

## <a name="general-issues"></a>Allmänna frågor

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Kontrollera statusen för funktionen och agenter för autentisering

Kontrollera att funktionen direkt-autentisering är fortfarande **aktiverad** på din klient och status för autentisering agenter visas **Active**, och inte **inaktiv**. Du kan kontrollera statusen genom att gå till den **Azure AD Connect** blad i den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter - bladet i Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory Administrationscenter - bladet direkt-autentisering](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Användarinriktad inloggning felmeddelanden

Om användaren inte kan logga in med hjälp av direkt-autentisering, kan de se något av följande användarinriktad fel på skärmen för Azure AD: 

|Fel|Beskrivning|Lösning
| --- | --- | ---
|AADSTS80001|Det gick inte att ansluta till Active Directory|Se till att agenten servrarna är medlemmar i samma AD-skog som de användare vars lösenord behöver verifieras och de kan ansluta till Active Directory.  
|AADSTS8002|En timeout uppstod vid anslutning till Active Directory|Kontrollera att Active Directory är tillgänglig och svarar på förfrågningar från agenter.
|AADSTS80004|Användarnamnet som skickades till agenten var inte giltig|Se till att användaren försöker logga in med rätt användarnamn.
|AADSTS80005|Verifieringen påträffade oväntade WebException|Ett tillfälligt fel. Gör om begäran. Om det fortfarande inte går att kontakta Microsoft support.
|AADSTS80007|Ett fel inträffade under kommunikation med Active Directory|Kontrollera agenten loggar för mer information och att Active Directory fungerar som förväntat.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Inloggningsfel orsaker på Azure Active Directory Administrationscenter (måste Premium-licens)

Om din klient har en Azure AD Premium-licens som är kopplade till den, kan du också titta på den [inloggningsaktivitet rapporten](../active-directory-reporting-activity-sign-ins.md) på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter - inloggningar rapport](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Gå till **Azure Active Directory** -> **inloggningar** på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) och klicka på en viss användare inloggningsaktivitet. Leta efter den **LOGGA IN FELKODEN** fältet. Mappa värdet för fältet till en orsaken till felet och en lösning med hjälp av följande tabell:

|Felkod för inloggning|Logga in felorsak|Lösning
| --- | --- | ---
| 50144 | Användarens Active Directory-lösenord har upphört att gälla. | Återställa användarens lösenord i din lokala Active Directory.
| 80001 | Ingen autentiseringsagent är tillgänglig. | Installera och registrera en Agent för autentisering.
| 80002 | Den tillåtna tiden för autentiseringsagentens lösenordsvalidering överskreds. | Kontrollera om din Active Directory kan nås från Authentication Agent.
| 80003 | Ogiltigt svar har tagits emot av autentiseringsagenten. | Om problemet konsekvent reproduceras mellan flera användare, kontrollera konfigurationen av Active Directory.
| 80004 | Felaktig UPN (User Principal Name) används i begäran om inloggning. | Uppmana användaren att logga in med rätt användarnamn.
| 80005 | Autentiseringsagent: Fel uppstod. | Tillfälligt fel. Försök igen senare.
| 80007 | Det gick inte att ansluta autentiseringsagenten till Active Directory. | Kontrollera om din Active Directory kan nås från Authentication Agent.
| 80010 | Autentiseringsagenten kan inte dekryptera lösenordet. | Om problemet är konsekvent reproduceras, installera och registrera en ny Agent för autentisering. Och avinstallera den aktuella processen. 
| 80011 | Det gick inte att hämta dekrypteringsnyckeln autentiseringsagent. | Om problemet är konsekvent reproduceras, installera och registrera en ny Agent för autentisering. Och avinstallera den aktuella processen.

## <a name="authentication-agent-installation-issues"></a>Problem med installationen av autentisering

### <a name="an-unexpected-error-occurred"></a>Ett oväntat fel uppstod

[Samla in agenten loggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft Support med ditt problem.

## <a name="authentication-agent-registration-issues"></a>Problem med autentisering Agent registrering

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registreringen av agenten autentisering misslyckades på grund av blockerade portar

Kontrollera att servern där autentisering-agenten har installerats kan kommunicera med vår tjänst-URL: er och portar [här](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registreringen av agenten autentisering misslyckades på grund av token eller konto auktorisering fel

Kontrollera att du använder ett globalt administratörskonto endast molnbaserad för alla Azure AD Connect eller fristående autentiseringsagent installation och registrering åtgärder. Det finns ett känt problem med MFA-aktiverade globala administratörskonton; inaktivera MFA tillfälligt (endast för att slutföra åtgärder) som en lösning.

### <a name="an-unexpected-error-occurred"></a>Ett oväntat fel uppstod

[Samla in agenten loggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft Support med ditt problem.

## <a name="authentication-agent-uninstallation-issues"></a>Problem med autentisering Agent avinstallation

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Varning vid avinstallation av Azure AD Connect

Om du har direkt-autentisering aktiverad på din klient och försök att avinstallera Azure AD Connect, den visar följande varning: ”användare kommer inte att kunna logga in på Azure AD såvida du inte har andra direkt autentisering agenter som installerats på andra servrar”.

Se till att inställningarna är [hög tillgänglighet](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) innan du avinstallerar den Azure AD Connect för att undvika att bryta användarinloggning.

## <a name="issues-with-enabling-the-feature"></a>Problem med att aktivera funktionen

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Aktiverar funktionen misslyckades eftersom det fanns inga agenter för autentisering

Du måste ha minst en aktiv autentiseringsagent aktivera direkt-autentisering på din klient. Du kan installera en Agent för autentisering genom att installera Azure AD Connect eller en fristående autentiseringsagent.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Aktiverar funktionen misslyckades på grund av blockerade portar

Kontrollera att servern där Azure AD Connect är installerat kan kommunicera med vår tjänst-URL: er och portar [här](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Aktiverar funktionen misslyckades på grund av token eller konto auktorisering fel

Kontrollera att du använder ett globalt administratörskonto endast molnbaserad när du aktiverar funktionen. Det är ett känt problem med multifaktorautentisering (MFA)-aktiverad globala administratörskonton; inaktivera MFA tillfälligt (endast för att slutföra åtgärden) som en lösning.

## <a name="exchange-activesync-configuration-issues"></a>Problem med Exchange ActiveSync-konfiguration

Det här är de vanliga problem när du konfigurerar Exchange ActiveSync-stöd för direkt-autentisering.

### <a name="exchange-powershell-issue"></a>Exchange PowerShell problemet

Om du ser den ”**går inte att hitta en parameter som matchar parameternamnet 'PerTenantSwitchToESTSEnabled'\.**” fel när du kör den `Set-OrganizationConfig` Exchange PowerShell kommandot, kontakta Microsoft Support.

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync fungerar inte

Konfigurationen tar tid att gälla, hur lång tid som beror på din miljö. Kontakta Microsoft Support om situationen kvarstår under lång tid.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Autentiseringsagent för att samla in direkt loggar

Beroende på vilken typ av problem som du kanske behöver du titta på olika platser för vidarekoppling autentiseringsagent loggar.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect-loggar

Fel som rör installationen finns på Azure AD Connect-loggfilerna på **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Autentisering Agent händelseloggar

Öppna Loggboken program på servern för fel som rör Authentication Agent, och kontrollera **program och tjänsten Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Aktivera ”sessionsloggen” för detaljerad analys. Kör inte Authentication Agent med den här loggen aktiveras under normal drift; Använd endast för felsökning. Logginnehållet visas bara när loggen är inaktiverad igen.

### <a name="detailed-trace-logs"></a>Detaljerad spårningsloggar

Om du vill felsöka användaren logga in fel, leta efter spårningsloggar på **%ProgramData%\Microsoft\Azure AD ansluta autentisering Agent\Trace\\**. Loggarna finns skäl varför en viss användare logga in som misslyckats med att använda funktionen direkt-autentisering. Dessa fel även mappas till inloggningsfel skälen visas i den föregående [tabellen](#sign-in-failure-reasons-on-the-Azure-portal). Följande är ett exempel på post i loggen:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Du kan hämta beskrivande information om felet ('1328' i föregående exempel) genom att öppna Kommandotolken och kör följande kommando (Obs: Ersätt '1328' med faktiska felnumret som visas i loggarna):

`Net helpmsg 1328`

![Direktautentisering](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Domänkontrollanten loggar

Om granskningsloggning är aktiverad, kan ytterligare information finns i säkerhetsloggen på domänkontrollanterna. Ett enkelt sätt att fråga inloggning förfrågningar som skickas av direkt autentisering agenter är följande:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

### <a name="performance-monitor-counters"></a>Prestandaräknare

Ett annat sätt att övervaka autentisering agenter är att spåra specifika prestandaräknare på varje server där autentisering-agenten är installerad. Använd följande globala räknare (**# Tereftalsyra autentiseringar**, **#PTA misslyckades autentiseringar** och **#PTA lyckade autentiseringar**) och fel räknare (**# Tereftalsyra autentiseringsfel**):

![Direkt-autentisering prestandaräknare](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Direkt-autentisering ger hög tillgänglighet med hjälp av flera autentisering agenter och _inte_ belastningsutjämning. Beroende på din konfiguration _inte_ alla autentisering agenter får ungefär _lika_ antal begäranden. Det är möjligt att en specifik autentiseringsagent får ingen trafik alls.
