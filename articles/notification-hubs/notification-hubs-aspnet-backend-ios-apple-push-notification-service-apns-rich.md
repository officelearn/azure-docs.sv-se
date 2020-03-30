---
title: Rich Push för Azure-meddelandehubbar
description: Läs om hur du skickar omfattande push-meddelanden till en iOS-app från Azure. Kodexempel skrivna i Objective-C och C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9da629929ca88f406dc503710477104be94c47e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212201"
---
# <a name="azure-notification-hubs-rich-push"></a>Rich Push för Azure-meddelandehubbar

## <a name="overview"></a>Översikt

För att engagera användare med direkt rikt innehåll, ett program kanske vill driva bortom oformaterad text. Dessa meddelanden främjar användarinteraktioner och presenterar innehåll som webbadresser, ljud, bilder/kuponger med mera. Den här självstudien bygger på avsnittet [Meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) och visar hur du skickar push-meddelanden som innehåller nyttolaster (till exempel bild).

Den här självstudien är kompatibel med iOS 7 & 8.

  ![][IOS1]

På en hög nivå:

1. Appens backend:
   * Lagrar den omfattande nyttolasten (i det här fallet avbildningen) i backend-databasen/den lokala lagringen
   * Skickar ID för det här omfattande meddelandet till enheten
2. App på enheten:
   * Kontaktar den bakre sökda som begär den omfattande nyttolasten med det ID som den tar emot
   * Skickar meddelanden till användarna på enheten när datahämtningen är klar och visar nyttolasten direkt när användarna trycker för att lära sig mer

## <a name="webapi-project"></a>WebAPI-projekt

1. Öppna **AppBackend-projektet** som du skapade i självstudien [Meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) i Visual Studio.
2. Skaffa en bild som du vill meddela användarna med och placera den i en **img-mapp** i projektkatalogen.
3. Klicka på **Visa alla filer** i lösningsutforskaren och högerklicka på mappen som ska ingå i **Project**.
4. När bilden är markerad ändrar du fönstret Skapa åtgärd i egenskaper till **Inbäddad resurs**.

    ![][IOS2]
5. I `Notifications.cs` lägger du till följande using-instruktion:

    ```csharp
    using System.Reflection;
    ```
6. Uppdatera hela `Notifications` klassen med följande kod. Var noga med att ersätta platshållarna med dina autentiseringsuppgifter för meddelandehubben och bildfilnamnet.

    ```csharp
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
    ```

   > [!NOTE]
   > (valfritt) Mer information om hur du lägger till och hämtar projektresurser finns [i Så här bäddar du in och använder resurser i Visual C#.](https://support.microsoft.com/kb/319292)

7. I `NotificationsController.cs`definierar du om 'NotificationsController med följande utdrag. Detta skickar ett första tyst rich notification-ID till enheten och tillåter hämtning på klientsidan av bilden:

    ```csharp
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
    ```
8. Nu kommer vi att distribuera om den här appen till en Azure-webbplats för att göra den tillgänglig från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
9. Välj Azure-webbplats som publiceringsmål. Logga in med ditt Azure-konto och välj en befintlig eller ny webbplats och anteckna egenskapen **mål-URL** på fliken **Anslutning.** Vi kommer att referera till den här webbadressen som din *backend-slutpunkt* senare i den här självstudien. Klicka på **Publicera**.

## <a name="modify-the-ios-project"></a>Ändra iOS-projektet

Nu när du har ändrat din app serverd för att skicka bara *id* för ett meddelande, kommer du att ändra din iOS app för att hantera detta ID och hämta det rika meddelandet från serveraden.

1. Öppna ditt iOS-projekt och aktivera fjärrmeddelanden genom att gå till huvudappmålet i avsnittet **Mål.**
2. Klicka på **Funktioner,** aktivera **bakgrundslägen**och markera kryssrutan **Fjärrmeddelanden.**

    ![][IOS3]
3. Öppna `Main.storyboard`och se till att du har en Vykontrollant (kallad Home View Controller i den här självstudien) från [Självstudien Meddela användare.](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
4. Lägg till en **navigeringskontroll i** storyboarden och dra kontroll till Hemvykontroll för att göra den till **navigeringens rotvy.** Kontrollera att **styrenheten är inledande vy** i attribut är markerad endast för navigeringsstyrenheten.
5. Lägg till en **vykontroll i** storyboard och lägg till en **bildvy**. Det här är sidan som användarna ser när de väljer att lära sig mer genom att klicka på meddelandet. Din storyboard ska se ut så här:

    ![][IOS4]
6. Klicka på **hemvykontroll i** storyboard och se till att den har **homeViewController** som **anpassat klass-** och **storyboard-ID** under identitetsinspektören.
7. Gör samma sak för Image View Controller som **imageViewController**.
8. Skapa sedan en ny klassen View Controller med namnet **imageViewController** för att hantera det användargränssnitt som du just skapade.
9. I **imageViewController.h**lägger du till följande i styrenhetens gränssnittsdeklarationer. Se till att styra-dra från storyboard bildvyn till dessa egenskaper för att länka de två:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. I `imageViewController.m`lägger du till `viewDidload`följande i slutet av :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. Importera `AppDelegate.m`den bildstyrenhet som du skapade i:

    ```objc
    #import "imageViewController.h"
    ```
12. Lägg till ett gränssnittsavsnitt med följande deklaration:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```
13. I `AppDelegate`kontrollerar du att appen registrerar `application: didFinishLaunchingWithOptions`för tysta meddelanden i :

    ```objc
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
    ```

14. Ersätt i följande `application:didRegisterForRemoteNotificationsWithDeviceToken` implementering för att ta hänsyn till ändringar i storyboard-användargränssnittet:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Lägg sedan till följande `AppDelegate.m` metoder för att hämta bilden från slutpunkten och skicka ett lokalt meddelande när hämtningen är klar. Se till att ersätta `{backend endpoint}` platshållaren med din slutpunkt för backend:

    ```objc
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
    ```
16. Hantera det lokala meddelandet ovan genom att `AppDelegate.m` öppna bildvisningskontrollen med följande metoder:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
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
    ```

## <a name="run-the-application"></a>Kör programmet

1. I XCode kör du appen på en fysisk iOS-enhet (push-meddelanden fungerar inte i simulatorn).
2. Ange ett användarnamn och lösenord av samma värde för autentisering i iOS-appgränssnittet och klicka på **Logga in**.
3. Klicka på **Skicka push** och du bör se en avisering i appen. Om du klickar på **Mer**kommer du till den bild du valde att inkludera i din app backend.
4. Du kan också klicka på **Skicka push** och omedelbart trycka på enhetens hemknapp. Om en liten stund får du ett push-meddelande. Om du trycker på den eller klickar på Mer kommer du till din app och det omfattande bildinnehållet.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
