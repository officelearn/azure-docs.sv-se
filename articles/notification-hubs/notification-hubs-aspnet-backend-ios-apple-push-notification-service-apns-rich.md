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
ms.openlocfilehash: f2fa2b8b43a4840d8c6956efb7704327cca78d7c
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232520"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs omfattande Push
## <a name="overview"></a>Översikt
För att engagera användare med omedelbar omfattande innehållet, kan ett program vill överföra mer än bara oformaterad text. Dessa aviseringar för enklare användarinteraktioner och finns innehåll, till exempel URL: er, ljud, bilder/kuponger med mera. Den här självstudien bygger på den [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) avsnittet, och visar hur du skickar push-meddelanden som införlivar nyttolaster (till exempel bild).

Den här självstudien är kompatibel med iOS 7 och 8.

  ![][IOS1]

På hög nivå:

1. App-serverdel:
   * Lagrar omfattande nyttolasten (i det här fallet bild) i databasen/lokal serverdelslagring
   * Skickar ID: T för det här omfattande meddelandet till enheten
2. Appen på enheten:
   * Kontaktar serverdelen som begär den omfattande nyttolasten med det ID som den tar emot
   * Skickar meddelanden för användare på enheten när hämtning av data är klar och visar nyttolasten omedelbart när användare trycker på mer

## <a name="webapi-project"></a>WebAPI-projekt
1. Visual Studio, öppna den **AppBackend** projektet som du skapade i den [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) självstudien.
2. Hämta en avbildning som du vill meddela användare med och placera den i en **img** mapp i projektkatalogen.
3. Klicka på **visa alla filer** i Solution Explorer och högerklicka på mappen till **inkluderar i projektet**.
4. Med den avbildning som har valt att ändra dess byggåtgärd i egenskapsfönstret till **inbäddad resurs**.
   
    ![][IOS2]
5. I **Notifications.cs**, Lägg till följande med instruktionen:
   
        using System.Reflection;
6. Uppdatera hela **meddelanden** klassen med följande kod. Var noga med att ersätta platshållarna med dina autentiseringsuppgifter för notification hub och filnamn för avbildning.
   
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
   > (valfritt) Referera till [bädda in och få åtkomst till resurser med hjälp av Visual C# ](http://support.microsoft.com/kb/319292) för mer information om hur du lägger till och hämta projektresurser.
   > 
   > 
7. I **NotificationsController.cs**, omdefiniera **NotificationsController** med följande kodfragment. Det skickar ett inledande tyst omfattande meddelande-id till enheten och att klientsidan hämtning av bild:
   
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
8. Nu ska vi nytt distribuera den här appen till en Azure-webbplats för att kunna göra det tillgängligt från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
9. Välj Azure-webbplats som din publiceringsmål. Logga in med ditt Azure-konto och välj en befintlig eller ny webbplats och anteckna den **mål-URL** -egenskapen i den **anslutning** fliken. Vi ska referera till den här URL:en som *serverdelens slutpunkt* senare i den här självstudiekursen. Klicka på **Publicera**.

## <a name="modify-the-ios-project"></a>Ändra på iOS-projektet
Nu när du har ändrat din appserverdel att skicka bara *id* av ett meddelande, ska du ändra din iOS-app för att hantera detta id och hämta omfattande meddelandet från din serverdel.

1. Öppna din iOS-projektet och aktivera fjärråtkomst meddelanden genom att gå till dina viktigaste app-mål i den **mål** avsnittet.
2. Klicka på **funktioner**, aktivera **bakgrundslägen**, och kontrollera den **Remote Notifications** kryssrutan.
   
    ![][IOS3]
3. Gå till **Main.storyboard**, och kontrollera att du har en View-Controller (kallas start View Controller i den här självstudien) från [meddela användaren](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) självstudien.
4. Lägg till en **navigering Controller** till storyboard och CTRL-dra för att start-vykontroll så att de blir den **rot visa** för navigering. Kontrollera att den **är inledande View-Controller** inspector är markerad för kontrollanten navigering i attribut.
5. Lägg till en **View Controller** storyboard och lägga till en **bild visa**. Det här är den sida som användarna ser när de väljer att lära dig mer genom att klicka på notifiication. Din storyboard bör se ut så här:
   
    ![][IOS4]
6. Klicka på den **Start View Controller** i storyboard och kontrollera att den har **homeViewController** som dess **anpassad klass** och **Storyboard ID**under Identity-inspector.
7. Gör likadant för avbildning View Controller som **imageViewController**.
8. Skapa sedan en ny View Controller-klass som heter **imageViewController** att hantera Användargränssnittet som du nyss skapade.
9. I **imageViewController.h**, lägger du till följande den styrenheten gränssnittet deklarationer. Se till att CTRL-dra från storyboard bild dessa egenskaper att koppla:
   
        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;
10. I **imageViewController.m**, Lägg till följande i slutet av **viewDidload**:
    
        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];
11. I **AppDelegate.m**, importera kontrollanten avbildning som du skapade:
    
        #import "imageViewController.h"
12. Lägg till ett gränssnitt avsnitt med följande försäkran:
    
        @interface AppDelegate ()
    
        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;
    
        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;
    
        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;
    
        @end
13. I **AppDelegate**, kontrollera att din app registreras för tyst meddelanden i **application: didFinishLaunchingWithOptions**:
    
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

1. Ersätt i följande implementering för **program: didRegisterForRemoteNotificationsWithDeviceToken** ska börja storyboard Användargränssnittet ändras på konto:
   
       // Access navigation controller which is at the root of window
       UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
       // Get home view controller from stack on navigation controller
       homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
       hvc.deviceToken = deviceToken;
2. Lägg sedan till följande metoder till **AppDelegate.m** hämtas avbildningen från din slutpunkt och skicka ett meddelande som lokal när hämtningen är klar. Ersätt platshållaren `{backend endpoint}` med serverdelens slutpunkt:
   
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
3. Hanteringen av lokala meddelanden ovan genom att öppna bild view controller i **AppDelegate.m** med följande metoder:
   
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

## <a name="run-the-application"></a>Kör programmet
1. Kör appen på en fysisk iOS-enhet (push-meddelanden inte fungerar i simulatorn) i XCode.
2. I iOS-appens användargränssnitt, anger du ett användarnamn och lösenord med samma värde för autentisering och klicka på **logga In**.
3. Klicka på **skicka push** och du bör se en avisering i appen. Om du klickar på **mer**, kommer du att den avbildning som du har valt att inkludera i appens serverdel.
4. Du kan också klicka på **skicka push** och tryck på startknappen av enheten omedelbart. Om en stund får du ett push-meddelande. Om du trycker på den eller klicka på mer kommer du att din app och innehållet i omfattande.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
