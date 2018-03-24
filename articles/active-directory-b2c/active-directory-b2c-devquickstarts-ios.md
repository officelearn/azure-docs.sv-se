---
title: Med hjälp av AppAuth i ett program för iOS - Azure Active Directory B2C
description: Den här artikeln visar hur du skapar en iOS-app som använder AppAuth med Azure Active Directory B2C hanterar användaridentiteter och autentiserar användare.
services: active-directory-b2c
documentationcenter: ios
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
ms.openlocfilehash: 18c3801b18fb9adb444918cc45ee70c2611b213d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Logga in med ett iOS-program

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Med en öppen standard protokollet erbjuder flera developer valet när du väljer ett bibliotek att integrera med våra tjänster. Vi har lagt till den här genomgången och andra som den hjälpa utvecklare skriva program som ansluter till Microsoft Identity-plattformen. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity-plattformen.

> [!WARNING]
> Microsoft inte tillhandahåller åtgärdar för tredjeparts-bibliotek och inte har gjort en genomgång av dessa bibliotek. Det här exemplet använder en tredje parts bibliotek kallas AppAuth har testats för kompatibilitet i grundläggande scenarier med Azure AD B2C. Problem och funktionsförfrågningar ska dirigeras till biblioteksprojekt öppen källkod. Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Om du har använt OAuth2 eller OpenID Connect eventuellt mycket av det här exempelkonfiguration ingen vits mycket för dig. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Skaffa en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](active-directory-b2c-get-started.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program
Därefter måste du skapa en app i B2C-katalogen. App-registreringen ger Azure AD den information som krävs för att kommunicera säkert med din app. Så här skapar du en mobil app [instruktionerna](active-directory-b2c-app-registration.md). Se till att:

* Inkludera en **Native client** i programmet.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver detta GUID senare.
* Konfigurera en **omdirigerings-URI** med ett anpassat schema (till exempel com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Du behöver den här URI senare.

## <a name="create-your-policies"></a>Skapa principer
I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Den här appen innehåller en identity-upplevelse: en kombinerad inloggning och registrering. Skapa den här principen enligt beskrivningen i den [referensartikeln om principer](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Tänk på följande när du skapar principen:

* Under **registreringsattribut**, väljer du attributet **visningsnamn**.  Du kan välja samt andra attribut.
* Under **Programanspråk**, Välj anspråk **visningsnamn** och **användarens objekt-ID**. Du kan välja andra anspråk.
* Kopiera **namnet** på varje princip när du har skapat den. Principens namn med prefixet `b2c_1_` när du sparar principen.  Principnamnet måste senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat dina principer är det dags att bygga appen.

## <a name="download-the-sample-code"></a>Hämta exempelkoden
Vi har angett ett fungerande exempel som använder AppAuth med Azure AD B2C [på GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Du kan hämta koden och kör den. Om du vill använda din egen Azure AD B2C-klient, följer du anvisningarna i den [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Det här exemplet har skapats genom att följa anvisningarna viktigt av den [iOS AppAuth projektet på GitHub](https://github.com/openid/AppAuth-iOS). Mer information om hur exemplet och biblioteket fungerar referera till AppAuth README på GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändra din app att använda Azure AD B2C med AppAuth

> [!NOTE]
> AppAuth stöder iOS 7 och senare.  Men för att stödja sociala inloggningar på Google SFSafariViewController behövs som kräver iOS 9 eller högre.
>

### <a name="configuration"></a>Konfiguration

Du kan konfigurera kommunikation med Azure AD B2C genom att ange både autentiseringsslutpunkt och tokenslutpunkten URI: er.  För att generera dessa URI: er, behöver du följande information:
* Klient-ID (till exempel contoso.onmicrosoft.com)
* Principens namn (till exempel B2C\_1\_SignUpIn)

Tokenslutpunkten URI kan genereras genom att ersätta innehavaren\_-ID och principen\_namn i följande URL:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Autentiseringsslutpunkt URI kan genereras genom att ersätta innehavaren\_-ID och principen\_namn i följande URL:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Kör följande kod för att skapa AuthorizationServiceConfiguration-objekt:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Auktoriserar

När du konfigurerar eller hämtar en auktorisering tjänstkonfiguration, kan en begäran om godkännande konstrueras. För att skapa begäran, behöver du följande information:  
* Klient-ID (till exempel 00000000-0000-0000-0000-000000000000)
* Omdirigerings-URI med ett anpassat schema (till exempel com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Båda objekten ska har sparats när du var [registrera din app](#create-an-application).

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

Om du vill konfigurera ditt program för att hantera omdirigering till URI: N med det anpassade schemat måste du uppdatera listan över URL: en scheman i din Info.pList:
* Öppna filen Info.pList.
* Hovra över en rad som 'Paket OS typen Code' och klicka på den \+ symbolen.
* Byt namn på den nya raden URL-typer.
* Klicka på pilen till vänster om URL-typer att öppna trädet.
* Klicka på pilen till vänster om ' objektet 0' om du vill öppna i trädet.
* Byt namn på första elementet under objektet 0 till URL-scheman.
* Klicka på pilen till vänster om URL: en scheman öppna trädet.
* I kolumnen 'Value' är ett tomt fält till vänster om 'Objektet 0' under URL-scheman.  Ange värdet till programmets unika systemet.  Värdet måste matcha det schema som används i RedirectUrl anges när du skapar OIDAuthorizationRequest-objekt.  I det här exemplet används schemat 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Referera till den [AppAuth guide](https://openid.github.io/AppAuth-iOS/) om hur du Slutför resten av processen. Om du behöver att snabbt komma igång med en fungerande app kan ta en titt [provet](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Följ stegen i den [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) att ange din egen Azure AD B2C-konfiguration.

Vi är alltid öppna för feedback och förslag! Om du har problem med den här artikeln eller rekommendationer för att förbättra det här innehållet, skulle vi uppskattar din feedback längst ned på sidan. För funktionsbegäranden, lägga till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
