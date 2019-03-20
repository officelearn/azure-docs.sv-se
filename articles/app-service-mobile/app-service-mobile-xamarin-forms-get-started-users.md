---
title: Kom igång med autentisering för Mobile Apps i Xamarin Forms-app | Microsoft Docs
description: Lär dig hur du använder Mobile Apps du autentiserar användare i din Xamarin Forms-app genom olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 1bbd481218128c482769cd6a28910e135c1ce16d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001047"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Lägg till autentisering i din Xamarin Forms-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du autentiserar användare i en App Service Mobile App från ditt klientprogram. I den här självstudien lägger du till autentisering till snabbstartsprojektet Xamarin Forms med hjälp av en identitetsprovider som stöds av App Service. Efter att har autentiseras och auktoriseras av din Mobilapp, användar-ID-värdet visas och du kommer att kunna få åtkomst till begränsade tabelldata.

## <a name="prerequisites"></a>Förutsättningar
För bästa resultat med den här självstudien rekommenderar vi att du först slutföra den [skapa en Xamarin Forms-app] [ 1] självstudien. När du har slutfört den här självstudien har du ett Xamarin Forms-projekt som är en app för flera plattformar TodoList.

Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till tillägget autentiseringspaket ditt projekt. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera din app för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa Omdirigeringswebbadresser

Säker autentisering måste du definiera en ny URL-schema för din app. På så sätt kan autentiseringssystem att omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder vi URL-schema _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på serversidan:

1. I den [Azure-portalen][8], Välj din App Service.

2. Klicka på den **autentisering / auktorisering** menyalternativ.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobilapp.  Den bör följa den normala URL specifikationen för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer eftersom du behöver ändra programkoden mobila med URL-schema på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Lägg till autentisering i portabelt klassbibliotek
Mobile Apps använder den [LoginAsync] [ 3] tilläggsmetod på den [MobileServiceClient] [ 4] att logga in en användare med App Service autentisering. Det här exemplet används en hanterad server autentiseringsflödet som visar leverantörens i inloggningsgränssnittet i appen. Mer information finns i [Server-hanterad autentisering][5]. För att ge en bättre användarupplevelse i din produktionsapp, bör du överväga att använda [hanteras med klientprogram autentisering][6].

För att autentisera med ett projekt i Xamarin Forms, definiera en **IAuthenticate** gränssnittet i klassbiblioteket i bärbar för appen. Lägg sedan till en **inloggning** knappen för att användargränssnittet som definierats i bärbar klassbiblioteket, som du klickar på Starta autentisering. Data har lästs in från serverdelen för mobilappen efter en lyckad autentisering.

Implementera de **IAuthenticate** gränssnitt för varje plattform som stöds av din app.

1. I Visual Studio eller Xamarin Studio, öppna App.cs från projektet med **bärbar** i namnet, som är portabelt klassbibliotek projekt, och Lägg sedan till följande `using` instruktionen:

        using System.Threading.Tasks;
2. Lägg till följande i App.cs, `IAuthenticate` gränssnittsdefinitionen omedelbart före den `App` klassdefinitionen.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. För att initiera gränssnittet med implementering av plattformsspecifika, lägger du till följande statiska medlemmar till den **App** klass.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Öppna TodoList.xaml från portabelt klassbibliotek-projektet, Lägg till följande **knappen** elementet i den *buttonsPanel* layoutelement efter knappen befintlig:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Den här knappen utlöser server-hanterad autentisering med din mobilappsserverdel.
5. Öppna TodoList.xaml.cs från portabelt klassbibliotek-projektet och sedan lägga till följande fält till den `TodoList` klass:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Ersätt den **OnAppearing** metoden med följande kod:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Den här koden ser till att data uppdateras bara från tjänsten när du har autentiserats.
7. Lägg till följande hanterare för den **Clicked** händelse ska den **TodoList** klass:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Spara dina ändringar och återskapa projektet portabelt klassbibliotek verifiera några fel.

## <a name="add-authentication-to-the-android-app"></a>Lägg till autentisering i Android-appen
Det här avsnittet visas hur du implementerar den **IAuthenticate** gränssnittet i Android-app-projekt. Hoppa över det här avsnittet om du inte stöder Android-enheter.

1. I Visual Studio eller Xamarin Studio högerklickar du på den **droid** projektet sedan **Set as StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökning, och sedan kontrollera att ett ohanterat undantag med en statuskod 401 (obehörig) aktiveras när appen startar. 401 koden produceras eftersom åtkomst på serverdelen begränsas till behöriga användare.
3. Öppna MainActivity.cs i Android-projektet och Lägg till följande `using` instruktioner:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatera den **MainActivity** klassen för att implementera den **IAuthenticate** gränssnitt på följande sätt:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Uppdatera den **MainActivity** klassen genom att lägga till en **MobileServiceUser** fält och en **autentisera** metoden, vilket krävs av den **IAuthenticate** gränssnitt på följande sätt:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Om du använder en identitetsprovider än Facebook, välja ett annat värde för [MobileServiceAuthenticationProvider][7].

