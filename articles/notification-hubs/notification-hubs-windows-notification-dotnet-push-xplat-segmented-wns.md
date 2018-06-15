---
title: Skicka meddelanden till specifika enheter (Universal Windows Platform) | Microsoft Docs
description: Använd Azure Notification Hubs med taggar i registreringen om du vill skicka de senaste nyheterna till en Universal Windows Platform-app.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 9b9e3b910162653c14c398e2c3392709abcd5fd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776693"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Självstudier: Skicka meddelanden till specifika Windows-enheter som kör Universal Windows Platform-program
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt
I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka senaste nytt-meddelanden till en Windows Store- eller Windows Phone 8.1-app (utan Silverlight). Om du vill skicka meddelanden till Windows Phone 8.1 Silverlight, så använd [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)-versionen. 

I de här självstudierna kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till specifika Windows-enheter som kör Universal Windows Platform-program (UWP). När du har slutfört den här självstudiekursen kan du registrera dig för olika nyhetskategorier som du är intresserad av och därmed endast få push-meddelanden för dessa kategorier. 

Sändningsscenarier aktiveras genom att du inkluderar en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg tar alla enheter som har registrerats för taggen emot meddelandet. Mer information om taggar finns i [Taggar i registreringar](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Windows Store och Windows Phone-projektversion 8.1 och tidigare stöds inte i Visual Studio 2017. Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet). 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Lägga till kategorival i mobilappen
> * Registrera sig för meddelanden
> * Skicka taggade meddelanden
> * Kör appen och generera meddelanden

## <a name="prerequisites"></a>Nödvändiga komponenter
Slutför [Självstudier: Skicka meddelanden till Universal Windows Platform-appar med hjälp av Azure Notification Hubs][get-started] innan du startar den här självstudiekursen.  

## <a name="add-category-selection-to-the-app"></a>Lägga till kategorival till appen
Det första steget är att lägga till UI-element till din befintliga huvudsida så att användarna kan välja kategorier att registrera. De valda kategorierna lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Öppna projektfilen MainPage.xaml och kopiera följande kod i **Rutnät**-elementet:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>

2. Högerklicka på projektet i **Solution Explorer** och lägg till en ny klass: **Meddelanden**. Lägg till den **offentliga** modifieraren till klassdefinitionen och lägg sedan till följande **using** instruktioner till den nya kodfilen:

    ```csharp   
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. Kopiera följande kod till den nya klassen **Meddelanden**:
   
    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```
   
    Den här klassen använder lokal lagring för att lagra de nyhetskategorier som den här enheten ska ta emot. I stället för att anropa metoden *RegisterNativeAsync* så anropa *RegisterTemplateAsync* om du vill registrera för kategorierna med hjälp av en mallregistrering. 
   
    Om du vill registrera mer än en mall (t.ex. en för popup-meddelanden och en för paneler), så ange ett mallnamn (t.ex. "simpleWNSTemplateExample"). Du namnger mallarna så att du kan uppdatera eller ta bort dem.
   
    >[!NOTE]
    >Om en enhet registrerar flera mallar med samma tagg, så gör ett inkommande meddelande som är inriktat på taggen att flera meddelanden levereras till enheten (ett för varje mall). Detta är användbart när samma logiska meddelande måste resultera i flera visuella meddelanden (t.ex. att visa både en aktivitetsikon och ett popup-meddelande i ett Windows Store-program).
   
    Mer information finns i [Mallar](notification-hubs-templates-cross-platform-push-messages.md).

4. Lägg till följande egenskap till **App**-klassen i projektfilen App.xaml.cs:

    ```csharp   
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```
   
    Den här egenskapen används för att skapa och få åtkomst till en **Meddelanden**-instans.
   
    Ersätt platshållarna `<hub name>` och `<connection string with listen access>` i koden med namnet på din meddelandehubb och anslutningssträngen för *DefaultListenSharedAccessSignature* som du fick tidigare.
   
   > [!NOTE]
   > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram vanligtvis inte är säkra så distribuera bara nyckeln för *lyssnings*åtkomst med din klientapp. Med lyssna-åtkomst kan du registrera din app för meddelanden, men befintliga registreringar kan inte ändras och meddelanden kan inte skickas. Nyckelln för fullständig åtkomst används i en skyddad serverdelstjänst för att skicka meddelanden och ändra befintliga registreringar.
   > 
   > 
