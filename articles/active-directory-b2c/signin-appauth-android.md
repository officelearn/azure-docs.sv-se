---
title: Skaffa en token i ett Android-program
titleSuffix: Azure AD B2C
description: Så här skapar du en Android-app som använder AppAuth med Azure Active Directory B2C för att hantera användaridentiteter och autentisera användare.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31ad373b1544fc601a9c37e05e324a9c1dfb3f73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183795"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Logga in med ett Android-program i Azure Active Directory B2C

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Med dessa standarder kan du använda alla bibliotek som du vill integrera med Azure Active Directory B2C. För att hjälpa dig att använda andra bibliotek kan du använda en genomgång som den här för att visa hur du konfigurerar bibliotek från tredje part för att ansluta till Microsofts identitetsplattform. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity-plattformen.

> [!WARNING]
> Microsoft tillhandahåller inte korrigeringar för bibliotek från tredje part och har inte gjort en granskning av dessa bibliotek. Det här exemplet använder ett tredjepartsbibliotek som heter AppAuth som har testats för kompatibilitet i grundläggande scenarier med Azure AD B2C. Problem och funktionsförfrågningar bör riktas till bibliotekets projekt med öppen källkod. Se [den här artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) för mer information.
>
>

Om du inte har erfarenhet av OAuth2 eller OpenID Connect kanske du inte får ut så mycket av den här exempelkonfigurationen. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Hämta en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en container för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](tutorial-create-tenant.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program

Registrera sedan ett program i din Azure AD B2C-klient. Detta ger Azure AD den information som behövs för att kommunicera säkert med din app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registrera **program-ID:t (klient)** för användning i ett senare steg.

Spela också in din anpassade omdirigerings-URI för användning i ett senare steg. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Skapa dina användarflöden

I Azure AD B2C definieras varje användarupplevelse av ett [användarflöde](user-flow-overview.md), vilket är en uppsättning principer som styr beteendet för Azure AD. Det här programmet kräver ett inloggnings- och registreringsanvändarflöde. När du skapar användarflödet måste du:

* Välj **visningsnamnet** som ett registreringsattribut i användarflödet.
* Välj anspråk på **visningsnamn** och **objekt-ID-program** i varje användarflöde. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje användarflöde när du har skapat det. Det bör ha prefixet `b2c_1_`.  Du behöver användarnamnet senare.

När du har skapat dina användarflöden är du redo att skapa din app.

## <a name="download-the-sample-code"></a>Ladda ner exempelkoden

Vi har tillhandahållit ett fungerande exempel som använder AppAuth med Azure AD B2C [på GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Du kan ladda ner koden och köra den. Du kan snabbt komma igång med din egen app med din egen Azure AD B2C-konfiguration genom att följa instruktionerna i [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Exemplet är en ändring av det exempel som tillhandahålls av [AppAuth](https://openid.github.io/AppAuth-Android/). Besök deras sida för att lära dig mer om AppAuth och dess funktioner.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändra din app för att använda Azure AD B2C med AppAuth

> [!NOTE]
> AppAuth stöder Android API 16 (Jellybean) och högre. Vi rekommenderar att du använder API 23 och uppåt.
>

### <a name="configuration"></a>Konfiguration

Du kan konfigurera kommunikation med Azure AD B2C genom att antingen ange identifierings-URI eller genom att ange både auktoriseringsslutpunkten och tokenslutpunkts-URI:er. I båda fallen behöver du följande information:

* Klient-ID (t.ex. contoso.onmicrosoft.com)
* Användarnamn (t.ex. B2C\_1\_SignUpIn)

Om du väljer att automatiskt identifiera uri:er för auktorisering och tokenslutpunkt måste du hämta information från identifierings-URI:n. Identifierings-URI kan genereras genom\_att ersätta\_klient-ID och principnamnet i följande URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Du kan sedan hämta uri:erna för auktoriserings- och tokenslutpunkt och skapa ett AuthorizationServiceConfiguration-objekt genom att köra följande:

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

I stället för att använda identifiering för att hämta urierna för auktorisering och tokenslutpunkt kan du också uttryckligen ange dem genom att ersätta klient-ID:t\_och principnamnet\_i url:en nedan:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Kör följande kod för att skapa objektet AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Bemyndigande

När du har konfigurerat eller hämtat en auktoriseringstjänstkonfiguration kan en auktoriseringsbegäran skapas. För att skapa begäran behöver du följande information:

* Klient-ID (APPLICATION ID) som du spelade in tidigare. Till exempel `00000000-0000-0000-0000-000000000000`.
* Anpassad omdirigerings-URI som du spelade in tidigare. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Båda objekten borde ha sparats när du [registrerade appen](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Se [AppAuth-guiden](https://openid.github.io/AppAuth-Android/) om hur du slutför resten av processen. Om du snabbt behöver komma igång med en fungerande app kan du läsa [vårt exempel.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Följ stegen i [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) för att ange din egen Azure AD B2C-konfiguration.
