---
title: Xamarin iOS överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Läs mer om hur du använder Xamarin iOS med Microsoft Authentication Library for .NET (MSAL.NET).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262716"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Överväganden för att använda Xamarin iOS med MSAL.NET
NÃ¤r microsoft authentication library for .NET (MSAL.NET) on Xamarin iOS, you should: 

- Åsidosätt och `OpenUrl` implementera `AppDelegate`funktionen i .
- Aktivera nyckelringsgrupper.
- Aktivera tokencachedelning.
- Aktivera nyckelringsåtkomst.
- Förstå kända problem med iOS 12 och autentisering.

## <a name="implement-openurl"></a>Implementera OpenUrl

Åsidosätt `OpenUrl` metoden för `FormsApplicationDelegate` den härledda klassen och anropet `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Här är ett exempel:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Gör även följande: 
* Definiera ett URL-schema.
* Kräv behörigheter för att appen ska kunna ringa en annan app.
* Har ett specifikt formulär för omdirigerings-URL:en.
* Registrera omdirigerings-URL:en i [Azure-portalen](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Aktivera nyckelringsåtkomst

Om du vill aktivera nyckelringsåtkomst kontrollerar du att programmet har en nyckelringsåtkomstgrupp. Du kan ange nyckelringsåtkomstgruppen när du skapar `WithIosKeychainSecurityGroup()` programmet med hjälp av API:et.

Om du vill dra nytta av cacheminnet och enkel inloggning (SSO) ställer du in nyckelringsåtkomstgruppen till samma värde i alla dina program.

I det här exemplet används MSAL 4.x i det här exemplet:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Aktivera även nyckelringsåtkomst `Entitlements.plist` i filen. Använd antingen följande åtkomstgrupp eller din egen åtkomstgrupp.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

När du `WithIosKeychainSecurityGroup()` använder API:et lägger MSAL automatiskt till din säkerhetsgrupp till`AppIdentifierPrefix`slutet av programmets *grupp-ID* ( ). MSAL lägger till din säkerhetsgrupp eftersom när du bygger ditt program i Xcode, kommer det att göra detsamma. Därför måste berättigandena `Entitlements.plist` i filen inkluderas `$(AppIdentifierPrefix)` före nyckelringsåtkomstgruppen.

Mer information finns i [dokumentationen för iOS-berättiganden](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Aktivera tokencachedelning mellan iOS-program

Med början i MSAL 2.x kan du ange en nyckelringsåtkomstgrupp för att bevara tokencachen i flera program. Med den här inställningen kan du dela tokencachen mellan flera program som har samma nyckelringsåtkomstgrupp. Du kan dela tokenpengarna mellan [ADAL.NET](https://aka.ms/adal-net) program, MSAL.NET Xamarin.iOS-program och inbyggda iOS-program som har utvecklats i [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) eller [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Genom att dela tokencachen tillåter du enkel inloggning (SSO) bland alla program som använder samma nyckelringsåtkomstgrupp.

Om du vill aktivera `WithIosKeychainSecurityGroup()` cachedelningen använder du metoden för att ange nyckelringsåtkomstgruppen till samma värde i alla program som delar samma cache. Det första kodexemplet i den här artikeln visar hur du använder metoden.

Tidigare i den här artikeln fick `$(AppIdentifierPrefix)` du veta `WithIosKeychainSecurityGroup()` att MSAL lägger till när du använder API: et. MSAL lägger till det `AppIdentifierPrefix` här elementet eftersom grupp-ID:t säkerställer att endast program som görs av samma utgivare kan dela nyckelringsåtkomst.

> [!NOTE]
> Egenskapen `KeychainSecurityGroup` är föråldrad.
> 
> Från och med MSAL 2.x tvingades utvecklare att inkludera `TeamId` prefixet när de använde egenskapen. `KeychainSecurityGroup` Men från och med MSAL 2.7.x, när du använder den nya `iOSKeychainSecurityGroup` egenskapen, löser MSAL prefixet `TeamId` under körning. När du använder den här egenskapen `TeamId` ska du inte inkludera prefixet i värdet. Prefixet krävs inte.
>
> Eftersom `KeychainSecurityGroup` egenskapen är föråldrad `iOSKeychainSecurityGroup` använder du egenskapen.

### <a name="use-microsoft-authenticator"></a>Använda Microsoft Authenticator

Ditt program kan använda Microsoft Authenticator som mäklare för att aktivera:

- **SSO**: När du aktiverar SSO behöver användarna inte logga in på varje program.
- **Enhetsidentifiering**: Använd enhetsidentifiering för att autentisera genom att komma åt enhetscertifikatet. Det här certifikatet skapas på enheten när det är anslutet till arbetsplatsen. Ditt program är klart om klientadministratörerna aktiverar villkorlig åtkomst som är relaterad till enheterna.
- **Verifiering av programidentifiering**: När ett program anropar mäklaren skickar den sin omdirigerings-URL. Mäklaren verifierar omdirigerings-URL:en.

Mer information om hur du aktiverar en mäklare finns i [Använda Microsoft Authenticator- eller Microsoft Intune Company Portal på Xamarin iOS- och Android-program](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Kända problem med iOS 12 och autentisering
Microsoft har publicerat en [säkerhetsrekommendation](https://github.com/aspnet/AspNetCore/issues/4647) om en inkompatibilitet mellan iOS 12 och vissa typer av autentisering. Inkompatibiliteten bryter sociala, WSFed och OIDC inloggningar. Säkerhetsrådgivningen hjälper utvecklare att förstå hur du tar bort ASP.NET säkerhetsbegränsningar från sina program för att göra dem kompatibla med iOS 12.  

När du utvecklar MSAL.NET program på Xamarin iOS kan du se en oändlig loop när du försöker logga in på webbplatser från iOS 12. Det här beteendet liknar det här [ADAL-problemet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Du kan också se en paus i ASP.NET Core OIDC-autentisering med iOS 12 Safari. Mer information finns i det här [WebKit-problemet](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Nästa steg

Information om egenskaper för Xamarin iOS finns i [iOS-specifika överväganden](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) i följande exempels README.md fil:

Exempel | Plattform | Beskrivning
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Universell Windows-plattform (UWP) | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera Microsofts personliga konton och Azure AD via slutpunkten för Azure AD 2.0. Appen visar också hur du använder den resulterande token för att komma åt Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->