---
title: Använd mäklare med Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar Xamarin iOS-program som kan använda Microsoft Authenticator och Microsoft Authentication Library för .NET (MSAL.NET). Lär dig också hur du migrerar från Azure AD Authentication Library för .NET (ADAL.NET) till Microsoft Authentication Library for .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262794"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Använda Microsoft Authenticator- eller Intune Company Portal på Xamarin-program

På Android och iOS aktiverar mäklare som Microsoft Authenticator och Android-specifika Microsoft Intune Company Portal:

- **Enkel inloggning (SSO)**: Användare behöver inte logga in på varje program.
- **Enhetsidentifiering:** Mäklaren kommer åt enhetscertifikatet. Det här certifikatet skapas på enheten när det är anslutet till arbetsplatsen.
- **Verifiering av programidentifiering**: När ett program anropar mäklaren skickar den sin omdirigerings-URL. Mäklaren verifierar webbadressen.

Om du vill aktivera en `WithBroker()` av dessa `PublicClientApplicationBuilder.CreateApplication` funktioner använder du parametern när du anropar metoden. Parametern `.WithBroker()` är inställd på true som standard. 

Använd även instruktionerna i följande avsnitt för att konfigurera förmedlad autentisering för [iOS-program](#brokered-authentication-for-ios) eller [Android-program.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Förmedlad autentisering för iOS

Följ följande steg för att aktivera din Xamarin.iOS-app för att prata med [Microsoft Authenticator-appen.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera mäklarsupport
Du måste aktivera mäklarstöd `PublicClientApplication`för enskilda instanser av . Supporten är inaktiverad som standard. När du `PublicClientApplication` `PublicClientApplicationBuilder`skapar igenom `WithBroker()` använder du parametern som följande exempel visar. Parametern `WithBroker()` är inställd på true som standard.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Steg 2: Aktivera åtkomst till nyckelring

Om du vill aktivera nyckelringsåtkomst måste du ha en nyckelringsåtkomstgrupp för ditt program. Du kan `WithIosKeychainSecurityGroup()` använda API:et för att ange din nyckelringsåtkomstgrupp när du skapar programmet:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Mer information finns i [Aktivera nyckelringsåtkomst](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Steg 3: Uppdatera AppDelegate för att hantera motringningen
När Microsoft Authentication Library for .NET (MSAL.NET) anropar mäklaren ringer mäklaren tillbaka `AppDelegate` till ditt program med `OpenUrl` hjälp av klassens metod. Eftersom MSAL väntar på mäklarens svar måste din ansökan samarbeta för att ringa MSAL.NET tillbaka. Om du vill aktivera `AppDelegate.cs` det här samarbetet uppdaterar du filen för att åsidosätta följande metod.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {                
         return false;                  
    }
    
    return true;     
}            
```

Den här metoden anropas varje gång programmet startas. Det används som en möjlighet att bearbeta svaret från mäklaren och slutföra autentiseringsprocessen som MSAL.NET startat.

### <a name="step-4-set-uiviewcontroller"></a>Steg 4: Ange UIViewController()
I `AppDelegate.cs` filen måste du ange ett objektfönster. Normalt behöver du inte ställa in objektfönstret för Xamarin iOS. Men du behöver ett objektfönster för att skicka och ta emot svar från mäklaren. 

Så här ställer du in objektfönstret: 
1. Ställ `AppDelegate.cs` in på `App.RootViewController` en `UIViewController()`ny . Den här tilldelningen säkerställer att `UIViewController`samtalet till mäklaren innehåller . Om den här inställningen har tilldelats felaktigt kan det här felet visas:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Använd `AcquireTokenInteractive` `.WithParentActivityOrWindow(App.RootViewController)` och skicka sedan in referensen till det objektfönster du ska använda i samtalet.

    Följande gäller i `App.cs`:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    Följande gäller i `AppDelegate.cs`:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    I `AcquireToken` samtalet:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Steg 5: Registrera ett URL-schema
MSAL.NET använder webbadresser för att anropa mäklaren och sedan returnera mäklarsvaret till din app. Slutför tur och retur genom att registrera ett `Info.plist` URL-schema för appen i filen.

Namnet `CFBundleURLSchemes` måste `msauth.` inkluderas som prefix. Följ prefixet `CFBundleURLName`med . 

I URL-schemat `BundleId` identifierar appen unikt: `$"msauth.(BundleId)"`. Så `BundleId` om `com.yourcompany.xforms`är , då `msauth.com.yourcompany.xforms`URL-schemat är .

> [!NOTE]
> Det här URL-schemat blir en del av den omdirigerar-URI som unikt identifierar din app när den tar emot svaret från mäklaren.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Steg 6: Lägg till mäklaridentifieraren i avsnittet LSApplicationQueriesSchemes

MSAL `–canOpenURL:` använder för att kontrollera om mäklaren är installerad på enheten. I iOS 9 har Apple låst de scheman som ett program kan fråga efter. 

Lägg `msauthv2` till `LSApplicationQueriesSchemes` i `Info.plist` avsnittet i filen, som i följande exempel:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Steg 7: Registrera din omdirigera URI i programportalen

När du använder mäklaren har din omdirigera URI ett extra krav. Omdirigerings-URI _måste_ ha följande format:

```csharp
$"msauth.{BundleId}://auth"
```

Här är ett exempel:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Observera att omdirigerings-URI:n `CFBundleURLSchemes` matchar namnet som du inkluderade i `Info.plist` filen.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Steg 8: Kontrollera att omdirigera URI är registrerad med din app

Omdirigerings-URI:n måste registreras på [appregistreringsportalen](https://portal.azure.com) som en giltig omdirigerings-URI för ditt program. 

Appregistreringsportalen ger en ny upplevelse som hjälper dig att beräkna den förmedlade svars-URI:n från bunt-ID:t. 

Så här beräknar du omdirigerings-URI:en:

1. I appregistreringsportalen väljer du **Autentisering** > **Prova den nya upplevelsen**.

   ![Prova den nya appregistreringsupplevelsen](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Välj **Lägg till en plattform**.

   ![Lägga till en plattform](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. När listan över plattformar stöds väljer du **iOS**.

   ![Konfigurera iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Ange ditt bunt-ID enligt begäran och välj sedan **Konfigurera**.

   ![Ange bunt-ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

När du är klar med stegen beräknas omdirigerings-URI:n åt dig.

![Kopiera omdirigera URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Förmedlad autentisering för Android

### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera mäklarsupport

Mäklarstöd är aktiverat per offentligclientApplication-basis. Den är inaktiverad som standard. Använd `WithBroker()` parametern (inställd på true som `IPublicClientApplication` standard) när du skapar genom `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Steg 2: Uppdatera AppDelegate för att hantera motringningen

När MSAL.NET anropar mäklaren kommer mäklaren i sin tur att ringa tillbaka till din ansökan med metoden OnActivityResult(). Eftersom MSAL väntar på svaret från mäklaren måste ditt program dirigera resultatet till MSAL.NET.
Detta kan uppnås genom att routing resultatet till `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` genom att åsidosätta OnActivityResult() metoden som visas nedan

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Den här metoden anropas varje gång mäklarprogrammet startas och används som en möjlighet att bearbeta svaret från mäklaren och slutföra autentiseringsprocessen som startades av MSAL.NET.

### <a name="step-3-set-an-activity"></a>Steg 3: Ange en aktivitet

För att förmedlad autentisering ska fungera måste du ange en aktivitet så att MSAL kan skicka och ta emot svaret från mäklare.

För att göra detta måste du ange aktiviteten (vanligtvis MainActivity) till `WithParentActivityOrWindow(object parent)` som det överordnade objektet. 

**Till exempel:**

I anropet Hämta token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Steg 4: Registrera din RedirectUri i programportalen

MSAL använder webbadresser för att anropa mäklaren och sedan återgå till din app. För att slutföra den rundturen måste du registrera ett URL-schema för din app. Den här omdirigerings-URI:n måste registreras på Azure AD-appregistreringsportalen som en giltig omdirigerings-URI för ditt program.


The redirect URI needed for your application is dependent on the certificate used to sign the APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Den sista delen av `hgbUYHVBYUTvuvT&Y6tr554365466=`URI, är signaturen som APK är signerad med, base64 kodad.
Men under utvecklingsfasen av ditt program med Visual Studio, om du felsöker din kod utan att signera apk med ett visst certifikat, kommer Visual Studio att signera apk för dig för felsökningsändamål, vilket ger APK en unik signatur för maskinen som den är byggd på. Varje gång du bygger din app på en annan dator måste du därför uppdatera omdirigerings-URI:n i programmets kod och programmets registrering i Azure-portalen för att autentisera med MSAL. 

När du felsöker kan du stöta på ett MSAL-undantag (eller ett loggmeddelande) som anger att den omdirigerings-URI som tillhandahålls är felaktig. **Det här undantaget ger dig också den omdirigera URI som du bör använda** med den aktuella datorn som du felsöker på. Du kan använda den här omdirigerings-URI:n för att fortsätta utvecklas tills vidare.

När du är redo att slutföra koden måste du uppdatera omdirigerings-URI:n i koden och på programmets registrering i Azure-portalen för att använda signaturen för certifikatet som du signerar APK med.

I praktiken innebär detta att du måste registrera en omdirigera URI för varje medlem i teamet, plus en omdirigera URI för den produktionssignerade versionen av APK.

Du kan också beräkna signaturen själv, på samma sätt som MSAL gör det: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Du har också möjlighet att skaffa signaturen för ditt paket genom att använda nyckelverktyget med följande kommandon:

För Windows:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

För Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [överväganden om hur du använder Universell Windows-plattform med MSAL.NET](msal-net-uwp-considerations.md).
