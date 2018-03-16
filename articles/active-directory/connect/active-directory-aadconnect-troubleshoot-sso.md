---
title: "Azure Active Directory Connect: Felsöka sömlös enkel inloggning | Microsoft Docs"
description: "Det här avsnittet beskrivs hur du felsöker Azure Active Directory sömlös enkel inloggning"
services: active-directory
keywords: "Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: b383a081141d2fde90cfc574ec4b9ffb16940158
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Felsöka Azure Active Directory sömlös enkel inloggning

Den här artikeln får du hittar felsökningsinformation om vanliga problem om Azure Active Directory (AD Azure) sömlös enkel inloggning (sömlös SSO).

## <a name="known-issues"></a>Kända problem

- I några fall kan det ta upp till 30 minuter att aktivera sömlös SSO.
- Om du inaktiverar och återaktivera sömlös SSO på din klient kan får användarna inte enkel inloggning till deras cachelagrade Kerberos-biljetter, oftast giltigt för 10 timmar har upphört att gälla.
- Det finns inte stöd för Edge-webbläsare.
- Om det lyckas sömlös SSO, användaren inte har möjlighet att välja **Håll mig inloggad**. På grund av det här beteendet fungerar inte SharePoint och OneDrive mappning scenarier.
- Office-klienterna under version 16.0.8730.xxxx stöder inte icke-interaktiv inloggning med sömlös SSO. Användare måste ange sina användarnamn, men inte lösenord, logga in på dessa klienter.
- Sömlös SSO fungerar inte i privata webbläsarens läge i Firefox.
- Sömlös SSO fungerar inte i Internet Explorer när förbättrad skyddat läge är aktiverat.
- Sömlös SSO fungerar inte på mobila webbläsare på iOS och Android.
- Om en användare tillhör för många grupper i Active Directory, användarens Kerberos-biljetten sannolikt för stort för att bearbeta och detta gör att sömlös SSO misslyckas. Azure AD-HTTPS-begäranden kan ha huvuden med en maximal storlek på 16 KB; Kerberos-biljetter måste vara mycket mindre än detta antal att hantera andra Azure AD-artefakter, till exempel cookies. Vår rekommendation är att minska användarens gruppmedlemskap och försök igen.
- Om du synkroniserar 30 eller flera Active Directory-skogar, kan du inte aktivera sömlös SSO via Azure AD Connect. Som en tillfällig lösning kan du [manuellt aktivera](#manual-reset-of-azure-ad-seamless-sso) funktionen på din klient.
- Lägga till URL: en för Azure AD-tjänsten (https://autologon.microsoftazuread-sso.com) i zonen Tillförlitliga platser i stället för den lokala intranätzonen *hindrar användare från att logga in*.
- Inaktivera användningen av den **RC4_HMAC_MD5** krypteringstyp för Kerberos i inställningarna för Active Directory bryts sömlös SSO. I Redigeraren för Grupprinciphantering-verktyget kontrollerar du att principvärdet **RC4_HMAC_MD5** under **Datorkonfiguration -> Windows-inställningar -> säkerhetsinställningar -> lokala principer -> säkerhetsalternativ - > ”Nätverkssäkerhet: Konfigurera krypteringstyper som tillåts för Kerberos”** är ”aktiverad”.

## <a name="check-status-of-feature"></a>Kontrollera statusen för funktionen

Kontrollera att funktionen sömlös SSO är fortfarande **aktiverad** på din klient. Du kan kontrollera statusen genom att gå till den **Azure AD Connect** -fönstret på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter: Azure AD Connect-fönstret](./media/active-directory-aadconnect-sso/sso10.png)

Klicka här för att se alla AD-skogar som har aktiverats för sömlös SSO.

![Azure Active Directory Administrationscenter: sömlös SSO-fönstret](./media/active-directory-aadconnect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Inloggningsfel orsaker i Azure Active Directory Administrationscenter (måste ha en Premium-licens)

Om din klient har en Azure AD Premium-licens som är kopplade till den, kan du också titta på den [inloggningsaktivitet rapporten](../active-directory-reporting-activity-sign-ins.md) i den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter: inloggningar rapport](./media/active-directory-aadconnect-sso/sso9.png)

Bläddra till **Azure Active Directory** > **inloggningar** i den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/), och välj sedan inloggningsaktivitet för en viss användare. Leta efter den **LOGGA IN FELKODEN** fältet. Mappa värdet för fältet till en orsaken till felet och lösningar med hjälp av följande tabell:

