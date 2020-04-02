---
title: Delat enhetsläge för iOS-enheter
titleSuffix: Microsoft identity platform | Azure
description: Lär dig hur du aktiverar delat enhetsläge så att Firstline-arbetare kan dela en iOS-enhet
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550258"
---
# <a name="shared-device-mode-for-ios-devices"></a>Delat enhetsläge för iOS-enheter

> [!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Firstline Workers som butiksmedarbetare, flygbesättningsmedlemmar och fältservicearbetare använder ofta en delad mobil enhet för att utföra sitt arbete. Dessa delade enheter kan medföra säkerhetsrisker om användarna delar sina lösenord eller PIN-koder, avsiktligt eller inte, för att komma åt kund- och affärsdata på den delade enheten.

Med läget delad enhet kan du konfigurera en iOS 13 eller högre enhet så att den kan delas enklare och säkrare av anställda. Medarbetare kan logga in och komma åt kundinformation snabbt. När de är klara med skiftet eller uppgiften kan de logga ut från enheten och den är omedelbart klar att användas av nästa medarbetare.

Läget delad enhet ger också Microsoft-identitetsbaserad hantering av enheten.

Den här funktionen använder [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md) för att hantera användarna på enheten och för att distribuera [Microsoft Enterprise SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Skapa en app för delat enhetsläge

Så här skapar du en app för delat enhetsläge, utvecklare och administratörer för molnenheter:

1. **Programutvecklare** skriver en app med ett konto (appar med flera konton stöds inte i delat enhetsläge) och skriver kod för att hantera saker som ut logga ut på delad enhet.

1. **Enhetsadministratörer** förbereder enheten så att den delas med hjälp av en MDM-leverantör (Mobile Device Management) som Microsoft Intune för att hantera enheterna i organisationen. MDM skickar Microsoft Authenticator-appen till enheterna och aktiverar "Delat läge" för varje enhet via en profiluppdatering till enheten. Den här inställningen för delat läge är vad som ändrar beteendet för de appar som stöds på enheten. Den här konfigurationen från MDM-providern anger läget för den delade enheten för enheten och aktiverar [Plugin-programmet Microsoft Enterprise SSO för Apple-enheter](apple-sso-plugin.md) som krävs för läget för delade enheter.

1. [Krävs**endast under offentlig förhandsversion]** En användare med rollen [Cloud Device Administrator](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) måste sedan starta Microsoft [Authenticator-appen](../user-help/user-help-auth-app-overview.md) och ansluta till sin enhet till organisationen.

    Så här konfigurerar du medlemskap i dina organisationsroller i Azure-portalen: **Azure Active Directory** > **Roles och Administratörer** > **Cloud Device Administrator**

Följande avsnitt hjälper dig att uppdatera programmet så att det stöder läget för delade enheter.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Använda Intune för att aktivera delat enhetsläge & SSO-tillägg

> [!NOTE]
> Följande steg krävs endast under offentlig förhandsversion.

Enheten måste konfigureras för att stödja läget för delade enheter. Det måste ha iOS 13 + installerat och vara MDM-inskrivna. MDM-konfiguration måste också aktivera [Microsoft Enterprise SSO-plugin-program för Apple-enheter](apple-sso-plugin.md). Mer information om SSO-tillägg finns i [Apple-videon](https://developer.apple.com/videos/play/tech-talks/301/).

1. I Intune Configuration Portal ber du enheten att aktivera [Microsoft Enterprise SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md) med följande konfiguration:

    - **Typ**: Omdirigering
    - **Tilläggs-ID**: com.microsoft.azureauthenticator.ssoextension
    - **Team-ID**: SGGM6D27TK
    - **Webbadresser:**https://login.microsoftonline.com
    - Ytterligare data för att konfigurera:
      - Nyckel: sharedDeviceMode
      - Typ: Boolean
      - Värde: Sant

    Mer information om hur du konfigurerar med Intune finns i dokumentationen till [Intune-konfigurationen](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

1. Konfigurera sedan DIN MDM så att den skickar Microsoft Authenticator-appen till enheten via en MDM-profil.

    Ange följande konfigurationsalternativ för att aktivera läget Delad enhet:

    - Konfiguration 1:
      - Nyckel: sharedDeviceMode
      - Typ: Boolean
      - Värde: Sant

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Ändra ditt iOS-program för att stödja delat enhetsläge

Användarna är beroende av att du ser till att deras data inte läcker ut till en annan användare. Följande avsnitt innehåller användbara signaler som visar ditt program att en ändring har skett och bör hanteras.

Du är ansvarig för att kontrollera användarens tillstånd på enheten varje gång appen används och sedan rensa den tidigare användarens data. Detta inkluderar om den laddas om från bakgrunden i multi-tasking.

Vid en användarändring bör du se till att både den föregående användarens data rensas och att alla cachelagrade data som visas i ditt program tas bort. Vi rekommenderar starkt att du och ditt företag genomför en säkerhetsgranskningsprocess efter att du har uppdaterat appen för att stödja läget för delade enheter.

### <a name="detect-shared-device-mode"></a>Identifiera läget för delade enheter

Det är viktigt för ditt program att identifiera läget för delade enheter. Många program kräver en ändring av användarupplevelsen (UX) när programmet används på en delad enhet. Ditt program kan till exempel ha en "Anmälningsfunktion", vilket inte är lämpligt för en Firstline Worker eftersom de sannolikt redan har ett konto. Du kanske också vill lägga till extra säkerhet i programmets hantering av data om det är i delat enhetsläge.

Använd `getDeviceInformationWithParameters:completionBlock:` API:et `MSALPublicClientApplication` för att avgöra om en app körs på en enhet i delat enhetsläge.

Följande kodavsnitt visar exempel på hur `getDeviceInformationWithParameters:completionBlock:` du använder API:et.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Hämta den inloggade användaren och ta reda på om en användare har ändrats på enheten

En annan viktig del av att stödja läget för delade enheter är att bestämma användarens tillstånd på enheten och rensa programdata om en användare har ändrats eller om det inte finns någon användare alls på enheten. Du är ansvarig för att se till att data inte läcker ut till en annan användare.

Du kan `getCurrentAccountWithParameters:completionBlock:` använda API för att fråga det inloggade kontot på enheten.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Logga in en användare globalt

När en enhet konfigureras som en delad enhet `acquireTokenWithParameters:completionBlock:` kan programmet anropa API:et för att logga in på kontot. Kontot är tillgängligt globalt för alla kvalificerade appar på enheten efter att den första appen har loggat in på kontot.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Logga ut en användare globalt

Följande kod tar bort det inloggade kontot och rensar cachelagrade token från inte bara appen utan även från enheten som är i läget för den delade enheten. Det rensar dock inte *data* från din ansökan. Du måste rensa data från ditt program, samt rensa alla cachelagrade data som ditt program kan visa för användaren.

#### <a name="clear-browser-state"></a>Rensa webbläsartillstånd

> [!NOTE]
> Följande steg krävs endast under offentlig förhandsversion.

I den här offentliga förhandsversionen rensar [Microsoft Enterprise SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md) tillstånd endast för program. Det är inte klart tillstånd på Webbläsaren Safari. Vi rekommenderar att du manuellt rensar webbläsarsessionen för att säkerställa att inga spår av användartillstånd lämnas kvar. Du kan använda `signoutFromBrowser` den valfria egenskapen som visas nedan för att rensa eventuella cookies. Detta gör att webbläsaren en kortstart på enheten.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Nästa steg

Om du vill se läget för delad enhet i praktiken innehåller följande kodexempel på GitHub ett exempel på hur du kör en Firstline Worker-app på en iOS-enhet i delat enhetsläge:

[MSAL iOS Swift Microsoft Graph API-exempel](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
