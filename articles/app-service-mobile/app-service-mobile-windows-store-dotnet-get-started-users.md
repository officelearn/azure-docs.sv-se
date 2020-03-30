---
title: Lägga till autentisering i din UWP-app
description: Lär dig hur du använder Azure App Service Mobile Apps för att autentisera användare av din UWP-app (Universal Windows Platform) med identitetsleverantörer som AAD, Google, Facebook, Twitter och Microsoft.
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 32d4313b345964a2db13d68e83f81756a4acf0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458944"
---
# <a name="add-authentication-to-your-windows-app"></a>Lägga till autentisering i Din Windows-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Översikt
I det här avsnittet visas hur du lägger till molnbaserad autentisering i mobilappen. I den här självstudien lägger du till autentisering i snabbstartsprojektet för Universell Windows-plattform (UWP) för mobilappar med hjälp av en identitetsprovider som stöds av Azure App Service. När användar-ID-värdet har autentiserats och auktoriserats av din mobile app-backend visas det.

Den här självstudien baseras på snabbstarten för mobilappar. Du måste först slutföra självstudien [Kom igång med mobilappar](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrera appen för autentisering och konfigurera App-tjänsten
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Lägga till appen i url:erna för tillåtna externa omdirigering

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör det möjligt för autentiseringssystemet att omdirigera tillbaka till din app när autentiseringsprocessen är klar. I den här självstudien använder vi _url-schemats appnamn_ hela tiden. Du kan dock använda vilket URL-schema du vill. Den ska vara unik för din mobilapplikation. Så här aktiverar du omdirigering på serversidan:

1. Välj din App-tjänst i [Azure-portalen.](https://ms.portal.azure.com)

2. Klicka på **menyalternativet Autentisering/auktorisering.**

3. Ange i **url:erna Tillåten extern omdirigering** `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobilapplikation.  Den bör följa normal URL-specifikation för ett protokoll (använd endast bokstäver och siffror och börja med en bokstav).  Du bör anteckna strängen som du väljer eftersom du måste justera din mobilprogramkod med URL-schemat på flera ställen.

4. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrollera att anonym åtkomst till din backend har inaktiverats. Med UWP-appprojektet inställt som startprojekt distribuerar och kör appen. kontrollera att ett ohanterat undantag med en statuskod på 401 (Obehörig) utlöses när appen startar. Detta beror på att appen försöker komma åt din mobilappkod som en oautentiserade användare, men *tabellen TodoItem* kräver nu autentisering.

Därefter uppdaterar du appen för att autentisera användare innan du begär resurser från apptjänsten.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Lägga till autentisering i appen
1. I UWP-appprojektfilen MainPage.xaml.cs och lägg till följande kodavsnitt:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Den här koden autentiserar användaren med en Facebook-inloggning. Om du använder en annan identitetsleverantör än Facebook ändrar du värdet för **MobileServiceAuthenticationProvider** ovan till värdet för din leverantör.
2. Ersätt metoden **OnNavigatedTo()** i MainPage.xaml.cs. Därefter lägger du till knappen **Logga in i** appen som utlöser autentisering.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Lägg till följande kodavsnitt i MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Öppna projektfilen MainPage.xaml, leta reda på elementet som definierar **knappen Spara** och ersätt den med följande kod:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Lägg till följande kodavsnitt i App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Öppna filen Package.appxmanifest, navigera till **deklarationer**, i listrutan **Tillgängliga deklarationer** väljer du **Protokoll** och klickar på Knappen **Lägg till.** Konfigurera nu **egenskaperna för** **protokolldeklarationen.** I **Visa namn**lägger du till det namn som du vill visa för användare av ditt program. Lägg till {url_scheme_of_your_app} i **Namn.**
7. Tryck på F5 för att köra appen, klicka på **inloggningsknappen** och logga in på appen med din valda identitetsleverantör. När inloggningen har framgångsrikt körs appen utan fel och du kan fråga din backend och göra uppdateringar av data.

## <a name="store-the-authentication-token-on-the-client"></a><a name="tokens"></a>Lagra autentiseringstoken på klienten
I föregående exempel visades en standard inloggning, som kräver att klienten kontaktar både identitetsleverantören och App-tjänsten varje gång appen startar. Den här metoden är inte bara ineffektiv, du kan stöta på problem med användning om många kunder försöker starta appen samtidigt. En bättre metod är att cachelagra auktoriseringstoken som returneras av din App-tjänst och försöka använda den här först innan du använder en providerbaserad inloggning.

> [!NOTE]
> Du kan cachelagra den token som utfärdats av App Services oavsett om du använder klienthanterad eller tjänsthanterad autentisering. Den här självstudien använder tjänsthanterad autentisering.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här grundläggande autentiseringsstudien bör du fortsätta till någon av följande självstudier:

* [Lägg till push-meddelanden i appen](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
