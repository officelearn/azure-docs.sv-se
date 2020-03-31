---
title: Xamarin Android överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Läs mer om hur du använder Xamarin Android med Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132499"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Överväganden för att använda Xamarin Android med MSAL.NET
I den här artikeln beskrivs vad du bör tänka på när du använder Xamarin Android med Microsoft Authentication Library for .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Ange den överordnade aktiviteten

På Xamarin Android anger du den överordnade aktiviteten så att token returneras efter interaktionen. Här är ett kodexempel:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

I MSAL 4.2 och senare kan du också ställa `PublicClientApplication`in den här funktionen på nivån . För att göra det, använd en motringning:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Om du använder [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)ser din `PublicClientApplication` byggkod ut som följande exempel.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Se till att kontrollen återgår till MSAL 
När den interaktiva delen av autentiseringsflödet slutar kontrollerar du att kontrollen går tillbaka till MSAL. På Android åsidosätter du `OnActivityResult` metoden `Activity`för . Anropa sedan `SetAuthenticationContinuationEventArgs` metoden `AuthenticationContinuationHelper` för klassen MSAL. 

Här är ett exempel:

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

Den här raden säkerställer att kontrollen återgår till MSAL i slutet av den interaktiva delen av autentiseringsflödet.

## <a name="update-the-android-manifest"></a>Uppdatera Android-manifestet
*Filen AndroidManifest.xml* ska innehålla följande värden:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Ersätt paketnamnet som du registrerade i `android:host=` Azure-portalen för värdet. Ersätt nyckelhhh som du registrerade i `android:path=` Azure-portalen mot värdet. Signaturhhhen bör *inte* url-kodas. Kontrollera att ett inledande`/`snedstreck ( ) visas i början av signatur hash.

Alternativt [kan du skapa aktiviteten i kod i](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) stället för att manuellt redigera *AndroidManifest.xml*. Om du vill skapa aktiviteten i kod `Activity` skapar du `IntentFilter` först en klass som innehåller attributet och attributet. 

Här är ett exempel på en klass som representerar xml-filens värden:

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.X manifest

Xamarin.Forms 4.3.x genererar kod `package` som `com.companyname.{appName}` anger attributet till i *AndroidManifest.xml*. Om du `DataScheme` `msal{client_id}`använder som , kanske du vill ändra värdet `MainActivity.cs` så att det matchar värdet för namnområdet.

## <a name="use-the-embedded-web-view-optional"></a>Använda den inbäddade webbvyn (valfritt)

Som standard använder MSAL.NET systemwebbläsaren. Med den här webbläsaren kan du få enkel inloggning (SSO) med hjälp av webbprogram och andra appar. I vissa sällsynta fall kanske du vill att systemet ska använda en inbäddad webbvy. 

Det här kodexemplet visar hur du ställer in en inbäddad webbvy:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Mer information finns i [Använda webbläsare för MSAL.NET](msal-net-web-browsers.md) och [Xamarin Android-systemwebbläsare.](msal-net-system-browser-android-considerations.md)


## <a name="troubleshoot"></a>Felsöka
Du kan skapa ett nytt Xamarin.Forms-program och lägga till en referens till paketet MSAL.NET NuGet.
Men du kan ha byggproblem om du uppgraderar ett befintligt Xamarin.Forms-program till MSAL.NET förhandsgranskning 1.1.2 eller senare.

Så här felsöker du byggproblem:

- Uppdatera det befintliga MSAL.NET NuGet-paketet för att MSAL.NET förhandsgranskning 1.1.2 eller senare.
- Kontrollera att Xamarin.Forms uppdateras automatiskt till version 2.5.0.122203. Om det behövs uppdaterar du Xamarin.Forms till den här versionen.
- Kontrollera att Xamarin.Android.Support.v4 automatiskt uppdateras till version 25.4.0.2. Om det behövs, uppdatera till version 25.4.0.2.
- Se till att alla Xamarin.Android.Support-paket är målversion 25.4.0.2.
- Rengör eller återskapa programmet.
- I Visual Studio kan du prova att ställa in det maximala antalet parallella projektversioner till 1. Det gör du genom att välja **Alternativ** > **projekt och lösningar** > **bygga och köra** > **maximalt antal parallella projekt bygger**.
- Om du bygger från kommandoraden och `/m`kommandot använder kan du prova att ta bort det här elementet från kommandot.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Fel: Namnet AuthenticationContinuationHelper finns inte i den aktuella kontexten

Om ett fel `AuthenticationContinuationHelper` indikerar att det inte finns i den aktuella kontexten kan Visual Studio ha uppdaterat Android.csproj*-filen felaktigt. Ibland innehåller * \<Filsökvägen>i HintPath* felaktigt *netstandard13* i stället för *monoandroid90*.

Det här exemplet innehåller en korrekt sökväg:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i exemplet med ett [Xamarin-mobilapp som använder Microsoft identity platform](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). I följande tabell sammanfattas relevant information i README-filen.

| Exempel | Plattform | Beskrivning |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | En enkel Xamarin.Forms-app som visar hur du använder MSAL för att autentisera Microsofts personliga konton och Azure AD via slutpunkten för Azure AD 2.0. Appen visar också hur du kommer åt Microsoft Graph och visar den resulterande token. <br>![Topologi](media/msal-net-xamarin-android-considerations/topology.png) |
