---
title: 'Azure Active Directory Connect: Felsöka sömlös enkel inloggning | Microsoft Docs'
description: Det här avsnittet beskrivs hur du felsöker Azure Active Directory sömlös enkel inloggning
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a86ce8c061450fd66b31a81ec00e51f98a39646
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415654"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Felsöka Azure Active Directory sömlös enkel inloggning

Den här artikeln beskriver hur du hittar felsökningsinformation om vanliga problem om Azure Active Directory (Azure AD) sömlös enkel inloggning (sömlös SSO).

## <a name="known-issues"></a>Kända problem

- I några fall kan det ta upp till 30 minuter att aktivera sömlös enkel inloggning.
- Om du inaktiverar och återaktiverar sömlös enkel inloggning på din klient, får användarna inte enkel inloggning till sina cachelagrade Kerberos-biljetter, vanligtvis är giltig i 10 timmar har gått ut.
- Det finns inte stöd för Microsoft Edge-webbläsaren.
- Om sömlös enkel inloggning kan användaren inte har möjlighet att välja **vill förbli inloggad**. På grund av det här beteendet [SharePoint och OneDrive mappning scenarier](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) fungerar inte.
- Office 365 Win32-klienter (Outlook, Word, Excel och andra) med versioner 16.0.8730.xxxx och senare stöds med hjälp av en icke-interaktiv flow. Andra versioner stöds inte. i de versionerna kommer användarna anger sina användarnamn, men inte lösenord, logga in. För OneDrive, måste du aktivera den [OneDrive tyst config funktionen](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) för en tyst inloggningsupplevelse.
- Sömlös SSO fungerar inte i privat bläddrat läge på Firefox.
- Sömlös SSO fungerar inte i Internet Explorer när förbättrad skyddat läge är aktiverat.
- Sömlös SSO fungerar inte i mobila webbläsare på iOS och Android.
- Om en användare tillhör för många grupper i Active Directory, användarens Kerberos-biljett kommer antagligen vara för stort för att bearbeta och detta innebär att sömlös SSO misslyckas. Azure AD-HTTPS-begäranden kan ha rubriker med en maximal storlek på 50 KB; Kerberos-biljetter måste vara mindre än den gränsen för andra Azure AD-artefakter (normalt 2 – 5 KB), till exempel cookies. Vår rekommendation är att minska användarens gruppmedlemskap och försök igen.
- Om du synkroniserar 30 eller fler Active Directory-skogar, kan du inte aktivera sömlös SSO via Azure AD Connect. Du kan komma runt [manuellt aktivera](#manual-reset-of-the-feature) funktionen på din klient.
- Att lägga till URL: en för Azure AD-tjänsten (https://autologon.microsoftazuread-sso.com) i zonen Betrodda platser i stället för den lokala intranätzonen *blockerar användare från att logga in*.
- Sömlös SSO använder den **RC4_HMAC_MD5** krypteringstyp för Kerberos. Inaktivera användningen av den **RC4_HMAC_MD5** bryter krypteringstyp i inställningarna för Active Directory sömlös enkel inloggning. I Redigeraren för Grupprinciphantering-verktyget kontrollerar du att principvärdet för **RC4_HMAC_MD5** under **Datorkonfiguration -> Windows-inställningar -> säkerhetsinställningar -> lokala principer -> säkerhetsalternativ - > ”Nätverkssäkerhet: Konfigurera krypteringstyper som tillåts för Kerberos ”** är **aktiverat**. Dessutom sömlös enkel inloggning kan inte använda andra krypteringstyper, så se till att de är **inaktiverat**.

## <a name="check-status-of-feature"></a>Kontrollera status för funktionen

Se till att sömlös SSO-funktionen är fortfarande **aktiverad** på din klient. Du kan kontrollera statusen genom att gå till den **Azure AD Connect** fönstret i den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter: Azure AD Connect-fönstret](./media/tshoot-connect-sso/sso10.png)

Klicka här för att se alla AD-skogar som har aktiverats för sömlös enkel inloggning.

![Azure Active Directory Administrationscenter: Sömlös SSO-fönstret](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Logga in orsaker till Återställningsfel i Azure Active Directory Administrationscenter (måste ha en premiumlicens)

Om klienten har en associerad med den Azure AD Premium-licens, kan du också titta på den [inloggningsaktivitet rapporten](../reports-monitoring/concept-sign-ins.md) i den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/).

![Azure Active Directory Administrationscenter: Rapport över inloggningar](./media/tshoot-connect-sso/sso9.png)

Bläddra till **Azure Active Directory** > **inloggningar** i den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/), och välj sedan en viss användares inloggningsaktivitet. Leta efter den **LOGGA IN FELKODEN** fält. Mappa fältets värde till en felorsak och en lösning med hjälp av följande tabell:

