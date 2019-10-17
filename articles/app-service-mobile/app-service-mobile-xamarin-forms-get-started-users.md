---
title: Kom igång med autentisering för Mobile Apps i Xamarin Forms-appen | Microsoft Docs
description: Lär dig hur du använder Mobile Apps för att autentisera användare av din Xamarin Forms-app via olika identitets leverantörer, till exempel AAD, Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d9bd698535b09ecb5c484eefcbe31228eb99e04f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388411"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Lägg till autentisering i din Xamarin Forms-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du autentiserar användare av en App Service mobilapp från klient programmet. I den här självstudien lägger du till autentisering i snabb starts projektet för Xamarin-formulär med en identitetsprovider som stöds av App Service. När din mobilapp har autentiserats och godkänts av din mobilapp visas värdet användar-ID och du kommer att kunna komma åt data i begränsad tabell.

## <a name="prerequisites"></a>Krav
För bästa resultat i den här självstudien rekommenderar vi att du först Slutför själv studie kursen [skapa en Xamarin Forms-app][1] . När du har slutfört den här självstudien har du ett Xamarin-formulär projekt som är en TodoList-app med flera plattformar.

Om du inte använder det nedladdade projektet för snabb starts Server måste du lägga till paketet med autentiserings tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera din app för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa omdirigerings-URL: erna

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör att Authentication-systemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder _vi program_ varan för URL-program i alla. Du kan dock använda alla URL-scheman du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på Server sidan:

1. I [Azure Portal][8]väljer du App Service.

2. Klicka på meny alternativet **autentisering/auktorisering** .

3. I de **tillåtna externa omdirigerings-URL: erna**anger du `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** i den här STRÄNGEN är URL-schemat för det mobila programmet.  Den bör följa normal URL-specifikation för ett protokoll (Använd bara bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera koden för mobil program med URL-schemat på flera platser.

4. Klicka på **OK**

5. Klicka på **Save** (Spara).

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Lägg till autentisering i det portabla klass biblioteket
Mobile Apps använder tilläggs metoden [LoginAsync][3] på [MobileServiceClient][4] för att logga in en användare med App Service autentisering. I det här exemplet används ett Server hanterat autentiseringsschema som visar providerns inloggnings gränssnitt i appen. Mer information finns i [Server-hanterad autentisering][5]. För att ge en bättre användar upplevelse i din webbapp bör du i stället använda [klient-hanterad autentisering][6].

För att autentisera med ett Xamarin Forms-projekt definierar du ett **iauthenticate** -gränssnitt i appens Portabela klass bibliotek. Lägg sedan till en **inloggnings** knapp i användar gränssnittet som definierats i biblioteket för portabela klasser, som du klickar på för att starta autentiseringen. Data läses in från Server delen för mobilappar efter en lyckad autentisering.

Implementera **iauthenticate** -gränssnittet för varje plattform som stöds av din app.

1. I Visual Studio eller Xamarin Studio öppnar du App.cs från projektet med **portabel** i namnet, som är ett biblioteks projekt med portabel klass och lägger sedan till följande `using`-sats:

        using System.Threading.Tasks;
2. I App.cs lägger du till följande `IAuthenticate`-gränssnitts definition omedelbart före `App`-klass definitionen.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Om du vill initiera gränssnittet med en plattformsspecifik implementering lägger du till följande statiska medlemmar i klassen **app** .

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Öppna TodoList. XAML från biblioteks projekt för portabel klass, Lägg till följande **knapp** element i *buttonsPanel* -Layout-elementet efter den befintliga knappen:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Den här knappen utlöser Server hanterad autentisering med din server del för mobilappar.
5. Öppna TodoList.xaml.cs från biblioteks projekt för portabel klass och Lägg sedan till följande fält i klassen `TodoList`:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Ersätt **OnAppearing** -metoden med följande kod:

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

    Den här koden kontrollerar att data bara uppdateras från tjänsten när du har autentiserats.
7. Lägg till följande hanterare för händelsen **klickning** i **TodoList** -klassen:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Spara ändringarna och bygg om det portabla klass biblioteks projektet verifiera inga fel.

## <a name="add-authentication-to-the-android-app"></a>Lägg till autentisering i Android-appen
I det här avsnittet visas hur du implementerar **iauthenticate** -gränssnittet i Android-Appaketet. Hoppa över det här avsnittet om du inte stöder Android-enheter.

1. I Visual Studio eller Xamarin Studio högerklickar du på projektet **Droid** och **anger som start projekt**.
2. Tryck på F5 för att starta projektet i fel sökaren och kontrol lera sedan att ett ohanterat undantag med status kod 401 (obehörig) utlöses när appen startar. 401-koden skapas eftersom åtkomst på Server delen är begränsad till endast auktoriserade användare.
3. Öppna MainActivity.cs i Android-projektet och Lägg till följande `using`-uttryck:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatera **MainActivity** -klassen för att implementera **iauthenticate** -gränssnittet enligt följande:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Uppdatera **MainActivity** -klassen genom att lägga till ett **MobileServiceUser** -fält och en metod för **autentisering** , vilket krävs av **iauthenticate** -gränssnittet, enligt följande:

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

    Om du använder en annan identitets leverantör än Facebook väljer du ett annat värde för [MobileServiceAuthenticationProvider][7].

6. Uppdatera filen **AndroidManifest. XML** genom att lägga till följande XML i elementet `<application>`:

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
    Ersätt `{url_scheme_of_your_app}` med ditt URL-schema.
7. Lägg till följande kod i **OnCreate** -metoden för **MainActivity** -klassen innan anropet till `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Den här koden säkerställer att autentiseraren initieras innan appen läses in.
8. Återskapa appen, kör den och logga sedan in med den autentiseringsprovider du valde och kontrol lera att du har åtkomst till data som en autentiserad användare.

