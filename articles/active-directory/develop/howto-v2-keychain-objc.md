---
title: Konfigurera nyckelring
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar nyckelring så att appen kan cachelagra token i nyckelringen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085864"
---
# <a name="configure-keychain"></a>Konfigurera nyckelring

När [Microsoft Authentication Library for iOS och macOS](msal-overview.md) (MSAL) loggar in en användare eller uppdaterar en token försöker den cachelagra token i nyckelringen. Cachelagringstoken i nyckelringen gör att MSAL kan tillhandahålla tyst enkel inloggning (SSO) mellan flera appar som distribueras av samma Apple-utvecklare. SSO uppnås via nyckelringsåtkomstgruppernas funktionalitet. Mer information finns i [Dokumentationen för Apples nyckelringsobjekt](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc).

Den här artikeln beskriver hur du konfigurerar apprättigheter så att MSAL kan skriva cachelagrade token till iOS- och macOS-nyckelring.

## <a name="default-keychain-access-group"></a>Standardgrupp för nyckelringsåtkomst

### <a name="ios"></a>iOS

MSAL på iOS `com.microsoft.adalcache` använder åtkomstgruppen som standard. Det här är den delade åtkomstgruppen som används av ADAL-SDK:er (Både MSAL och Azure AD Authentication Library) och säkerställer den bästa enskilda inloggningsupplevelsen (SSO) mellan flera appar från samma utgivare.

På iOS `com.microsoft.adalcache` lägger du till nyckelringsgruppen i appens berättigande i XCode under**Nyckelringsdelning** **Capabilities** > av **projektinställningar** > 

### <a name="macos"></a>macOS

MSAL på macOS använder `com.microsoft.identity.universalstorage` åtkomstgrupp som standard.

På grund av macOS-nyckelringsbegränsningar `access group` översätter MSAL:s inte direkt till attributet keychain access group (se [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) på macOS 10.14 och tidigare. Det beter sig dock på samma sätt ur ett SSO-perspektiv genom att se till att flera program som distribueras av samma Apple-utvecklare kan ha tyst SSO.

På macOS 10.15 och framåt (macOS Catalina) använder MSAL nyckelringsåtkomstgruppattribut för att uppnå tyst SSO, på samma sätt som iOS.

## <a name="custom-keychain-access-group"></a>Anpassad nyckelringsåtkomstgrupp

Om du vill använda en annan nyckelringsåtkomstgrupp kan du skicka `MSALPublicClientApplicationConfig` din `MSALPublicClientApplication`anpassade grupp när du skapar innan du skapar , så här:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Inaktivera nyckelringsdelning

Om du inte vill dela SSO-tillstånd mellan flera appar eller använda någon nyckelringsåtkomstgrupp inaktiverar du nyckelringsdelning genom att skicka programpaket-ID:t som nyckelringGrupp:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Hantera -34018-fel (det gick inte att ange objektet i nyckelring)

Fel -34018 innebär normalt att nyckelringen inte har konfigurerats korrekt. Kontrollera att nyckelringsåtkomstgruppen som har konfigurerats i MSAL matchar den som konfigurerats i berättiganden.

## <a name="ensure-your-application-is-properly-signed"></a>Se till att din ansökan är korrekt signerad

På macOS kan program köras utan att signeras av utvecklaren. Medan de flesta av MSAL: s funktioner kommer att fortsätta att fungera, SSO via nyckelring tillgång kräver ansökan undertecknas. Om du upplever flera nyckelringsuppmaningar kontrollerar du att programmets signatur är giltig.

## <a name="next-steps"></a>Nästa steg

Läs mer om nyckelringsåtkomstgrupper i Apples [delningsåtkomst till nyckelringsobjekt bland](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) en artikel med appar.
