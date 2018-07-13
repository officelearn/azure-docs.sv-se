---
title: Push-meddelanden till specifika iOS-enheter med Azure Notification Hubs | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till specifika iOS-enheter.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f6096238deb2186edfac2eb9d1c9a9e76db07553
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308527"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Självstudier: Push-meddelanden till specifika iOS-enheter med Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt
Den här självstudien visar hur du använder Azure Notification Hubs för att sända senaste nytt till en iOS-app. När du är klar du kan registrera dig för att bryta nyhetskategorier som du är intresserad av och får endast push-meddelanden för dessa kategorier. Det här scenariot är ett vanligt mönster för många appar där meddelanden måste skickas till olika grupper av användare som tidigare har deklarerat intresse för dem, t.ex. RSS-läsare, appar för musiklyssnare osv.

Du aktiverar sändningsscenarier genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg, enheter som har registrerats för taggen tar emot meddelandet. Eftersom taggar bara är strängar behöver de inte etableras i förväg. Mer information om taggar finns i [Notification Hubs-routning och tagguttryck](notification-hubs-tags-segment-push-message.md).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Lägga till en Kategorival i appen
> * Skicka taggade meddelanden
> * Skicka meddelanden från enheten
> * Kör appen och generera meddelanden

## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet bygger på den app som du skapade i [Självstudier: Push-meddelanden till iOS-appar med Azure Notification Hubs][get-started]. Innan du påbörjar den här självstudiekursen måste du ha redan slutfört [Självstudier: Push-meddelanden till iOS-appar med Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Lägga till kategorival till appen
Det första steget är att lägga till de UI-element i din befintliga storyboard som gör att användaren väljer kategorier för att registrera. De kategorier som valts av en användare lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Lägg till följande komponenter från objektbiblioteket i din MainStoryboard_iPhone.storyboard:
   
   * Etikett med texten ”större nyheter”,
   * Etiketter med kategorin text ”World”, ”politik”, ”företag”, ”teknik”, ”Science”, ”sport”
   * Sex växlar, en per kategori, ange varje växel **tillstånd** vara **av** som standard.
   * En knapp med etiketten ”Subscribe”
     
     Din storyboard bör se ut så här:
     
     ![][3]
2. I redigeraren assistent, skapa kanaler för alla växlar och anropa dem ”WorldSwitch”, ”PoliticsSwitch”, ”BusinessSwitch”, ”TechnologySwitch”, ”ScienceSwitch”, ”SportsSwitch”
3. Skapa en åtgärd för din knapp som kallas **prenumerera**. Din ViewController.h bör innehålla följande kod:
   
    ```obj-c
        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
   
        - (IBAction)subscribe:(id)sender;
    ```
4. Skapa en ny **Cocoa Touch klass** kallas `Notifications`. Kopiera följande kod i gränssnittet i Notifications.h-fil:
   
    ```obj-c
        @property NSData* deviceToken;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;
   
        - (NSSet*)retrieveCategories;
   
        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```
5. Lägg till följande importdirektiv Notifications.m:
   
    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```
6. Kopiera följande kod i implementeringsavsnittet av filen Notifications.m.
   
    ```obj-c
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

    Den här klassen använder lokal lagring för att lagra och hämta kategorier av nyheter som tar emot den här enheten. Den innehåller också en metod för att registrera dig för dessa kategorier med hjälp av en [mall](notification-hubs-templates-cross-platform-push-messages.md) registrering.

1. Lägg till en import-sats för Notifications.h och lägga till en egenskap för en instans av klassen meddelanden i filen appdelegate-H::
   
    ```obj-c
        #import "Notifications.h"
   
        @property (nonatomic) Notifications* notifications;
    ```
2. I den **didFinishLaunchingWithOptions** -metod i AppDelegate.m, Lägg till koden för att initiera meddelanden-instans i början av metoden.  
   
    `HUBNAME` och `HUBLISTENACCESS` (definieras i hubinfo.h) bör redan ha den `<hub name>` och `<connection string with listen access>` platshållare ersätts med namnet på din meddelandehubb och anslutningssträngen för *DefaultListenSharedAccessSignature* som du tidigare hämtade
   
    ```obj-c
        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```
   
   > [!NOTE]
   > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram vanligtvis inte är säkra bör du bara distribuera nyckeln för lyssningsåtkomst med din klientapp. Lyssningsåtkomst gör det möjligt för din app att registrera sig för meddelanden, men befintliga registreringar kan inte ändras och meddelanden kan inte skickas. Nyckeln för fullständig åtkomst används i en skyddad serverdelstjänst för att skicka meddelanden och ändra befintliga registreringar.
   > 
   > 
