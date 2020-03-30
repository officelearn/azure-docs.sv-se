---
title: Komma igång med autentisering i appen Xamarin Forms
description: Läs om hur du använder mobilappar för att autentisera användare av din Xamarin Forms-app med identitetsleverantörer som AAD, Google, Facebook, Twitter och Microsoft.
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4788aa50074016a34d906353f5b37dbba85ef104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458774"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Lägga till autentisering i din Xamarin Forms-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Översikt
I det här avsnittet visas hur du autentiserar användare av en App Service Mobile App från klientprogrammet. I den här självstudien lägger du till autentisering i snabbstartsprojektet Xamarin Forms med hjälp av en identitetsprovider som stöds av App Service. När du har autentiserats och auktoriserats av mobilappen visas användar-ID-värdet och du kan komma åt begränsade tabelldata.

## <a name="prerequisites"></a>Krav
För bästa resultat med den här självstudien rekommenderar vi att du först slutför [självstudien Skapa en Xamarin Forms-app.][1] När du har slutfört den här självstudien har du ett Xamarin Forms-projekt som är en TodoList-app med flera plattformar.

Om du inte använder det hämtade snabbstartsserverprojektet måste du lägga till autentiseringstilläggspaketet i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrera appen för autentisering och konfigurera App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Lägga till appen i url:erna för tillåtna externa omdirigering

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör det möjligt för autentiseringssystemet att omdirigera tillbaka till din app när autentiseringsprocessen är klar. I den här självstudien använder vi _url-schemats appnamn_ hela tiden. Du kan dock använda vilket URL-schema du vill. Den ska vara unik för din mobilapplikation. Så här aktiverar du omdirigering på serversidan:

1. Välj din App-tjänst i [Azure-portalen.][8]

2. Klicka på **menyalternativet Autentisering/auktorisering.**

