---
title: Notification Hubs senaste nyheterna kursen - iOS
description: "Lär dig hur du använder Azure Service Bus Notification Hubs för att skicka senaste nyheterna meddelanden till iOS-enheter."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 8aec171b46df3e0e7f2a2d3cc9d44084d064e6fd
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Använda Notification Hubs för att skicka de senaste nyheterna
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du använder Azure Notification Hubs för att sända senaste nyheterna meddelanden till en iOS-app. När du är klar kommer du att kunna registrera för att analysera nyhetskategorier som du är intresserad av och får endast push-meddelanden för dessa kategorier. Det här scenariot är ett vanligt mönster för många appar där meddelanden har skickas till grupper av användare som har tidigare ha deklarerats intresse för dem, t.ex. RSS-läsare, appar för musik fläktar, osv.

Broadcast scenarier aktiveras genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg för tar alla enheter som har registrerats för taggen emot meddelandet. Eftersom taggar är bara strängar, behöver de inte etableras i förväg. Mer information om taggar finns i [Notification Hubs Routning och Tagguttryck](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Krav
Det här avsnittet bygger på appen som du skapade i [Kom igång med Notification Hubs][get-started]. Innan du börjar den här kursen ska du måste redan har slutfört [Kom igång med Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Lägg till kategori markeringen i appen
Det första steget är att lägga till de UI-element i din befintliga storyboard som gör att användaren kan välja kategorier för att registrera. Vilka kategorier av en användare som lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Lägg till följande komponenter från objektbiblioteket i din MainStoryboard_iPhone.storyboard:
   
   * En etikett med ”bryter nyheter” text
   * Etiketter med kategori text ”World”, ”politik”, ”företag”, ”teknik”, ”vetenskap”, ”sport”
   * Sex växlar en per kategori och ställa in varje switch **tillstånd** ska **av** som standard.
   * En knapp med etiketten ”prenumerera”
     
     Storyboard bör se ut så här:
     
     ![][3]
2. I Redigeraren för assistenten skapa uttag för alla växlar och anropa dem ”WorldSwitch”, ”PoliticsSwitch”, ”BusinessSwitch”, ”TechnologySwitch”, ”ScienceSwitch”, ”SportsSwitch”
3. Skapa en åtgärd för en knapp som kallas ”prenumerera”. Din ViewController.h bör innehålla följande:
   
        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
   
        - (IBAction)subscribe:(id)sender;
4. Skapa en ny **Cocoa Touch klassen** kallas `Notifications`. Kopiera följande kod i avsnittet gränssnitt i filen Notifications.h:
   
        @property NSData* deviceToken;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;
   
        - (NSSet*)retrieveCategories;
   
        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
5. Lägg till följande importdirektiv Notifications.m:
   
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
6. Kopiera följande kod i implementeringsavsnittet av filen Notifications.m.
   
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



    Den här klassen använder lokal lagring för att lagra och hämta kategorier av nyheter som den här enheten ska ta emot. Den innehåller också en metod för att registrera dig för dessa kategorier med hjälp av en [mallen](notification-hubs-templates-cross-platform-push-messages.md) registrering.

1. Lägg till en import-sats för Notifications.h i filen AppDelegate.h och lägga till en egenskap för en instans av klassen meddelanden:
   
        #import "Notifications.h"
   
        @property (nonatomic) Notifications* notifications;
2. I den **didFinishLaunchingWithOptions** metod i AppDelegate.m, Lägg till koden för att initiera meddelanden instans i början av metoden.  
   
    `HUBNAME`och `HUBLISTENACCESS` (definieras i hubinfo.h) har redan den `<hub name>` och `<connection string with listen access>` platshållare ersättas med namnet på din meddelandehubb och anslutningssträngen för *DefaultListenSharedAccessSignature* som du tidigare hämtade
   
        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
   
   > [!NOTE]
   > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram inte är vanligtvis säker kan distribuera du endast nyckel för lyssna åtkomst med din klientapp. Lyssna åtkomst aktiverar inte går att ändra din app att registrera för meddelanden, men befintliga registreringar och går inte att skicka meddelanden. Fullständig åtkomst-nyckeln används i en skyddad backend-tjänst för att skicka meddelanden och ändra befintliga registreringar.
   > 
   > 
3. I den **didRegisterForRemoteNotificationsWithDeviceToken** metod i AppDelegate.m, Ersätt Koden i metoden med följande kod för att skicka enhetstoken till klassen meddelanden. Klassen meddelanden utför registrering för meddelanden med kategorier. Om användaren ändrar kategori, som vi kallar det `subscribeWithCategories` metod som svar på de **prenumerera** om du vill uppdatera dem.
   
   > [!NOTE]
   > Eftersom enhetstoken som tilldelats av Apple Push Notification Service (APNS) kan risken när som helst, bör du registrera dig för meddelanden ofta att undvika fel i meddelande. Det här exemplet registrerar för meddelande varje gång appen startar. För appar som körs ofta mer än en gång om dagen, du kan förmodligen hoppa över registrering för att bevara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.
   > 
   > 
   
        self.notifications.deviceToken = deviceToken;
   
        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.
   
        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    Observera att då det bör finnas någon kod i den **didRegisterForRemoteNotificationsWithDeviceToken** metod.

1. Följande metoder ska redan finnas i AppDelegate.m från att utföra den [Kom igång med Notification Hubs] [ get-started] kursen.  Om du inte lägga till dem.
   
    -(void) MessageBox:(NSString *) rubrik meddelandet:(NSString *) messageText {
   
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }
   
   * (void) programmet:(UIApplication *) program didReceiveRemoteNotification: (NSDictionary *) användarinformationen {NSLog (@ ”% @”, användarinformationen);   [self MessageBox:@"Notification" meddelande: [[användarinformationen objectForKey:@"aps"] valueForKey:@"alert"]];}
   
   Den här metoden hanterar meddelanden när appen körs genom att visa en enkel **UIAlert**.
2. Lägg till en import-sats för AppDelegate.h i ViewController.m, och kopiera följande kod i XCode-genererade **prenumerera** metod. Den här koden kommer att uppdatera registreringen för meddelanden om du vill använda de nya kategori etiketter som användaren har valt i användargränssnittet.
   
       ```
       #import "Notifications.h"
       ```
   
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
               [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
           } else {
               NSLog(@"Error subscribing: %@", error);
           }
       }];
   
   Den här metoden skapar en **NSMutableArray** kategorier och använder den **meddelanden** klass som lagrar listan i lokal lagring och registrerar motsvarande taggar med meddelandehubben. När kategorier ändras, återskapas registreringen med de nya kategorierna.
