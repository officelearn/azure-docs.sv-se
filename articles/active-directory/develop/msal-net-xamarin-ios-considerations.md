---
title: Xamarin iOS-överväganden (Microsoft Authentication Library för .NET) | Azure
description: Lär dig mer om att tänka på när du använder Xamarin iOS med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 054033c0fc9f1138ef9ecf7eaceca626f6f53423
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872840"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin iOS-/regionsspecifika överväganden med MSAL.NET
På Xamarin iOS finns det flera saker som du måste tänka på när du använder MSAL.NET

- [Kända problem med iOS 12 och autentisering](#known-issues-with-ios-12-and-authentication)
- [Åsidosätt och implementera `OpenUrl` funktionen i`AppDelegate`](#implement-openurl)
- [Aktivera nyckel rings grupper](#enable-keychain-access)
- [Aktivera delning av token cache](#enable-token-cache-sharing-across-ios-applications)
- [Aktivera nyckel rings åtkomst](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Kända problem med iOS 12 och autentisering
Microsoft har publicerat en [säkerhets rekommendation](https://github.com/aspnet/AspNetCore/issues/4647) för att tillhandahålla information om inkompatibilitet mellan iOS12 och vissa typer av autentisering. Inkompatibiliteten bryter mot sociala, WSFed och OIDC-inloggningar. Den här rekommendationen ger också vägledning om vilka utvecklare som kan göra för att ta bort aktuella säkerhets begränsningar som har lagts till av ASP.NET i sina program för att bli kompatibla med iOS12.  

När du utvecklar MSAL.NET-program på Xamarin iOS kan du se en oändlig loop när du försöker logga in på webbplatser från iOS 12 (liknar detta [ADAL-problem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Du kan också se en rast i ASP.NET Core OIDC-autentisering med iOS 12 Safari enligt beskrivningen i det här [webkit-problemet](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementera OpenUrl

Först måste du åsidosätta `OpenUrl` metoden för den `FormsApplicationDelegate` härledda klassen och anropet `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Du måste också definiera ett URL-schema, kräva behörigheter för din app för att anropa en annan app, ha ett särskilt format för omdirigerings-URL och registrera denna omdirigerings-URL i [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Aktivera nyckel rings åtkomst

Om du vill aktivera nyckel rings åtkomst måste ditt program ha en åtkomst grupp för nyckel ringar.
Du kan ange din åtkomst grupp för nyckel ringar med `WithIosKeychainSecurityGroup()` hjälp av API: et när du skapar ditt program enligt nedan:

Om du vill aktivera enkel inloggning måste du ange `PublicClientApplication.iOSKeychainSecurityGroup` egenskapen till samma värde i alla program.

Ett exempel på detta är att använda MSAL v3. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Rättigheterna. plist bör uppdateras för att se ut som följande XML-fragment:

Den här ändringen är *förutom* att aktivera nyckel rings åtkomst `Entitlements.plist` i filen, antingen med hjälp av åtkomst gruppen nedan eller din egen:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Ett exempel på detta är att använda MSAL v4. x:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

När du använder `WithIosKeychainSecurityGroup()` API: et lägger MSAL automatiskt till din säkerhets grupp i slutet av programmets "Team-ID" (AppIdentifierPrefix) eftersom när du skapar ditt program med hjälp av Xcode kommer det att göra samma sak. [Se dokumentation om iOS-berättigande för mer information](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). Det är därför du behöver uppdatera rättigheterna för att inkludera $ (AppIdentifierPrefix) innan nyckel rings åtkomst gruppen i rättigheten. plist.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Aktivera delning av token cache i iOS-program

Från MSAL 2. x kan du ange en åtkomst grupp för nyckel ringar som ska användas för att bevara token-cachen över flera program. Med den här inställningen kan du dela token-cachen mellan flera program som har samma nyckel rings grupp, inklusive de som har utvecklats med [ADAL.net](https://aka.ms/adal-net), MSAL.net Xamarin. iOS-program och inbyggda iOS-program som utvecklats med [ ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) eller [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Genom att dela token-cachen kan du använda enkel inloggning mellan alla program som använder samma nyckel rings åtkomst grupp.

Om du vill aktivera denna cache-delning måste du ange Använd metoden ' WithIosKeychainSecurityGroup () ' för att ställa in nyckel rings åtkomst gruppen till samma värde i alla program som delar samma cacheminne som visas i exemplet ovan.

Tidigare nämnde vi att MSAL lade till $ (AppIdentifierPrefix) när du använder `WithIosKeychainSecurityGroup()` API: et. Detta beror på att AppIdentifierPrefix eller "Team-ID" används för att säkerställa att endast program som görs av samma utgivare kan dela nyckel rings åtkomst.

> [!NOTE]
> **`KeychainSecurityGroup` Egenskapen är föråldrad.**
> 
> Tidigare var utvecklare tvungen att ta med TeamId-prefixet när `KeychainSecurityGroup` egenskapen användes från MSAL 2. x.
>
>  Från MSAL 2.7. x, när den nya `iOSKeychainSecurityGroup` egenskapen används, kommer MSAL att matcha TeamId-prefixet under körning. När du använder den här egenskapen ska värdet inte innehålla TeamId-prefixet.
>  Använd den nya `iOSKeychainSecurityGroup` egenskapen, som inte kräver att du anger TeamId, eftersom föregående `KeychainSecurityGroup` egenskap nu är föråldrad.

### <a name="use-microsoft-authenticator"></a>Använd Microsoft Authenticator

Programmet kan använda Microsoft Authenticator (en Broker) för att aktivera:

- Enkel inloggning (SSO). Användarna behöver inte logga in på varje program.
- Enhets identifiering. Genom att få åtkomst till enhets certifikatet som skapades på enheten när den var ansluten till arbets platsen. Programmet är redo om klient administratörerna aktiverar villkorlig åtkomst för enheterna.
- Verifiering av program identifiering. När ett program anropar Broker, skickar den sin omdirigerings-URL och Service Broker verifierar den.

Mer information om hur du aktiverar Service Broker finns i [använda Microsoft Authenticator eller Microsoft Intune företags Portal på Xamarin iOS-och Android-program](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Exempel som illustrerar Xamarin iOS-/regionsspecifika egenskaper

Mer information finns i stycket om [särskilt överväganden för iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) i följande exempel: s Readme.MD-fil:

Exempel | Plattform | Beskrivning
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En enkel Xamarin Forms-app som demonstrerar hur du använder MSAL för att autentisera MSA och Azure AD via Azure AD V 2.0-slutpunkten och få åtkomst till Microsoft Graph med den resulterande token.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
