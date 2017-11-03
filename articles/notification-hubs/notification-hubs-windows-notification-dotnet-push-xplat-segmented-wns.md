---
title: "Använd Azure Notification Hubs för att skicka de senaste nyheterna (Universal Windows Platform)"
description: "Använd Azure Notification Hubs med taggar i registreringen för att skicka de senaste nyheterna till en Uwp-app."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: d510e7e665adec9607aeee80802c466b363d5d5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Använda Notification Hubs för att skicka de senaste nyheterna
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du använder Azure Notification Hubs för att sända senaste nyheterna meddelanden till en Windows Store eller en app för Windows Phone 8.1 (utan Silverlight). Om du utvecklar för Windows Phone 8.1 Silverlight, referera till den [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) version. 

När du har slutfört den här processen, du kan registrera för de senaste nyhetskategorier som du är intresserad av och får du push-meddelanden för endast dessa kategorier. Det här scenariot är gemensamt för många appar (till exempel RSS-läsare eller appar för musik fläktar) där meddelanden skickas till grupper av användare som har deklarerats intresse för dem. 

Du kan aktivera broadcast scenarier genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg för alla enheter som har registrerats för taggen ta emot meddelandet. Eftersom taggar är bara strängar, behöver de inte ställas in i förväg. Mer information om taggar finns [Meddelandehubbar Routning och tagg uttryck](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Windows Store och Windows Phone-projektet version 8.1 och tidigare stöds inte i Visual Studio-2017. Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet). 

