---
title: Skicka push-meddelanden till specifika iOS-enheter med Azure Notification Hubs | Microsoft-dokument
description: I den här självstudien får du lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till specifika iOS-enheter.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: a775963f1b0fa19cd687c839f527f4a078c76864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127001"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Självstudiekurs: Skicka push-meddelanden till specifika iOS-enheter med Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt

Den här självstudien visar hur du använder Azure Notification Hubs för att sända senaste nyhetsaviseringar till en iOS-app. När du är klar kan du registrera dig för senaste nyhetskategorier som du är intresserad av och får endast push-meddelanden för dessa kategorier. Det här scenariot är ett vanligt mönster för många appar där meddelanden måste skickas till olika grupper av användare som tidigare har deklarerat intresse för dem, t.ex. RSS-läsare, appar för musiklyssnare osv.

Du aktiverar sändningsscenarier genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg får enheter som har registrerats för taggen meddelandet. Eftersom taggar bara är strängar behöver de inte etableras i förväg. Mer information om taggar finns i [Notification Hubs-routning och tagguttryck](notification-hubs-tags-segment-push-message.md).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Lägga till ett kategorival i appen
> * Skicka taggade meddelanden
> * Skicka meddelanden från enheten
> * Kör appen och generera meddelanden

## <a name="prerequisites"></a>Krav