6. Uppdatera den **AndroidManifest.xml** filen genom att lägga till följande XML-filen i den `<application>` element:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Ersätt `{url_scheme_of_your_app}` med URL-schema.
7. Lägg till följande kod till den **OnCreate** -metoden för den **MainActivity** innan anropet till `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Den här koden säkerställer autentiseraren initieras innan appen belastning.
8. Återskapa appen, köra den och sedan logga in med den authentication-provider du har valt och kontrollera att du kan komma åt data som en autentiserad användare.

### <a name="troubleshooting"></a>Felsökning

**Programmet kraschade med `Java.Lang.NoSuchMethodError: No static method startActivity`**

I vissa fall är i konflikt i supportpaket visas som bara en varning i Visual studio, men programkrascher med det här undantaget vid körning. I det här fallet måste du se till att alla supportpaket som refereras till i ditt projekt har samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) har ett `Xamarin.Android.Support.CustomTabs`-beroende för Android-plattformen, så om ditt projekt använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

## <a name="add-authentication-to-the-ios-app"></a>Lägg till autentisering i appen för iOS
Det här avsnittet visas hur du implementerar den **IAuthenticate** gränssnittet i iOS-app-projekt. Hoppa över det här avsnittet om du inte stöder iOS-enheter.

1. I Visual Studio eller Xamarin Studio högerklickar du på den **iOS** projektet sedan **Set as StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökning, och sedan kontrollera att ett ohanterat undantag med en statuskod 401 (obehörig) aktiveras när appen startar. 401-svar skapas eftersom åtkomst på serverdelen begränsas till behöriga användare.
3. Öppna AppDelegate.cs i iOS-projektet och Lägg till följande `using` instruktioner:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatera den **AppDelegate** klassen för att implementera den **IAuthenticate** gränssnitt på följande sätt:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Uppdatera den **AppDelegate** klassen genom att lägga till en **MobileServiceUser** fält och en **autentisera** metoden, vilket krävs av den **IAuthenticate** gränssnitt på följande sätt:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Om du använder en identitetsprovider än Facebook, väljer du ett annat värde för [MobileServiceAuthenticationProvider].
    
6. Uppdatera den **AppDelegate** klassen genom att lägga till den **OpenUrl** metoden överbelasta på följande sätt:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Lägg till följande rad med kod till den **FinishedLaunching** sättet innan anropet till `LoadApplication()`:

        App.Init(this);

    Den här koden säkerställer autentiseraren initieras innan appen läses in.

8. Öppna Info.plist och Lägg till en **URL-typen**. Ange den **identifierare** till ett namn du väljer den **URL-scheman** till URL-schema för din app och **rollen** till ingen.

9. Återskapa appen, köra den och sedan logga in med den authentication-provider du har valt och kontrollera att du kan komma åt data som en autentiserad användare.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Lägg till autentisering i Windows 10 (inklusive telefon) app-projekt
Det här avsnittet visas hur du implementerar den **IAuthenticate** gränssnittet i Windows 10-app-projekt. Samma steg som gäller för Universal Windows Platform (UWP)-projekt, men med hjälp av den **UWP** projekt (med antecknat ändringar). Hoppa över det här avsnittet om du inte stöder Windows-enheter.

1. I Visual Studio högerklickar du på den **UWP** projektet sedan **Set as StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökning, och sedan kontrollera att ett ohanterat undantag med en statuskod 401 (obehörig) aktiveras när appen startar. 401-svar inträffar eftersom åtkomst på serverdelen begränsas till behöriga användare.
3. Öppna MainPage.xaml.cs för Windows-app-projekt och Lägg till följande `using` instruktioner:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Ersätt `<your_Portable_Class_Library_namespace>` med namnområdet för ditt portabelt klassbibliotek.
4. Uppdatera den **MainPage** klassen för att implementera den **IAuthenticate** gränssnitt på följande sätt:

        public sealed partial class MainPage : IAuthenticate
5. Uppdatera den **MainPage** klassen genom att lägga till en **MobileServiceUser** fält och en **autentisera** metoden, vilket krävs av den **IAuthenticate**gränssnitt på följande sätt:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Om du använder en identitetsprovider än Facebook, välja ett annat värde för [MobileServiceAuthenticationProvider][7].

1. Lägg till följande kodrad i konstruktorn för den **MainPage** innan anropet till `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Ersätt `<your_Portable_Class_Library_namespace>` med namnområdet för ditt portabelt klassbibliotek.

3. Om du använder **UWP**, Lägg till följande **OnActivated** metoden åsidosätta till den **App** klass:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Öppna Package.appxmanifest och Lägg till en **protokollet** deklaration. Ange den **visningsnamn** till ett namn du väljer och **namn** till URL-schema för appen.

4. Återskapa appen, köra den och sedan logga in med den authentication-provider du har valt och kontrollera att du kan komma åt data som en autentiserad användare.

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här självstudien för grundläggande autentisering, Överväg fortsätter in på någon av följande självstudiekurser:

* [Lägg till push-meddelanden i appen](app-service-mobile-xamarin-forms-get-started-push.md)

  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Offlinesynkronisering kan slutanvändarna kan interagera med en mobil app - visa, lägga till eller ändra data, även om det inte finns någon nätverksanslutning.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
