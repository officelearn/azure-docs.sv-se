---
title: Förmedlad autentisering i Android | Azure
titlesuffix: Microsoft identity platform
description: En översikt över förmedlad autentisering & auktorisering för Android i Microsofts identitetsplattform
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697905"
---
# <a name="brokered-authentication-in-android"></a>Förmedlad autentisering i Android

Du måste använda en av Microsofts autentiseringsmäklare för att delta i enhetsomfattande enkel inloggning (SSO) och för att uppfylla organisationens principer för villkorlig åtkomst. Att integrera med en mäklare ger följande fördelar:

- Enkel inloggning för enhet
- Villkorlig åtkomst för:
  - Intune App Protection
  - Enhetsregistrering (Arbetsplatskoppling)
  - Hantering av mobila enheter
- Kontohantering för hela enheten
  -  via Android AccountManager & kontoinställningar
  - "Arbetskonto" - anpassad kontotyp

På Android är Microsoft Authentication Broker en komponent som ingår i [Microsoft Authenticator App](https://play.google.com/store/apps/details?id=com.azure.authenticator) och [Intune Company Portal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Endast ett program som är värd för mäklaren kommer att vara aktiv som mäklare åt gången. Vilket program som är aktivt som mäklare bestäms av installationsordern på enheten. Den första som installeras, eller den sista som finns på enheten, blir den aktiva mäklaren.

Följande diagram illustrerar förhållandet mellan din app, Microsoft Authentication Library (MSAL) och Microsofts autentiseringsmäklare.

![Distributionsdiagram för mäklare](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Installera appar som är värdar för en mäklare

Appar med mäklarhosting kan installeras av enhetsägaren från deras App Store (vanligtvis Google Play Store) när som helst. Vissa API:er (resurser) skyddas dock av principer för villkorlig åtkomst som kräver att enheter är:

- Registrerad (arbetsplats förenade) och/eller
- Registrerad i Enhetshantering eller
- Inskriven i Intune-appskydd

Om en enhet inte redan har en mäklarapp installerad instruerar MSAL användaren att installera en så fort appen försöker hämta en token interaktivt. Appen måste sedan leda användaren genom stegen för att göra enheten kompatibel med den princip som krävs.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effekter av att installera och avinstallera en mäklare

### <a name="when-a-broker-is-installed"></a>När en mäklare installeras

När en mäklare installeras på en enhet hanteras `acquireToken()`alla efterföljande interaktiva tokenbegäranden (anrop) av mäklaren i stället för lokalt av MSAL. Alla SSO-tillstånd som tidigare varit tillgängliga för MSAL är inte tillgängligt för mäklaren. Därför måste användaren autentisera igen eller välja ett konto i den befintliga listan över konton som enheten känner till.

Om du installerar en mäklare krävs inte att användaren loggar in igen. Endast när användaren behöver `MsalUiRequiredException` lösa en kommer nästa begäran att gå till mäklaren. `MsalUiRequiredException`av flera skäl och måste lösas interaktivt. Dessa är några vanliga orsaker:

- Användaren ändrade lösenordet som är kopplat till deras konto.
- Användarens konto uppfyller inte längre en princip för villkorlig åtkomst.
- Användaren återkallade sitt samtycke för att appen skulle associeras med sitt konto.

### <a name="when-a-broker-is-uninstalled"></a>När en mäklare avinstalleras

Om det bara finns en mäklarvärd app installerad, och den tas bort, måste användaren logga in igen. Om du avinstallerar den aktiva mäklaren tas kontot och associerade token bort från enheten.

Om Intune Company Portal är installerat och fungerar som aktiv mäklare, och Microsoft Authenticator också är installerat, måste användaren logga in igen om Intune Company Portal (aktiv mäklare) avinstalleras. När de loggar in igen blir Microsoft Authenticator-appen den aktiva mäklaren.

## <a name="integrating-with-a-broker"></a>Integrera med en mäklare

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generera en omdirigera URI för en mäklare

Du måste registrera en omdirigera URI som är kompatibel med mäklaren. Omdirigerings-URI:n för mäklaren måste innehålla appens paketnamn, samt den base64-kodade representationen av appens signatur.

Formatet för omdirigera URI är:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Generera din Base64 url kodade signatur med hjälp av appens signeringsnycklar. Här är några exempelkommandon som använder felsökningssigneringsnycklarna:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Se [Logga in appen](https://developer.android.com/studio/publish/app-signing) för information om hur du signerar appen.

> [!IMPORTANT]
> Använd produktionssigneringsnyckeln för produktionsversionen av appen.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurera MSAL för att använda en mäklare

Om du vill använda en mäklare i appen måste du intyga att du har konfigurerat omdirigering av mäklaren. Inkludera till exempel både din mäklare aktiverad omdirigera URI - och ange att du registrerat det - genom att inkludera följande i din MSAL konfigurationsfil:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Det nya användargränssnittet för registrering av Azure-portalappar hjälper dig att generera uri-et för mäklaredirigering. Om du har registrerat din app med den äldre upplevelsen, eller gjorde det med hjälp av Microsoft-appregistreringsportalen, kan du behöva generera omdirigerings-URI:n och uppdatera listan över omdirigera URI:er i portalen manuellt.

### <a name="broker-related-exceptions"></a>Mäklarrelaterade undantag

MSAL kommunicerar med mäklaren på två sätt:

- Tjänst för mäklaresbunden tjänst
- Android-kontoManager

MSAL använder först den mäklare bundna tjänsten eftersom ringa denna tjänst inte kräver några Android-behörigheter. Om bindningen till den bundna tjänsten misslyckas kommer MSAL att använda Api:et för Android AccountManager. MSAL gör detta bara om din `"READ_CONTACTS"` app redan har beviljats behörigheten.

Om du `MsalClientException` får en `"BROKER_BIND_FAILURE"`med felkod finns det två alternativ:

- Be användaren att inaktivera energioptimering för Microsoft Authenticator-appen och Intune Company Portal.
- Be användaren att `"READ_CONTACTS"` bevilja behörigheten
