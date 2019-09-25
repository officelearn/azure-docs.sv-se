---
title: 'Mobilapp som anropar webb-API: er – anropar ett webb-API | Microsoft Identity Platform'
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (anropar ett webb-API)'
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ada6ee6247deb3d4c72edb8237a40a0f47f96be
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268319"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobilapp som anropar webb-API: er – anropa ett webb-API

När din app har loggat in en användare och fått tokens, visar MSAL flera delar av information om användaren, användarens miljö och de token som utfärdas. Din app kan använda dessa värden för att anropa ett webb-API eller visa ett välkomst meddelande till användaren.

Först ska vi titta på MSAL-resultatet. Sedan ska vi titta på hur man använder en åtkomsttoken från `AuthenticationResult` eller `result` för att anropa ett skyddat webb-API.

## <a name="msal-result"></a>MSAL resultat
MSAL innehåller följande värden: 

- `AccessToken`: Används för att anropa skyddade webb-API: er i en HTTP Bearer-begäran.
- `IdToken`: Innehåller användbar information om den inloggade användaren, t. ex. användarens namn, hem klienten och en unik identifierare för lagring.
- `ExpiresOn`: Giltighets tiden för token. MSAL hanterar automatisk uppdatering av appar.
- `TenantId`: Identifieraren för den klient som användaren loggade in med. För gäst användare (Azure Active Directory B2B) identifierar det här värdet den klient som användaren har loggat in med, inte användarens hem klient.  
- `Scopes`: De omfattningar som har beviljats med din token. De beviljade omfattningarna kan vara en delmängd av de omfattningar som du har begärt.

MSAL tillhandahåller också en abstraktion för en `Account`. En `Account` representerar den aktuella användarens inloggade konto.

- `HomeAccountIdentifier`: Identifieraren för användarens hem klient organisation.
- `UserName`: Användarens önskade användar namn. Detta kan vara tomt för Azure Active Directory B2C användare.
- `AccountIdentifier`: Identifieraren för den inloggade användaren. Det här värdet är detsamma som `HomeAccountIdentifier` värdet i de flesta fall, om inte användaren är en gäst i en annan klient.

## <a name="call-an-api"></a>Anropa ett API

När du har åtkomst-token är det enkelt att anropa ett webb-API. Din app kommer att använda token för att skapa en HTTP-begäran och sedan köra begäran.

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

Metoderna för att hämta tokens returnerar ett `MSALResult` objekt. `MSALResult`exponerar en `accessToken` egenskap som kan användas för att anropa ett webb-API. Åtkomsttoken ska läggas till i HTTP-Authorization-huvudet innan anropet för att få åtkomst till det skyddade webb-API: et.

Mål-C:

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

Införliva

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

## <a name="making-several-api-requests"></a>Göra flera API-begäranden

Om du behöver anropa samma API flera gånger, eller om du behöver anropa flera API: er, bör du tänka på följande när du skapar appen:

- **Stegvist tillstånd**: Med Microsoft Identity Platform kan appar få användar medgivande eftersom behörigheter krävs, i stället för alla i början. Varje gången appen är redo att anropa ett API bör den endast begära de omfattningar som den behöver använda.
- **Villkorlig åtkomst**: I vissa fall kan du få ytterligare krav för villkorlig åtkomst när du gör flera API-begäranden. Detta kan inträffa om den första begäran inte har några tillämpade principer för villkorlig åtkomst och appen försöker få tyst åtkomst till ett nytt API som kräver villkorlig åtkomst. För att hantera det här scenariot ska du se till att fånga fel från tysta begär Anden och bli redo att göra en interaktiv begäran.  Mer information finns i [rikt linjer för villkorlig åtkomst](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Anropa flera API: er i Xamarin eller UWP – stegvisa medgivande och villkorlig åtkomst

Om du behöver anropa flera API: er för samma användare, så när du har skaffat en token för en användare, kan du undvika att upprepade gånger be användaren om `AcquireTokenSilent` autentiseringsuppgifter genom att sedan anropa för att hämta en token.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

De fall där interaktion krävs är när:

- Användaren har skickat för det första API: et, men nu måste du godkänna för fler omfattningar (stegvist godkännande)
- Det första API: t krävde inte multifaktorautentisering, men nästa gör.

```CSharp
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
