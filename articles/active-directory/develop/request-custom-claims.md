---
title: Begär anpassade anspråk (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du begär anpassade anspråk.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085602"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Så här begär du anpassade anspråk med MSAL för iOS och macOS

OpenID Connect kan du eventuellt begära retur av enskilda anspråk från UserInfo-slutpunkten och/eller i ID-token. En begäran om anspråk representeras som ett JSON-objekt som innehåller en lista över begärda anspråk. Mer information finns i [OpenID Connect Core 1.0.](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)

Microsoft Authentication Library (MSAL) för iOS och macOS gör det möjligt att begära specifika anspråk i både interaktiva och tysta tokeninhämtningsscenarier. Det gör det `claimsRequest` genom parametern.

Det finns flera scenarier där detta behövs. Ett exempel:

- Begära anspråk utanför standarduppsättningen för ditt program.
- Begär specifika kombinationer av standardanspråk som inte kan anges med hjälp av scope för ditt program. Om till exempel en åtkomsttoken avvisas på grund av saknade anspråk kan programmet begära saknade anspråk med MSAL.

> [!NOTE]
> MSAL kringgår åtkomsttokens cache när en anspråksbegäran anges. Det är viktigt att `claimsRequest` bara ange parameter när ytterligare anspråk behövs `claimsRequest` (i motsats till att alltid tillhandahålla samma parameter i varje MSAL API-anrop).

`claimsRequest`kan anges i `MSALSilentTokenParameters` `MSALInteractiveTokenParameters`och:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`kan konstrueras från en NSString-representation av JSON Claims request. 

Mål C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Det kan också ändras genom att begära ytterligare specifika påståenden:

Mål C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`bör sedan anges i tokenparametrarna och tillhandahållas till en av MSAL token acquisitions API:er:

Mål C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Nästa steg

Läs mer om [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)
