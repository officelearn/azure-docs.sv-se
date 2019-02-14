---
title: Skapa en iOS-app som integreras med Azure för inloggning och anropar skyddade API:er med OAuth 2.0 | Microsoft Docs
description: Lär dig hur du loggar in användare och anropar Microsoft Graph API från iOS-appen.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53efe44f17f174073ea5ed39f94c9dc8078a9779
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206777"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en iOS-app

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) har Active Directory Authentication Library (ADAL) för iOS-klienter som behöver åtkomst till skyddade resurser. ADAL förenklar den process som appen använder för att hämta åtkomsttoken. 

I den här snabbstarten skapar du en ”att göra-lista”-app med Objective C som:

* Hämtar åtkomsttoken för att anropa Azure AD Graph API med hjälp av OAuth 2.0-autentiseringsprotokollet
* Söker efter en katalog för användare med ett visst alias

Om du vill skapa en komplett, fungerande app måste du:

1. Registrera appen med Azure AD.
1. Installera och konfigurera ADAL.
1. Använda ADAL för att hämta token från Azure AD.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom igång genom att slutföra följande krav:

* [Ladda ned appens stomme](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) eller [ladda ned det färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).
* Ha en Azure AD-klientorganisation där du kan skapa användare och registrera en app. Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).

> [!TIP]
> Prova [utvecklarportalen](https://identity.microsoft.com/Docs/iOS) för att komma igång med Azure AD på bara några minuter. Utvecklarportalen guidar dig genom registreringen av en app och integreringen av Azure AD i din kod. När du är klar har du en enkel app som kan autentisera användare i klientorganisationen och en serverdel som kan acceptera token och utföra validering.

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>Steg 1: Fastställa din omdirigerings-URI för iOS

För att kunna starta dina appar i vissa SSO-scenarier måste du skapa en *omdirigerings-URI* i ett visst format. En omdirigerings-URI används till att se till att token returneras till rätt app som har frågat efter dem.

iOS-formatet för en omdirigerings-URI är:

```
<app-scheme>://<bundle-id>
```

* **Appschema** – Registreras i ditt XCode-projekt och är hur andra appar kan anropa dig. Du hittar appschemat under **Info.plist > URL types > URL Identifier** (Info.plist > URL-typer > URL-identifierare). Skapa ett appschema om du inte redan har ett eller flera konfigurerade.
* **bundle-id** (ID för programpaket) – Är det paket-ID som finns under **identity** i projektinställningarna för XCode.

Ett exempel på den här snabbstartskoden:

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>Steg 2: Registrera DirectorySearcher-programmet

Om du vill konfigurera appen så att den hämtar apptoken måste du registrera appen i din Azure AD-klientorganisation och bevilja den behörighet för åtkomst till Azure AD Graph API.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj konto i det översta fältet. Under **kataloglistan** väljer du den Active Directory-klientorganisation där du vill registrera appen.
3. Välj **Alla tjänster** i navigeringsfönstret längst till vänster och välj sedan **Azure Active Directory**.
4. Välj **Appregistreringar** och sedan **Lägg till**.
5. Följ anvisningarna för att skapa ett nytt **internt** klientprogram.
    * **Namn** är appens namn och beskriver appen för användarna.
    * **Omdirigerings-URI** är ett schema och en strängkombination som Azure AD använder till att returnera tokensvar. Ange ett värde som är specifikt för appen och baseras på den tidigare informationen om omdirigerings-URI.
6. När du har slutfört registreringen tilldelar Azure AD din app ett unikt program-ID. Du behöver det här värdet i nästa avsnitt, så kopiera det från appfliken.
7. På sidan **Inställningar** väljer du **Nödvändiga behörigheter > Lägg till > Microsoft Graph**, och sedan lägger du under **Delegerade behörigheter** till behörigheten **Läsa katalogdata**. Den här behörigheten konfigurerar appen att fråga Azure AD Graph API efter användare.

## <a name="step-3-install-and-configure-adal"></a>Steg 3: Installera och konfigurera ADAL

Nu när du har en app i Azure AD kan du installera ADAL och skriva din identitetsrelaterade kod. För att ADAL ska kunna kommunicera med Azure AD måste du ange viss information om appregistreringen.

1. Börja med att lägga till ADAL i DirectorySearcher-projektet med hjälp av CocoaPods.

    ```
    $ vi Podfile
    ```
1. Lägg till följande i Podfile:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

1. Läs in Podfile med CocoaPods. Det här steget skapar en ny XCode-arbetsyta som du kan läsa in.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. I QuickStart-projektet öppnar du plist-filen `settings.plist`.
1. Ersätt värdena för elementen i avsnittet för att använda samma värden som du angav i Azure-portalen. Koden refererar till dessa värden när den använder ADAL.
    * `tenant` är domänen för din Azure AD-klientorganisation, till exempel contoso.onmicrosoft.com.
    * `clientId` är klient-ID:t för din app som du har kopierat från portalen.
    * `redirectUri` är den omdirigerings-URL som du registrerade i portalen.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Steg 4: Använda ADAL för att hämta token från Azure AD

Grundprincipen bakom ADAL är att när appen behöver en åtkomsttoken anropar den helt enkelt completionBlock `+(void) getToken : `, så gör ADAL resten.

1. I `QuickStart`-projektet öppnar du `GraphAPICaller.m` och letar reda på kommentaren `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` högst upp.

    Här skickar du ADAL koordinaterna via ett CompletionBlock, för att kommunicera med Azure AD, och ange hur den ska cachelagra token.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Du måste använda denna token för användare i diagrammet. Leta reda på kommentaren `// TODO: implement SearchUsersList`. Den här metoden gör en GET-begäran till Azure AD Graph API för att fråga efter användare vars UPN börjar med den angivna söktermen. 

    Om du vill fråga Azure AD Graph API måste du inkludera en access_token i `Authorization`-huvudet för begäran. Det är här ADAL kommer in i bilden.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```

3. När appen begär en token genom att anropa `getToken(...)` försöker ADAL returnera en token utan att fråga efter autentiseringsuppgifter. Om ADAL avgör att användaren måste logga in för att få en token visas en dialogruta för inloggning. Användarens autentiseringsuppgifter samlas in och sedan returneras en token efter utförd autentisering. Om ADAL inte kan returnera en token av någon anledning genererar det ett `AdalException`.

> [!NOTE]
> Objektet `AuthenticationResult` innehåller ett `tokenCacheStoreItem`-objekt som du kan använda till att samla in information som appen kan behöva. I QuickStart används `tokenCacheStoreItem` till att avgöra om autentisering redan har genomförts.

## <a name="step-5-build-and-run-the-application"></a>Steg 5: Skapa och kör appen

Grattis! Nu har du en fungerande iOS-app som kan autentisera användare, på ett säkert sätt anropa webb-API:er med hjälp av OAuth 2.0 och få grundläggande information om användaren.

Om du inte redan har gjort det är det dags att fylla i klientorganisationen med några användaren.

1. Starta QuickStart-appen och logga sedan in med någon av dessa användare.
1. Sök efter andra användare utifrån deras UPN.
1. Stäng appen och starta den sedan igen. Observera att användarens session förblir intakt.

ADAL gör det enkelt att inkorporera alla dessa vanliga identitetsfunktioner i appen. Det tar hand om allt grovjobb åt dig, som cachehantering, stöd för OAuth-protokoll, visa ett användargränssnitt för användaren för inloggning och uppdatera token som gått ut. Allt du verkligen behöver veta är ett enda API-anrop, `getToken`.

Som referens tillhandahålls det slutförda exemplet (utan dina konfigurationsvärden) på [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med fler scenarier. Som nästa steg föreslår vi att du testar det här:

* [Skydda ett Node.JS-webb-API med Azure AD](quickstart-v1-nodejs-webapi.md)
* Lär dig [hur du aktiverar SSO mellan appar på iOS med ADAL](howto-v1-enable-sso-ios.md)  
