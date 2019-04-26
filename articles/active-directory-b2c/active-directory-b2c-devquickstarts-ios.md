---
title: Med AppAuth i ett iOS-program i Azure Active Directory B2C | Microsoft Docs
description: Den här artikeln visar hur du skapar en iOS-app som använder AppAuth med Azure Active Directory B2C för att hantera användaridentiteter och autentiserar användare.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3c7eace7c643286575e2625cb81f84d858bbaa8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317929"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Logga in med ett iOS-program

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Med hjälp av ett öppet standardprotokoll erbjuder flera utvecklare val när du väljer ett bibliotek för att integrera med våra tjänster. Vi tillhandahåller den här genomgången och andra som den för att hjälpa utvecklare med att skriva program som ansluter till Microsoft Identity-plattformen. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity-plattformen.

> [!WARNING]
> Microsoft inte tillhandahåller korrigeringar av bibliotek från tredje part och inte har gjort en genomgång av dessa bibliotek. Det här exemplet använder en tredjeparts-library, även kallat AppAuth som har testats för kompatibilitet i grundläggande scenarier med Azure AD B2C. Problem och funktionsförfrågningar ska dirigeras till biblioteksprojekt öppen källkod. Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Om du inte har använt OAuth2 eller OpenID Connect eventuellt mycket av den här exempelkonfigurationen ingen vits mycket till dig. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Skaffa en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](tutorial-create-tenant.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program
Därefter måste du skapa en app i B2C-katalogen. Registreringen ger Azure AD den information som krävs för att kommunicera säkert med din app. Så här skapar du en mobil app [instruktionerna](active-directory-b2c-app-registration.md). Se till att:

* Inkludera en **Native client** i programmet.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver detta GUID senare.
* Konfigurera en **omdirigerings-URI** med ett anpassat schema (till exempel com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Du behöver den här URI: N senare.

## <a name="create-your-user-flows"></a>Skapa dina användarflöden
I Azure AD B2C definieras varje användarupplevelse av en [användarflödet](active-directory-b2c-reference-policies.md). Det här programmet innehåller en identitetslösning: en kombinerad inloggning och registrering. När du skapar användarflödet, måste du kontrollera att:

* Under **registreringsattribut**, väljer du attributet **visningsnamn**.  Du kan välja samt andra attribut.
* Under **Programanspråk**, Välj anspråk **visningsnamn** och **användarobjekt-ID**. Du kan välja andra anspråk också.
* Kopiera den **namn** av varje användarflöde när du har skapat. Ditt flödesnamn har prefixet `b2c_1_` när du sparar användarflödet.  Userjourney-namnet som du behöver senare.

När du har skapat din användarflöden, är du redo att skapa din app.

## <a name="download-the-sample-code"></a>Hämta exempelkoden
Vi har lagt till ett fungerande exempel som använder AppAuth med Azure AD B2C [på GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Du kan ladda ned koden och kör den. Om du vill använda en egen Azure AD B2C-klient, följer du anvisningarna i den [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Det här exemplet har skapats genom att följa anvisningarna README genom den [iOS AppAuth projektet på GitHub](https://github.com/openid/AppAuth-iOS). Mer information om hur exemplet och biblioteket fungerar referera till AppAuth README på GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändra din app för att använda Azure AD B2C med AppAuth

> [!NOTE]
> AppAuth har stöd för iOS 7 och senare.  Men för att stödja sociala inloggningar på Google, SFSafariViewController krävs som kräver iOS 9 eller högre.
>

### <a name="configuration"></a>Konfiguration

Du kan konfigurera kommunikation med Azure AD B2C genom att ange både auktoriseringsslutpunkt och tokenslutpunkten URI: er.  För att generera dessa URI: er, behöver du följande information:
* Klient-ID (exempel: contoso.onmicrosoft.com)
* Userjourney-namnet (till exempel B2C\_1\_SignUpIn)

Tokenslutpunkten URI kan genereras genom att ersätta klienten\_-ID och principen\_namn i följande URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Auktoriseringsslutpunkten URI kan genereras genom att ersätta klienten\_-ID och principen\_namn i följande URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Kör följande kod för att skapa AuthorizationServiceConfiguration-objekt:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Auktoriserar

En begäran om godkännande kan skapas när du konfigurerar eller hämtar en tjänstkonfiguration för auktorisering. För att skapa begäran, behöver du följande information:  
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

Om du vill konfigurera ditt program för att hantera omdirigering till URI: N med det anpassa schemat måste du uppdatera listan över URL: en scheman i din Info.pList:
* Öppna filen Info.pList.
* Hovra över en rad som ”paket OS-typ Code” och klicka på den \+ symbolen.
* Byt namn på den nya raden URL-typer.
* Klicka på pilen till vänster om URL-typer att öppna trädet.
* Klicka på pilen till vänster om ”objektet 0' om du vill öppna i trädet.
* Byt namn på första objektet under objektet 0 till URL-scheman.
* Klicka på pilen till vänster om ”URL-scheman” öppna i trädet.
* I kolumnen 'Value' är ett tomt fält till vänster om ”objektet 0' under URL-scheman.  Ange värdet till ditt programs unika schema.  Värdet måste matcha det schema som används i RedirectUrl anges när du skapar OIDAuthorizationRequest-objekt.  I det här exemplet används schemat 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Referera till den [AppAuth guide](https://openid.github.io/AppAuth-iOS/) om hur du Slutför resten av processen. Om du behöver snabbt komma igång med en fungerande app kan du kolla [exemplet](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Följ stegen i den [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) att ange din egen Azure AD B2C-konfiguration.

Vi är alltid öppna för återkoppling och förslag! Om du har problem med den här artikeln eller har rekommendationer för att förbättra det här innehållet, skulle vi uppskattar din feedback längst ned på sidan. För förfrågningar om ny funktionalitet, vänligen lägg till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
