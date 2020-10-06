---
title: Brokered Authentication i Android | Azure
titleSuffix: Microsoft identity platform
description: En översikt över Brokered Authentication & auktorisering för Android i Microsoft Identity Platform
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 5042bfad2cfe06c7c368c6b476aa1b02d67bcc9c
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760762"
---
# <a name="brokered-authentication-in-android"></a>Brokered Authentication i Android

Du måste använda någon av Microsofts autentiserings mäklare för att delta i enkel inloggning (SSO) för hela enheten och för att uppfylla organisationens principer för villkorlig åtkomst. Att integrera med en Service Broker ger följande fördelar:

- Enkel inloggning för enhet
- Villkorlig åtkomst för:
  - Intune App Protection
  - Enhets registrering (Workplace Join)
  - Hantering av mobila enheter
- Konto hantering för hela enheten
  -  via Android-AccountManager & konto inställningar
  - "Arbets konto" – anpassad kontotyp

På Android är Microsoft Authentication Broker en komponent som ingår i [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) och [Intune-företagsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal).

Följande diagram illustrerar förhållandet mellan appen, Microsoft Authentication Library (MSAL) och Microsofts autentiserings mäklare.

![Diagram över hur ett program relaterar till MSAL, Broker-appar och Android-konto hanteraren.](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Installera appar som är värdar för en Broker

Broker-värdbaserade appar kan installeras av enhetens ägare från App Store (vanligt vis Google Play Butik). Vissa API: er (resurser) skyddas dock av principer för villkorlig åtkomst som kräver att enheter:

- Registrerad (arbets plats ansluten) och/eller
- Registrerad i enhets hantering eller
- Registrerad i Intune-appskydd

Om en enhet inte redan har en Service Broker-app installerad instruerar MSAL användaren att installera en så fort appen försöker hämta en token interaktivt. Appen måste sedan leda användaren genom stegen för att göra enheten kompatibel med den princip som krävs.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effekter av installation och avinstallation av en Broker

### <a name="when-a-broker-is-installed"></a>När en Service Broker installeras

När en Broker installeras på en enhet hanteras alla efterföljande interaktiva Tokenbegäran (anrop till `acquireToken()` ) av Service Broker i stället för lokalt av MSAL. SSO-tillstånd som tidigare var tillgängligt för MSAL är inte tillgängligt för Broker. Detta innebär att användaren måste autentisera igen eller välja ett konto i den befintliga listan över konton som är kända för enheten.

Att installera en Broker kräver inte att användaren loggar in igen. Endast när användaren behöver matcha en `MsalUiRequiredException` skickas nästa begäran till Service Broker. `MsalUiRequiredException` kan genereras av flera olika orsaker och måste lösas interaktivt. Exempel:

- Användaren ändrade lösen ordet som är kopplat till sitt konto.
- Användarens konto uppfyller inte längre en princip för villkorlig åtkomst.
- Användaren återkallade sitt medgivande för att appen ska associeras med sitt konto.

#### <a name="multiple-brokers"></a>Flera mäklare

Om flera hanterare är installerade på en enhet är den Broker som installerades först alltid den aktiva Service Broker. Endast en enskild Broker kan vara aktiv på en enhet.

### <a name="when-a-broker-is-uninstalled"></a>När en Broker avinstalleras

Om det bara finns en Service Broker-värd app installerad och den tas bort måste användaren logga in igen. Om du avinstallerar den aktiva Broker tas kontot och tillhör ande token bort från enheten.

Om Intune-företagsportal är installerat och fungerar som den aktiva utjämningen, och Microsoft Authenticator också är installerat, kommer användaren att behöva logga in igen om Intune-företagsportal (aktiv Broker) har avinstallerats. När de loggar in igen blir Microsoft Authenticator-appen den aktiva koordinatorn.

## <a name="integrating-with-a-broker"></a>Integrera med en Service Broker

### <a name="generating-a-redirect-uri-for-a-broker"></a>Skapa en omdirigerings-URI för en Broker

Du måste registrera en omdirigerings-URI som är kompatibel med Broker. Omdirigerings-URI: n för Service Broker ska innehålla appens paket namn och den base64-kodade åter givningen av appens signatur.

Formatet för omdirigerings-URI: n är: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Du kan använda [verktyget](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) för att generera en Base64-kodad signatur hash med hjälp av appens signerings nycklar och sedan använda Azure Portal för att generera omdirigerings-URI: n med denna hash.

Linux och macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

När du har genererat en signatur-hash med ett- *verktyg*använder du Azure Portal för att generera omdirigerings-URI: n:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj din Android-app i **Appregistreringar**.
1. Välj **autentisering**  >  **Lägg till en plattforms**-  >  **Android**.
1. I fönstret **Konfigurera din Android-app** som öppnas anger du **signaturens hash** som du skapade tidigare och ett **paket namn**.
1. Välj knappen **Konfigurera** .

Azure Portal genererar omdirigerings-URI: n åt dig och visas i fältet för **omdirigerings-URI** för **Android** -fönstret.

Mer information om hur du signerar din app finns i [signera din app](https://developer.android.com/studio/publish/app-signing) i användar handboken för Android Studio.

> [!IMPORTANT]
> Använd din produktions signerings nyckel för produktions versionen av din app.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurera MSAL för att använda en Service Broker

Om du vill använda en Broker i din app måste du intyga att du har konfigurerat omdirigeringen av Service Broker. Inkludera till exempel både din Broker-aktiverade omdirigerings-URI – och indikerar att du har registrerat den, genom att inkludera följande inställningar i MSAL-konfigurations filen:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>Undantag som rör Broker

MSAL kommunicerar med Service Broker på två sätt:

- Service Broker-bindning
- Android-AccountManager

MSAL använder först den Broker-kopplade tjänsten eftersom det inte krävs några Android-behörigheter för att anropa den här tjänsten. Om bindningen till den kopplade tjänsten Miss lyckas använder MSAL Android AccountManager-API: et. MSAL gör det bara om din app redan har beviljats `"READ_CONTACTS"` behörigheten.

Om du får en `MsalClientException` med-felkod `"BROKER_BIND_FAILURE"` finns det två alternativ:

- Be användaren att inaktivera energi optimering för Microsoft Authenticator-appen och Intune-företagsportal.
- Be användaren att bevilja `"READ_CONTACTS"` behörigheten

## <a name="verifying-broker-integration"></a>Verifiera Broker-integrering

Det kan hända att den inte omedelbart rensas om Broker-integreringen fungerar, men du kan använda följande steg för att kontrol lera:

1. På din Android-enhet slutför du en begäran med hjälp av Service Broker.
1. I inställningarna på din Android-enhet letar du efter ett nytt konto som motsvarar det konto som du autentiserade med. Kontot måste vara av typen *arbets konto*.

Du kan ta bort kontot från inställningarna om du vill upprepa testet.

## <a name="next-steps"></a>Nästa steg

Med [delad enhets läge för Android-enheter](msal-android-shared-devices.md) kan du konfigurera en Android-enhet så att den enkelt kan delas av flera anställda.
