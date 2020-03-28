---
title: Skicka meddelanden till specifika enheter (Universal Windows Platform) | Microsoft Docs
description: Använd Azure Notification Hubs med taggar i registreringen om du vill skicka de senaste nyheterna till en Universal Windows Platform-app.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 9151870836b1a616a79e54275ed185a425c11f0c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72385607"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Självstudiekurs: Skicka meddelanden till specifika enheter som kör Universella Windows-plattformsprogram

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt

Den här självstudien visar hur du använder Azure Notification Hubs för att sända meddelanden om nyheter. Den här självstudien omfattar Windows Store- eller Windows Phone 8.1-program (som inte är Silverlight). Om du riktar in dig på Windows Phone 8.1 Silverlight läser du [Push-meddelanden till specifika Windows Phone-enheter med hjälp av Azure Notification Hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

I den här självstudien får du lära dig hur du använder Azure Notification Hubs för att skicka meddelanden till specifika Windows-enheter som kör ett UWP-program (Universal Windows Platform). När du har slutfört självstudien kan du registrera dig för de senaste nyhetskategorierna som du är intresserad av. Du får endast push-meddelanden för dessa kategorier.

Om du vill aktivera broadcast-scenarier inkluderar du en eller *flera taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg får alla enheter som är registrerade för taggen meddelandet. Mer information om taggar finns i [Routning och tagguttryck](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Windows Store- och Windows Phone-projektversioner 8.1 och tidigare stöds inte i Visual Studio 2019. Mer information finns i [Visual Studio 2019 Plattformsinriktning och kompatibilitet](/visualstudio/releases/2019/compatibility).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Lägga till kategorival i mobilappen
> * Registrera sig för meddelanden
> * Skicka taggade meddelanden
> * Kör appen och generera meddelanden

## <a name="prerequisites"></a>Krav

Slutför [Självstudier: Skicka meddelanden till Universal Windows Platform-appar med hjälp av Azure Notification Hubs][get-started] innan du startar den här självstudiekursen.  

## <a name="add-category-selection-to-the-app"></a>Lägga till kategorival till appen

Det första steget är att lägga till UI-element till din befintliga huvudsida så att användarna kan välja kategorier att registrera. De valda kategorierna lagras på enheten. När appen startar skapas en enhetsregistrering i meddelandehubben, med de valda kategorierna som taggar.

1. Öppna projectfilen *MainPage.xaml* och kopiera sedan följande `Grid` kod i elementet:

    ```xml
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
    ```

1. Högerklicka på projektet i **Solution Explorer**och välj **Lägg till** > **klass**. I **Lägg till nytt objekt**namnger du klassen *Meddelanden*och väljer Lägg **till**. Om det behövs `public` lägger du till modifieraren i klassdefinitionen.

1. Lägg till `using` följande satser i den nya filen:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Kopiera följande kod till den nya `Notifications`-klassen:

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

    Den här klassen använder lokal lagring för att lagra de nyhetskategorier som den här enheten ska ta emot. I stället för att anropa metoden `RegisterNativeAsync` anropar du `RegisterTemplateAsync` om du vill registrera för kategorierna med hjälp av en mallregistrering.

    Om du vill registrera mer än en mall anger du ett mallnamn, till exempel *simpleWNSTemplateExample*. Du namnger mallarna så att du kan uppdatera eller ta bort dem. Du kan registrera mer än en mall för att ha en för popup-meddelanden och en för paneler.

    >[!NOTE]
    > Med Notification Hubs kan en enhet registrera flera mallar med samma tagg. I det här fallet resulterar ett inkommande meddelande som riktar sig till taggen i att flera meddelanden levereras till enheten, ett för varje mall. Med den här processen kan du visa samma meddelande i flera visuella meddelanden, till exempel både ett märke och som ett popup-meddelande i en Windows Store-app.

    Mer information finns i [Mallar](notification-hubs-templates-cross-platform-push-messages.md).

1. Lägg till följande egenskap i klassen `App` i App.xaml.cs-projektfilen: *App.xaml.cs*

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Den här egenskapen används för att skapa och få åtkomst till en `Notifications`-instans.

    Ersätt platshållarna `<hub name>` och `<connection string with listen access>` i koden med namnet på din meddelandehubb och anslutningssträngen för **DefaultListenSharedAccessSignature** som du fick tidigare.

   > [!NOTE]
   > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram vanligtvis inte är säkra så distribuera bara nyckeln för *lyssnings*åtkomst med din klientapp. Med lyssna-åtkomst kan du registrera din app för meddelanden, men befintliga registreringar kan inte ändras och meddelanden kan inte skickas. Nyckelln för fullständig åtkomst används i en skyddad serverdelstjänst för att skicka meddelanden och ändra befintliga registreringar.

1. Lägg till följande rad i *filen MainPage.xaml.cs:*

    ```csharp
    using Windows.UI.Popups;
    ```

1. Lägg *MainPage.xaml.cs* till följande metod i MainPage.xaml.cs-filen:

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

    Den här metoden skapar en lista över kategorier och använder klassen `Notifications` för att lagra listan lokalt. Det garanterar även motsvarande taggar med meddelandehubben. När kategorierna ändras återskapas registreringen med de nya kategorierna.

Din app kan nu användas för att lagra en uppsättning kategorier i lokal lagring på enheten. Appen registreras i meddelandehubben närhelst användare ändrar kategoriurvalet.

## <a name="register-for-notifications"></a>Registrera sig för meddelanden

I det här avsnittet registrerar du med meddelandehubben vid start med hjälp av de kategorier som du har lagrat lokalt.

> [!NOTE]
> Eftersom den kanal-URI som tilldelats av Windows Notification Service (WNS) kan ändras när som helst bör du ofta registrera dig för meddelanden för att undvika meddelandefel. Det här exemplet registrerar för meddelande varje gång som appen startas. För appar som du kör ofta, till exempel, mer än en gång om dagen, kan du förmodligen hoppa över registreringen för att bevara bandbredden om mindre än en dag har gått sedan den tidigare registreringen.

1. Om du `notifications` vill använda klassen för att prenumerera baserat på kategorier `InitNotificationsAsync` öppnar du *App.xaml.cs* filen och uppdaterar sedan metoden.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Den här processen säkerställer att när appen startar hämtar den kategorierna från lokal lagring. Den begär sedan registrering av dessa kategorier. Du har `InitNotificationsAsync` skapat metoden som en del av [send-meddelanden till universella Windows-plattformsappar med hjälp av självstudiekursen Azure Notification Hubs.][get-started]
2. Lägg *MainPage.xaml.cs* till följande kod i `OnNavigatedTo` metoden i MainPage.xaml.cs projektfilen:

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

Appen är nu klar. Den kan lagra en uppsättning kategorier i enhetens lokala lagring. När användare ändrar kategorivalet används de sparade kategorierna för att registrera sig med meddelandehubben. I nästa avsnitt definierar du en serverdel som kan skicka kategorimeddelanden till den här appen.

## <a name="run-the-uwp-app"></a>Kör UWP-appen

1. Kompilera och starta appen genom att trycka på F5 i Visual Studio. Appens användargränssnitt innehåller en uppsättning växlar med vilka du kan välja vilka kategorier du vill prenumerera på.

   ![Senaste nytt-app](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Aktivera en eller flera kategoriväxlingsknappar och välj sedan **Prenumerera**.

   Appen konverterar de valda kategorierna till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben. Appen visar de registrerade kategorierna i en dialogruta.

    ![Kategoriväxlingsknapparna och prenumerationsknappen](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Skapa en konsolapp för att skicka taggade meddelanden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Kör konsolappen för att skicka taggade meddelanden

Kör appen som skapats i föregående avsnitt. Meddelanden för de valda kategorierna visas som popup-meddelanden.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du skickar senaste nytt efter kategori. Backend-programmet skickar taggade meddelanden till enheter som har registrerats för att ta emot meddelanden för taggen. Om du vill veta hur du skickar meddelanden till specifika användare oberoende av vilken enhet de använder går du vidare till följande självstudiekurs:

> [!div class="nextstepaction"]
> [Skicka lokaliserade meddelanden till Windows-appar med hjälp av Azure Notification Hubs](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
