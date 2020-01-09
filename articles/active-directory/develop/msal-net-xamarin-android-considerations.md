---
title: Xamarin Android-överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 678b581e09fe1eac49e4f2bf07eabbbc944c8d4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424156"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-/regionsspecifika överväganden med MSAL.NET
I den här artikeln beskrivs olika aspekter när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Ange överordnad aktivitet

På Xamarin. Android måste du ange den överordnade aktiviteten så att token blir tillbaka när interaktionen har skett.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Du kan också ange detta på PublicClientApplication-nivån (i MSAL 4.2 +) via ett återanrop.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

En rekommendation är att använda CurrentActivityPlugin [här](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Sedan skulle din PublicClientApplication Builder-kod se ut så här:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Se till att kontrollen går tillbaka till MSAL när den interaktiva delen av autentiserings flödet slutar
På Android måste du åsidosätta `OnActivityResult`-metoden för `Activity` och anropa SetAuthenticationContinuationEventArgs-metoden för AuthenticationContinuationHelper MSAL-klassen.

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
Den raden ser till att kontrollen går tillbaka till MSAL när den interaktiva delen av autentiseringen har slutförts.

## <a name="update-the-android-manifest"></a>Uppdatera Android-manifestet
`AndroidManifest.xml` ska innehålla följande värden:
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

Du kan också [skapa aktiviteten i kod](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) och inte redigera `AndroidManifest.xml`manuellt. För det måste du skapa en klass som har attributet `Activity` och `IntentFilter`. En klass som representerar samma värden i ovanstående XML skulle vara:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X-manifest

Den kod som genereras av XamarinForms 4.3. x anger `package` attributet som ska `com.companyname.{appName}` i `AndroidManifest.xml`. Du kanske vill ändra värdet till samma som `MainActivity.cs` namn området, om du använder `DataScheme` som `msal{client_id}`.

## <a name="use-the-embedded-web-view-optional"></a>Använda den inbäddade webb visningen (valfritt)

Som standard använder MSAL.NET system webbläsare, vilket gör att du kan få SSO med webb program och andra appar. I vissa sällsynta fall kanske du vill ange att du vill använda den inbäddade webb visningen. Mer information finns i [MSAL.net använder en](msal-net-web-browsers.md) webbläsare och en webbläsare för [Android-systemet](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Felsöka
Om du skapar ett nytt Xamarin. Forms-program och lägger till en referens till MSAL.Net NuGet-paketet fungerar det bara.
Men om du vill uppgradera ett befintligt Xamarin. Forms-program till MSAL.NET Preview 1.1.2 eller senare kan det uppstå build-problem.

För att felsöka de här problemen bör du:
- Uppdatera det befintliga MSAL.NET NuGet-paketet till MSAL.NET Preview 1.1.2 eller senare
- Kontrol lera att Xamarin. Forms uppdateras automatiskt till version 2.5.0.122203 (om inte, uppdatera till den här versionen)
- Kontrol lera att Xamarin. Android. support. v4 uppdateras automatiskt till version 25.4.0.2 (om inte, uppdatera till den här versionen)
- Alla Xamarin. Android. support paket ska vara mål version 25.4.0.2
- Rensa/återskapa
- Försök att ställa in Max Parallel Project-versioner till 1 i Visual Studio (alternativ-> projekt och lösningar – > build och Run-> maximalt antal parallella projekt versioner)
- Om du skapar från kommando raden kan du prova att ta bort/m från kommandot om du använder det.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Fel: namnet ' AuthenticationContinuationHelper ' finns inte i den aktuella kontexten

Detta beror förmodligen på att Visual Studio inte har uppdaterat Android. CSPROJ *-filen på rätt sätt. Ibland innehåller **\<HintPath >** -sökvägen felaktigt netstandard13 i stället för **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Nästa steg

Mer information och exempel finns i stycket om [Android-särskilt överväganden](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) i följande exempel: s Readme.MD-fil:

| Exempel | Plattform | Beskrivning |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | En enkel Xamarin Forms-app som demonstrerar hur du använder MSAL för att autentisera MSA och Azure AD via den AADD v 2.0-slutpunkten och få åtkomst till Microsoft Graph med den resulterande token. <br>![Topologi](media/msal-net-xamarin-android-considerations/topology.png) |
