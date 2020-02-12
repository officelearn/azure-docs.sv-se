---
title: Xamarin iOS-överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Xamarin iOS med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132476"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Att tänka på när du använder Xamarin iOS med MSAL.NET
När du använder Microsoft Authentication Library för .NET (MSAL.NET) på Xamarin iOS bör du: 

- Åsidosätt och implementera funktionen `OpenUrl` i `AppDelegate`.
- Aktivera nyckel rings grupper.
- Aktivera delning av token cache.
- Aktivera nyckel rings åtkomst.
- Förstå kända problem med iOS 12 och autentisering.

## <a name="implement-openurl"></a>Implementera OpenUrl

Åsidosätt `OpenUrl`-metoden för den `FormsApplicationDelegate` härledda klassen och anrops `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Här är ett exempel:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Utför även följande uppgifter: 
* Definiera ett URL-schema.
* Kräv att appen ska anropa en annan app.
* Ha ett speciellt format för omdirigerings-URL: en.
* Registrera omdirigerings-URL: en i [Azure Portal](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Aktivera nyckel rings åtkomst

Om du vill aktivera nyckel rings åtkomst kontrollerar du att programmet har en åtkomst grupp för nyckel ringar. Du kan ställa in åtkomst gruppen för nyckel ringar när du skapar programmet med hjälp av `WithIosKeychainSecurityGroup()`-API: et.

Om du vill dra nytta av cachen och enkel inloggning (SSO) anger du åtkomst gruppen för nyckel ringar till samma värde i alla dina program.

I det här exemplet i installations programmet används MSAL 4. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Aktivera även nyckel rings åtkomst i `Entitlements.plist`s filen. Använd antingen följande åtkomst grupp eller din egen åtkomst grupp.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

När du använder `WithIosKeychainSecurityGroup()`-API: t lägger MSAL automatiskt till din säkerhets grupp i slutet av programmets *Team-ID* (`AppIdentifierPrefix`). MSAL lägger till din säkerhets grupp eftersom när du skapar ditt program i Xcode. Det är därför som rättigheterna i `Entitlements.plist`-filen måste innehålla `$(AppIdentifierPrefix)` före åtkomst gruppen för nyckel ringar.

Mer information finns i dokumentationen om [iOS-rättigheter](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Aktivera delning av token cache i iOS-program

Från och med MSAL 2. x kan du ange en åtkomst grupp för nyckel ringar för att bevara token-cachen över flera program. Med den här inställningen kan du dela token-cachen mellan flera program som har samma åtkomst grupp för nyckel ringar. Du kan dela token-kontanter bland [ADAL.net](https://aka.ms/adal-net) -program, MSAL.net Xamarin. iOS-program och inbyggda iOS-program som har utvecklats i [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) eller [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Genom att dela token cache kan du tillåta enkel inloggning (SSO) bland alla program som använder samma nyckel rings åtkomst grupp.

Om du vill aktivera denna cache-delning använder du metoden `WithIosKeychainSecurityGroup()` för att ange åtkomst gruppen för nyckel ringar till samma värde i alla program som delar samma cacheminne. Det första kod exemplet i den här artikeln visar hur du använder-metoden.

Tidigare i den här artikeln har du lärt dig att MSAL lägger till `$(AppIdentifierPrefix)` när du använder `WithIosKeychainSecurityGroup()` API. MSAL lägger till det här elementet eftersom grupp-ID: t `AppIdentifierPrefix` garanterar att endast program som har skapats av samma utgivare kan dela nyckel rings åtkomst.

> [!NOTE]
> Egenskapen `KeychainSecurityGroup` är föråldrad.
> 
> Från och med MSAL 2. x tvingades utvecklare att ta med `TeamId`-prefixet när de använde egenskapen `KeychainSecurityGroup`. Men från och med MSAL 2.7. x, och om du använder egenskapen New `iOSKeychainSecurityGroup`, matchar MSAL prefixet `TeamId` under körning. När du använder den här egenskapen ska du inte inkludera `TeamId`-prefixet i värdet. Prefixet krävs inte.
>
> Eftersom `KeychainSecurityGroup`-egenskapen är föråldrad använder du egenskapen `iOSKeychainSecurityGroup`.

### <a name="use-microsoft-authenticator"></a>Använd Microsoft Authenticator

Programmet kan använda Microsoft Authenticator som en koordinator för att aktivera:

- **SSO**: när du aktiverar SSO behöver användarna inte logga in på varje program.
- **Enhets identifiering**: Använd enhets identifiering för att autentisera genom att komma åt enhets certifikatet. Det här certifikatet skapas på enheten när det är anslutet till arbets platsen. Programmet är redo om klient administratörerna aktiverar villkorlig åtkomst för enheterna.
- **Verifiering av program identifiering**: när ett program anropar Service Broker skickas dess omdirigerings-URL. Service Broker verifierar omdirigerings-URL: en.

Mer information om hur du aktiverar en Service Broker finns i [använda Microsoft Authenticator eller Microsoft Intune företagsportal på Xamarin iOS-och Android-program](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Kända problem med iOS 12 och autentisering
Microsoft har publicerat en [säkerhets rekommendation](https://github.com/aspnet/AspNetCore/issues/4647) om inkompatibilitet mellan iOS 12 och vissa typer av autentisering. Inkompatibiliteten bryter mot sociala, WSFed och OIDC inloggningar. Säkerhets rekommendationen hjälper utvecklare att förstå hur de tar bort ASP.NET säkerhets begränsningar från sina program för att göra dem kompatibla med iOS 12.  

När du utvecklar MSAL.NET-program på Xamarin iOS kan du se en oändlig loop när du försöker logga in på webbplatser från iOS 12. Det här beteendet liknar detta [ADAL-problem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Du kan också se en rast i ASP.NET Core OIDC-autentisering med iOS 12 Safari. Mer information finns i det här [webkit-problemet](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Nästa steg

Information om egenskaper för Xamarin iOS finns i stycket om [iOS-/regionsspecifika överväganden](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) i följande exempel: s Readme.MD-fil:

Exempel | Plattform | Beskrivning
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universell Windows-plattform (UWP) | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera Microsoft-personliga konton och Azure AD via Azure AD 2,0-slutpunkten. Appen visar också hur du använder den resulterande token för att få åtkomst till Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->