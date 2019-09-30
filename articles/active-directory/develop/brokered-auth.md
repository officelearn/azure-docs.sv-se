---
title: Utjämnad autentisering i Android | Azure
description: En översikt över Brokered Authentication & auktorisering för Android i Microsoft Identity Platform
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb1ed81c03e7c5ba30b813897dac5796c550ed23
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679833"
---
# <a name="brokered-auth-in-android"></a>Utjämnad autentisering i Android

## <a name="introduction"></a>Introduktion

Du måste använda någon av Microsofts autentiserings mäklare för att delta i enkel inloggning (SSO) för hela enheten och för att uppfylla organisationens principer för villkorlig åtkomst. Att integrera med en Service Broker ger följande fördelar:

- Enkel inloggning för enhet
- Villkorlig åtkomst för:
  - Intune-appskydd
  - Enhets registrering (Workplace Join)
  - Hantering av mobilenheter
- Konto hantering för hela enheten
  -  via Android-AccountManager & konto inställningar
  - "Arbets konto" – anpassad kontotyp

På Android är Microsoft Authentication Broker en komponent som ingår i [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) och [Intune-företagsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Endast ett program som är värd för Service Broker aktive ras som koordinatorn i taget. Vilket program som är aktivt som en Broker bestäms av installations ordningen på enheten. Den första som ska installeras eller den som senast fanns på enheten blir aktiv Broker.

Följande diagram illustrerar förhållandet mellan appen, Microsoft Authentication Library (MSAL) och Microsofts autentiserings mäklare.

![Diagram över Broker-distribution](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Installera appar som är värdar för en Broker

Broker-värdbaserade appar kan installeras av enhetens ägare från App Store (vanligt vis Google Play Butik). Vissa API: er (resurser) skyddas dock av principer för villkorlig åtkomst som kräver att enheter:

- registrerad (arbets plats ansluten) och/eller
- registrerad i enhets hantering eller
- registrerad i Intune-appskydd

Om en enhet inte redan har en Service Broker-app installerad instruerar MSAL användaren att installera en så fort appen försöker hämta en token interaktivt. Appen måste sedan leda användaren genom stegen för att göra enheten kompatibel med den princip som krävs.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effekter av installation och avinstallation av en Broker

### <a name="when-a-broker-is-installed"></a>När en Service Broker installeras

När en Broker installeras på en enhet hanteras alla efterföljande interaktiva Tokenbegäran (anrop till `acquireToken()`) av Service Broker i stället för lokalt av MSAL. SSO-tillstånd som tidigare var tillgängligt för MSAL är inte tillgängligt för Broker. Detta innebär att användaren måste autentisera igen eller välja ett konto i den befintliga listan över konton som är kända för enheten.

Att installera en Broker kräver inte att användaren loggar in igen. Endast när användaren behöver lösa en `MsalUiRequiredException` går nästa förfrågan till Service Broker. `MsalUiRequiredException` genereras av flera orsaker och måste lösas interaktivt. Detta är några vanliga orsaker:

- Användaren ändrade lösen ordet som är kopplat till sitt konto.
- Användarens konto uppfyller inte längre en princip för villkorlig åtkomst.
- Användaren återkallade sitt medgivande för att appen ska associeras med sitt konto.

### <a name="when-a-broker-is-uninstalled"></a>När en Broker avinstalleras

Om det bara finns en Service Broker-värd app installerad och den tas bort måste användaren logga in igen. Om du avinstallerar den aktiva Broker tas kontot och tillhör ande token bort från enheten.

Om Intune-företagsportal är installerat och fungerar som den aktiva utjämningen, och Microsoft Authenticator också är installerat, kommer användaren att behöva logga in igen om Intune-företagsportal (aktiv Broker) har avinstallerats. När de loggar in igen blir Microsoft Authenticator-appen den aktiva koordinatorn.

## <a name="integrating-with-a-broker"></a>Integrera med en Service Broker

### <a name="generating-a-redirect-uri-for-a-broker"></a>Skapa en omdirigerings-URI för en Broker

Du måste registrera en omdirigerings-URI som är kompatibel med Broker. Omdirigerings-URI för Broker måste innehålla appens paket namn, samt den base64-kodade representationen av appens signatur.

Formatet för omdirigerings-URI: n är: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Generera din base64 URL-kodade signatur med appens signerings nycklar. Här följer några exempel på kommandon som använder dina fel söknings signerings nycklar:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Se [signera din app](https://developer.android.com/studio/publish/app-signing) för information om att signera din app.

> [!IMPORTANT]
> Använd din produktions signerings nyckel för produktions versionen av din app.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurera MSAL för att använda en Service Broker

Om du vill använda en Broker i din app måste du intyga att du har konfigurerat omdirigeringen av Service Broker. Inkludera till exempel både din Broker-aktiverade omdirigerings-URI – och indikerar att du har registrerat den – genom att inkludera följande i din MSAL-konfigurations fil:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Det nya användar gränssnittet för Azure Portal app-registrering hjälper dig att generera omdirigerings-URI för Broker. Om du har registrerat din app med hjälp av den tidigare versionen, eller gjort det med hjälp av registrerings portalen för Microsoft-appen, kan du behöva generera omdirigerings-URI: n och uppdatera listan över omdirigerings-URI: er i portalen manuellt.

### <a name="broker-related-exceptions"></a>Undantag som rör Broker

MSAL kommunicerar med Service Broker på två sätt:

- Service Broker-bindning
- Android-AccountManager

MSAL använder först den Broker-kopplade tjänsten eftersom anrop till den här tjänsten inte kräver några Android-behörigheter. Om bindningen till den kopplade tjänsten Miss lyckas använder MSAL Android AccountManager-API: et. MSAL gör detta endast om din app redan har beviljats behörigheten `"READ_CONTACTS"`.

Om du får en `MsalClientException` med felkod `"BROKER_BIND_FAILURE"` finns det två alternativ:

- Be användaren att inaktivera energi optimering för Microsoft Authenticator-appen och Intune-företagsportal.
- Be användaren att ge behörigheten `"READ_CONTACTS"`
