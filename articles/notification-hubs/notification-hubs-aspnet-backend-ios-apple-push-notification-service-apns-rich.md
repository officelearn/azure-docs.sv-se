---
title: Azure Notification Hubs omfattande Push
description: Lär dig hur du skickar omfattande push-meddelanden till en iOS-app från Azure. Kodexempel som skrivits i Objective-C och C#.
documentationcenter: ios
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: 21ed7dd1120958576651703283a0732e3843546d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs omfattande Push
## <a name="overview"></a>Översikt
För att kunna interagera med snabbmeddelanden omfattande innehållet användarna kanske ett program push utöver oformaterad text. Dessa aviseringar främja användarinteraktioner och finns innehåll, till exempel URL: er, ljud, bilder/kuponger och mycket mer. Den här kursen bygger på den [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) avsnittet, och visar hur du skickar push-meddelanden med nyttolaster (till exempel bild).

Den här kursen är kompatibel med iOS 7 och 8.

  ![][IOS1]

På en hög nivå:

1. Appens serverdel:
   * Lagrar omfattande nyttolasten (i det här fallet bild) i databasen/lokal serverdelslagring
   * Skickar ID för omfattande meddelandet till enheten
2. Appen på enheten:
   * Kontaktar serverdelen som begär den omfattande nyttolasten med det ID som den tar emot
   * Skickar meddelanden för användare på enheten när datahämtning är klar och visar nyttolasten omedelbart när användarna trycker på Läs mer

## <a name="webapi-project"></a>WebAPI-projekt
1. Öppna i Visual Studio den **AppBackend** projekt som du skapade i den [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kursen.
2. Hämta en avbildning som du vill meddela användare med och placera den i en **img** mappen i projektkatalogen.
3. Klicka på **visa alla filer** i Solution Explorer och högerklicka på mappen till **inkluderar i projektet**.
4. Ändra bilden är markerad i fönstret Egenskaper för att skapa åtgärden **inbäddad resurs**.
   
    ![][IOS2]
5. I **Notifications.cs**, Lägg till följande med instruktionen:
   
        using System.Reflection;
6. Uppdatera hela **meddelanden** klassen med följande kod. Se till att ersätta platshållarna med dina autentiseringsuppgifter för notification hub och bildfilens namn.
   
        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }
   
        public class Notifications {
            public static Notifications Instance = new Notifications();
   
            private List<Notification> notifications = new List<Notification>();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }
   
            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };
   
                notifications.Add(notification);
   
                return notification;
            }
   
            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }
   
   > [!NOTE]
   > (valfritt) Referera till [så att bädda in och komma åt resurser med hjälp av Visual C#](http://support.microsoft.com/kb/319292) för mer information om hur du lägger till och hämta projektresurser.
   > 
   > 
7. I **NotificationsController.cs**, omdefiniera **NotificationsController** med följande kodavsnitt. Detta skickar ett inledande tyst omfattande meddelande-id till enheten och kan klientsidan hämtning av avbildningen:
   
        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);
   
            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");
   
            return result;
        }
   
        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");
   
            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";
   
            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
8. Vi ska nu omdistribuera den här appen till en Azure-webbplats för att göra den tillgänglig från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
9. Välj Azure-webbplatsen som publicera-mål. Logga in med ditt Azure-konto och markera en befintlig eller ny webbplats och anteckna den **Måladress** egenskap i den **anslutning** fliken. Vi ska referera till den här URL:en som *serverdelens slutpunkt* senare i den här självstudiekursen. Klicka på **Publicera**.

## <a name="modify-the-ios-project"></a>Ändra iOS-projektet
Nu när du har ändrat din Apps serverdel för att skicka bara den *id* av ett meddelande ska du ändra din iOS-app för att hantera detta id och hämta omfattande meddelandet från din serverdel.

1. Öppna projektet iOS och aktivera fjärråtkomst-meddelanden genom att gå till dina viktigaste app mål i den **mål** avsnitt.
2. Klicka på **funktioner**, aktivera **bakgrundslägen**, och kontrollera den **Remote Notifications** kryssrutan.
   
    ![][IOS3]
3. Gå till **Main.storyboard**, och kontrollera att du har en View-Controller (kallade Start View Controller i den här självstudiekursen) från [meddela användaren](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kursen.
4. Lägg till en **navigering Controller** till storyboard och CTRL-dra till Start View Controller så att den den **rot visa** av navigeringen. Kontrollera att den **är inledande View Controller** i attribut inspector väljs för navigering-styrenhet.
5. Lägg till en **View Controller** storyboard och lägga till en **bild visa**. Detta är den sida som användarna ser när de vill veta mer genom att klicka på notifiication. Storyboard bör se ut så här:
   
    ![][IOS4]
6. Klicka på den **Start View Controller** storyboard och kontrollera att den har **homeViewController** som dess **anpassad klass** och **Storyboard ID**under Identity-inspector.
7. Gör likadant för avbildningen View Controller som **imageViewController**.
8. Skapa sedan en ny View Controller klass som heter **imageViewController** att hantera Användargränssnittet som du nyss skapade.
9. I **imageViewController.h**, lägger du till följande den styrenheten gränssnittet deklarationer. Se till att CTRL-dra från storyboard avbildningen dessa egenskaper för att länka samman:
   
        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;
10. I **imageViewController.m**, Lägg till följande i slutet av **viewDidload**:
    
        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];