5. Lägg till följande rad i MainPage.xaml.cs:
   
    ```csharp
    using Windows.UI.Popups;
    ```

6. Lägg till följande metod i MainPage.xaml.cs:
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Den här metoden skapar en lista över kategorier och använder klassen **Meddelanden** för att lagra listan lokalt. Det garanterar även motsvarande taggar med meddelandehubben. När kategorierna ändras skapas registreringen på nytt med nya kategorier.

Din app kan nu användas för att lagra en uppsättning kategorier i lokal lagring på enheten. Appen registreras i meddelandehubben närhelst användare ändrar kategoriurvalet.

## <a name="register-for-notifications"></a>Registrera sig för meddelanden
I det här avsnittet registrerar du med meddelandehubben vid start med hjälp av de kategorier som du har lagrat lokalt.

> [!NOTE]
> Eftersom den kanal-URI som tilldelats av Windows Notification Service (WNS) kan ändras när som helst bör du ofta registrera dig för meddelanden för att undvika meddelandefel. Det här exemplet registrerar för meddelande varje gång som appen startas. För appar som du kör ofta, mer än en gång om dagen, kan du förmodligen hoppa över registreringen och spara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.
> 
> 

1. Att använda klassen `notifications` för att prenumerera utifrån kategorier. Öppna filen App.xaml.cs och uppdatera sedan metoden **InitNotificationsAsync**.
   
    ```csharp
    // *** Remove or comment out these lines *** 
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
   ```
    Den här processen säkerställer att när appen startas så hämtas kategorier från lokal lagring och begär registrering av dessa kategorier. Du har skapat metoden **InitNotificationsAsync** som en del av självstudiekursen [Kom igång med Notification Hubs][get-started].
2. Lägg till följande kod till metoden *OnNavigatedTo* i MainPage.xaml.cs:
   
    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Den här koden uppdaterar huvudsidan baserat på tidigare sparade kategoriers status.

Appen är nu klar. Den kan lagra en uppsättning kategorier i enhetens lokala lagring som används för att registrera med meddelandehubben när användaren ändrar kategoriurvalet. I nästa avsnitt definierar du en serverdel som kan skicka kategorimeddelanden till den här appen.

## <a name="send-tagged-notifications"></a>Skicka taggade meddelanden
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Kör appen och generera meddelanden
1. Kompilera och starta appen genom att trycka på **F5** i Visual Studio. Appens användargränssnitt innehåller en uppsättning växlar med vilka du kan välja vilka kategorier du vill prenumerera på. 
   
    ![Senaste nytt-app][1]

2. Aktivera en eller flera kategoriväxlar och klicka sedan på **Prenumerera**.
   
    Appen konverterar de valda kategorierna till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben. De registrerade kategorierna returneras och visas i en dialogruta.
   
    ![Kategoriväxlingsknapparna och prenumerationsknappen][19]

3. Skicka ett nytt meddelande från serverdelen på något av följande sätt:

   * **Konsolapp**: starta konsolappen.
   * **Java/PHP**: kör din app eller ditt skript.
     
     Meddelanden för de valda kategorierna visas som popup-meddelanden.
     
     ![Popup-meddelanden][14]

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du skickar senaste nytt efter kategori. Serverdelsprogrammet skickar taggade meddelanden till enheter som har registrerats för att ta emot meddelanden för den taggen. Information om hur du skickar meddelanden till specifika användare, oavsett vilken enhet de använder, finns i följande självstudie:

> [!div class="nextstepaction"]
> [Skicka lokaliserade meddelanden](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Use Notification Hubs to broadcast localized breaking news]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