3. I ViewController.m, lägger du till följande kod i den **viewDidLoad** metod för att ange användargränssnittet baserat på kategorierna som sparats tidigare.

        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Appen kan nu användas för att lagra en uppsättning kategorier i enhetens lokala lagring som används för att registrera med notification hub när appen startar.  Användaren kan ändra valet av kategorier vid körning och klicka på den **prenumerera** metod för att uppdatera registreringen för enheten. Därefter uppdaterar du appen för att skicka meddelanden för senaste nyheterna direkt i själva appen.

## <a name="optional-sending-tagged-notifications"></a>(valfritt) Skicka taggade meddelanden
Om du inte har åtkomst till Visual Studio, kan du gå vidare till nästa avsnitt och skicka meddelanden från själva appen. Du kan också skicka rätt mall-meddelande från den [Azure-portalen] med felsökningsfliken för meddelandehubben. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(valfritt) Skicka meddelanden från enheten
Meddelanden skickas normalt med en serverdelstjänst, men du kan skicka senaste nyheterna meddelanden direkt från appen. Om du vill vi kommer att uppdatera den `SendNotificationRESTAPI` metod som vi har definierat i den [Kom igång med Notification Hubs] [ get-started] kursen.

1. I ViewController.m uppdatering av `SendNotificationRESTAPI` metod som följer så att den accepterar en parameter för taggen kategori och skickar rätt [mallen](notification-hubs-templates-cross-platform-push-messages.md) meddelande.
   
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
2. I ViewController.m uppdatering av **skicka meddelande** åtgärd som visas i den kod som följer. Så att den ska skicka meddelanden med hjälp av varje tagg individuellt och skicka till flera plattformar.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



1. Återskapa projektet och kontrollera att du har inga build-fel.

## <a name="run-the-app-and-generate-notifications"></a>Kör appen och generera meddelanden
1. Klicka på Kör för att bygga projektet och starta appen. Välj senaste nyheterna alternativ prenumererar på och trycker sedan på den **prenumerera** knappen. Du bör se en dialogruta som visar meddelanden prenumererar på.
   
    ![][1]
   
    När du väljer **prenumerera**, appen konverterar valda kategorier till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben.
2. Ange ett meddelande som ska skickas som senaste nyheterna tryck sedan på den **skicka meddelande** knappen. Du kan också köra .NET-konsolapp för att generera meddelanden.
   
    ![][2]
3. Varje enhet som prenumererar på senaste nytt får senaste nyheterna meddelanden som du har skickat.

## <a name="next-steps"></a>Nästa steg
Vi lärt dig hur du broadcast senaste nyheterna efter kategori i den här självstudiekursen. Överväg att fylla i en av följande kurser som markerar andra avancerade Meddelandehubbar scenarier:

* **[Använda Notification Hubs för att sända lokaliserade senaste nyheterna]**
  
    Lär dig mer om att utöka senaste nyheterna appen om du vill aktivera skicka lokaliserade meddelanden.

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Använda Notification Hubs för att sända lokaliserade senaste nyheterna]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure-portalen]: https://portal.azure.com
