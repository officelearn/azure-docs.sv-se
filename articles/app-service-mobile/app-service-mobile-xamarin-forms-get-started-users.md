---
title: "Komma igång med autentisering för Mobile Apps i Xamarin Forms app | Microsoft Docs"
description: "Lär dig hur du använder Mobilappar för att autentisera användare för Xamarin Forms appen via en mängd olika identitetsleverantörer, inklusive AAD, Google, Facebook, Twitter och Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: syntaxc4
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: 9e14e95793bcc81ad46783fd50ba223eec4ea360
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Lägg till autentisering i appen Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du autentiserar användare i en Apptjänst Mobile App från klientprogrammet. I kursen får du lägger till autentisering i Xamarin Forms quickstart projektet som en identitetsleverantör som stöds av App Service. Efter att har autentiseras och auktoriseras av din Mobilapp användar-ID-värde visas och du kommer att kunna få åtkomst till begränsade tabelldata.

## <a name="prerequisites"></a>Krav
För bästa resultat med den här självstudiekursen, rekommenderar vi att du först slutföra den [skapa en app i Xamarin Forms] [ 1] kursen. När den här kursen har du en Xamarin Forms-projekt som är en app för flera plattformar TodoList.

Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till tillägget autentiseringspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera din app för autentisering och konfigurera Apptjänster
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i tillåtna externa omdirigerings-URL

Säker autentisering måste du definiera en ny URL-schema för din app. Detta gör att autentiseringssystemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudiekursen kommer vi använda URL-schemat _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för din mobila program. Du vill aktivera omdirigering på serversidan:

1. Välj din Apptjänst i [Azure-portalen].

2. Klicka på den **autentisering / auktorisering** menyalternativet.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobila program.  Det bör följa den normala URL specifikation för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera mobilprogram koden med URL-schemat på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Lägg till autentisering i klassbiblioteket i bärbar
Mobile Apps använder den [LoginAsync] [ 3] metod på den [MobileServiceClient] [ 4] att logga in en användare med App Service-autentisering. Det här exemplet används en hanterad server autentiseringsflödet som visar leverantörens i inloggningsgränssnittet i appen. Mer information finns i [Server-hanterade autentisering][5]. För att ge en bättre användarupplevelse i din produktionsapp, bör du istället använda [klienten hanteras autentisering][6].

För att autentisera med ett projekt för Xamarin Forms definiera en **IAuthenticate** gränssnittet i klassbiblioteket i bärbara för appen. Lägg sedan till en **inloggning** knappen användargränssnitt som definierats i bärbara klassbiblioteket, som du klickar på Starta autentisering. Data läses från serverdelen för mobilappen efter en lyckad autentisering.

Implementera den **IAuthenticate** gränssnitt för varje plattform som stöds av din app.

1. Öppna i Visual Studio eller Xamarin Studio App.cs från projektet med **bärbar** i namnet, vilket är bärbara klassbiblioteket projektet och Lägg sedan till följande `using` instruktionen:

        using System.Threading.Tasks;
2. Lägg till följande i App.cs, `IAuthenticate` gränssnitt definition omedelbart före den `App` klassen definition.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. För att initiera gränssnittet med en plattformsspecifik implementering, lägger du till följande statiska medlemmar till den **App** klass.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Öppna TodoList.xaml från bärbara klassbiblioteket projekt, Lägg till följande **knappen** element i den *buttonsPanel* layout-elementet efter knappen befintlig:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Den här knappen utlöser server-hanterade autentisering med mobilappsserverdelen.
5. Öppna TodoList.xaml.cs från bärbara klassbiblioteket projektet och Lägg till följande fält till den `TodoList` klass:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Ersätt den **OnAppearing** metod med följande kod:

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
7. Lägg till följande hanteraren för den **Clicked** händelsen för att den **TodoList** klass:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Spara dina ändringar och återskapa projektet bärbara klassbiblioteket verifierar utan fel.

## <a name="add-authentication-to-the-android-app"></a>Lägg till autentisering i appen för Android
Det här avsnittet visas hur du implementerar den **IAuthenticate** gränssnitt i Android-app-projekt. Hoppa över det här avsnittet om du inte stöder Android-enheter.

1. I Visual Studio eller Xamarin Studio högerklickar du på den **droid** projektet sedan **Set as StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökaren, kontrollera att ett undantag med en statuskod 401 (obehörig) aktiveras när appen startar. 401 koden produceras eftersom åtkomst på serverdelen begränsas till behöriga användare.
3. Öppna MainActivity.cs i Android-projekt och Lägg till följande `using` instruktioner:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatering av **MainActivity** klassen för att implementera den **IAuthenticate** gränssnitt, enligt följande:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Uppdatering av **MainActivity** klassen genom att lägga till en **MobileServiceUser** fältet och en **autentisera** metod, vilket krävs av den **IAuthenticate** gränssnitt, enligt följande:

        // Define a authenticated user.
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

    Om du använder en identitetsleverantör än Facebook, välja ett annat värde för [MobileServiceAuthenticationProvider][7].

