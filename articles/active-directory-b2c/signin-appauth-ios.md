---
title: Använda AppAuth i ett iOS-program
titleSuffix: Azure AD B2C
description: Så här skapar du en iOS-app som använder AppAuth med Azure Active Directory B2C för att hantera användaridentiteter och autentisera användare.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c659280ebc8c91b53cbc3a176c84397edd942c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186836"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Logga in med ett iOS-program

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Att använda ett öppet standardprotokoll ger fler utvecklare val när du väljer ett bibliotek att integrera med våra tjänster. Vi har tillhandahållit den här genomgången och andra gillar det för att hjälpa utvecklare med att skriva program som ansluter till Microsoft Identity-plattformen. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity-plattformen.

> [!WARNING]
> Microsoft tillhandahåller inte korrigeringar för tredjepartsbibliotek och har inte gjort en granskning av dessa bibliotek. Det här exemplet använder ett tredjepartsbibliotek som heter AppAuth som har testats för kompatibilitet i grundläggande scenarier med Azure AD B2C. Problem och funktionsförfrågningar bör riktas till bibliotekets projekt med öppen källkod. Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Om du inte har tidigare till OAuth2 eller OpenID Connect kanske mycket av den här exempelkonfigurationen inte är särskilt meningsfull. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Hämta en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper med mera. Om du inte redan har en [skapar du en B2C-katalog](tutorial-create-tenant.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program

Registrera sedan ett program i din Azure AD B2C-klient. Detta ger Azure AD den information som behövs för att kommunicera säkert med din app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registrera **program-ID:t (klient)** för användning i ett senare steg.

Spela också in din anpassade omdirigerings-URI för användning i ett senare steg. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Skapa dina användarflöden
I Azure AD B2C definieras varje användarupplevelse av ett [användarflöde](user-flow-overview.md). Det här programmet innehåller en identitetsupplevelse: en kombinerad inloggning och registrering. När du skapar användarflödet måste du:

* Under **Registreringsattribut**väljer du attributet **Visningsnamn**.  Du kan också välja andra attribut.
* Under **Programanspråk**väljer du **anspråkens visningsnamn** och **användarens objekt-ID**. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje användarflöde när du har skapat det. Ditt användarnamn föregås av `b2c_1_` när du sparar användarflödet.  Du behöver användarnamnet senare.

När du har skapat dina användarflöden är du redo att skapa din app.

## <a name="download-the-sample-code"></a>Ladda ner exempelkoden
Vi har tillhandahållit ett fungerande exempel som använder AppAuth med Azure AD B2C [på GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Du kan ladda ner koden och köra den. Om du vill använda din egen Azure AD B2C-klient följer du instruktionerna i [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Det här exemplet skapades genom att följa README-instruktionerna från [iOS AppAuth-projektet på GitHub](https://github.com/openid/AppAuth-iOS). Mer information om hur exemplet och biblioteket fungerar finns i AppAuth README på GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändra din app för att använda Azure AD B2C med AppAuth

> [!NOTE]
> AppAuth stöder iOS 7 och uppåt.  Men för att stödja sociala inloggningar på Google, SFSafariViewController behövs som kräver iOS 9 eller högre.
>

### <a name="configuration"></a>Konfiguration

Du kan konfigurera kommunikation med Azure AD B2C genom att ange både auktoriseringsslutpunkten och tokenslutpunkts-URI:er.  För att generera dessa URI:er behöver du följande information:
* Klient-ID (till exempel contoso.onmicrosoft.com)
* Namn på användarflöde (till\_exempel\_B2C 1 SignUpIn)

Tokenslutpunkts-URI kan genereras genom\_att ersätta\_klient-ID och principnamnet i följande URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Auktoriseringsslutpunkt URI kan\_genereras genom att\_ersätta klient-ID och principnamnet i följande URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Kör följande kod för att skapa objektet AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Bemyndigande

När du har konfigurerat eller hämtat en auktoriseringstjänstkonfiguration kan en auktoriseringsbegäran skapas. För att skapa begäran behöver du följande information:

* Klient-ID (APPLICATION ID) som du spelade in tidigare. Till exempel `00000000-0000-0000-0000-000000000000`.
* Anpassad omdirigerings-URI som du spelade in tidigare. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Båda objekten borde ha sparats när du [registrerade appen](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Om du vill konfigurera programmet för att hantera omdirigeringen till URI med det anpassade schemat måste du uppdatera listan över URL-scheman i din Info.pList:
* Öppna Info.pList.
* Hovra över en rad som "Bundle \+ OS Type Code" och klicka på symbolen.
* Byt namn på den nya raden "URL-typer".
* Klicka på pilen till vänster om "URL-typer" för att öppna trädet.
* Klicka på pilen till vänster om "Punkt 0" för att öppna trädet.
* Byt namn på det första objektet under objekt 0 till "URL-scheman".
* Klicka på pilen till vänster om "URL-scheman" för att öppna trädet.
* I kolumnen Värde finns ett tomt fält till vänster om "Objekt 0" under URL-scheman.  Ange värdet till programmets unika schema.  Värdet måste matcha det schema som används i redirectURL när du skapar OIDAuthorizationRequest-objektet.  I urvalet används schemat "com.onmicrosoft.fabrikamb2c.exampleapp".

Se [AppAuth-guiden](https://openid.github.io/AppAuth-iOS/) om hur du slutför resten av processen. Om du snabbt behöver komma igång med en fungerande app kan du läsa [exemplet](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Följ stegen i [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) för att ange din egen Azure AD B2C-konfiguration.

Vi är alltid öppna för feedback och förslag! Om du har några problem med den här artikeln, eller har rekommendationer för att förbättra detta innehåll, skulle vi uppskatta din feedback längst ner på sidan. Om du vill ha funktionsförfrågningar lägger du till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
