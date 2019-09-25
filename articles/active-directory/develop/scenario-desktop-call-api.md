---
title: 'Skriv bords app som anropar webb-API: er (anropar ett webb-API) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (anropar ett webb-API)'
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268285"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Skriv bords app som anropar webb-API: er – anropa ett webb-API

Nu när du har en token kan du anropa ett skyddat webb-API.

## <a name="calling-a-web-api-from-net"></a>Anropa ett webb-API från .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Anropa ett webb-API i MSAL för iOS och macOS

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

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Anropa flera API: er – stegvisa medgivande och villkorlig åtkomst

Om du behöver anropa flera API: er för samma användare kan du, när du har fått en token för det första API: `AcquireTokenSilent`et, bara anropa, och du får en token för de andra API: erna i bakgrunden av tiden.

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
> [Flytta till produktion](scenario-desktop-production.md)
