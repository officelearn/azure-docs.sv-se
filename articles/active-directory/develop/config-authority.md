---
title: Konfigurera MSAL för iOS och macOS för att använda olika identitets leverantörer | Microsoft Identity Platform
description: Lär dig hur du använder olika myndigheter som B2C, suveräna moln och gäst användare med MSAL för iOS och macOS.
services: active-directory
documentationcenter: ''
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 114e67e2dca7ba304cb92b21a894e045cbe0c9e9
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269095"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Anvisningar: Konfigurera MSAL för iOS och macOS för att använda olika identitets leverantörer

Den här artikeln visar hur du konfigurerar din Microsoft Authentication Library-app för iOS och macOS (MSAL) för olika myndigheter, till exempel Azure Active Directory (Azure AD), företags-till-konsument (B2C), suveräna moln och gäst användare.  I den här artikeln kan du vanligt vis tänka på en utfärdare som en identitets leverantör.

## <a name="default-authority-configuration"></a>Standard auktoritets konfiguration

`MSALPublicClientApplication`är konfigurerad med standard auktoritets-URL `https://login.microsoftonline.com/common`: en, som är lämplig för de flesta Azure Active Directory-scenarier (AAD). Om du inte implementerar avancerade scenarier som nationella moln eller arbetar med B2C behöver du inte ändra det.

> [!NOTE]
> Modern autentisering med Active Directory Federation Services (AD FS) som identitets leverantör (ADFS) stöds inte (se [ADFS för utvecklare](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers) för mer information). ADFS stöds via Federation.

## <a name="change-the-default-authority"></a>Ändra standard auktoritet

I vissa fall, t. ex. företags-till-konsument (B2C), kan du behöva ändra standard utfärdaren.

### <a name="b2c"></a>B2C

För att arbeta med B2C kräver [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) en annan auktoritets konfiguration. MSAL känner igen ett URL-format för auktoritet som B2C. Formatet känd B2C Authority är `https://<host>/tfp/<tenant>/<policy>`till exempel. `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` Du kan dock också använda andra URL: er som stöds av B2C genom att deklarera utfärdare som B2C-auktoritet.

För att stödja ett godtyckligt URL-format `MSALB2CAuthority` för B2C kan ställas in med en godtycklig URL, så här:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
SWIFT
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Alla B2C-myndigheter som inte använder standard formatet för B2C-utfärdare måste deklareras som kända myndigheter.

Lägg till varje annan B2C-auktoritet i listan med kända utfärdare även om myndigheterna bara skiljer sig åt i principen.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
SWIFT
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

När din app begär en ny princip måste auktoritets-URL: en ändras eftersom auktoritets-URL: en är olika för varje princip. 

Konfigurera ett B2C-program genom att `@property MSALAuthority *authority` ange en instans av `MSALB2CAuthority` i `MSALPublicClientApplicationConfig` innan du `MSALPublicClientApplication`skapar, så här:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
SWIFT
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Suveräna moln

Om din app körs i ett suveränt moln kan du behöva ändra auktoritets-URL: en `MSALPublicClientApplication`i. I följande exempel anges auktoritets-URL: en för att fungera med tyska AAD-molnet:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
SWIFT
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Du kan behöva skicka olika omfattningar till varje suverän moln. Vilka omfattningar som ska skickas beror på vilken resurs du använder. Du kan till exempel använda `"https://graph.microsoft.com/user.read"` i globalt moln och `"https://graph.microsoft.de/user.read"` i tyskt moln.

### <a name="signing-a-user-into-a-specific-tenant"></a>Signera en användare till en speciell klient

När auktoritets-URL: en `"login.microsoftonline.com/common"`är inställt på, kommer användaren att loggas in på sin hem klient. Vissa appar kan dock behöva signera användaren till en annan klient och vissa appar fungerar bara med en enda klient.

Om du vill signera användaren till en speciell klient konfigurerar `MSALPublicClientApplication` du med en speciell myndighet. Exempel:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Följande visar hur du signerar en användare till en speciell klient:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
SWIFT
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Myndigheter som stöds

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority` Klassen är den grundläggande abstrakta klassen för MSAL-auktoritets klasserna. Försök inte att skapa en instans av den `alloc` med `new`hjälp av eller. Skapa i stället en av dess underklasser direkt (`MSALAADAuthority`, `MSALB2CAuthority`) eller Använd fabriks metoden `authorityWithURL:error:` för att skapa underklasser med en auktoritets-URL.

`url` Använd egenskapen för att hämta en normaliserad auktoritets-URL. Extra parametrar och Sök vägs komponenter eller fragment som inte tillhör en instans finns inte i den returnerade normaliserade auktoritets-URL: en.

Följande är underklasser till som `MSALAuthority` du kan skapa en instans av, beroende på vilken auktoritet som ska användas.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`representerar en AAD-utfärdare. Auktoritets-URL: en ska ha följande format, `<port>` där är valfritt:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`representerar en B2C-utfärdare. Som standard ska URL: en för B2C-utfärdare ha följande format, `<port>` där är valfritt `https://<host>:<port>/tfp/<tenant>/<policy>`:. MSAL stöder dock även andra godtyckliga B2C-auktoritets format.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)