Det här avsnittet bygger på appen som du skapade i [Självstudiekurs: Push-meddelanden till iOS-appar med Azure Notification Hubs][get-started]. Innan du startar den här självstudien måste du redan ha slutfört [självstudiekursen: Push-meddelanden till iOS-appar med Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Lägga till kategorival till appen

Det första steget är att lägga till gränssnittselementen i din befintliga storyboard som gör det möjligt för användaren att välja kategorier att registrera. De kategorier som valts av en användare lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Lägg till följande komponenter från objektbiblioteket **i MainStoryboard_iPhone.storyboard:**

   * En etikett med "Breaking News"-text,
   * Etiketter med kategoritexterna "World", "Politics", "Business", "Technology", "Science", "Sports",
   * Sex växlar, en per kategori, ställer in varje **switch-tillstånd** som **av** som standard.
   * En knapp med etiketten "Prenumerera"

     Din storyboard ska se ut så här:

     ![Xcode-gränssnittsbyggare][3]

2. I biträdande redaktör, skapa försäljningsställen för alla växlar och kalla dem "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Skapa en åtgärd för `subscribe`din knapp som heter ; du `ViewController.h` ska innehålla följande kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Skapa en ny **Cocoa Touch-klass** som heter `Notifications`. Kopiera följande kod i gränssnittsavsnittet i filen Notifications.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Lägg till följande importdirektiv i Notifications.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Kopiera följande kod i implementeringsavsnittet i filen Notifications.m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Den här klassen använder lokal lagring för att lagra och hämta de kategorier av nyheter som den här enheten tar emot. Den innehåller också en metod för att registrera sig för dessa kategorier med hjälp av en [mallregistrering.](notification-hubs-templates-cross-platform-push-messages.md)

7. Lägg `AppDelegate.h` till en importsats `Notifications.h` för och lägger till `Notifications` en egenskap för en förekomst av klassen i filen:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. I `didFinishLaunchingWithOptions` metoden `AppDelegate.m`i lägger du till koden för att initiera meddelandeinstansen i början av metoden.  
    `HUBNAME`och `HUBLISTENACCESS` `hubinfo.h`(definierad i) `<hub name>` bör `<connection string with listen access>` redan ha och platshållarna ersatta med ditt meddelandehubbnamn och anslutningssträngen för *DefaultListenSharedAccessSignature* som du fick tidigare

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram vanligtvis inte är säkra bör du bara distribuera nyckeln för lyssningsåtkomst med din klientapp. Lyssningsåtkomst gör det möjligt för din app att registrera sig för meddelanden, men befintliga registreringar kan inte ändras och meddelanden kan inte skickas. Nyckeln för fullständig åtkomst används i en skyddad serverdelstjänst för att skicka meddelanden och ändra befintliga registreringar.

9. I `didRegisterForRemoteNotificationsWithDeviceToken` metoden `AppDelegate.m`i ersätter du koden i metoden med följande kod `notifications` för att skicka enhetstoken till klassen. Klassen `notifications` utför registreringen för meddelanden med kategorierna. Om användaren ändrar kategorival `subscribeWithCategories` anropar du metoden som svar på **knappen Prenumerera för** att uppdatera dem.

    > [!NOTE]
    > Eftersom enhetstoken som tilldelats av Apple Push Notification Service (APNS) kan ändras när som helst, bör du registrera dig för meddelanden ofta för att undvika meddelandefel. Det här exemplet registrerar för meddelande varje gång som appen startas. För appar som körs ofta, mer än en gång om dagen, kan du förmodligen hoppa över registreringen och spara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Vid denna punkt bör det inte `didRegisterForRemoteNotificationsWithDeviceToken` finnas någon annan kod i metoden.

10. Följande metoder bör redan `AppDelegate.m` finnas i från att slutföra guiden [Kom igång med Notification Hubs.][get-started] Om inte, lägg till dem.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Den här metoden hanterar meddelanden som tas emot när appen körs genom att visa ett enkelt **UIAlert**.

11. I `ViewController.m`lägger `import` du `AppDelegate.h` till en sats för och `subscribe` kopierar följande kod till den XCode-genererade metoden. Den här koden uppdaterar meddelanderegistreringen så att den nya kategoritaggarna som användaren har valt i användargränssnittet används.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Den här metoden `NSMutableArray` skapar en `Notifications` av kategorierna och använder klassen för att lagra listan i det lokala lagringsutrymmet och registrerar motsvarande taggar med meddelandehubben. När kategorier ändras återskapas registreringen med de nya kategorierna.

12. I `ViewController.m`lägger du till `viewDidLoad` följande kod i metoden för att ange användargränssnittet baserat på de tidigare sparade kategorierna.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Appen kan nu lagra en uppsättning kategorier i enhetens lokala lagring som används för att registrera sig med meddelandehubben när appen startar. Användaren kan ändra valet av kategorier vid `subscribe` körning och klicka på metoden för att uppdatera registreringen för enheten. Därefter uppdaterar du appen för att skicka de senaste nyheterna direkt i själva appen.

## <a name="optional-send-tagged-notifications"></a>(valfritt) Skicka taggade meddelanden

Om du inte har tillgång till Visual Studio kan du gå vidare till nästa avsnitt och skicka meddelanden från själva appen. Du kan också skicka rätt mallmeddelande från [Azure-portalen] med hjälp av felsökningsfliken för meddelandehubben.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(valfritt) Skicka meddelanden från enheten

Normalt skickas meddelanden av en servergrupp, men du kan skicka meddelanden om nyheter direkt från appen. För att göra det `SendNotificationRESTAPI` uppdaterar du den metod som du definierade i självstudien [Kom igång med meddelandehubbar.][get-started]

1. I `ViewController.m`uppdaterar `SendNotificationRESTAPI` du metoden enligt följande så att den accepterar en parameter för kategoritaggen och skickar rätt [mallmeddelande.](notification-hubs-templates-cross-platform-push-messages.md)

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. I `ViewController.m`uppdaterar `Send Notification` du åtgärden enligt koden som följer. Så att den skickar meddelanden med varje tagg individuellt och skickar till flera plattformar.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Återskapa projektet och se till att du inte har några byggfel.

## <a name="run-the-app-and-generate-notifications"></a>Kör appen och generera meddelanden

1. Tryck på Run (Kör) för att skapa projektet och starta appen. Välj några senaste nyhetsalternativ att prenumerera på och tryck sedan på **knappen Prenumerera.** Du bör se en dialogruta som anger att meddelandena har prenumererats på.

    ![Exempel på meddelande på iOS][1]

    När du väljer **Prenumerera**konverterar appen de valda kategorierna till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben.

2. Ange ett meddelande som ska skickas som senaste nytt och tryck sedan på knappen **Skicka meddelande.** Du kan också köra .NET-konsolappen för att generera meddelanden.

    ![Ändra meddelandeinställningar i iOS][2]

3. Varje enhet som prenumererar på nyheter får de senaste nyheterna meddelanden du just skickat.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skickade du broadcast-meddelanden till specifika iOS-enheter som har registrerats för kategorierna. Om du vill veta hur du skickar lokaliserade meddelanden går du vidare till följande självstudiekurs:

> [!div class="nextstepaction"]
>[Skicka lokaliserade meddelanden](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure-portal]: https://portal.azure.com
