---
title: Anpassa webbläsare och webbvyer
titleSuffix: Microsoft identity platform
description: Lär dig hur du anpassar webb läsar miljön som används av MSAL för iOS och macOS för att logga in användare
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb314e46094bb6c283a17508c35b7fc17e010e5
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803372"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Gör så här: anpassa webbläsare och webbvy för iOS/macOS

En webbläsare krävs för interaktiv autentisering. I iOS använder Microsoft Authentication Library (MSAL) systemets webbläsare som standard (som kan visas ovanpå appen) för att göra interaktiva autentisering att logga in användare. Med hjälp av system webbläsaren kan du dela läget för enkel inloggning (SSO) med andra program och med webb program.

Du kan ändra upplevelsen genom att anpassa konfigurationen till andra alternativ för att Visa webb innehåll, till exempel:

Endast för iOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

För iOS och macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL för macOS stöder bara `WKWebView`.

## <a name="system-browsers"></a>System webbläsare

För iOS, `ASWebAuthenticationSession`, `SFAuthenticationSession` och `SFSafariViewController` betraktas som system webbläsare. I allmänhet delar system webbläsare cookies och andra webbplats data med webb programmet Safari.

Som standard kommer MSAL att identifiera iOS-versionen dynamiskt och välja den rekommenderade system webbläsaren som är tillgänglig i den versionen. På iOS 12 + blir det `ASWebAuthenticationSession`. 

| Version | Webbläsare |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Utvecklare kan också välja en annan system webbläsare för MSAL-appar:

- `SFAuthenticationSession` är iOS 11-versionen av `ASWebAuthenticationSession`.
- `SFSafariViewController` är ett allmänt syfte och innehåller ett gränssnitt för att surfa på webben och kan även användas för inloggnings ändamål. I iOS 9 och 10 delas cookies och andra webbplats data med Safari – men inte i iOS 11 och senare.

## <a name="in-app-browser"></a>Webbläsare i app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) är en webbläsare i appen som visar webb innehåll. Den delar inte ut cookies eller webbplats data med andra **WKWebView** -instanser eller med Safari-webbläsaren. WKWebView är en plattforms oberoende webbläsare som är tillgänglig för både iOS och macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Effekter för cookie-delning och enkel inloggning (SSO)

Den webbläsare du använder påverkar SSO-upplevelsen på grund av hur de delar cookies. I följande tabeller sammanfattas SSO-upplevelser per webbläsare.

| Teknik    | Typ av webbläsare  | tillgänglighet för iOS | macOS-tillgänglighet | Delar cookies och andra data  | MSAL tillgänglighet | DEFINITION |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | System | iOS12 och upp | macOS 10,15 och uppåt | Ja | endast iOS | b/Safari-instanser
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | System | iOS11 och upp | Gäller inte | Ja | endast iOS |  b/Safari-instanser
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | System | iOS11 och upp | Gäller inte | Nej | endast iOS | Nej * *
| **SFSafariViewController** | System | iOS10 | Gäller inte | Ja | endast iOS |  b/Safari-instanser
| **WKWebView**  | In-app | iOS8 och upp | macOS 10,10 och uppåt | Nej | iOS och macOS | Nej * *

\* * För att SSO ska fungera måste tokens delas mellan appar. Detta kräver ett token-cache-eller Broker-program, till exempel Microsoft Authenticator för iOS.

## <a name="change-the-default-browser-for-the-request"></a>Ändra standard webbläsare för begäran

Du kan använda en webbläsare i appen eller en speciell system webbläsare, beroende på dina UX-krav, genom att ändra följande egenskap i `MSALWebviewParameters`:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Ändra per interaktiv begäran

Varje begäran kan konfigureras för att åsidosätta standard webbläsaren genom att ändra `MSALInteractiveTokenParameters.webviewParameters.webviewType` egenskap innan du skickar den till `acquireTokenWithParameters:completionBlock:`-API: et.

Dessutom stöder MSAL överföring i en anpassad `WKWebView` genom att ange egenskapen `MSALInteractiveTokenParameters.webviewParameters.customWebView`.

Exempel:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Om du använder en anpassad webbvy används meddelanden för att ange status för det webb innehåll som visas, till exempel:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Alternativ

Alla webb läsar typer som stöds av MSAL deklareras i [MSALWebviewType-uppräkningen](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)
