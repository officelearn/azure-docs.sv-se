---
title: Lägg till autentisering till din Universell Windows-plattform-app (UWP) | Microsoft Docs
description: 'Lär dig hur du använder Azure App Service Mobile Apps för att autentisera användare av din Universell Windows-plattform-app (UWP) med hjälp av olika identitets leverantörer, inklusive: AAD, Google, Facebook, Twitter och Microsoft.'
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: ae1a4c7570a4430c9961109c425298e356c9fa9a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027084"
---
# <a name="add-authentication-to-your-windows-app"></a>Lägg till autentisering i Windows-appen
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du lägger till molnbaserad autentisering i mobilappen. I den här självstudien lägger du till autentisering i snabb starts projektet för Universell Windows-plattform (UWP) för Mobile Apps med hjälp av en identitetsprovider som stöds av Azure App Service. När du har autentiserat och auktoriserat av din server del för mobilappen visas värdet användar-ID.

Den här självstudien baseras på Mobile Apps snabb start. Du måste först gå igenom kursen [Kom igång med Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa omdirigerings-URL: erna

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör att Authentication-systemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder _vi program_ varan för URL-program i alla. Du kan dock använda alla URL-scheman du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på Server sidan:

1. I [Azure Portal](https://ms.portal.azure.com)väljer du App Service.

2. Klicka på meny alternativet **autentisering/auktorisering** .

3. I de **tillåtna externa omdirigerings-URL: erna**anger du `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** i den här STRÄNGEN är URL-schemat för det mobila programmet.  Den bör följa normal URL-specifikation för ett protokoll (Använd bara bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera koden för mobil program med URL-schemat på flera platser.

4. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrol lera att anonym åtkomst till Server delen har inaktiverats. Med UWP app-projektet inställt som start projekt, distribuera och kör appen. kontrol lera att ett ohanterat undantag med status kod 401 (obehörig) utlöses när appen startar. Detta inträffar eftersom appen försöker komma åt din mobilapp som en oautentiserad användare, men *TodoItem* -tabellen kräver nu autentisering.

Sedan ska du uppdatera appen för att autentisera användare innan du begär resurser från din App Service.

## <a name="add-authentication"></a>Lägg till autentisering i appen
1. I projekt filen för UWP-appen MainPage.xaml.cs och lägger till följande kodfragment:
   
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
   
    Den här koden autentiserar användaren med en Facebook-inloggning. Om du använder en annan identitets leverantör än Facebook ändrar du värdet för **MobileServiceAuthenticationProvider** ovanför till värdet för din Provider.
2. Ersätt metoden **OnNavigatedTo ()** i mainpage.XAML.cs. Sedan lägger du till knappen **Logga** in i appen som utlöser autentisering.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Lägg till följande kodfragment i MainPage.xaml.cs:
   
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
4. Öppna projekt filen MainPage. XAML, leta upp det element som definierar knappen **Spara** och ersätt den med följande kod:
   
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
5. Lägg till följande kodfragment i App.xaml.cs:

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
6. Öppna Package. appxmanifest-filen, navigera till **deklarationer**i list rutan **tillgängliga deklarationer** , Välj **protokoll** och klicka på knappen **Lägg till** . Nu ska du konfigurera **egenskaperna** för **protokoll** deklarationen. I **visnings namn**lägger du till det namn som du vill visa för användare av ditt program. I **namn**lägger du till {url_scheme_of_your_app}.
7. Tryck på F5 för att köra appen, klicka på knappen **Logga in** och logga in på appen med den valda identitets leverantören. När inloggningen är klar körs appen utan fel och du kan ställa frågor till Server delen och göra uppdateringar av data.

## <a name="tokens"></a>Lagra autentiseringstoken på klienten
Föregående exempel visade en standard inloggning, som kräver att klienten kontaktar både identitets leverantören och App Service varje gång appen startas. Det är inte bara den här metoden som är ineffektiv, du kan köra användnings problem om många kunder försöker starta appen samtidigt. En bättre metod är att cachelagra autentiseringstoken som returneras av din App Service och försöka använda det först innan du använder en providerspecifik inloggning.

> [!NOTE]
> Du kan cachelagra token som utfärdas av App Services oavsett om du använder klient-eller tjänstehanterad autentisering. I den här självstudien används tjänst hanterad autentisering.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här grundläggande autentiseringen kan du fortsätta med någon av följande själv studie kurser:

* [Lägg till push-meddelanden i appen](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