### <a name="troubleshooting"></a>Felsöka

**Programmet kraschade med `Java.Lang.NoSuchMethodError: No static method startActivity`**

I vissa fall visas konflikter i support paketen som en varning i Visual Studio, men programmet kraschar med detta undantag vid körning. I så fall måste du se till att alla de support paket som refereras till i ditt projekt har samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) har ett `Xamarin.Android.Support.CustomTabs`-beroende för Android-plattformen, så om ditt projekt använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

## <a name="add-authentication-to-the-ios-app"></a>Lägg till autentisering i iOS-appen
I det här avsnittet visas hur du implementerar **iauthenticate** -gränssnittet i iOS-exempelprojektet. Hoppa över det här avsnittet om du inte stöder iOS-enheter.

1. Högerklicka på **iOS** -projektet i Visual Studio eller Xamarin Studio och **Ställ in som start projekt**.
2. Tryck på F5 för att starta projektet i fel sökaren och kontrol lera sedan att ett ohanterat undantag med status kod 401 (obehörig) utlöses när appen startar. 401-svaret skapas eftersom åtkomst på Server delen är begränsad till endast auktoriserade användare.
3. Öppna AppDelegate.cs i iOS-projektet och Lägg till följande `using`-uttryck:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatera **AppDelegate** -klassen för att implementera **iauthenticate** -gränssnittet enligt följande:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Uppdatera **AppDelegate** -klassen genom att lägga till ett **MobileServiceUser** -fält och en metod för **autentisering** , vilket krävs av **iauthenticate** -gränssnittet, enligt följande:

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
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Om du använder en annan identitets leverantör än Facebook väljer du ett annat värde för [MobileServiceAuthenticationProvider].
    
6. Uppdatera **AppDelegate** -klassen genom att lägga till **OpenURL** -metoden överlagring på följande sätt:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Lägg till följande kodrad i **FinishedLaunching** -metoden innan anropet till `LoadApplication()`:

        App.Init(this);

    Den här koden säkerställer att autentiseraren initieras innan appen läses in.

8. Öppna info. plist och Lägg till en **URL-typ**. Ange **identifieraren** till ett namn som du väljer, **URL-scheman** till URL-schemat för din app och **rollen** till ingen.

9. Återskapa appen, kör den och logga sedan in med den autentiseringsprovider du valde och kontrol lera att du har åtkomst till data som en autentiserad användare.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Lägg till autentisering i appar för Windows 10 (inklusive Phone)
I det här avsnittet visas hur du implementerar **iauthenticate** -gränssnittet i app-projekt i Windows 10. Samma steg gäller för Universell Windows-plattform (UWP)-projekt, men med hjälp av **UWP** -projektet (med observerade ändringar). Hoppa över det här avsnittet om du inte stöder Windows-enheter.

1. I Visual Studio högerklickar du på projektet **UWP** och **anger som start projekt**.
2. Tryck på F5 för att starta projektet i fel sökaren och kontrol lera sedan att ett ohanterat undantag med status kod 401 (obehörig) utlöses när appen startar. 401-svaret inträffar eftersom åtkomst på Server delen är begränsad till endast auktoriserade användare.
3. Öppna MainPage.xaml.cs för Windows-Appaketet och Lägg till följande `using`-uttryck:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Ersätt `<your_Portable_Class_Library_namespace>` med namn området för ditt portabla klass bibliotek.
4. Uppdatera **mainpage** -klassen för att implementera **iauthenticate** -gränssnittet enligt följande:

        public sealed partial class MainPage : IAuthenticate
5. Uppdatera **mainpage** -klassen genom att lägga till ett **MobileServiceUser** -fält och en metod för **autentisering** , vilket krävs av **iauthenticate** -gränssnittet, enligt följande:

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

    Om du använder en annan identitets leverantör än Facebook väljer du ett annat värde för [MobileServiceAuthenticationProvider][7].

1. Lägg till följande kodrad i konstruktorn för klassen **mainpage** innan anropet till `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Ersätt `<your_Portable_Class_Library_namespace>` med namn området för ditt portabla klass bibliotek.

3. Om du använder **UWP**lägger du till följande **OnActivated** -metod för att åsidosätta klassen **app** :

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Öppna Package. appxmanifest och Lägg till en **protokoll** deklaration. Ange **visnings namnet** till ett namn som du väljer och **namnet** på appens URL-schema.

4. Återskapa appen, kör den och logga sedan in med den autentiseringsprovider du valde och kontrol lera att du har åtkomst till data som en autentiserad användare.

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här grundläggande autentiseringen kan du fortsätta med någon av följande själv studie kurser:

* [Lägg till push-meddelanden i appen](app-service-mobile-xamarin-forms-get-started-push.md)

  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Offline-synkronisering ger slutanvändare möjlighet att interagera med en mobilapp – Visa, lägga till eller ändra data – även om det inte finns någon nätverks anslutning.

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
