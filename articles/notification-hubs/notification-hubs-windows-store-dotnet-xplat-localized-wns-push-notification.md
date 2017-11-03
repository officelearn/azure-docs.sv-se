---
title: "Notification Hubs lokaliserade senaste nyheterna självstudiekursen"
description: "Lär dig hur du använder Azure Notification Hubs för att skicka lokaliserade senaste nyheterna meddelanden."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 8f205188bd68e53b187b71981ed36dcf9129ec62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Använda Notification Hubs för att skicka lokaliserade senaste nyheterna
> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du använder den **mallen** funktion i Azure Notification Hubs för att sända senaste nyheterna meddelanden som har översatts av språk och enheten. I den här kursen börjar du med Windows Store-app som skapats i [använda Notification Hubs för att skicka de senaste nyheterna]. När du är klar kommer du att kunna registrera för kategorier som du vill, ange ett språk som ska ta emot meddelanden och får endast push-meddelanden i valda kategorier på det språket.

Det finns två delar i det här scenariot:

* Windows Store-appen kan klienten enheter för att ange ett språk och prenumerera på olika senaste nyheterna kategorier.
* backend-skickar meddelanden med hjälp av den **taggen** och **mallen** feautres i Azure Notification Hubs.

## <a name="prerequisites"></a>Krav
Du måste redan har slutfört den [använda Notification Hubs för att skicka de senaste nyheterna] självstudier och har kod som är tillgängliga, eftersom den här kursen bygger direkt på koden.

Du måste också Visual Studio 2012 eller senare.

## <a name="template-concepts"></a>Mall-begrepp
I [använda Notification Hubs för att skicka de senaste nyheterna] du skapat en app som används för **taggar** att prenumerera på meddelanden om nyheterna i olika kategorier.
Många appar dock mål flera marknader och kräver lokalisering. Detta innebär att innehållet i meddelandena själva måste lokaliserade och levereras till rätt uppsättning enheter.
I det här avsnittet visar vi hur du använder den **mallen** funktion i Notification Hubs för att leverera enkelt lokaliserade senaste nyheterna meddelanden.

Obs: ett sätt att skicka lokaliserade meddelanden är att skapa flera versioner av varje tagg. Till exempel för att stödja engelska, franska och Mandarin, vi behöver tre olika taggar för world news: ”world_en”, ”world_fr” och ”world_ch”. Vi har sedan skicka en lokaliserad version av world nyheter till var och en av dessa taggar. Vi använder mallar för att undvika alltför många taggar och kravet flera meddelanden i det här avsnittet.

Mallar är ett sätt att ange hur en specifik enhet bör få ett meddelande på en hög nivå. Mallen anger exakt nyttolastformatet genom att referera till egenskaper som ingår i meddelandet som skickas av din appens serverdel. I vårt fall skickar vi ett språkvariant-oberoende meddelande som innehåller alla språk som stöds:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Vi kommer sedan att säkerställa att registrera enheter med en mall som refererar till egenskapen korrekt. Till exempel registrerar en Windows Store-app som du vill få ett enkelt popup-meddelande för följande mall med en motsvarande taggar:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Mallar är en mycket kraftfull funktion kan du läsa mer om i vår [mallar](notification-hubs-templates-cross-platform-push-messages.md) artikel. 

## <a name="the-app-user-interface"></a>Användargränssnittet för app
Vi kommer nu att ändra bryta nyheter appen som du skapade i avsnittet [använda Notification Hubs för att skicka de senaste nyheterna] att skicka lokaliserade senaste nytt med hjälp av mallar.

I Windows Store-appen:

Ändra din MainPage.xaml om du vill inkludera en combobox språk:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

## <a name="building-the-windows-store-client-app"></a>Skapa Windows Store-klientappen
1. Klassen meddelanden, lägga till en språkvariantparameter till din *StoreCategoriesAndSubscribe* och *SubscribeToCateories* metoder.
   
        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }
   
        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }
   
    Observera att i stället för att anropa den *RegisterNativeAsync* metoden som vi kallar *RegisterTemplateAsync*: vi registrerar ett visst meddelande format där mallen beror på språket. Vi kan också ange ett namn för mallen (”localizedWNSTemplateExample”), eftersom det kan vara bra att registrera mer än en mall (till exempel en för popup-meddelanden) och en för paneler och behöver ge dem för att kunna uppdatera eller ta bort dem.
   
    Observera att om en enhet registrerar flera mallar med samma tagg, ett inkommande meddelande mål som tagg leder till flera meddelanden levereras till enheten (en för varje mall). Detta är användbart när samma logiska meddelande måste resultera i flera visuella meddelanden, till exempel visar både en Aktivitetsikon och ett popup-meddelande i en Windows Store-programmet.
2. Lägg till följande metod för att hämta lagrade språk:
   
        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }
3. Uppdatera din knapp i din MainPage.xaml.cs på hanterare av hämtar det aktuella värdet för kombinationsrutan språk och att anropet till klassen meddelanden som visas:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;
   
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);
   
            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
4. Slutligen i filen App.xaml.cs, se till att uppdatera din `InitNotificationsAsync` metoden för att hämta språk och använda den när prenumerera:
   
        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

## <a name="send-localized-notifications-from-your-back-end"></a>Skicka lokaliserade meddelanden från serverdelen
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[använda Notification Hubs för att skicka de senaste nyheterna]: /notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
