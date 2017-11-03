---
title: "Lägg till autentisering i appen universella Windowsplattformen (UWP) | Microsoft Docs"
description: "Lär dig hur du använder Azure Apptjänst Mobilappar för att autentisera användare i appen universella Windowsplattformen (UWP) med olika identitetsleverantörer, inklusive: AAD, Google, Facebook, Twitter och Microsoft."
services: app-service\mobile
documentationcenter: windows
author: ggailey777
manager: panarasi
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 47da343d4ec956ec2e669757f56e853675f887a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-windows-app"></a>Lägg till autentisering i Windows-appen
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Det här avsnittet visar hur du lägger till molnbaserad autentisering till din mobilapp. I kursen får du lägger till autentisering snabbstartsprojekt universella Windowsplattformen (UWP) för Mobilappar som använder en identitetsleverantör som stöds av Azure App Service. Efter att kunna autentiseras och auktoriseras av din mobilappsserverdel visas användar-ID-värdet.

Den här kursen är baserad på Mobile Apps-Snabbstart. Du måste slutföra kursen [Kom igång med Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i tillåtna externa omdirigerings-URL

Säker autentisering måste du definiera en ny URL-schema för din app. Detta gör att autentiseringssystemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudiekursen kommer vi använda URL-schemat _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för din mobila program. Du vill aktivera omdirigering på serversidan:

1. Välj din Apptjänst i [Azure-portalen].

2. Klicka på den **autentisering / auktorisering** menyalternativet.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `url_scheme_of_your_app://easyauth.callback`.  Den **url_scheme_of_your_app** i den här strängen är URL-schemat för din mobila program.  Det bör följa den normala URL specifikation för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera mobilprogram koden med URL-schemat på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrollera att anonym åtkomst till din serverdel har inaktiverats. Med UWP-appsprojektet som start-projektet, distribuera och köra appen; Kontrollera att ett undantag med en statuskod 401 (obehörig) aktiveras när appen startar. Detta beror på att appen försöker få åtkomst till din mobila Appkod som en oautentiserad användare men *TodoItem* tabellen nu kräver autentisering.

Därefter uppdaterar du appen för att autentisera användare innan du begär resurser från din Apptjänst.

## <a name="add-authentication"></a>Lägg till autentisering i appen
1. I UWP app projekt filen MainPage.xaml.cs och Lägg till följande kodfragment:
   
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
   
    Den här koden autentiserar användaren med en inloggning med Facebook. Om du använder en identitetsleverantör än Facebook, ändrar du värdet för **MobileServiceAuthenticationProvider** ovan med värdet för leverantören.
2. Ersätt den **OnNavigatedTo()** metod i MainPage.xaml.cs. Sedan lägger du till en **inloggning** knappen till appen som utlöser verifiering.

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
4. Öppna projektfilen MainPage.xaml, leta upp det element som definierar den **spara** knappen och Ersätt den med följande kod:
   
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
6. Öppna filen Package.appxmanifest, gå till **deklarationer**i **tillgängliga deklarationer** listrutan, Välj **protokollet** och klicka på **Lägg till** knappen. Nu konfigurera den **egenskaper** av den **protokollet** deklaration. I **visningsnamn**, lägga till namn som du vill visa användare av ditt program. I **namn**, lägga till {url_scheme_of_your_app}.
7. Tryck på F5 för att köra appen klickar du på den **inloggning** knappen och logga in i appen med din valda identitetsprovider. När inloggningen är klar, appen körs utan fel och du kan fråga din serverdel och göra uppdateringar till data.

## <a name="tokens"></a>Lagra autentiseringstoken på klienten
Föregående exempel visade en standard inloggning, vilket kräver att klienten kontakta både identitetsleverantören och App Service varje gång appen startar. Är inte bara den här metoden ineffektiv, kan du köra till användning-relaterar problem bör många kunder försöker starta appen på samma gång. En bättre metod är att cachelagra den autentiseringstoken som returneras av App Service och försök att använda den här första innan du använder en provider-baserad inloggning.

> [!NOTE]
> Du kan cachelagra den token som utfärdas av Apptjänster oavsett om du använder klient-hanteras eller service autentisering. Den här kursen använder autentisering som hanteras av tjänsten.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här självstudiekursen för grundläggande autentisering, Överväg fortsätter in på något av följande kurser:

* [Lägg till push-meddelanden i appen](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
