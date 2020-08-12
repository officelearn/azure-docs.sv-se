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
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d244bbbe96bcea45da5c0860e4af52409123fb7f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118696"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Att tänka på när du använder Xamarin Android med MSAL.NET
I den här artikeln beskrivs vad du bör tänka på när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Ange överordnad aktivitet

På Xamarin Android anger du den överordnade aktiviteten så att token returneras efter interaktionen. Här är ett kod exempel:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

I MSAL 4,2 och senare kan du också ställa in den här funktionen på nivån `PublicClientApplication` . Det gör du med hjälp av motringning:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Om du använder [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin) `PublicClientApplication` ser din Builder-kod ut som i följande exempel.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Se till att kontrollen återgår till MSAL 
När den interaktiva delen av autentiserings flödet slutar ser du till att kontrollen går tillbaka till MSAL. På Android åsidosätter du `OnActivityResult` metoden för `Activity` . Anropa sedan `SetAuthenticationContinuationEventArgs` metoden för MSAL- `AuthenticationContinuationHelper` klassen. 

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

Den här raden säkerställer att kontrollen återgår till MSAL i slutet av den interaktiva delen av autentiseringsschemat.

## <a name="update-the-android-manifest"></a>Uppdatera Android-manifestet
*AndroidManifest.xml* -filen ska innehålla följande värden:

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

Ersätt det paket namn som du registrerade i Azure Portal för `android:host=` värdet. Ersätt den nyckel-hash som du registrerade i Azure Portal för `android:path=` värdet. Signaturens hash ska *inte* vara URL-kodad. Se till att ett inledande snedstreck ( `/` ) visas i början av signaturens hash.

Du kan också [skapa aktiviteten i kod](/xamarin/android/platform/android-manifest#the-basics) i stället för att manuellt redigera *AndroidManifest.xml*. Skapa en aktivitet i kod genom att först skapa en klass som inkluderar `Activity` attributet och `IntentFilter` attributet. 

Här är ett exempel på en klass som representerar värdena i XML-filen:

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin. Forms 4.3. X-manifest

Xamarin. Forms 4.3. x genererar kod som anger `package` attributet till `com.companyname.{appName}` i *AndroidManifest.xml*. Om du använder `DataScheme` som `msal{client_id}` kan du vilja ändra värdet så att det matchar värdet för `MainActivity.cs` namn området.

## <a name="use-the-embedded-web-view-optional"></a>Använda den inbäddade webb visningen (valfritt)

Som standard använder MSAL.NET system webbläsare. Med den här webbläsaren kan du hämta enkel inloggning (SSO) med hjälp av webb program och andra appar. I vissa sällsynta fall kanske du vill att systemet ska använda en inbäddad webbvy. 

I det här kod exemplet visas hur du konfigurerar en inbäddad webbvy:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Mer information finns i [använda webbläsare för MSAL.net](msal-net-web-browsers.md) och Xamarin för [Android-systemet](msal-net-system-browser-android-considerations.md).


## <a name="troubleshoot"></a>Felsöka
Du kan skapa ett nytt Xamarin. Forms-program och lägga till en referens till MSAL.NET NuGet-paketet.
Men du kan ha build-problem om du uppgraderar ett befintligt Xamarin. Forms-program till MSAL.NET Preview 1.1.2 eller senare.

Felsöka build-problem:

- Uppdatera det befintliga MSAL.NET NuGet-paketet till MSAL.NET Preview 1.1.2 eller senare.
- Kontrol lera att Xamarin. Forms uppdateras automatiskt till version 2.5.0.122203. Om det behövs uppdaterar du Xamarin. Forms till den här versionen.
- Kontrol lera att Xamarin. Android. support. v4 uppdateras automatiskt till version 25.4.0.2. Vid behov kan du uppdatera till version 25.4.0.2.
- Se till att alla Xamarin. Android. support paket är mål version 25.4.0.2.
- Rengör eller återskapa programmet.
- I Visual Studio kan du försöka ange maximalt antal parallella projekt versioner till 1. Det gör du genom att välja **alternativ**  >  **projekt och lösningar**  >  **build och kör**  >  **maximalt antal parallella projekt versioner**.
- Om du skapar från kommando raden och ditt kommando använder `/m` , kan du försöka ta bort det här elementet från kommandot.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Fel: namnet AuthenticationContinuationHelper finns inte i den aktuella kontexten

Om ett fel indikerar att `AuthenticationContinuationHelper` inte finns i den aktuella kontexten kan Visual Studio ha uppdaterat filen Android. CSPROJ * felaktigt. Ibland *\<HintPath>* innehåller fil Sök vägen felaktigt *netstandard13* i stället för *monoandroid90*.

Det här exemplet innehåller en korrekt fil Sök väg:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i exemplet på ett [Xamarin mobil program som använder Microsoft Identity Platform](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). I följande tabell sammanfattas relevant information i README-filen.

| Exempel | Plattform | Beskrivning |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | En enkel Xamarin. Forms-app som visar hur du använder MSAL för att autentisera Microsoft-personliga konton och Azure AD via Azure AD 2,0-slutpunkten. Appen visar också hur du kommer åt Microsoft Graph och visar den resulterande token. <br>![Topologi](media/msal-net-xamarin-android-considerations/topology.png) |