11. I **AppDelegate.m**, importera bild-domänkontrollant som du skapade:
    
        #import "imageViewController.h"
12. Lägg till ett gränssnitt avsnitt med följande deklaration:
    
        @interface AppDelegate ()
    
        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;
    
        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;
    
        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;
    
        @end
13. I **AppDelegate**, kontrollera att din app registrerar för tyst meddelanden i **program: didFinishLaunchingWithOptions**:
    
        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];
    
        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");
    
            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";
    
            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];
    
            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

1. Ersätt i följande implementeringen för **program: didRegisterForRemoteNotificationsWithDeviceToken** ta storyboard Användargränssnittet ändras i beräkningen:
   
       // Access navigation controller which is at the root of window
       UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
       // Get home view controller from stack on navigation controller
       homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
       hvc.deviceToken = deviceToken;
2. Lägg sedan till följande metoder för att **AppDelegate.m** att hämta bilden från din slutpunkt och skicka ett meddelande om lokala när hämtningen är klar. Ersätt platshållaren `{backend endpoint}` med backend-slutpunkten:
   
       NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";
   
       // Helper: retrieve notification content from backend with rich notification id
       - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
           UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
           homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
           NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
           // Check if authenticated
           if (!authenticationHeader) return;
   
           NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];
   
           NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
           NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
           [request setHTTPMethod:@"GET"];
           NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
           [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
   
           NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
   
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
               if (!error && httpResponse.statusCode == 200) {
                   // From NSData to UIImage
                   self.imagePayload = [UIImage imageWithData:data];
   
                   completion(nil);
               }
               else {
                   NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                   if (error)
                       completion(error);
                   else {
                       completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                   }
               }
           }];
           [dataTask resume];
       }
   
       // Handle silent push notifications when id is sent from backend
       - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
           self.userInfo = userInfo;
           int richId = [[self.userInfo objectForKey:@"richId"] intValue];
           NSString* richType = [self.userInfo objectForKey:@"richType"];
   
           // Retrieve image data
           if ([richType isEqualToString:@"img"]) {  
               [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                   if (!error){
                       // Send local notification
                       UILocalNotification* localNotification = [[UILocalNotification alloc] init];
   
                       // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                       localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                       localNotification.userInfo = self.userInfo;
                       localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                       localNotification.timeZone = [NSTimeZone defaultTimeZone];
   
                       // iOS8 categories
                       if (self.iOS8) {
                           localNotification.category = @"richPush";
                       }
   
                       [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                       handler(UIBackgroundFetchResultNewData);
                   }
                   else{
                       handler(UIBackgroundFetchResultFailed);
                   }
               }];
           }
           // Add "else if" here to handle more types of rich content such as url, sound files, etc.
       }
3. Hantera lokala meddelandet ovan genom att öppna bilden view controller i **AppDelegate.m** med följande metoder:
   
       // Helper: redirect users to image view controller
       - (void)redirectToImageViewWithImage: (UIImage *)img {
           UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
           UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                    bundle: nil];
           imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
           // Pass data/image to image view controller
           imgViewController.imagePayload = img;
   
           // Redirect
           [navigationController pushViewController:imgViewController animated:YES];
       }
   
       // Handle local notification sent above in didReceiveRemoteNotification
       - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
           if (application.applicationState == UIApplicationStateActive) {
               // Show in-app alert with an extra "more" button
               UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
   
               [alert show];
           }
           // App becomes active from user's tap on notification
           else {
               [self redirectToImageViewWithImage:self.imagePayload];
           }
       }
   
       // Handle buttons in in-app alerts and redirect with data/image
       - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
           // Handle "more" button
           if (buttonIndex == 1)
           {
               [self redirectToImageViewWithImage:self.imagePayload];
           }
           // Add "else if" here to handle more buttons
       }
   
       // Handle notification setting actions in iOS8
       - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
           // Handle richPush related buttons
           if ([identifier isEqualToString:@"richPushMore"]) {
               [self redirectToImageViewWithImage:self.imagePayload];
           }
           completionHandler();
       }

## <a name="run-the-application"></a>Köra programmet
1. Kör appen i XCode på en fysisk iOS-enhet (push-meddelanden inte fungerar i simulatorn).
2. I appens användargränssnitt, anger du ett användarnamn och lösenord med samma värde för autentisering och klickar på **loggar In**.
3. Klicka på **skicka push** och du bör se en avisering i appen. Om du klickar på **mer**, kommer du till den bild som du vill inkludera i din Apps serverdel.
4. Du kan också klicka på **skicka push** och tryck på knappen Startsida för enheten omedelbart. Om en stund får du ett push-meddelande. Om du knackar på den eller klicka på mer kommer din app och omfattande avbildningen innehåll.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
