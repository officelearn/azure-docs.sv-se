---
title: Anropa ett webb-API från en mobilapp | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du skapar en mobilapp som anropar webb-API:er. (Anropa ett webb-API.)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: bd848fa6f74f049f97956ef1736ac2b08f3a6148
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160159"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Anropa ett webb-API från en mobilapp

När appen har loggat in en användare och tagit emot token exponerar Microsoft Authentication Library (MSAL) information om användaren, användarens miljö och de utfärdade token. Din app kan använda dessa värden för att anropa ett webb-API eller visa ett välkomstmeddelande till användaren.

I den här artikeln ska vi först titta på MSAL-resultatet. Sedan ska vi titta på hur du `AuthenticationResult` `result` använder en åtkomsttoken från eller för att anropa ett skyddat webb-API.

## <a name="msal-result"></a>MSAL-resultat
MSAL ger följande värden: 

- `AccessToken`anropar skyddade webb-API:er i en HTTP-bärarebegäran.
- `IdToken`innehåller användbar information om den inloggade användaren. Den här informationen innehåller användarens namn, hemklienten och en unik identifierare för lagring.
- `ExpiresOn`är tokens utgångstid. MSAL hanterar en apps automatiska uppdatering.
- `TenantId`är identifieraren för klienten där användaren loggade in. För gästanvändare i Azure Active Directory (Azure AD) B2B identifierar det här värdet klienten där användaren loggade in. Värdet identifierar inte användarens hemklient.  
- `Scopes`anger de scope som har beviljats med din token. De beviljade scopen kan vara en delmängd av de scope som du har begärt.

MSAL ger också en `Account` abstraktion för ett värde. Ett `Account` värde representerar den aktuella användarens inloggade konto:

- `HomeAccountIdentifier`identifierar användarens hemklient.
- `UserName`är användarens föredragna användarnamn. Det här värdet kan vara tomt för Azure AD B2C-användare.
- `AccountIdentifier`identifierar den inloggade användaren. I de flesta fall är det `HomeAccountIdentifier` här värdet detsamma som värdet om inte användaren är gäst i en annan klient.

## <a name="call-an-api"></a>Anropa ett API

När du har åtkomsttoken kan du anropa ett webb-API. Din app använder token för att skapa en HTTP-begäran och kör sedan begäran.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL för iOS och macOS

Metoderna för att hämta `MSALResult` token returnerar ett objekt. `MSALResult`exponerar `accessToken` en egenskap. Du kan `accessToken` använda för att anropa ett webb-API. Lägg till den här egenskapen i HTTP-auktoriseringshuvudet innan du anropar för att komma åt det skyddade webb-API:et.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Skapa flera API-begäranden

Om du behöver anropa samma API flera gånger, eller om du behöver ringa flera API:er, bör du tänka på följande ämnen när du skapar appen:

- **Inkrementellt samtycke**: Microsoft identity platform tillåter appar att få användarens medgivande när behörigheter krävs i stället för alla i början. Varje gång appen är redo att anropa ett API bör den endast begära de scope som den behöver.

- **Villkorlig åtkomst**: När du gör flera API-begäranden kan du i vissa scenarier behöva uppfylla ytterligare villkorsstyrda åtkomstkrav. Kraven kan öka på det här sättet om den första begäran inte har några principer för villkorlig åtkomst och din app försöker komma åt ett nytt API som kräver villkorlig åtkomst. För att hantera det här problemet, se till att fånga fel från tysta begäranden och vara beredd att göra en interaktiv begäran.  Mer information finns i [Vägledning för villkorlig åtkomst](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Anropa flera API:er med hjälp av inkrementellt medgivande och villkorlig åtkomst

Om du behöver anropa flera API:er för samma användare, när du har hämtat en token `AcquireTokenSilent` för användaren, kan du undvika att upprepade gånger be användaren om autentiseringsuppgifter genom att senare ringa för att hämta en token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interaktion krävs när:

- Användaren samtyckte till det första API:et men måste nu godkänna fler scope. I det här fallet använder du inkrementellt medgivande.
- Det första API:et kräver inte autentisering med flera faktorer, men nästa API gör det.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-mobile-production.md)
