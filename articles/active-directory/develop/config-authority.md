---
title: Konfigurera identitetsleverantörer (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du använder olika myndigheter som B2C, suveräna moln och gästanvändare, med MSAL för iOS och macOS.
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
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085203"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Så här konfigurerar du MSAL för iOS och macOS för att använda olika identitetsleverantörer

I den här artikeln visas hur du konfigurerar microsoft-autentiseringsbiblioteksappen för iOS och macOS (MSAL) för olika myndigheter som Azure Active Directory (Azure AD), Business-to-Consumer (B2C), suveräna moln och gästanvändare.  I den här artikeln kan du i allmänhet tänka på en myndighet som en identitetsleverantör.

## <a name="default-authority-configuration"></a>Standardutfärdarkonfiguration

`MSALPublicClientApplication`konfigureras med en standardprincip-URL `https://login.microsoftonline.com/common`för , som är lämplig för de flesta Azure Active Directory-scenarier (AAD). Om du inte implementerar avancerade scenarier som nationella moln eller arbetar med B2C behöver du inte ändra det.

> [!NOTE]
> Modern autentisering med Active Directory Federation Services som identitetsprovider (ADFS) stöds inte (se [ADFS för utvecklare](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) för mer information). ADFS stöds via federationen.

## <a name="change-the-default-authority"></a>Ändra standardutfärdaren

I vissa fall, till exempel B2C (Business-to-Consumer), kan du behöva ändra standardmyndigheten.

### <a name="b2c"></a>B2C

För att arbeta med B2C kräver [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) en annan behörighetskonfiguration. MSAL känner igen en myndighet URL-format som B2C av sig själv. Det erkända B2C-auktoritetsformatet är `https://<host>/tfp/<tenant>/<policy>`, till exempel `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`. Du kan dock också använda andra B2C-myndighetsadresser som stöds genom att uttryckligen deklarera behörighet som B2C-myndighet.

Om du vill stödja ett godtyckligt URL-format för B2C `MSALB2CAuthority` kan du ställas in med en godtycklig URL, så här:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Alla B2C-myndigheter som inte använder standardformatet B2C-auktoritet måste deklareras som kända myndigheter.

Lägg till varje annan B2C-myndighet i listan över kända myndigheter, även om myndigheterna endast skiljer sig åt i politiken.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

När appen begär en ny princip måste behörighetsadressen ändras eftersom behörighets-URL:en är olika för varje princip. 

Om du vill konfigurera ett `@property MSALAuthority *authority` B2C-program anger du in med en instans av `MSALB2CAuthority` in `MSALPublicClientApplicationConfig` innan du skapar `MSALPublicClientApplication`, så här:

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
Swift
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

Om din app körs i ett suveränt moln kan `MSALPublicClientApplication`du behöva ändra behörighetsadressen i . I följande exempel anges behörighets-URL:en för att arbeta med det tyska AAD-molnet:

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
Swift
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

Du kan behöva skicka olika scope till varje suveränt moln. Vilka scope som ska skickas beror på vilken resurs du använder. Du kan till `"https://graph.microsoft.com/user.read"` exempel använda i `"https://graph.microsoft.de/user.read"` moln över hela världen och i tyska molnet.

### <a name="signing-a-user-into-a-specific-tenant"></a>Signera en användare i en viss klient

När behörighets-URL:en är inställd `"login.microsoftonline.com/common"`på loggas användaren in i sin hemklient. Vissa appar kan dock behöva signera användaren i en annan klientorganisation och vissa appar fungerar bara med en enda klientorganisation.

Konfigurera `MSALPublicClientApplication` med en specifik behörighet om du vill signera användaren i en viss klientorganisation. Ett exempel:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Följande visar hur du signerar en användare i en viss klient:

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
Swift
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

Klassen `MSALAuthority` är basabstratklassen för MSAL-auktoritetsklasserna. Försök inte att skapa instans `alloc` av `new`den med eller . Skapa i stället en av dess`MSALAADAuthority`underklasser direkt ( , `MSALB2CAuthority`) eller använd fabriksmetoden `authorityWithURL:error:` för att skapa underklasser med hjälp av en behörighets-URL.

Använd `url` egenskapen för att få en normaliserad auktoritets-URL. Extra parametrar och sökvägskomponenter eller fragment som inte ingår i behörigheten finns inte i url:en för returnerade normaliserade behörighet.

Följande är underklasser `MSALAuthority` som du kan instansiera beroende på vilken myndighet som vill använda.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`representerar en AAD-myndighet. Myndigheten url bör vara i följande `<port>` format, där är valfritt:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`representerar en B2C-myndighet. Som standard bör B2C-authority url vara i `<port>` följande `https://<host>:<port>/tfp/<tenant>/<policy>`format, där är valfritt: . MSAL stöder dock även andra godtyckliga B2C-auktoritetsformat.

## <a name="next-steps"></a>Nästa steg

Läs mer om [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)
