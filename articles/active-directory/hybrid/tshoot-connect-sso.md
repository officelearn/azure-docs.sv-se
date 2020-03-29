---
title: 'Azure Active Directory Connect: Felsöka sömlös enkel inloggning | Microsoft-dokument'
description: I det här avsnittet beskrivs felsÃ¶kning av Azure Active Directory Seamless Single Sign-On
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759748124893a8f906a4bc336f835546202b0b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049496"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Felsöka Sömlös enkel inloggning i Azure Active Directory

Den här artikeln hjälper dig att hitta felsökningsinformation om vanliga problem med Azure Active Directory (Azure AD) Seamless Single Sign-On (Seamless SSO).

## <a name="known-issues"></a>Kända problem

- I några fall kan det ta upp till 30 minuter att aktivera Sömlös SSO.
- Om du inaktiverar och återaktiverar Sömlös SSO på din klientorganisation får användarna inte den enda inloggningsupplevelsen förrän deras cachelagrade Kerberos-biljetter, som vanligtvis är giltiga i 10 timmar, har gått ut.
- Om Seamless SSO lyckas har användaren inte möjlighet att välja **Håll mig inloggad**. På grund av det här beteendet fungerar inte [SharePoint- och OneDrive-mappningsscenarier.](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec)
- Office 365 Win32-klienter (Outlook, Word, Excel och andra) med versionerna 16.0.8730.xxxx och högre stöds med hjälp av ett icke-interaktivt flöde. Andra versioner stöds inte. på dessa versioner kommer användarna att ange sina användarnamn, men inte lösenord, för att logga in. För OneDrive måste du aktivera [OneDrives tysta konfigurationsfunktion](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) för en tyst inloggningsupplevelse.
- Sömlös SSO fungerar inte i privat surfningsläge på Firefox.
- Sömlös SSO fungerar inte i Internet Explorer när utökat skyddat läge är aktiverat.
- Sömlös SSO fungerar inte på mobila webbläsare på iOS och Android.
- Om en användare ingår i för många grupper i Active Directory är användarens Kerberos-biljett troligen för stor för att bearbetas, vilket gör att Sömlös SSO misslyckas. Azure AD HTTPS-begäranden kan ha rubriker med en maximal storlek på 50 KB. Kerberos-biljetter måste vara mindre än den gränsen för att rymma andra Azure AD-artefakter (vanligtvis 2 – 5 KB) till exempel cookies. Vår rekommendation är att minska användarens gruppmedlemskap och försöka igen.
- Om du synkroniserar 30 eller fler Active Directory-skogar kan du inte aktivera Sömlös SSO via Azure AD Connect. Som en lösning kan du [manuellt aktivera](#manual-reset-of-the-feature) funktionen på din klient.
- Om du lägger`https://autologon.microsoftazuread-sso.com`till Azure AD-tjänst-URLn ( ) i zonen Betrodda platser i stället för zonen Lokalt intranät *blockeras användare från att logga in*.
- Sömlös SSO stöder AES256_HMAC_SHA1, AES128_HMAC_SHA1 och RC4_HMAC_MD5 krypteringstyper för Kerberos. Vi rekommenderar att krypteringstypen för AzureADSSOAcc$-kontot är inställd på AES256_HMAC_SHA1, eller någon av AES-typerna jämfört med RC4 för ökad säkerhet. Krypteringstypen lagras på attributet msDS-SupportedEncryptionTypes för kontot i Active Directory.  Om krypteringstypen För AzureADSSOAcc$-kontot är inställd på RC4_HMAC_MD5 och du vill ändra den till en av AES-krypteringstyperna, se till att du först rullar över Kerberos dekrypteringsnyckeln för AzureADSSOAcc$-kontot som förklaras i [FAQ-dokumentet](how-to-connect-sso-faq.md) under relevant fråga, annars kommer Sömlös SSO inte att hända.

## <a name="check-status-of-feature"></a>Kontrollera funktionens status

Kontrollera att seamless SSO-funktionen fortfarande är **aktiverad** på din klientorganisation. Du kan kontrollera statusen genom att gå till azure **AD Connect-fönstret** i [Administrationscentret](https://aad.portal.azure.com/)för Azure Active Directory .

![Administrationscenter för Azure Active Directory: Azure AD Connect-fönstret](./media/tshoot-connect-sso/sso10.png)

Klicka vidare för att se alla AD-skogar som har aktiverats för Sömlös SSO.

![Administrationscenter för Azure Active Directory: Sömlös SSO-fönster](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Orsaker till inloggningsfel i Administrationscentret för Azure Active Directory (behöver en Premium-licens)

Om din klient har en Azure AD Premium-licens kopplad till sig kan du också titta på [inloggningsaktivitetsrapporten i](../reports-monitoring/concept-sign-ins.md) [Administrationscentret](https://aad.portal.azure.com/)för Azure Active Directory .

![Administrationscenter för Azure Active Directory: Rapporten Inloggningar](./media/tshoot-connect-sso/sso9.png)

Bläddra till Azure Active > **Directory-inloggningar** i [Administrationscentret](https://aad.portal.azure.com/)för Azure Active Directory och välj sedan en viss användares inloggningsaktivitet. **Azure Active Directory** Leta efter fältet **INLOGGNINGSFELKOD.** Mappa värdet för det fältet till en felorsak och lösning med hjälp av följande tabell:

|Felkod för inloggning|Orsaken till inloggningsfel|Lösning
| --- | --- | ---
| 81001 | Användarens Kerberos-biljett är för stor. | Minska användarens gruppmedlemskap och försök igen.
| 81002 | Det gick inte att validera användarens Kerberos-biljett. | Se [felsökningschecklistan](#troubleshooting-checklist).
| 81003 | Det gick inte att validera användarens Kerberos-biljett. | Se [felsökningschecklistan](#troubleshooting-checklist).
| 81004 | Kerberos-autentiseringsförsök misslyckades. | Se [felsökningschecklistan](#troubleshooting-checklist).
| 81008 | Det gick inte att validera användarens Kerberos-biljett. | Se [felsökningschecklistan](#troubleshooting-checklist).
| 81009 | Det gick inte att validera användarens Kerberos-biljett. | Se [felsökningschecklistan](#troubleshooting-checklist).
| 81010 | Sömlös SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig. | Användaren måste logga in från en domänansluten enhet i företagets nätverk.
| 81011 | Det gick inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. | Använd Azure AD Connect för att synkronisera användarens information till Azure AD.
| 81012 | Användaren som försöker logga in på Azure AD skiljer sig från användaren som är inloggad på enheten. | Användaren måste logga in från en annan enhet.
| 81013 | Det gick inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. |Använd Azure AD Connect för att synkronisera användarens information till Azure AD. 

## <a name="troubleshooting-checklist"></a>Checklista för felsökning

Använd följande checklista för att felsöka sömlösa SSO-problem:

- Kontrollera att seamless SSO-funktionen är aktiverad i Azure AD Connect. Om du inte kan aktivera funktionen (till exempel på grund av en blockerad port) kontrollerar du att du har alla [förutsättningar](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) på plats.
- Om du har aktiverat både [Azure AD Join](../active-directory-azureadjoin-overview.md) och Seamless SSO på din klientorganisation, se till att problemet inte är med Azure AD Join. SSO från Azure AD Join har företräde framför Sömlös SSO om enheten är både registrerad med Azure AD och domänansluten. Med SSO från Azure AD Join ser användaren en inloggningspanel med namnet "Ansluten till Windows".
- Kontrollera att Azure AD-URL:en (`https://autologon.microsoftazuread-sso.com`) är en del av användarens intranätzonsinställningar.
- Kontrollera att företagsenheten är ansluten till Active Directory-domänen. Enheten behöver _inte_ vara [Azure AD-ansluten](../active-directory-azureadjoin-overview.md) för sömlös SSO för att fungera.
- Kontrollera att användaren är inloggad på enheten via ett Active Directory-domänkonto.
- Kontrollera att användarens konto kommer från en Active Directory-skog där Sömlös SSO har konfigurerats.
- Kontrollera att enheten är ansluten till företagsnätverket.
- Kontrollera att enhetens tid synkroniseras med tiden i både Active Directory och domänkontrollanterna och att de är inom fem minuter från varandra.
- Kontrollera att `AZUREADSSOACC` datorkontot finns och är aktiverat i varje AD-skog som du vill ha Sömlös SSO aktiverat. Om datorkontot har tagits bort eller saknas kan du använda [PowerShell-cmdletar](#manual-reset-of-the-feature) för att återskapa dem.
- Lista befintliga Kerberos-biljetter på enheten `klist` med kommandot från en kommandotolk. Se till att de `AZUREADSSOACC` biljetter som utfärdats för datorkontot finns. Användarnas Kerberos-biljetter är vanligtvis giltiga i 10 timmar. Du kan ha olika inställningar i Active Directory.
- Om du har inaktiverat och återaktiverat Seamless SSO på din klientorganisation får användarna inte den enda inloggningsupplevelsen förrän deras cachelagrade Kerberos-biljetter har gått ut.
- Rensa befintliga Kerberos-biljetter från `klist purge` enheten med kommandot och försök igen.
- Ta reda på om det finns JavaScript-relaterade problem genom att läsa webbläsarens konsolloggar (under **Utvecklarverktyg).**
- Granska [domänkontrollantloggarna](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Loggar för domänkontrollant

Om du aktiverar granskning av lyckade försök på domänkontrollanten registreras en säkerhetspost i händelseloggen varje gång en användare loggar in via Sömlös SSO. Du hittar dessa säkerhetshändelser med hjälp av följande fråga. (Leta efter händelse **4769** som är associerad med datorkontot **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Manuell återställning av funktionen

Om felsökningen inte hjälpte kan du återställa funktionen manuellt på din klient. Följ dessa steg på den lokala servern där du kör Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Steg 1: Importera seamless SSO PowerShell-modulen

1. Hämta och installera [först Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Bläddra till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera seamless SSO PowerShell-modulen `Import-Module .\AzureADSSO.psd1`med det här kommandot: .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Steg 2: Få en lista över Active Directory-skogar där Sömlös SSO har aktiverats

1. Kör PowerShell som administratör. Ring `New-AzureADSSOAuthenticationContext`i PowerShell. Ange klientens globala administratörsautentiseringsuppgifter när du uppmanas att göra det.
2. Ring. `Get-AzureADSSOStatus` Det här kommandot ger dig en lista över Active Directory-skogar (titta på listan "Domäner" som den här funktionen har aktiverats på.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Steg 3: Inaktivera sömlös SSO för varje Active Directory-skog där du har konfigurerat funktionen

1. Ring. `$creds = Get-Credential` Ange domänadministratörsuppgifter för den avsedda Active Directory-skogen när du uppmanas att göra det.

   > [!NOTE]
   >Användarnamnet för domänadministratörsuppgifter måste anges i namnformatet för SIM-konton (contoso\johndoe eller contoso.com\johndoe). Vi använder domändelen av användarnamnet för att hitta domänkontrollanten för domänadministratören med HJÄLP AV DNS.

   >[!NOTE]
   >Det domänadministratörskonto som används får inte vara medlem i gruppen Skyddade användare. Om så är fallet misslyckas åtgärden.

2. Ring. `Disable-AzureADSSOForest -OnPremCredentials $creds` Det här kommandot `AZUREADSSOACC` tar bort datorkontot från den lokala domänkontrollanten för den här specifika Active Directory-skogen.
3. Upprepa föregående steg för varje Active Directory-skog där du har konfigurerat funktionen.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Steg 4: Aktivera sömlös SSO för varje Active Directory-skog

1. Ring. `Enable-AzureADSSOForest` Ange domänadministratörsuppgifter för den avsedda Active Directory-skogen när du uppmanas att göra det.

   > [!NOTE]
   >Användarnamnet för domänadministratörsuppgifter måste anges i namnformatet för SIM-konton (contoso\johndoe eller contoso.com\johndoe). Vi använder domändelen av användarnamnet för att hitta domänkontrollanten för domänadministratören med HJÄLP AV DNS.

   >[!NOTE]
   >Det domänadministratörskonto som används får inte vara medlem i gruppen Skyddade användare. Om så är fallet misslyckas åtgärden.

2. Upprepa föregående steg för varje Active Directory-skog där du vill konfigurera funktionen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Steg 5. Aktivera funktionen på din klientorganisation

Om du vill aktivera funktionen `Enable-AzureADSSO -Enable $true`på din klient ringer du .
