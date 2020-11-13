---
title: Microsoft Enterprise SSO-plugin-program för Apple-enheter
titleSuffix: Microsoft identity platform | Azure
description: Lär dig mer om Microsofts Azure Active Directory SSO-plugin-program för iOS-och macOS-enheter.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: b7ec6ab8b52d9d43d898f481a2f36310e5c0897d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561088"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO-plugin-program för Apple-enheter (för hands version)

>[!IMPORTANT]
> Den här funktionen [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Microsoft Enterprise SSO-plugin-programmet för Apple-enheter* tillhandahåller enkel inloggning (SSO) för Azure Active Directory (Azure AD)-konton i alla program som stöder Apples funktion [för enkel inloggning i](https://developer.apple.com/documentation/authenticationservices) Apple. Microsoft har arbetat nära med Apple för att utveckla det här plugin-programmet för att öka programmets användbarhet samtidigt som det ger bästa möjliga skydd som Apple och Microsoft kan tillhandahålla.

I den här offentliga för hands versionen är Enterprise SSO-plugin-programmet endast tillgängligt för iOS-enheter och distribueras i vissa Microsoft-program.

## <a name="features"></a>Funktioner

Microsoft Enterprise SSO-plugin-programmet för Apple-enheter ger följande fördelar:

- Ger enkel inloggning för Azure AD-konton i alla program som har stöd för Apples Enterprise Single Sign-On-funktion.
- Levereras automatiskt i Microsoft Authenticator och kan aktive ras av en lösning för hantering av mobila enheter (MDM).

## <a name="requirements"></a>Krav

Använda Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:

- iOS 13,0 eller senare måste vara installerat på enheten.
- Ett Microsoft-program som tillhandahåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste installeras på enheten. För den allmänt tillgängliga för hands versionen inkluderar programmen [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md).
- Enheten måste vara MDM-registrerad (till exempel med Microsoft Intune).
- Konfigurationen måste flyttas till enheten för att aktivera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter på enheten. Den här säkerhets begränsningen krävs av Apple.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Aktivera plugin-programmet för SSO med hantering av mobila enheter (MDM)

Om du vill aktivera plugin-programmet Microsoft Enterprise SSO för Apple-enheter måste enheterna skickas en signal via en MDM-tjänst. Eftersom Microsoft innehåller plugin-programmet för enkel inloggning i [Microsoft Authenticator-appen](..//user-help/user-help-auth-app-overview.md)använder du MDM för att konfigurera appen så att den aktiverar Microsoft Enterprise SSO-plugin-programmet.

Använd följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:

- **Typ** : omdirigera
- **Tilläggs-ID** : `com.microsoft.azureauthenticator.ssoextension`
- **Team-ID** : (det här fältet behövs inte för iOS)
- **URL: er** :
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Ytterligare konfigurationsalternativ
Ytterligare konfigurations alternativ kan läggas till för att utöka SSO-funktioner till ytterligare appar.

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Aktivera SSO för appar som inte använder MSAL

SSO-plugin-programmet gör det möjligt för alla program att delta i enkel inloggning, även om det inte har utvecklats med hjälp av ett Microsoft-SDK som Microsoft Authentication Library (MSAL).

SSO-plugin-programmet installeras automatiskt av enheter som har laddat ned Microsoft Authenticator-appen och registrerat sin enhet med din organisation. I din organisation används Authenticator-appen idag för scenarier som Multi-Factor Authentication, lösen ords lös autentisering och villkorlig åtkomst. Den kan aktive ras för dina program med hjälp av en MDM-Provider, men Microsoft har gjort det enkelt att konfigurera i Microsoft Endpoint Manager för Intune. En lista över tillåtna program används för att konfigurera dessa program att använda SSO-plugin-programmet som installeras av Authenticator-appen.

Endast appar som använder inbyggd teknik för Apple-nätverk eller webviews stöds. Om ett program levererar sin egen implementering av nätverks skikt, stöds inte Microsoft Enterprise SSO-plugin-programmet.  

Använd följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för appar som inte använder MSAL:

- **Nyckel** : `AppAllowList`
- **Typ** : `String`
- **Värde** : kommaavgränsad lista över programpaket-ID: n för program som tillåts delta i SSO
- **Exempel** : `com.contoso.workapp, com.contoso.travelapp`

[Samskickade appar](./application-consent-experience.md) som tillåts av MDM-administratören för att delta i SSO kan tyst hämta en token för slutanvändaren. Därför är det viktigt att bara lägga till betrodda program i listan över tillåtna. 

Du behöver inte lägga till program som använder MSAL eller ASWebAuthenticationSession i den här listan. Dessa program är aktiverade som standard. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Tillåt att en SSO-session skapas från alla program

Som standard tillhandahåller Microsoft Enterprise SSO-plugin-programmet SSO endast för auktoriserade appar när SSO-plugin-programmet redan har en delad autentiseringsuppgift. Microsoft Enterprise SSO-plugin-programmet kan hämta en delad autentiseringsuppgift när den anropas av en annan ADAL eller MSAL-baserad app vid hämtning av token. De flesta av Microsoft-apparna använder Microsoft Authenticator eller SSO-plugin-programmet. Det innebär att som standard är enkel inloggning utanför interna app-flöden.  

Aktiverings `browser_sso_interaction_enabled` flaggan aktiverar icke-MSAL appar och Safari-webbläsare för att utföra den första start filen och hämta en delad autentiseringsuppgift. Om Microsoft Enterprise SSO-plugin-programmet inte har någon delad autentiseringsuppgift ännu, försöker det att hämta en när en inloggning begärs från en Azure AD-URL i Safari-webbläsare, ASWebAuthenticationSession, SafariViewController eller något annat tillåtet internt program.  

- **Nyckel** : `browser_sso_interaction_enabled`
- **Typ** : `Integer`
- **Värde** : 1 eller 0

Vi rekommenderar att du aktiverar den här flaggan för att få mer konsekvent upplevelse i alla appar. Det är inaktiverat som standard. 

#### <a name="disable-oauth2-application-prompts"></a>Inaktivera OAuth2-programprompter

Microsoft Enterprise SSO-plugin-programmet tillhandahåller SSO genom att bifoga delade autentiseringsuppgifter till nätverks begär Anden som kommer från tillåtna program. Vissa OAuth2-program kan tvinga slutanvändarens prompt i protokoll skiktet. Delade autentiseringsuppgifter ignoreras för dessa appar.  

Aktiverings `disable_explicit_app_prompt` flaggan begränsar möjligheten för både ursprungliga och webb program att tvinga fram en slutanvändares fråga om protokoll skiktet och kringgå SSO.

- **Nyckel** : `disable_explicit_app_prompt`
- **Typ** : `Integer`
- **Värde** : 1 eller 0

Vi rekommenderar att du aktiverar den här flaggan för att få mer konsekvent upplevelse i alla appar. Det är inaktiverat som standard. 

#### <a name="use-intune-for-simplified-configuration"></a>Använd Intune för förenklad konfiguration

Du kan använda Microsoft Intune som MDM-tjänst för att under lätta konfigurationen av Microsoft Enterprise SSO-plugin-programmet. Mer information finns i dokumentation om [Intune-konfiguration](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Använda SSO-plugin-programmet i ditt program

[Microsoft Authentication Library (MSAL) för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) version 1.1.0 och högre stöder Microsoft Enterprise SSO-plugin-programmet för Apple-enheter.

Om du skapar ett program för Frontline Worker-scenarier, se [delad enhets läge för iOS-enheter](msal-ios-shared-devices.md) om du vill ha ytterligare konfiguration av funktionen.

## <a name="how-the-sso-plug-in-works"></a>Så här fungerar SSO-plugin-programmet

Microsoft Enterprise SSO-plugin-programmet är beroende av [Apples Enterprise Single Sign-On Framework](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Identitets leverantörer som är inbyggda i ramverket kan fånga nätverks trafik för sina domäner och förbättra eller ändra hur dessa begär Anden hanteras. SSO-plugin-programmet kan till exempel Visa ytterligare gränssnitt för att samla in autentiseringsuppgifter för slutanvändare på ett säkert sätt, kräva MFA eller tyst tillhandahålla token till programmet.

Inbyggda program kan också implementera anpassade åtgärder och prata direkt med SSO-plugin-programmet.
Du kan lära dig mer om Single Sign-in-ramverket i den här [2019 WWDC-videon från Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>Program som använder MSAL

[Microsoft Authentication Library (MSAL) för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) version 1.1.0 och högre stöder Microsoft Enterprise SSO-plugin-programmet för Apple-enheter för arbets-och skol konton. 

Det behövs ingen särskild konfiguration om du har följt [alla rekommenderade steg](./quickstart-v2-ios.md) och använt standardvärdet för [omdirigerings-URI](./redirect-uris-ios.md). När du kör på en enhet som har SSO-plugin-programmet så anropar MSAL automatiskt för alla interaktiva och tysta token-begäranden, samt konto uppräkning och borttagning av konto. Eftersom MSAL implementerar inbyggt SSO-plugin-protokoll som förlitar sig på anpassade åtgärder, ger den här installationen den smidigaste inbyggda upplevelsen för slutanvändaren. 

Om SSO-plugin-programmet inte är aktiverat av MDM, men Microsoft Authenticator-appen finns på enheten, använder MSAL i stället Microsoft Authenticator-appen för alla interaktiva token-begäranden. SSO-plugin-programmet delar SSO med Microsoft Authenticator-appen.

### <a name="applications-that-dont-use-msal"></a>Program som inte använder MSAL

Program som inte använder MSAL kan fortfarande få SSO om en administratör lägger till dem i listan över tillåtna explicit. 

Det behövs inga kod ändringar i dessa appar så länge följande villkor är uppfyllda:

- Programmet använder Apple Frameworks för att köra nätverks begär Anden (till exempel [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- Programmet använder standard protokoll för att kommunicera med Azure AD (till exempel OAuth2, SAML, WS-Federation)
- Programmet samlar inte in användar namn och lösen ord i klartext i det inbyggda användar gränssnittet

I det här fallet anges SSO när programmet skapar en nätverks förfrågan och öppnar en webbläsare för att logga in användaren i. När en användare omdirigeras till en inloggnings-URL för Azure AD, verifierar SSO-plugin-programmet URL: en och kontrollerar om det finns en SSO-autentiseringsuppgift som är tillgänglig för den URL: en. Om det finns en så skickar SSO-plugin-programmet SSO-autentiseringsuppgiften till Azure AD, som auktoriserar programmet att slutföra nätverksbegäran utan att be användaren ange sina autentiseringsuppgifter. Dessutom, om enheten är känd för Azure AD, så skickar SSO-plugin-programmet även enhets certifikatet för att uppfylla den enhets villkorliga åtkomst kontrollen. 

För att stödja SSO för icke-MSAL appar implementerar SSO-plugin-programmet ett protokoll som liknar plugin-programmet för Windows-webbläsare, [vilket beskrivs i vad är en primär uppdateringstoken?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Jämfört med MSAL-baserade appar fungerar SSO-plugin-programmet mer transparent för icke-MSAL appar genom att integrera med den befintliga webbläsarens inloggnings upplevelse som appar tillhandahåller. Slutanvändaren ser sin välbekanta upplevelse, med fördelen att du inte behöver utföra ytterligare inloggningar i vart och ett av programmen. I stället för att visa den inbyggda konto väljaren, lägger till exempel SSO-plugin-programmet SSO-sessioner till den webbaserade konto väljaren. 

## <a name="next-steps"></a>Nästa steg

För mer information om delad enhets läge på iOS, se [delad enhets läge för iOS-enheter](msal-ios-shared-devices.md).
