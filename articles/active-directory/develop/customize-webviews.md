---
title: Anpassa webbläsare & WebViews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du anpassar webbläsarupplevelsen MSAL iOS/macOS för att logga in användare.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 759f61860c62bcb668db6844df28c52fa28eac80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085901"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Så här anpassar du webbläsare och WebViews för iOS/macOS

En webbläsare krävs för interaktiv autentisering. På iOS använder Microsoft Authentication Library (MSAL) systemwebbläsaren som standard (som kan visas ovanpå appen) för att göra interaktiv autentisering för att logga in användare. Använda systemet webbläsaren har fördelen av att dela single sign on (SSO) tillstånd med andra program och med webbapplikationer.

Du kan ändra upplevelsen genom att anpassa konfigurationen till andra alternativ för att visa webbinnehåll, till exempel:

Endast för iOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

För iOS och macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL för macOS `WKWebView`stöder bara .

## <a name="system-browsers"></a>Systemwebbläsare

För `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`, `SFSafariViewController` och betraktas som systemwebbläsare. I allmänhet delar systemwebbläsare cookies och andra webbplatsdata med webbläsaren Safari.

Som standard identifierar MSAL iOS-versionen dynamiskt och väljer den rekommenderade systembläddraren som är tillgänglig på den versionen. På iOS 12+ `ASWebAuthenticationSession`blir det . 

| Version | Webbläsare |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Utvecklare kan också välja en annan systemwebbläsare för MSAL-appar:

- `SFAuthenticationSession`är iOS 11-versionen av `ASWebAuthenticationSession`.
- `SFSafariViewController`är mer allmänt ändamål och ger ett gränssnitt för att surfa på webben och kan användas för inloggningsändamål också. I iOS 9 och 10 delas cookies och andra webbplatsdata med Safari – men inte i iOS 11 och senare.

## <a name="in-app-browser"></a>Webbläsare i appen

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) är en webbläsare i appen som visar webbinnehåll. Den delar inte cookies eller webbplatsdata med andra **WKWebView-instanser** eller med webbläsaren Safari. WKWebView är en plattformsoberoende webbläsare som är tillgänglig för både iOS och macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Konsekvenser för cookie-delning och enkel inloggning

Webbläsaren du använder påverkar SSO-upplevelsen på grund av hur de delar cookies. Följande tabeller sammanfattar SSO-upplevelserna per webbläsare.

| Teknologi    | Typ av webbläsare  | iOS-tillgänglighet | macOS-tillgänglighet | Delar cookies och andra data  | MSAL-tillgänglighet | Enkel inloggning |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | System | iOS12 och uppåt | macOS 10,15 och uppåt | Ja | enbart iOS | w/ Safari-instanser
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | System | iOS11 och uppåt | Ej tillämpligt | Ja | enbart iOS |  w/ Safari-instanser
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | System | iOS11 och uppåt | Ej tillämpligt | Inga | enbart iOS | Nej**
| **SFSafariViewController** | System | iOS10 | Ej tillämpligt | Ja | enbart iOS |  w/ Safari-instanser
| **WKWebView (WKWebView)**  | I appen | iOS8 och uppåt | macOS 10,10 och uppåt | Inga | iOS och macOS | Nej**

** För att SSO ska fungera måste token delas mellan appar. Detta kräver en tokencache eller ett mäklarprogram, till exempel Microsoft Authenticator för iOS.

## <a name="change-the-default-browser-for-the-request"></a>Ändra standardwebbläsaren för begäran

Du kan använda en webbläsare i appen, eller en specifik systemwebbläsare beroende `MSALWebviewParameters`på dina ux-krav, genom att ändra följande egenskap i:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Ändra per interaktiv begäran

Varje begäran kan konfigureras för att åsidosätta standardwebbläsaren genom att ändra egenskapen innan den `MSALInteractiveTokenParameters.webviewParameters.webviewType` skickas till API:et. `acquireTokenWithParameters:completionBlock:`

Dessutom stöder MSAL att skicka `WKWebView` in `MSALInteractiveTokenParameters.webviewParameters.customWebView` en anpassad genom att ange egenskapen.

Ett exempel:

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

Om du använder en anpassad webbvy används meddelanden för att ange status för webbinnehållet som visas, till exempel:

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

Alla webbläsartyper som stöds av MSAL deklareras i [MSALWebviewType-uppräkningen](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

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

Läs mer om [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)
