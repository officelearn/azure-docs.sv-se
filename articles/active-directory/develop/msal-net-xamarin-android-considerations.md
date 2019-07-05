---
title: Överväganden för Xamarin Android (Microsoft Authentication Library för .NET) | Azure
description: Läs mer om specifika överväganden när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
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
ms.openlocfilehash: 357c83cfd0ae3fed8b13419e72f50fcb90c04186
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550658"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Överväganden för Xamarin Android-specifika med MSAL.NET
Den här artikeln beskriver specifika överväganden när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).

Den här artikeln är MSAL.NET 3.x. Om du är intresserad av MSAL.NET 2.x kan se [Xamarin Android som anges i MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>Ange den överordnade aktiviteten

Du måste ange den överordnade aktiviteten så att token hämtar tillbaka när interaktionen har skett på Xamarin.Android.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Att säkerställa kontroll går tillbaka till MSAL när den interaktiva delen av authentication flow slutar
På Android, du behöver åsidosätta den `OnActivityResult` -metoden för den `Activity` och anropar metoden SetAuthenticationContinuationEventArgs AuthenticationContinuationHelper MSAL-klassen.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Raden garanterar att kontrollen går tillbaka till MSAL när den interaktiva delen av autentiseringsflödet avslutades.

## <a name="update-the-android-manifest"></a>Uppdatera Android-manifestet
Den `AndroidManifest.xml` bör innehålla följande värden:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Använd inbäddade webbvyn (valfritt)

Som standard använder MSAL.NET webbläsare system, där du kan få enkel inloggning med webbprogram och andra appar. I sällsynta fall, kan du ange att du vill använda den inbäddade webbvyn. Mer information finns i [MSAL.NET använder en webbläsare](msal-net-web-browsers.md) och [Android system webbläsare](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Felsökning
Om du skapar ett nytt program för Xamarin.Forms och Lägg till en referens till MSAL.Net NuGet-paketet, fungerar det bara.
Om du vill uppgradera ett befintligt Xamarin.Forms-program till MSAL.NET Förhandsgranska 1.1.2 eller senare version problem kan uppstå.

Om du vill felsöka problemen, bör du:
- Uppdatera befintliga MSAL.NET NuGet-paketet till MSAL.NET preview 1.1.2 eller senare
- Kontrollera att Xamarin.Forms automatiskt uppdaterad till version 2.5.0.122203 (om inte, uppdatera till den här versionen)
- Kontrollera att Xamarin.Android.Support.v4 automatiskt uppdaterad till version 25.4.0.2 (om inte, uppdatera till den här versionen)
- Alla Xamarin.Android.Support paket ska vara inriktad version 25.4.0.2
- Rensa/återskapning
- Försöka ange det maximala parallella projektet versioner till 1 i Visual Studio (alternativ -> projekt och lösningar > Skapa och kör -> maximalt antal parallella projekt versioner)
- Om du skapar från kommandoraden kan du också försöka ta bort /m från kommandot om du använder den.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Fel: Namnet 'AuthenticationContinuationHelper' inte finns i den aktuella kontexten

Detta beror troligen på att Visual Studio inte uppdaterades korrekt Android.csproj*-filen. Ibland den  **\<HintPath >** filepath innehåller felaktigt netstandard13 i stället för **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Nästa steg

Mer information och exempel finns i den [Android specifika överväganden](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) punkt i följande exempel readme.md-filen:

| Exempel | Plattform | Beskrivning |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera MSA och Azure AD via AADD v2.0-slutpunkten och få åtkomst till Microsoft Graph med resulterande token. <br>![Topologi](media/msal-net-xamarin-android-considerations/topology.png) |