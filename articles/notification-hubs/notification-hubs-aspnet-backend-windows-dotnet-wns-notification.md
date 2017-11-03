---
title: "Azure Notification Hubs Meddelandeanvändare med .NET-serverdel"
description: "Lär dig mer om att skicka säkra push-meddelanden i Azure. Kodexempel som skrivits i C# med hjälp av .NET-API."
documentationcenter: windows
author: ysxu
manager: erikre
services: notification-hubs
editor: 
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: c0b963ef661612b1a176dd8e5f01d56e61eb5acb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-notify-users-with-net-backend"></a>Azure Notification Hubs Meddelandeanvändare med .NET-serverdel
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Översikt
Stöd för push-meddelanden i Azure kan du få ett enkelt att använda och multiplatform skalats ut push-infrastruktur, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila plattformar. Denna självstudie visar hur du använder Azure-meddelandehubbar för att skicka push-meddelanden till en specifik app-användare på en viss enhet. En ASP.NET-WebAPI-serverdel används för att autentisera klienter. Med hjälp av autentiserade klientanvändare och tagg automatiskt läggas till av serverdelen registreringen av meddelanden. Den här etiketten används för att skicka av serverdelen att generera meddelanden för en viss användare. Mer information om registrering för meddelanden med hjälp av en appserverdel finns i avsnittet vägledning [registrering från din Apps serverdel](http://msdn.microsoft.com/library/dn743807.aspx). Den här kursen bygger på meddelandehubben och projekt som du skapade i den [Kom igång med Notification Hubs] kursen.

Den här kursen är också nödvändigt för att den [Secure Push] kursen. När du har slutfört stegen i den här självstudiekursen, kan du fortsätta till den [Secure Push] kursen visar hur du ändrar koden i självstudierna för att skicka ett push-meddelande på ett säkert sätt.

## <a name="before-you-begin"></a>Innan du börjar
Vi tar din feedback på allvar. Om du har problem med att slutföra det här ämnet, eller om du har tips på hur innehållet kan förbättras, tar vi tacksamt emot din feedback längst ner på sidan.

Den färdiga koden för den här självstudiekursen hittar du på GitHub [här](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

## <a name="prerequisites"></a>Krav
Innan du börjar den här självstudiekursen, måste du redan har slutfört de här kurserna i Mobile Services:

* [Kom igång med Notification Hubs]<br/>Du skapar din meddelandehubb och reservera namnet på appen och registrera dig för att ta emot meddelanden i den här självstudiekursen. Den här kursen förutsätter att du redan har slutfört de här stegen. Om inte, följer du stegen i [komma igång med Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md); specifikt avsnitten [registrera din app för Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store) och [konfigurera din Meddelandehubb](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Särskilt, se till att du har angett den **paket-SID** och **Klienthemlighet** i portalen av värdena i den **konfigurera** för meddelandehubben. Den här proceduren konfiguration beskrivs i avsnittet [konfigurera din Meddelandehubb](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Detta är ett viktigt steg: om autentiseringsuppgifter på portalen inte matchar dem som anges för namnet på appen du väljer, push-meddelande kommer att misslyckas.

> [!NOTE]
> Om du använder Mobile Apps i Azure App Service som backend-tjänst finns i [Mobile Apps version](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) i den här kursen.
> 
> 

&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Uppdatera koden för klientprojektet
I det här avsnittet kan du uppdatera koden i projektet som du har slutförts för den [Kom igång med Notification Hubs] kursen. Den redan vara som är kopplade till arkivet och konfigurerats för meddelandehubben. I det här avsnittet kan du lägga till kod för att anropa den nya WebAPI-serverdelen och använda den för registrering och skicka meddelanden.

1. Öppna i Visual Studio den lösning som du skapade för den [Kom igång med Notification Hubs] kursen.
2. I Solution Explorer högerklickar du på den **(Windows 8.1)** projektet och klicka sedan på **hantera NuGet-paket**.
3. Klicka på den vänstra sidan **Online**.
4. I den **Sök** skriver **http-klienten**.
5. Klicka i resultatlistan **klientbibliotek för Microsoft HTTP**, och klicka sedan på **installera**. Slutför installationen.
6. Tillbaka i NuGet **Sök** skriver **Json.net**. Installera den **Json.NET** paketera och stäng sedan NuGet Package Manager-fönstret.
7. Upprepa stegen ovan för den **(Windows Phone 8.1)** projekt för att installera den **JSON.NET** NuGet-paket för Windows Phone-projektet.
8. I Solution Explorer i den **(Windows 8.1)** projektet genom att dubbelklicka på **MainPage.xaml** att öppna den i Visual Studio-redigeraren.
9. I den **MainPage.xaml** XML-kod, ersätter den `<Grid>` avsnitt med följande kod. Den här koden lägger till en textruta för användarnamn och lösenord som användaren autentiseras med. Det ger också textrutor för meddelandet och username-tagg som ska ta emot meddelandet:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
   
            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>
   
            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
   
                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>
   
                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />
   
                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>
10. I Solution Explorer i den **(Windows Phone 8.1)** projektet öppnar **MainPage.xaml** och ersätter Windows Phone 8.1 `<Grid>` avsnitt med samma koden ovan. Gränssnittet bör likna nyheter som visas nedan.
    
    ![][13]
11. I Solution Explorer öppnar den **MainPage.xaml.cs** för den **(Windows 8.1)** och **(Windows Phone 8.1)** projekt. Lägg till följande `using` instruktioner överst i båda filerna:
    
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;
12. I **MainPage.xaml.cs** för den **(Windows 8.1)** och **(Windows Phone 8.1)** projekt, Lägg till följande medlem till den `MainPage` klass. Se till att ersätta `<Enter Your Backend Endpoint>` med den faktiska backend-slutpunkt som hämtats tidigare. Till exempel `http://mybackend.azurewebsites.net`.
    
        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
13. Lägg till koden nedan i klassen MainPage i **MainPage.xaml.cs** för den **(Windows 8.1)** och **(Windows Phone 8.1)** projekt.
    
    Den `PushClick` metod är Klicka hanterare för den **skicka Push-** knappen. Anropar serverdelen för att utlösa en avisering till alla enheter med en tagg för användarnamn som matchar den `to_tag` parameter. Meddelandet skickas som JSON-innehåll i begärandetexten.
    
    Den `LoginAndRegisterClick` metod är Klicka hanterare för den **logga in och registrera** knappen. Lagrar grundläggande autentiseringstoken i lokal lagring (Observera att detta motsvarar alla token som använder din autentiseringsschema), använder sedan `RegisterClient` att registrera för meddelanden med serverdelen.

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                // The device handle used will be different depending on the device and PNS. 
                // Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



1. I Solution Explorer under den **delade** projektet öppnar den **App.xaml.cs** fil. Hitta anropet till `InitNotificationsAsync()` i den `OnLaunched()` händelsehanterare. Kommentera ut eller ta bort anropet till `InitNotificationsAsync()`. Knappen hanteraren lades till ovan ska initiera notification registreringar.

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


1. I Solution Explorer högerklickar du på den **delade** projektet och klicka sedan på **Lägg till**, och klicka sedan på **klassen**. Klassen namnet **RegisterClient.cs**, klicka på **OK** att skapa klassen.
   
   Den här klassen radbryts REST-anrop som krävs för att kontakta appens serverdel för att kunna registrera för push-meddelanden. Den lagrar även lokalt på *registrationIds* skapas av Meddelandehubben som anges i [registrering från din Apps serverdel](http://msdn.microsoft.com/library/dn743807.aspx). Observera att den använder en autentiseringstoken som lagras i lokal lagring när du klickar på den **logga in och registrera** knappen.
2. Lägg till följande `using` instruktioner överst i filen RegisterClient.cs:
   
       using Windows.Storage;
       using System.Net;
       using System.Net.Http;
       using System.Net.Http.Headers;
       using Newtonsoft.Json;
       using System.Threading.Tasks;
       using System.Linq;
3. Lägg till följande kod i `RegisterClient`-klassdefinitionen.
   
       private string POST_URL;
   
       private class DeviceRegistration
       {
           public string Platform { get; set; }
           public string Handle { get; set; }
           public string[] Tags { get; set; }
       }
   
       public RegisterClient(string backendEndpoint)
       {
           POST_URL = backendEndpoint + "/api/register";
       }
   
       public async Task RegisterAsync(string handle, IEnumerable<string> tags)
       {
           var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
   
           var deviceRegistration = new DeviceRegistration
           {
               Platform = "wns",
               Handle = handle,
               Tags = tags.ToArray<string>()
           };
   
           var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
   
           if (statusCode == HttpStatusCode.Gone)
           {
               // regId is expired, deleting from local storage & recreating
               var settings = ApplicationData.Current.LocalSettings.Values;
               settings.Remove("__NHRegistrationId");
               regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
               statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
           }
   
           if (statusCode != HttpStatusCode.Accepted)
           {
               // log or throw
               throw new System.Net.WebException(statusCode.ToString());
           }
       }
   
       private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
       {
           using (var httpClient = new HttpClient())
           {
               var settings = ApplicationData.Current.LocalSettings.Values;
               httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);
   
               var putUri = POST_URL + "/" + regId;
   
               string json = JsonConvert.SerializeObject(deviceRegistration);
                               var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
               return response.StatusCode;
           }
       }
   
       private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
       {
           var settings = ApplicationData.Current.LocalSettings.Values;
           if (!settings.ContainsKey("__NHRegistrationId"))
           {
               using (var httpClient = new HttpClient())
               {
                   httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                   var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                   if (response.IsSuccessStatusCode)
                   {
                       string regId = await response.Content.ReadAsStringAsync();
                       regId = regId.Substring(1, regId.Length - 2);
                       settings.Add("__NHRegistrationId", regId);
                   }
                   else
                   {
                       throw new System.Net.WebException(response.StatusCode.ToString());
                   }
               }
           }
           return (string)settings["__NHRegistrationId"];
   
       }
4. Spara alla ändringar.

## <a name="testing-the-application"></a>Testa programmet
1. Starta programmet på både Windows 8.1 och Windows Phone 8.1. Du kan köra instansen i emulatorn eller en enhet för Windows Phone 8.1.
2. I Windows 8.1-instans av appen anger du en **användarnamn** och **lösenord** som visas på skärmen nedan. Det bör skiljer sig från det användarnamn och lösenord som du anger på Windows Phone.
3. Klicka på **logga in och registrera** och verifiera en dialogruta visar att du har loggat in. Detta kommer också att aktivera den **skicka Push-** knappen.
   
    ![][14]
4. Ange sträng en användare på Windows Phone 8.1-instans i både den **användarnamn** och **lösenord** fält klicka **logga in och registrera**.
5. I den **mottagaren användarnamn taggen** anger det användarnamn som är registrerad på Windows 8.1. Ange ett meddelande och klicka på **skicka Push-**.
   
    ![][16]
6. Endast de enheter som har registrerats med taggen matchande användarnamn får meddelandet.
   
    ![][15]

## <a name="next-steps"></a>Nästa steg
* Om du vill dela in användarna efter intressegrupper, kan du gå till [Använda Notification Hubs för att skicka de senaste nyheterna].
* Mer information om hur du använder Notification Hubs finns [riktlinjer om Notification Hubs].

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Kom igång med Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[riktlinjer om Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