|Felkod för inloggning|Logga in felorsak|Lösning
| --- | --- | ---
| 81001 | Användarens Kerberos-biljett är för stor. | Minska användarens gruppmedlemskap och försök igen.
| 81002 | Det gick inte att verifiera användarens Kerberos-biljett. | Finns det [felsökning checklista](#troubleshooting-checklist).
| 81003 | Det gick inte att verifiera användarens Kerberos-biljett. | Finns det [felsökning checklista](#troubleshooting-checklist).
| 81004 | Kerberos-autentiseringsförsök misslyckades. | Finns det [felsökning checklista](#troubleshooting-checklist).
| 81008 | Det gick inte att verifiera användarens Kerberos-biljett. | Finns det [felsökning checklista](#troubleshooting-checklist).
| 81009 | Det gick inte att verifiera användarens Kerberos-biljett. | Finns det [felsökning checklista](#troubleshooting-checklist).
| 81010 | Sömlös SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig. | Användaren måste logga in från en domänansluten enhet i företagsnätverket.
| 81011 | Det går inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. | Använda Azure AD Connect för att synkronisera användarinformation i Azure AD.
| 81012 | Användaren försöker logga in på Azure AD skiljer sig från den användare som har loggat in till enheten. | Användaren måste logga in från en annan enhet.
| 81013 | Det går inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. |Använda Azure AD Connect för att synkronisera användarinformation i Azure AD. 

## <a name="troubleshooting-checklist"></a>Checklista för felsökning

Använd följande checklista för att felsöka problem med sömlös SSO:

- Se till att sömlös SSO-funktionen är aktiverad i Azure AD Connect. Om du inte kan aktivera funktionen (t.ex, på grund av en blockerad-port), se till att du har alla de [krav](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites) på plats.
- Om du har aktiverat båda [Azure AD Join](../active-directory-azureadjoin-overview.md) och sömlös SSO på din klient, kontrollera att problemet inte är med Azure AD Join. Enkel inloggning från Azure AD Join företräde framför sömlös SSO om enheten är registrerad med Azure AD såväl som ingår i domänen. Med enkel inloggning från Azure AD Join ser användaren en inloggning panel med texten ”ansluten till Windows”.
- Kontrollera att Azure AD-URL (https://autologon.microsoftazuread-sso.com) är en del av användarens intranätsinställningar för zonen.
- Se till att företagets enheter är anslutna till Active Directory-domänen.
- Se till att användaren är inloggad på enheten via ett domänkonto för Active Directory.
- Kontrollera att användarkontot har från en Active Directory-skog där sömlös SSO har ställts in.
- Kontrollera att enheten är ansluten till företagsnätverket.
- Kontrollera att enhetens tiden har synkroniserats med tiden i både Active Directory och domänkontrollanter och att de är inom fem minuter efter varandra.
- Visa en lista med befintliga Kerberos-biljetter på enheten med hjälp av den `klist` kommandot från en kommandotolk. Se till att biljetter som utfärdats för den `AZUREADSSOACCT` datorkonto finns. Användarnas Kerberos-biljetter gäller vanligtvis under 10 timmar. Du kan ha olika inställningar i Active Directory.
- Om du inaktiveras och återaktiveras sömlös SSO på din klient kan får användarna inte enkel inloggning till deras cachelagrade Kerberos-biljetter har upphört att gälla.
- Rensa befintliga Kerberos-biljetter från enheten med hjälp av den `klist purge` kommandot och försök igen.
- Granska loggarna för konsolen i webbläsaren för att fastställa om det finns JavaScript-relaterade problem (under **Developer Tools**).
- Granska de [domain controller loggar](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Domain controller loggar

Om du aktiverar granskning av lyckade på domänkontrollanten och varje gång en användare loggar in via sömlös SSO registreras en post för säkerhet i händelseloggen. Du hittar dessa säkerhetshändelser med hjälp av följande fråga. (Sök efter händelse **4769** som är associerade med datorkontot **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Manuell återställning av funktionen

Om felsökningen inte hjälper kan återställa du manuellt funktionen på din klient. Följ dessa steg på den lokala servern där du kör Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Steg 1: Importera sömlös SSO-PowerShell-modulen

1. Hämta och installera den [Microsoft Online Services-inloggningsassistent](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Hämta och installera den [64-bitars Azure Active Directory-modulen för Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Bläddra till den `%programfiles%\Microsoft Azure Active Directory Connect` mapp.
4. Importera sömlös SSO-PowerShell-modulen med hjälp av det här kommandot: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Steg 2: Hämta en lista över Active Directory-skogar där sömlös SSO har aktiverats

1. Kör PowerShell som administratör. I PowerShell anropa `New-AzureADSSOAuthenticationContext`. När du uppmanas, anger du autentiseringsuppgifter för global administratör för din klient.
2. Anropa `Get-AzureADSSOStatus`. Det här kommandot ger listan över Active Directory-skogar (titt på listan ”domäner”) på som den här funktionen har aktiverats.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Steg 3: Inaktivera sömlös SSO för varje Active Directory-skog där du har ställt in funktionen

1. Anropa `$creds = Get-Credential`. När du uppmanas du ange autentiseringsuppgifter som domänadministratör för avsedda Active Directory-skogen.
2. Anropa `Disable-AzureADSSOForest -OnPremCredentials $creds`. Detta kommando tar bort den `AZUREADSSOACCT` datorkontot från den lokala domänkontrollanten för den här specifika Active Directory-skogen.
3. Upprepa föregående steg för varje Active Directory-skog där du har ställt in funktionen.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Steg 4: Aktivera sömlös SSO för varje Active Directory-skog

1. Anropa `Enable-AzureADSSOForest`. När du uppmanas du ange autentiseringsuppgifter som domänadministratör för avsedda Active Directory-skogen.
2. Upprepa det föregående steget för varje Active Directory-skog där du vill ställa in funktionen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Steg 5. Aktivera funktionen på din klient

Om du vill aktivera funktionen på din klient anropa `Enable-AzureADSSO` och ange **SANT** på den `Enable:` prompt.
