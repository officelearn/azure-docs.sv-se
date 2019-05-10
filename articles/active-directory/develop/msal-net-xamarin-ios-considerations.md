---
title: Överväganden för Xamarin iOS (Microsoft Authentication Library för .NET) | Azure
description: Läs mer om specifika överväganden när du använder Xamarin iOS med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb07fa00b9e1d917cb64df18fff6466dd5c0193d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406995"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin iOS-specifika överväganden med MSAL.NET
Det finns flera saker som du måste tänka på när du använder MSAL.NET på Xamarin iOS

- [Kända problem med iOS 12 och autentisering](#known-issues-with-ios-12-and-authentication)
- [Åsidosätta och implementera de `OpenUrl` fungera i den `AppDelegate`](#implement-openurl)
- [Aktivera nyckelringsgrupper](#enable-keychain-groups)
- [Aktivera delning av token-cache](#enable-token-cache-sharing-across-ios-applications)
- [Aktivera Nyckelhanterare](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Kända problem med iOS 12 och autentisering
Microsoft har publicerat en [security advisory](https://github.com/aspnet/AspNetCore/issues/4647) att ge information om inkompatibilitet mellan iOS12 och vissa typer av autentisering. Inkompatibilitet radbrytningar social, WSFed och OIDC-inloggningar. Det här dokumentet innehåller också information om utvecklare kan göra att ta bort aktuell säkerhetsbegränsningar som lagts till sina program för att bli kompatibel med iOS12 av ASP.NET.  

När du utvecklar MSAL.NET program på Xamarin iOS, du kan se en oändlig loop vid försök att logga in till webbplatser från iOS 12 (ungefär så här [ADAL problemet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Du kan också se ett avbrott i ASP.NET Core OIDC-autentisering med iOS 12 Safari enligt beskrivningen i det här [WebKit problemet](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implement OpenUrl

Du måste först att åsidosätta den `OpenUrl` -metoden för den `FormsApplicationDelegate` härledda klassen och anropa `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Du måste också definiera ett URL-schema, kräver behörigheter för din app för att anropa en annan app, har ett visst formulär för omdirigerings-URL och registrera den här omdirigerings-URL i den [Azure-portalen](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Aktivera Nyckelringsgrupper

För att göra token cachelagra arbete och har den `AcquireTokenSilentAsync` metoden work flera steg måste följas:
1. Aktivera Nyckelhanterare i din *`* Entitlements.plist* filen och ange den **Nyckelringsgrupper** i paket-ID.
2. Välj *`*Entitlements.plist*`* fil i den **anpassade rättigheter** i iOS-projektet alternativ fönstrets **paket signering visa**.
3. När du registrerar ett certifikat, kontrollera att XCode använder samma Apple-ID.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Aktivera token-cache delning över iOS-program

Från och med MSAL 2.x kan du ange en säkerhetsgrupp i nyckelringen ska användas för att spara token-cache i flera program. Detta kan du dela tokencache mellan flera program som har samma nyckelring-säkerhetsgrupp, inklusive de som har utvecklats med [ADAL.NET](https://aka.ms/adal-net), MSAL.NET Xamarin.iOS-program och interna iOS-program som utvecklats med [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) eller [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Delar token-cache kan enkel inloggning (SSO) mellan alla program som använder samma nyckelring säkerhetsgrupp.

Om du vill aktivera enkel inloggning, måste du ange den `PublicClientApplication.iOSKeychainSecurityGroup` egenskapen till samma värde i alla program.

Ett exempel på detta med MSAL v3.x skulle vara:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Ett exempel på detta med MSAL v2.7.x är:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> Den `KeychainSecurityGroup` egenskapen är inaktuell. Tidigare i MSAL 2.x kan utvecklare var tvungen att inkludera prefixet TeamId när du använder den `KeychainSecurityGroup` egenskapen. 
> 
> Nu, från och med MSAL 2.7.x MSAL löser prefixet TeamId under körning när du använder den `iOSKeychainSecurityGroup` egenskapen. När du använder den här egenskapen får värdet inte innehålla prefixet TeamId. 
> 
> Använd den nya `iOSKeychainSecurityGroup` egenskapen, som inte kräver utvecklare att tillhandahålla TeamId. Den `KeychainSecurityGroup` egenskapen är nu föråldrad. 

## <a name="enable-keychain-access"></a>Aktivera Nyckelhanterare

I MSAL 2.x och ADAL 4.x TeamId används för åtkomst till nyckelringen, vilket gör att autentiseringsbibliotek att tillhandahålla enkel inloggning (SSO) mellan program från samma utgivare. 

Vad är den [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? Det är en unik identifierare (företag eller personal) i App Store. AppId är unikt för en app. Om du har fler än en app, TeamId för alla appar blir detsamma, men AppId kommer att bli annorlunda. Gruppen med Nyckelhanterare är prefixet TeamId automatiskt för varje grupp av systemet. Det är hur Operativsystemet tillämpar att appar från samma utgivare kan komma åt delade nyckelringen. 

När du initierar den `PublicClientApplication`, om du får en `MsalClientException` med meddelandet: `TeamId returned null from the iOS keychain...`, behöver du göra följande i Xamarin iOS-appen:

1. I VS, under felsökningsfliken, går du till nameOfMyApp.iOS egenskaper...
2. Gå sedan till iOS-paket signering 
3. Under anpassade rättigheter, klickar du på den... och markera filen Entitlements.plist från din app
4. Du bör ha den här raden ingår nu i filen csproj för iOS-appen: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Återskapa** projektet.

Det här är *dessutom* aktivering av Nyckelhanterare i den `Entitlements.plist` fil, använda antingen den nedan åtkomstgruppen eller dina egna:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i den [iOS specifika överväganden](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) punkt i följande exempel readme.md-filen:

Exempel | Plattform | Beskrivning 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera MSA och Azure AD via AAD V2.0-slutpunkten och få åtkomst till Microsoft Graph med resulterande token. <br>![Topologi](media/msal-net-xamarin-ios-considerations/topology.png)