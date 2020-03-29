---
title: 'Azure AD Connect: Felsöka direktautentisering | Microsoft-dokument'
description: I den här artikeln beskrivs hur du felsöker Azure Active Directory (Azure AD) direktautentisering.
services: active-directory
keywords: Felsöka Azure AD Connect Pass-through-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae83cea866367fa6a6596caa683d0287bea96c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456175"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Felsöka Azure Active Directory-direktautentisering

Den här artikeln hjälper dig att hitta felsökningsinformation om vanliga problem med Azure AD-direktautentisering.

>[!IMPORTANT]
>Om du står inför användarinloggningsproblem med direktautentisering ska du inte inaktivera funktionen eller avinstallera autentiseringsagenter för direktutströmning utan att ha ett globalt administratörskonto för molnet att falla tillbaka på. Läs mer om hur du [lägger till ett globalt administratörskonto för molnet](../active-directory-users-create-azure-portal.md). Att göra det här steget är kritiskt och ser till att du inte blir utelåst från din klient.

## <a name="general-issues"></a>Allmänna frågor

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Kontrollera status för funktionen och autentiseringsagenter

Kontrollera att direktautentiseringsfunktionen fortfarande är **aktiverad** på din klientorganisation och statusen för autentiseringsagenter visar **Aktiv**och inte **inaktiv**. Du kan kontrollera status genom att gå till **Azure AD Connect-bladet** i [Administrationscentret](https://aad.portal.azure.com/)för Azure Active Directory .

![Administrationscenter för Azure Active Directory – Azure AD Connect-blad](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Administrationscenter för Azure Active Directory – Bladet Direktautentisering](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Felmeddelanden om användarinriktad inloggning

Om användaren inte kan logga in med direktautentisering kan de se något av följande användarinriktade fel på inloggningsskärmen för Azure AD: 

|Fel|Beskrivning|Lösning
| --- | --- | ---
|AADSTS80001|Det går inte att ansluta till Active Directory|Kontrollera att agentservrar är medlemmar i samma AD-skog som de användare vars lösenord måste valideras och de kan ansluta till Active Directory.  
|AADSTS8002|En timeout uppstod som ansluter till Active Directory|Kontrollera att Active Directory är tillgängligt och svarar på begäranden från agenterna.
|AADSTS80004|Användarnamnet som skickades till agenten var ogiltigt|Se till att användaren försöker logga in med rätt användarnamn.
|AADSTS80005|Validering påträffade oförutsägbar webexception|Ett tillfälligt fel. Försök igen. Om det fortsätter att misslyckas kontaktar du Microsoft-supporten.
|AADSTS80007|Ett fel uppstod när det kommunicerade med Active Directory|Kontrollera agentloggarna för mer information och kontrollera att Active Directory fungerar som förväntat.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Orsaker till inloggningsfel i administrationscentret för Azure Active Directory (behöver Premium-licens)

Om din klient har en Azure AD Premium-licens kopplad till sig kan du också titta på [inloggningsaktivitetsrapporten i](../reports-monitoring/concept-sign-ins.md) [Administrationscentret](https://aad.portal.azure.com/)för Azure Active Directory .

![Administrationscenter för Azure Active Directory – rapporten Inloggningar](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navigera till Azure Active -> **Directory-inloggningar i Administrationscentret** för [Azure Active Directory](https://aad.portal.azure.com/) och klicka på en viss användares inloggningsaktivitet. **Azure Active Directory** Leta efter fältet **INLOGGNINGSFELKOD.** Mappa värdet för det fältet till en felorsak och lösning med hjälp av följande tabell:

|Felkod för inloggning|Orsaken till inloggningsfel|Lösning
| --- | --- | ---
| 50144 | Användarens Active Directory-lösenord har upphört att gälla. | Återställ användarens lösenord i den lokala Active Directory.
| 80001 | Ingen autentiseringsagent är tillgänglig. | Installera och registrera en autentiseringsagent.
| 80002 | Den tillåtna tiden för autentiseringsagentens lösenordsvalidering överskreds. | Kontrollera om Active Directory kan nås från autentiseringsagenten.
| 80003 | Ogiltigt svar har tagits emot av autentiseringsagenten. | Om problemet konsekvent kan återskapas mellan flera användare kontrollerar du Active Directory-konfigurationen.
| 80004 | Felaktig UPN (User Principal Name) används i begäran om inloggning. | Be användaren att logga in med rätt användarnamn.
| 80005 | Autentiseringsagent: Fel uppstod. | Övergående fel. Försök igen senare.
| 80007 | Det gick inte att ansluta autentiseringsagenten till Active Directory. | Kontrollera om Active Directory kan nås från autentiseringsagenten.
| 80010 | Autentiseringsagenten kan inte dekryptera lösenordet. | Om problemet är konsekvent reproducerbart installerar och registrerar du en ny autentiseringsagent. Och avinstallera den nuvarande. 
| 80011 | Autentiseringsagenten kunde inte hämta dekrypteringsnyckeln. | Om problemet är konsekvent reproducerbart installerar och registrerar du en ny autentiseringsagent. Och avinstallera den nuvarande.

>[!IMPORTANT]
>Direktautentiseringsagenter autentiserar Azure AD-användare genom att verifiera deras användarnamn och lösenord mot Active Directory genom att anropa [Win32 LogonUser-API:et](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx). Om du har ställt in inställningen "Inloggning till" i Active Directory för att begränsa åtkomsten till arbetsstationsinloggning måste du också lägga till servrar som är värdar för direktautentiseringsagenter i listan över "Inloggning till"-servrar. Om du inte gör detta blockeras dina användare från att logga in på Azure AD.

## <a name="authentication-agent-installation-issues"></a>Installationsproblem för autentiseringsagent

### <a name="an-unexpected-error-occurred"></a>Ett oväntat fel uppstod

[Samla in agentloggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft Support med ditt problem.

## <a name="authentication-agent-registration-issues"></a>Registreringsproblem för autentiseringsagent

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registreringen av autentiseringsagenten misslyckades på grund av blockerade portar

Kontrollera att den server där autentiseringsagenten har installerats kan kommunicera med våra tjänstadresser och portar som anges [här](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registreringen av autentiseringsagenten misslyckades på grund av token- eller kontoauktoriseringsfel

Se till att du använder ett globalt administratörskonto endast för molnet för alla Azure AD Connect- eller fristående installations- och registreringsåtgärder för autentiseringsagent. Det finns ett känt problem med MFA-aktiverade globala administratörskonton. stänga av MFA tillfälligt (endast för att slutföra åtgärderna) som en lösning.

### <a name="an-unexpected-error-occurred"></a>Ett oväntat fel uppstod

[Samla in agentloggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft Support med ditt problem.

## <a name="authentication-agent-uninstallation-issues"></a>Problem med avinstallation av autentiseringsagent

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Varningsmeddelande vid avinstallation av Azure AD Connect

Om du har direktautentisering aktiverat på din klientorganisation och du försöker avinstallera Azure AD Connect visas följande varningsmeddelande: "Användare kan inte logga in på Azure AD om du inte har andra direktautentiseringsagenter installerade på andra servrar."

Se till att din konfiguration är [mycket tillgänglig](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) innan du avinstallerar Azure AD Connect för att undvika att bryta användarinloggning.

## <a name="issues-with-enabling-the-feature"></a>Problem med att aktivera funktionen

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Det gick inte att aktivera funktionen eftersom det inte fanns några autentiseringsagenter tillgängliga

Du måste ha minst en aktiv autentiseringsagent för att aktivera direktautentisering på din klientorganisation. Du kan installera en autentiseringsagent genom att antingen installera Azure AD Connect eller en fristående autentiseringsagent.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Det gick inte att aktivera funktionen på grund av blockerade portar

Kontrollera att servern där Azure AD Connect är installerat kan kommunicera med våra tjänstadresser och portar som anges [här](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Det gick inte att aktivera funktionen på grund av fel på token- eller kontoauktorisering

Se till att du använder ett globalt administratörskonto endast för molnet när du aktiverar funktionen. Det finns ett känt problem med MFA-aktiverade globala administratörskonton (Multi Factor Authentication). stänga av MFA tillfälligt (endast för att slutföra åtgärden) som en lösning.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Samla in direktautentiseringsagentloggar

Beroende på vilken typ av problem du kan ha måste du leta på olika platser för direktautentiseringsagentloggar.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect-loggar

Om det finns fel relaterade till installationen kontrollerar du Azure AD Connect-loggarna på **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Händelseloggar för autentiseringsagent

Om du vill ha fel som är relaterade till autentiseringsagenten öppnar du Loggboken-programmet på servern och kontrollerar under **Program- och tjänstloggar\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

För detaljerad analys aktiverar du loggen "Session" (högerklickar i Loggboken för att hitta det här alternativet). Kör inte autentiseringsagenten med den här loggen aktiverad under normala åtgärder. endast användas för felsökning. Logginnehållet visas bara när loggen har inaktiverats igen.

### <a name="detailed-trace-logs"></a>Detaljerade spårningsloggar

Om du vill felsöka inloggningsfel för användare letar du efter spårningsloggar på **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**. Dessa loggar innehåller orsaker till att en viss användarloggning misslyckades med funktionen Direktautentisering. Dessa fel mappas också till de inloggningsfelsorsaker som visas i tabellen för föregående inloggningsfel. Följande är ett exempel loggpost:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Du kan få beskrivande information om felet ("1328" i föregående exempel) genom att öppna kommandotolken och köra följande kommando (Obs: Ersätt '1328' med det faktiska felnumret som du ser i dina loggar):

`Net helpmsg 1328`

![Direktautentisering](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Loggar för domänkontrollant

Om granskningsloggning är aktiverat finns ytterligare information i domänkontrollanternas säkerhetsloggar. Ett enkelt sätt att fråga inloggningsbegäranden som skickas av direktautentiseringsagenter är följande:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Räknare för prestandaövervakare

Ett annat sätt att övervaka autentiseringsagenter är att spåra specifika prestandaövervakarräknare på varje server där autentiseringsagenten är installerad. Använd följande globala räknare (**# PTA-autentiseringar,** **#PTA misslyckade autentiseringar** och **#PTA lyckade autentiseringar)** och felräknare (**# PTA-autentiseringsfel):**

![Övervakarräknare för direktautentiseringsprestanda](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Direktautentisering ger hög tillgänglighet med hjälp av flera autentiseringsagenter och _inte_ belastningsutjämning. Beroende på din konfiguration får _inte_ alla dina autentiseringsagenter ungefär _lika många_ begäranden. Det är möjligt att en specifik autentiseringsagent inte tar emot någon trafik alls.
