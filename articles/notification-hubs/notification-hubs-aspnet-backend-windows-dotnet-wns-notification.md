---
title: Skicka meddelanden till specifika användare med Azure Notification Hubs | Microsoft Docs
description: Lär dig mer om att skicka meddelanden till specifika användare med hjälp av Universal Windows Platform-program (UWP).
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.author: dimazaid
ms.openlocfilehash: d2d0e878aed5b200852e56a29ce0430305d4efc6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251550"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Självstudier: Skicka meddelanden till specifika användare med Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Översikt
Denna självstudie visar hur du använder Azure Notification Hubs till att skicka push-meddelanden till en specifik appanvändare på en specifik enhet. En ASP.NET-WebAPI-serverdel används för att autentisera klienter. När serverdelen autentiserar en klientprogramsanvändare lägger den automatiskt till en tagg till meddelanderegistreringen. Serverdelen använder den här taggen för att skicka meddelanden till specifika användare. 

> [!NOTE]
> Den slutförda koden för den här självstudiekursen hittar du [på GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett WebAPI-projekt
> * Autentisera klienter mot WebAPI-serverdelen
> * Registrera för meddelanden med hjälp av WebAPI-serverdelen
> * Skicka meddelanden från WebAPI-serverdelen
> * Publicera den nya WebAPI-serverdelen
> * Uppdatera klientprojektets kod
> * Testa programmet


## <a name="prerequisites"></a>Nödvändiga komponenter
Den här kursen bygger på meddelandehubben och det Visual Studio-projekt som du skapade i kursen [Självstudier: Skicka meddelanden till UWP-appar med Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Slutför den därför innan du påbörjar den här kursen. 

> [!NOTE]
> Om du använder Mobile Apps i Azure App Service som serverdelstjänst, så läs [Mobile Apps-version](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) i den här kursen.


&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Uppdatera klientprojektets kod
I det här avsnittet kommer du att uppdatera koden i det projekt som du slutförde i kursen [Självstudier: Skicka meddelanden till UWP-appar med Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Projektet bör redan ha associerats till Windows Store. Det bör också ha konfigurerats för meddelandehubben. I det här avsnittet kommer du att lägga till kod för att anropa den nya WebAPI-serverdelen och använda den för att registrera och skicka meddelanden.

1. Öppna den lösning som du slutförde i [Självstudier: Skicka meddelanden till UWP-appar med Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) i Visual Studio.
2. Högerklicka på projektet **WindowsApp** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
3. Klicka på **Online** till vänster.
4. Skriv **Http-klient** i rutan **Sök**.
5. Klicka på **System.Net.Http** i resultatlistan och klicka på **Installera**. Slutför installationen.
6. Gå tillbaka till rutan **Sök** i NuGet och skriv **Json.net**. Installera **Newtonsoft.json**-paketet och stäng sedan NuGet Package Manager-fönstret.
8. Dubbelklicka på filen **MainPage.xaml** i **WindowsApp**-projektet i Solution Explorer och öppna den i Visual Studio-redigeraren.
9. Ersätt avsnittet `<Grid>` i **MainPage.xaml**-XML-koden med följande kod: den här koden lägger till en textruta för användarnamn och lösenord som användaren autentiserar sig med. Den lägger också till textrutor för aviseringsmeddelandet och username-taggen som ska ta emot meddelandet:

    ```xml   
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
    ```
11. Öppna filen **MainPage.xaml.cs** för projekten **(Windows 8.1)** och **(Windows Phone 8.1)** i Solution Explorer. Lägg till följande `using`-uttryck högst upp i respektive fil:

    ```csharp    
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```
12. Lägg till följande medlem till `MainPage`-klassen i **MainPage.xaml.cs** för **WindowsApp**-projektet. Kom ihåg att ersätta `<Enter Your Backend Endpoint>` med din faktiska serverdelsslutpunkt som hämtades tidigare. Till exempel `http://mybackend.azurewebsites.net`.
    
    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```
13. Lägg till koden nedan i MainPage-klassen i **MainPage.xaml.cs** för projekten **(Windows 8.1)** och **(Windows Phone 8.1)**.
    
    Metoden `PushClick` är klickhanterare för knappen **Skicka Push**. Anropar serverdelen för att utlösa en avisering till alla enheter med en användarnamnstagg som matchar `to_tag`-parametern. Meddelandet skickas som JSON-innehåll i begärandetexten.
    
    Metoden `LoginAndRegisterClick` är klickhanterare för knappen **Logga in och registrera**. Den laggrar grundläggande autentiseringstoken (motsvarar alla token ditt autentiseringsschema använder) i lokal lagring, och använder sedan `RegisterClient` för att registrera för meddelanden som använder serverdelen.

    ```csharp
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
            // The device handle used is different depending on the device and PNS. 
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
    ```
1. Öppna filen **App.xaml.cs**. Hitta anropet till `InitNotificationsAsync()` i `OnLaunched()`-händelsehanteraren. Kommentera ut eller ta bort anropet till `InitNotificationsAsync()`. Knapphanteraren initierar meddelanderegistreringar.

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```
1. Högerklicka på **WindowsApp**-projektet, klicka på **Lägg till** och sedan på **Klass**. Namnge klassen **RegisterClient.cs** och generera sedan klassen genom att klicka på **OK**.
   
   Den här klassen omsluter de REST-anrop som krävs för att kontakta appens serverdel och registrera push-meddelanden. Den lagrar även lokalt de *registrationIds* som skapas av den meddelandehubb som anges i [Registrering från din apps serverdel](https://msdn.microsoft.com/library/dn743807.aspx). Den använder en autentiseringstoken som lagras lokalt när du klickar på knappen **Logga in och registrera**.
2. Lägg till följande `using`-uttryck högst upp i filen RegisterClient.cs:

    ```csharp   
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```
3. Lägg till följande kod i `RegisterClient`-klassdefinitionen.
   
    ```csharp
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

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
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
    ```
4. Spara alla ändringar.

## <a name="test-the-application"></a>Testa programmet
1. Starta programmet i båda Windows-versionerna.
2. Ange **Användarnamn** och **Lösenord** så som visas på skärmen nedan. Det bör skilja sig från det användarnamn och lösenord som du anger på Windows Phone.
3. Klicka på **Logga in och registrera** och en verifieringsdialogruta visar att du har loggat in. Den här koden aktiverar även knappen **Skicka Push**.
   
    ![][14]
5. Ange sedan det registrerade användarnamnet i fältet **Mottagarens användarnamnstagg**. Skriv ett aviseringsmeddelande och klicka på **Skicka Push**.
6. Endast de enheter som har registrerats med matchande användarnamnstagg får aviseringsmeddelandet.
   
    ![][15]

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig mer om push-meddelanden till specifika användare som har taggar associerade med sina registreringar. Information om hur du skickar platsbaserade meddelanden finns i nästa självstudie: 

> [!div class="nextstepaction"]
>[Platsbaserade push-meddelanden](notification-hubs-push-bing-spartial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
