---
title: "Azure Active Directory B2C: Hämta en token som använder en Android-App | Microsoft Docs"
description: "Den här artikeln visar hur du skapar en Android-app som använder AppAuth med Azure Active Directory B2C hanterar användaridentiteter och autentiserar användare."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/06/2017
ms.author: parakhj
ms.openlocfilehash: cd4b8048245be49ea79bcb1b364f2f99c56f8291
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C: Logga in med ett Android-program

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Det innebär att utvecklare kan utnyttja alla bibliotek som de vill integrera med våra tjänster. Vi har skrivit några genomgång som detta att demonstrera hur du konfigurerar 3 part bibliotek för att ansluta till Microsoft identity-plattformen för att hjälpa utvecklare i vår plattform med andra bibliotek. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity-plattformen.

> [!WARNING]
> Microsoft tillhandahåller inte korrigeringar för 3 part bibliotek och inte har gjort en genomgång av dessa bibliotek. Det här exemplet använder ett 3 part bibliotek kallas AppAuth har testats för kompatibilitet i grundläggande scenarier med Azure AD B2C. Problem och funktionsförfrågningar ska dirigeras till biblioteksprojekt öppen källkod. Se [i den här artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) för mer information.  
>
>

Om du inte har erfarenhet av OAuth2 eller OpenID Connect kanske du inte får ut så mycket av den här exempelkonfigurationen. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Skaffa en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](active-directory-b2c-get-started.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program

Därefter måste du skapa en app i B2C-katalogen. Det ger Azure AD den information som tjänsten behöver för att kommunicera säkert med din app. Så här skapar du en mobil app [instruktionerna](active-directory-b2c-app-registration.md). Se till att:

* Inkludera en **Native Client** i programmet.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver det senare.
* Konfigurera en intern klient **omdirigerings-URI** (t.ex. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Du behöver även detta senare.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Skapa principer

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Den här appen innehåller en identity-upplevelse: en kombinerad inloggning och registrering. Du måste skapa den här principen, som beskrivs i den [referensartikeln om principer](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Tänk på följande när du skapar principen:

* Välj den **visningsnamn** som ett anmälan attribut i principen.
* Välj det **visningsnamn** och **objekt-ID** som programmet gör anspråk på i varje princip. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje princip när du har skapat den. Det bör ha prefixet `b2c_1_`.  Du behöver principnamnet senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat dina principer är det dags att bygga appen.

## <a name="download-the-sample-code"></a>Hämta exempelkoden

Vi har angett ett fungerande exempel som använder AppAuth med Azure AD B2C [på GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Du kan hämta koden och kör den. Du kan snabbt komma igång med din egen app med Azure AD B2C konfigurationen genom att följa anvisningarna i den [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Exemplet är en ändring av exemplet som anges av [AppAuth](https://openid.github.io/AppAuth-Android/). Besök deras sidan om du vill veta mer om AppAuth och dess funktioner.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändra din app att använda Azure AD B2C med AppAuth

> [!NOTE]
> AppAuth stöder Android API 16 (Jellybean) och senare. Vi rekommenderar att du använder API-23 och högre.
>

### <a name="configuration"></a>Konfiguration

Du kan konfigurera kommunikation med Azure AD B2C genom att ange anger identifieringen URI eller genom att ange både autentiseringsslutpunkt och tokenslutpunkten URI: er. I båda fallen måste följande information:

* Klient-ID (t.ex. contoso.onmicrosoft.com)
* Principens namn (t.ex. B2C\_1\_SignUpIn)

Om du väljer att automatiskt identifiera auktoriserings- och tokenslutpunkten URI: er måste att hämta information från identifiering URI. Identifieringen URI kan genereras genom att ersätta innehavaren\_-ID och principen\_namn i följande URL:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Du kan hämta auktoriserings- och tokenslutpunkten URI: er och skapa ett AuthorizationServiceConfiguration objekt genom att köra följande:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

I stället för att få fram auktorisering och tokenslutpunkten URI: er med hjälp av identifiering du kan också ange dem explicit genom att ersätta innehavaren\_-ID och principen\_namn i URL: er nedan:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Kör följande kod för att skapa AuthorizationServiceConfiguration-objekt:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Auktorisera

När du konfigurerar eller hämtar en auktorisering tjänstkonfiguration, kan en begäran om godkännande konstrueras. För att skapa begäran, behöver du följande information:

* Klient-ID (t.ex. 00000000-0000-0000-0000-000000000000)
* Omdirigerings-URI med ett anpassat schema (t.ex. com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Båda objekten ska har sparats när du var [registrera din app](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Mer information finns i [AppAuth guide](https://openid.github.io/AppAuth-Android/) om hur du Slutför resten av processen. Om du behöver att snabbt komma igång med en fungerande app kan ta en titt [exemplet](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Följ stegen i den [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) att ange din egen Azure AD B2C-konfiguration.

Vi är alltid öppna för feedback och förslag! Om du har problem med det här avsnittet eller rekommendationer för att förbättra det här innehållet, skulle vi uppskattar din feedback längst ned på sidan. För funktionsbegäranden, lägga till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