3. Ange i **url:erna Tillåten extern omdirigering** `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobilapplikation.  Den bör följa normal URL-specifikation för ett protokoll (använd endast bokstäver och siffror och börja med en bokstav).  Du bör anteckna strängen som du väljer eftersom du måste justera din mobilprogramkod med URL-schemat på flera ställen.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Lägga till autentisering i det bärbara klassbiblioteket
Mobilappar använder metoden [LoginAsync-tillägg][3] på [MobileServiceClient][4] för att logga in en användare med App Service-autentisering. Det här exemplet använder ett serverhanterade autentiseringsflöde som visar leverantörens inloggningsgränssnitt i appen. Mer information finns i [Serverhanterad autentisering][5]. Om du vill ge en bättre användarupplevelse i produktionsappen bör du i stället använda [klienthanterad autentisering][6].

Om du vill autentisera med ett Xamarin Forms-projekt definierar du ett **IAuthenticate-gränssnitt** i appens portabla klassbibliotek. Lägg sedan till en **inloggningsknapp i** användargränssnittet som definierats i biblioteket För bärbar klass, som du klickar på för att starta autentiseringen. Data läses in från mobilappens serverd efter lyckad autentisering.

Implementera **IAuthenticate-gränssnittet** för varje plattform som stöds av din app.

1. Öppna App.cs från projektet med **Portable** i namnet, som är projektet Portable Class Library i Visual Studio `using` eller Xamarin Studio:

        using System.Threading.Tasks;
2. I App.cs lägger du `IAuthenticate` till följande gränssnittsdefinition omedelbart före klassdefinitionen. `App`

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Om du vill initiera gränssnittet med en plattformsspecifik implementering lägger du till följande statiska medlemmar i **klassen App.**

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Öppna TodoList.xaml från projektet Portable Class Library och lägg till följande **knappelement** i *layoutelementet buttonsPanel* efter den befintliga knappen:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Den här knappen utlöser serverhanterad autentisering med din mobilappsservering.
5. Öppna TodoList.xaml.cs från projektet Portable Class Library och lägg sedan `TodoList` till följande fält i klassen:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Ersätt **OnAppearing-metoden** med följande kod:

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

    Den här koden ser till att data bara uppdateras från tjänsten efter att du har autentiserats.
7. Lägg till följande hanterare för händelsen **Klickad** i klassen **TodoList:**

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Spara ändringarna och återskapa projektet Portable Class Library som inte verifierar några fel.

## <a name="add-authentication-to-the-android-app"></a>Lägga till autentisering i Android-appen
Det här avsnittet visar hur du implementerar **IAuthenticate-gränssnittet** i Android-appprojektet. Hoppa över det här avsnittet om du inte stöder Android-enheter.

1. Högerklicka på **droidprojektet** i Visual Studio eller Xamarin Studio och ange **sedan StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökningsprogrammet och kontrollera sedan att ett ohanterat undantag med statuskoden 401 (Obehörig) utlöses när appen startar. 401-koden produceras eftersom åtkomst på backend endast är begränsad till behöriga användare.
3. Öppna MainActivity.cs i Android-projektet och lägg `using` till följande satser:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatera klassen **MainActivity** för att implementera **gränssnittet IAuthenticate** enligt följande:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Uppdatera klassen **MainActivity** genom att lägga till ett **MobileServiceUser-fält** och en **authenticate-metod,** som krävs av **gränssnittet IAuthenticate,** enligt följande:

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

    Om du använder en annan identitetsleverantör än Facebook väljer du ett annat värde för [MobileServiceAuthenticationProvider][7].

6. Uppdatera filen **AndroidManifest.xml** genom att lägga `<application>` till följande XML i elementet:

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
    Ersätt `{url_scheme_of_your_app}` med url-schemat.
7. Lägg till följande kod i **OnCreate-metoden** för klassen `LoadApplication()` **MainActivity** före anropet till:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Den här koden säkerställer att autentiseraren initieras innan appen läses in.
8. Återskapa appen, kör den och logga sedan in med den autentiseringsleverantör du valde och verifiera att du kan komma åt data som autentiserade användare.

### <a name="troubleshooting"></a>Felsökning

**Programmet kraschade med`Java.Lang.NoSuchMethodError: No static method startActivity`**

I vissa fall visas konflikter i supportpaketen som bara en varning i Visual-studion, men programmet kraschar med det här undantaget vid körning. I det här fallet måste du se till att alla supportpaket som refereras i projektet har samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) är `Xamarin.Android.Support.CustomTabs` beroende av Android-plattformen, så om projektet använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

## <a name="add-authentication-to-the-ios-app"></a>Lägga till autentisering i iOS-appen
Det här avsnittet visar hur du implementerar **IAuthenticate-gränssnittet** i iOS-appprojektet. Hoppa över det här avsnittet om du inte stöder iOS-enheter.

1. Högerklicka på **iOS-projektet** i Visual Studio eller Xamarin Studio och ange sedan **startupprojekt**.
2. Tryck på F5 för att starta projektet i felsökningsprogrammet och kontrollera sedan att ett ohanterat undantag med statuskoden 401 (Obehörig) utlöses när appen startar. 401-svaret produceras eftersom åtkomst på backend endast är begränsad till behöriga användare.
3. Öppna AppDelegate.cs i iOS-projektet och lägg `using` till följande satser:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Uppdatera **klassen AppDelegate** för att implementera **gränssnittet IAuthenticate** enligt följande:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Uppdatera klassen **AppDelegate** genom att lägga till ett **MobileServiceUser-fält** och en **authenticate-metod,** som krävs av **gränssnittet IAuthenticate,** enligt följande:

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

    Om du använder en annan identitetsleverantör än Facebook väljer du ett annat värde för [MobileServiceAuthenticationProvider].
    
6. Uppdatera klassen **AppDelegate** genom att lägga till **OpenUrl-metodens** överbelastning enligt följande:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Lägg till följande kodrad i metoden **Färdigstart** innan `LoadApplication()`anropet till:

        App.Init(this);

    Den här koden säkerställer att autentiseraren initieras innan appen läses in.

8. Öppna Info.plist och lägg till en **URL-typ**. Ange **identifieraren** till ett namn som du väljer, **URL-scheman** till URL-schemat för din app och **rollen** till Ingen.

9. Återskapa appen, kör den och logga sedan in med den autentiseringsleverantör du valde och verifiera att du kan komma åt data som autentiserade användare.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Lägga till autentisering i Windows 10-appprojekt (inklusive telefon)
I det här avsnittet visas hur du **implementerar IAuthenticate-gränssnittet** i Windows 10-appprojekten. Samma steg gäller för UWP-projekt (Universal Windows Platform), men med hjälp av **UWP-projektet** (med noterade ändringar). Hoppa över det här avsnittet om du inte stöder Windows-enheter.

1. Högerklicka på **UWP-projektet** i Visual Studio och ange **sedan StartUp Project**.
2. Tryck på F5 för att starta projektet i felsökningsprogrammet och kontrollera sedan att ett ohanterat undantag med statuskoden 401 (Obehörig) utlöses när appen startar. 401-svaret sker eftersom åtkomst på backend endast är begränsad till behöriga användare.
3. Öppna MainPage.xaml.cs för Windows-appprojektet och lägg `using` till följande satser:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Ersätt `<your_Portable_Class_Library_namespace>` med namnområdet för det bärbara klassbiblioteket.
4. Uppdatera **klassen MainPage** för att implementera **gränssnittet IAuthenticate** enligt följande:

        public sealed partial class MainPage : IAuthenticate
5. Uppdatera klassen **MainPage** genom att lägga till ett **MobileServiceUser-fält** och en **authenticate-metod,** som krävs av **IAuthenticate-gränssnittet,** enligt följande:

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

    Om du använder en annan identitetsleverantör än Facebook väljer du ett annat värde för [MobileServiceAuthenticationProvider][7].

1. Lägg till följande kodrad i konstruktorn för klassen `LoadApplication()` **MainPage** före anropet i:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Ersätt `<your_Portable_Class_Library_namespace>` med namnområdet för det bärbara klassbiblioteket.

3. Om du använder **UWP**lägger du till följande **OnActivated-metod** åsidosättning i **klassen App:**

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Öppna Package.appxmanifest och lägg till en **protokolldeklaration.** Ange **visningsnamnet** till ett namn som du väljer och **namnet** på url-schemat för dig-appen.

4. Återskapa appen, kör den och logga sedan in med den autentiseringsleverantör du valde och verifiera att du kan komma åt data som autentiserade användare.

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här grundläggande autentiseringsstudien bör du fortsätta till någon av följande självstudier:

* [Lägg till push-meddelanden i appen](app-service-mobile-xamarin-forms-get-started-push.md)

  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Offlinesynkronisering gör det möjligt för slutanvändare att interagera med en mobilapp - visa, lägga till eller ändra data - även när det inte finns någon nätverksanslutning.

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