3. I den **didRegisterForRemoteNotificationsWithDeviceToken** -metod i AppDelegate.m, Ersätt Koden i metoden med följande kod för att skicka enhetstoken till klassen meddelanden. Klassen meddelanden utför registrering för meddelanden med kategorierna. Om användaren ändrar kategori, anropa den `subscribeWithCategories` metod som svar på den **prenumerera** knappen uppdatera dem.
   
   > [!NOTE]
   > Eftersom enhetstoken som tilldelats av Apple Push Notification Service (APNS) kan risken när som helst, bör du registrera för meddelanden ofta att undvika meddelandet-fel. Det här exemplet registrerar för meddelande varje gång som appen startas. För appar som körs ofta, mer än en gång om dagen, kan du förmodligen hoppa över registreringen och spara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.
   > 
   > 
   
    ```obj-c
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

    Nu bör det finnas ingen annan kod i den **didRegisterForRemoteNotificationsWithDeviceToken** metod.

1. Följande metoder ska redan finnas i AppDelegate.m inte slutföra den [Kom igång med Notification Hubs] [ get-started] självstudien. Annars kan du lägga till dem.
   
    ```obj-c    
    -(void)MessageBox:(NSString *)title message:(NSString *)messageText
    {
   
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }
   
   * (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```
   
   Den här metoden hanterar meddelanden som tas emot när programmet körs genom att visa en enkel **UIAlert**.
2. Lägg till en import-sats för H: i ViewController.m, och kopiera följande kod i XCode-genererade **prenumerera** metod. Den här koden uppdaterar registrering för meddelanden om du vill använda de nya kategorin taggar som användaren har valt i användargränssnittet.
   
    ```obj-c
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
               [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
           } else {
               NSLog(@"Error subscribing: %@", error);
           }
       }];
    ```

   Den här metoden skapar en **NSMutableArray** kategorier och använder den **meddelanden** klassen för att lagra listan i lokal lagring och registrerar motsvarande taggar på meddelandehubben. När kategorier ändras återskapas registreringen med de nya kategorierna.
3. I ViewController.m, lägger du till följande kod i den **viewDidLoad** metoden för att ange användargränssnittet baserat på de tidigare sparad kategorierna.

    ```obj-c    
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


Appen kan nu användas för att lagra en uppsättning kategorier i enhetens lokala lagring används för att registrera med notification hub när appen startar. Användaren kan ändra valet av kategorier vid körning och klicka på den **prenumerera** metod för att uppdatera registreringen för enheten. Därefter uppdaterar du appen för att skicka aviseringar om inkomna Nyheter direkt i själva appen.

## <a name="optional-send-tagged-notifications"></a>(valfritt) Skicka taggade meddelanden
Om du inte har åtkomst till Visual Studio kan du gå vidare till nästa avsnitt och skicka meddelanden från själva appen. Du kan också skicka rätt mall-meddelande från den [Azure Portal] med fliken för felsökning för meddelandehubben. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(valfritt) Skicka meddelanden från enheten
Normalt meddelanden skulle skickas genom en serverdelstjänst, men du kan skicka aviseringar om inkomna Nyheter direkt från appen. För att göra det, uppdaterar du den `SendNotificationRESTAPI` metod som du definierade i den [Kom igång med Notification Hubs] [ get-started] självstudien.

1. I `ViewController.m`, uppdatera den `SendNotificationRESTAPI` metod som följer så att den accepterar en parameter för kategori-taggen och skickar rätt [mall](notification-hubs-templates-cross-platform-push-messages.md) meddelande.
   
    ```obj-c
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
2. I `ViewController.m`, uppdatera den **skicka meddelande** åtgärden som visas i den kod som följer. Så att den skickar meddelanden med hjälp av varje tagg individuellt och skickar till flera plattformar.

    ```obj-c
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
    ```


1. Återskapa projektet och kontrollera att du har inga build-fel.

## <a name="run-the-app-and-generate-notifications"></a>Kör appen och generera meddelanden
1. Tryck på knappen Kör för att skapa projektet och starta appen. Välj några inkomna nyheter alternativ att prenumerera på och tryck sedan på den **prenumerera** knappen. Du bör se en dialogruta som visar meddelandena prenumererar på.
   
    ![][1]
   
    När du väljer **prenumerera**, appen konverterar de valda kategorierna till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben.
2. Ange ett meddelande som ska skickas som de senaste nyheterna tryck sedan på den **skicka meddelande** knappen. Du kan också köra .NET-konsolapp för att generera meddelanden.
   
    ![][2]
3. Varje enhet som prenumererar på senaste nytt tar emot de aviseringar om inkomna nyheter du just skickade.

## <a name="next-steps"></a>Nästa steg
I den här självstudien får skickat du broadcast-meddelanden till specifika iOS-enheter som har registrerats i kategorier. Om du vill lära dig mer om att skicka lokaliserade meddelanden går du vidare till följande självstudie: 

> [!div class="nextstepaction"]
>[Skicka lokaliserade meddelanden](notification-hubs-ios-xplat-localized-apns-push-notification.md)


<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure Portal]: https://portal.azure.com