## <a name="prerequisites"></a>Krav
Det här avsnittet bygger på den app som du skapade i [Kom igång med Notification Hubs][get-started]. Innan du börjar den här självstudiekursen, måste du slutföra [Kom igång med Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Lägg till kategori markeringen i appen
Det första steget är att lägga till UI-element i din befintliga huvudsidan så att användarna kan välja kategorier för att registrera. De valda kategorierna som lagras på enheten. När appen startar, skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Öppna projektfilen MainPage.xaml och kopiera följande kod i den **rutnätet** element:
   
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

2. Högerklicka på den **delade** projektet, Lägg till en ny klass med namnet **meddelanden**, lägga till den **offentliga** modifierare till klassdefinitionen, och Lägg sedan till följande **med** instruktioner till den nya kodfilen:
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Kopiera följande kod till den nya **meddelanden** klass:
   
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
   
    Den här klassen använder lokal lagring för att lagra kategorier av nyheter som den här enheten måste ta emot. I stället för att anropa den *RegisterNativeAsync* metod, anropet *RegisterTemplateAsync* att registrera i kategorier med hjälp av en mall för registrering. 
   
    Eftersom du kanske vill registrera mer än en mall (t.ex, en för popup-meddelanden) och en för paneler, också ange ett mallnamn (till exempel ”simpleWNSTemplateExample”). Du namnger mallarna så att du kan uppdatera eller ta bort dem.
   
    >[!NOTE]
    >Om en enhet registrerar flera mallar med samma tagg, gör ett inkommande meddelande som riktar sig till taggen flera meddelanden som ska levereras till enheten (en för varje mall). Detta är användbart när samma logiska meddelande måste resultera i flera visuella meddelanden (till exempel visar både en Aktivitetsikon och ett popup-meddelande i en Windows Store-programmet).
   
    Mer information finns i [mallar](notification-hubs-templates-cross-platform-push-messages.md).

4. Lägg till följande egenskapen i projektfilen App.xaml.cs den **App** klass:
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
    Den här egenskapen används för att skapa och komma åt en **meddelanden** instans.
   
    I koden, ersätter den `<hub name>` och `<connection string with listen access>` platshållarna med namnet på din meddelandehubb och anslutningssträngen för *DefaultListenSharedAccessSignature*, som du fick tidigare.
   
   > [!NOTE]
   > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram inte är vanligtvis säkra distribuera nyckeln för *lyssna* åtkomst med klientappen. Med lyssna-åtkomst kan du registrera din app för meddelanden, men går inte att ändra befintliga registreringar och går inte att skicka meddelanden. Fullständig åtkomst-nyckeln används i en skyddad backend-tjänst för att skicka meddelanden och ändra befintliga registreringar.
   > 
   > 
5. Lägg till följande rad i filen MainPage.xaml.cs projektet:
   
        using Windows.UI.Popups;

6. Lägg till följande metod i projektfilen MainPage.xaml.cs:
   
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
   
    Den här metoden skapar en lista över kategorier och använder den **meddelanden** klass som lagrar listan i den lokala lagringen. Det garanterar även motsvarande taggar med meddelandehubben. När kategorierna ändras, skapas registreringen på nytt med nya kategorier.

Din app kan nu användas för att lagra en uppsättning kategorier i lokal lagring på enheten. Appen registreras i meddelandehubben när användare ändra valet av kategori.

## <a name="register-for-notifications"></a>Registrera dig för meddelanden
I det här avsnittet kan registrera du med notification hub vid start med hjälp av de kategorier som du har lagrat i lokal lagring.

> [!NOTE]
> Eftersom URI som är tilldelad av Windows Notification Service (WNS)-kanalen kan ändras när som helst, bör du registrera dig för meddelanden ofta att undvika fel i meddelande. Det här exemplet registrerar för meddelande varje gång appen startar. Du kan förmodligen hoppa över registrering för att bevara bandbredd om mindre än en dag har gått sedan den tidigare registreringen för appar som du kör ofta (mer än en gång om dagen).
> 
> 

1. Att använda den `notifications` klassen prenumerera utifrån kategorier, öppna filen App.xaml.cs och sedan uppdatera den **InitNotificationsAsync** metod.
   
        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
   
        var result = await notifications.SubscribeToCategories();
   
    Den här processen säkerställer att när appen startar hämtas kategorier från lokal lagring och begär registrering av dessa kategorier. Du har skapat den **InitNotificationsAsync** metod som en del av den [Kom igång med Notification Hubs] [ get-started] kursen.

2. Lägg till följande kod i filen MainPage.xaml.cs projekt i *OnNavigatedTo* metod:
   
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
   
    Den här koden uppdaterar huvudsidan, baserat på status för tidigare sparad kategorier.

Appen är nu klar. Det kan lagra en uppsättning kategorier i enhetens lokala lagring som används för att registrera med notification hub när användaren ändrar urvalet kategori. I nästa avsnitt definierar du en serverdel som kategori meddelanden kan skickas till den här appen.

## <a name="send-tagged-notifications"></a>Skicka taggade meddelanden
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Kör appen och generera meddelanden
1. I Visual Studio väljer **F5** att kompilera och starta appen.  
    Appen Användargränssnittet innehåller en uppsättning växlar där du kan välja kategorier för att prenumerera på. 
   
    ![Bryta nyheter app][1]

2. Aktivera en eller flera växlar för kategori och klicka sedan på **prenumerera**.
   
    Appen konverterar valda kategorier till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben. Registrerade kategorier returneras och visas i en dialogruta.
   
    ![Knapparna för kategori och prenumerera knapp][19]

3. Skicka ett nytt meddelande från serverdelen på något av följande sätt:

   * **Konsolapp**: starta konsolen appen.
   * **Java/PHP**: kör din app eller skript.
     
     Meddelanden i valda kategorier visas som popup-meddelanden.
     
     ![Popup-meddelanden][14]

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du broadcast senaste nyheterna efter kategori. Tänk igenom följande kursen, vilket tyder på ett annat avancerade Meddelandehubbar scenario:

* [Använda Notification Hubs för att sända lokaliserade senaste nyheterna] den här självstudiekursen beskrivs hur du expandera senaste nyheterna appen om du vill aktivera lokaliserade meddelanden.

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
[Använda Notification Hubs för att sända lokaliserade senaste nyheterna]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