|Felkod för inloggning|Logga in felorsak|Lösning
| --- | --- | ---
| 81001 | Användarens Kerberos-biljett är för stor. | Minska användarens gruppmedlemskap och försök igen.
| 81002 | Det går inte att verifiera användarens Kerberos-biljett. | Se den [checklista för felsökning](#troubleshooting-checklist).
| 81003 | Det går inte att verifiera användarens Kerberos-biljett. | Se den [checklista för felsökning](#troubleshooting-checklist).
| 81004 | Kerberos-autentiseringsförsök misslyckades. | Se den [checklista för felsökning](#troubleshooting-checklist).
| 81008 | Det går inte att verifiera användarens Kerberos-biljett. | Se den [checklista för felsökning](#troubleshooting-checklist).
| 81009 | Det går inte att verifiera användarens Kerberos-biljett. | Se den [checklista för felsökning](#troubleshooting-checklist).
| 81010 | Sömlös SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig. | Användaren måste logga in från en domänansluten enhet i företagsnätverket.
| 81011 | Det går inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. | Använda Azure AD Connect för att synkronisera användarinformation i Azure AD.
| 81012 | Användaren som försöker logga in på Azure AD skiljer sig från den användare som är inloggad på enheten. | Användaren måste logga in från en annan enhet.
| 81013 | Det går inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. |Använda Azure AD Connect för att synkronisera användarinformation i Azure AD. 

## <a name="troubleshooting-checklist"></a>Checklista för felsökning

Använd följande checklista för att felsöka problem med sömlös enkel inloggning:

- Se till att sömlös SSO-funktionen är aktiverad i Azure AD Connect. Om du inte kan aktivera funktionen (till exempel på grund av en blockerad port), kontrollera att du har alla de [krav](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) på plats.
- Om du har aktiverat båda [Azure AD Join](../active-directory-azureadjoin-overview.md) och sömlös enkel inloggning på din klient, kontrollera att problemet inte är med Azure AD Join. Enkel inloggning från Azure AD Join har företräde framför sömlös enkel inloggning om enheten är både registrerade med Azure AD och ansluten till domänen. Med enkel inloggning från Azure AD Join ser användaren ett panelen som säger ”är ansluten till Windows”.
- Se till att Azure AD-URL (https://autologon.microsoftazuread-sso.com) är en del av användarens Zoninställningar för intranätet.
- Se till att företagets enheten är ansluten till Active Directory-domänen. Enheten _inte_ måste vara [Azure AD har anslutits](../active-directory-azureadjoin-overview.md) för sömlös SSO ska fungera.
- Se till att användaren är inloggad på enheten via en Active Directory-domänkonto.
- Se till att användarkontot är från en Active Directory-skog där sömlös enkel inloggning har ställts in.
- Kontrollera att enheten är ansluten till företagsnätverket.
- Kontrollera att enhetens tiden har synkroniserats med tiden i både Active Directory och domänkontrollanterna och att de är inom fem minuter efter varandra.
- Se till att den `AZUREADSSOACC` datorkontot är tillgänglig och aktiverad i varje AD-skog som du vill att sömlös SSO aktiverat. Om datorkontot saknas eller har tagits bort, kan du använda [PowerShell-cmdletar](#manual-reset-of-the-feature) skapa dem igen.
- Lista över de befintliga Kerberos-biljetterna på enheten med hjälp av den `klist` från en kommandotolk. Se till att biljetter som utfärdats för den `AZUREADSSOACC` datorkontot finns. Användarens Kerberos-biljetter är vanligtvis giltiga i 10 timmar. Du kan ha olika inställningar i Active Directory.
- Om du inaktiveras och återaktiveras sömlös enkel inloggning på din klient kan får användarna inte enkel inloggning till sina cachelagrade Kerberos-biljetter har upphört att gälla.
- Rensa befintliga Kerberos-biljetter från enheten med hjälp av den `klist purge` kommandot och försök igen.
- Granska loggarna för konsolen i webbläsaren för att avgöra om det finns problem med JavaScript (under **Developer Tools**).
- Granska den [domain controller loggar](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Domain controller loggar

Om du aktiverar granskning av lyckade försök på domänkontrollanten och sedan varje gång en användare loggar in med sömlös enkel inloggning, registreras en security-post i händelseloggen. Du hittar dessa säkerhetshändelser med hjälp av följande fråga. (Leta upp händelsen **4769** som är associerade med datorkontot **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Manuell återställning av funktionen

Om inte hjälpte felsökning, kan du manuellt återställa funktionen på din klient. Följ dessa steg på den lokala servern där du kör Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Steg 1: Importera sömlös SSO-PowerShell-modulen

1. Först, hämta och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Bläddra till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera sömlös SSO-PowerShell-modulen med hjälp av det här kommandot: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Steg 2: Hämta en lista över Active Directory-skogar där sömlös enkel inloggning har aktiverats

1. Kör PowerShell som administratör. I PowerShell, anropa `New-AzureADSSOAuthenticationContext`. När du uppmanas, anger du autentiseringsuppgifterna för global administratör för din klient.
2. Anropa `Get-AzureADSSOStatus`. Det här kommandot ger dig med en lista över Active Directory-skogar (titt på listan ”domäner”) för som den här funktionen har aktiverats.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Steg 3: Inaktivera sömlös SSO för varje Active Directory-skog där du har ställt in funktionen

1. Anropa `$creds = Get-Credential`. När du uppmanas, anger du autentiseringsuppgifter som domänadministratör för den avsedda Active Directory-skogen.

    >[!NOTE]
    >Vi använder den domänadministratör, det tillhandahållna användarnamnet i User Principal namn (UPN) (johndoe@contoso.com)-format eller domän kvalificerade sam-konto (contoso\johndoe eller contoso.com\johndoe) namnformatet, att hitta rätt AD-skogen. Om du använder domän kvalificerade sam-kontonamnet som vi använder domändelen i användarnamnet för att [hitta domänkontrollant av domänadministratören med hjälp av DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Om du använder UPN i stället vi [översätta det till en domän kvalificerade sam-kontonamnet](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) innan du hitta rätt domänkontrollanten.

2. Anropa `Disable-AzureADSSOForest -OnPremCredentials $creds`. Detta kommando tar bort den `AZUREADSSOACC` datorkontot från den lokala domänkontrollanten för den här specifika Active Directory-skogen.
3. Upprepa föregående steg för varje Active Directory-skog där du har konfigurerat funktionen.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Steg 4: Aktivera sömlös enkel inloggning för varje Active Directory-skog

1. Anropa `Enable-AzureADSSOForest`. När du uppmanas, anger du autentiseringsuppgifter som domänadministratör för den avsedda Active Directory-skogen.

   >[!NOTE]
   >Vi använder den domänadministratör, det tillhandahållna användarnamnet i User Principal namn (UPN) (johndoe@contoso.com)-format eller domän kvalificerade sam-konto (contoso\johndoe eller contoso.com\johndoe) namnformatet, att hitta rätt AD-skogen. Om du använder domän kvalificerade sam-kontonamnet som vi använder domändelen i användarnamnet för att [hitta domänkontrollant av domänadministratören med hjälp av DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Om du använder UPN i stället vi [översätta det till en domän kvalificerade sam-kontonamnet](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) innan du hitta rätt domänkontrollanten.

2. Upprepa det föregående steget för varje Active Directory-skog där du vill konfigurera funktionen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Steg 5. Aktivera funktionen på din klient

Om du vill aktivera funktionen på din klient anropa `Enable-AzureADSSO -Enable $true`.