6. Lägg till följande kod inuti <application> nod i AndroidManifest.xml:

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

1. Lägg till följande kod i den **OnCreate** metod för den **MainActivity** klass innan anropet till `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Den här koden garanterar autentiseraren är initierad innan app belastning.
2. Återskapa appen, köra den och sedan logga in med den autentiseringsprovider som du har valt och kontrollera att du ska kunna komma åt data som en autentiserad användare.

## <a name="add-authentication-to-the-ios-app"></a>Lägg till autentisering i iOS-app
Det här avsnittet visas hur du implementerar den **IAuthenticate** gränssnittet i iOS app-projekt. Hoppa över det här avsnittet om du inte stöder iOS-enheter.

1. I Visual Studio eller Xamarin Studio högerklickar du på den **iOS** projektet sedan **Set as StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökaren, kontrollera att ett undantag med en statuskod 401 (obehörig) aktiveras när appen startar. 401-svar produceras eftersom åtkomst på serverdelen begränsas till behöriga användare.
3. Öppna AppDelegate.cs i iOS-projektet och Lägg till följande `using` instruktioner:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatering av **AppDelegate** klassen för att implementera den **IAuthenticate** gränssnitt, enligt följande:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Uppdatering av **AppDelegate** klassen genom att lägga till en **MobileServiceUser** fältet och en **autentisera** metod, vilket krävs av den **IAuthenticate** gränssnitt, enligt följande:

        // Define a authenticated user.
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

    Välj ett annat värde för [MobileServiceAuthenticationProvider] om du använder en identitetsleverantör än Facebook.

6. Uppdatera klassen AppDelegate genom att lägga till metodöverlagringen OpenUrl (UIApplication app NSUrl url NSDictionary alternativ)

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }

6. Lägg till följande kod för att den **FinishedLaunching** metoden innan anropet till `LoadApplication()`:

        App.Init(this);

    Den här koden garanterar autentiseraren initieras innan appen har lästs in.

6. Lägg till **{url_scheme_of_your_app}** till URL-scheman i filen Info.plist.

7. Återskapa appen, köra den och sedan logga in med den autentiseringsprovider som du har valt och kontrollera att du ska kunna komma åt data som en autentiserad användare.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Lägg till autentisering i Windows 10 (inklusive telefon) app-projekt
Det här avsnittet visas hur du implementerar den **IAuthenticate** gränssnittet i Windows 10-app-projekt. Samma steg gäller för universella Windowsplattformen (UWP) projekt, men med den **UWP** projekt (med noterats ändringar). Hoppa över det här avsnittet om du inte stöder Windows-enheter.

1. ”I Visual Studio högerklickar du på någon av **UWP** projektet sedan **Set as StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökaren, kontrollera att ett undantag med en statuskod 401 (obehörig) aktiveras när appen startar. 401-svar inträffar eftersom åtkomst på serverdelen begränsas till behöriga användare.
3. Öppna MainPage.xaml.cs för Windows-app-projekt och Lägg till följande `using` instruktioner:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Ersätt `<your_Portable_Class_Library_namespace>` med namnområdet för din bärbara klassbiblioteket.
4. Uppdatering av **MainPage** klassen för att implementera den **IAuthenticate** gränssnitt, enligt följande:

        public sealed partial class MainPage : IAuthenticate
5. Uppdatering av **MainPage** klassen genom att lägga till en **MobileServiceUser** fältet och en **autentisera** metod, vilket krävs av den **IAuthenticate** gränssnitt, enligt följande:

        // Define a authenticated user.
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

    Välj ett annat värde för [MobileServiceAuthenticationProvider] om du använder en identitetsleverantör än Facebook.

1. Lägg till följande kodrad i konstruktören för den **MainPage** klass innan anropet till `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Ersätt `<your_Portable_Class_Library_namespace>` med namnområdet för din bärbara klassbiblioteket.

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

   Lägg till villkorlig kod från föregående kodfragment när Metodåsidosättningen redan finns.  Den här koden krävs inte för universella Windows-projekt.

3. Lägg till **{url_scheme_of_your_app}** i Package.appxmanifest. 

4. Återskapa appen, köra den och sedan logga in med den autentiseringsprovider som du har valt och kontrollera att du ska kunna komma åt data som en autentiserad användare.

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här självstudiekursen för grundläggande autentisering, Överväg fortsätter in på något av följande kurser:

* [Lägg till push-meddelanden i appen](app-service-mobile-xamarin-forms-get-started-push.md)

  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Lär dig hur du lägger till offlinestöd i appen med hjälp av en mobilappsserverdel. Offlinesynkronisering kan slutanvändarna interagera med en mobil app - visa, lägga till eller ändra data – även om det inte finns någon nätverksanslutning.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
