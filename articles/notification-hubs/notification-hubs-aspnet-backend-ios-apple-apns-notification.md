---
title: Skicka push-meddelanden till specifika användare med Azure Notification Hubs | Microsoft-dokument
description: Lär dig att använda Azure Notification Hubs till att skicka push-meddelanden till specifika användare.
documentationcenter: ios
author: sethm
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 48135ea614bbab4ca6649a83895ae5f632918c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387466"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Självstudiekurs: Skicka push-meddelanden till specifika användare med Hjälp av Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Denna självstudie visar hur du använder Azure Notification Hubs till att skicka push-meddelanden till en specifik appanvändare på en specifik enhet. En ASP.NET WebAPI-backend används för att autentisera klienter och generera meddelanden, vilket visas i vägledningsavsnittet [Registrera från appens backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett WebAPI-projekt
> * Autentisera klienter mot WebAPI-serverdelen
> * Registrera för meddelanden med hjälp av WebAPI-serverdelen
> * Skicka meddelanden från WebAPI-serverdelen
> * Publicera den nya WebAPI-serverdelen
> * Ändra din iOS-app
> * Testa programmet

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du har skapat och konfigurerat meddelandehubben enligt beskrivningen i [Komma igång med Meddelandehubbar (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md) Den här självstudien är också en förutsättning för självstudien [Secure Push (iOS).](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
Om du vill använda Mobilappar som backend-tjänst läser du [Komma igång med Push i mobilappar.](../app-service-mobile/app-service-mobile-ios-get-started-push.md)

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Ändra din iOS-app

1. Öppna den app för enkelsidig sida som du skapade i självstudien [Komma igång med meddelandehubbar (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

   > [!NOTE]
   > Det här avsnittet förutsätter att projektet är konfigurerat med ett tomt organisationsnamn. Om inte, måste du förbereda organisationens namn till alla klassnamn.

2. Lägg `Main.storyboard` till de komponenter som visas i skärmbilden från objektbiblioteket i filen.

    ![Redigera storyboard i Xcode-gränssnittsverktyget][1]

   * **Användarnamn**: Ett UITextField med platshållartext, *Ange användarnamn*, omedelbart under etiketten skicka resultat och begränsad till vänster och höger marginal och under etiketten skicka resultat.
   * **Lösenord**: Ett UITextField med platshållartext, *Ange lösenord*, omedelbart under textfältet för användarnamn och begränsad till vänster- och högermarginalerna och under textfältet för användarnamn. Markera alternativet **Säker textpost** i attributkontroll under *Returnyckel*.
   * **Logga in**: Ett UIButton som är märkt omedelbart under textfältet för lösenord och avmarkera alternativet **Aktiverad** i attributinspektören under *Kontrollinnehåll*
   * **WNS**: Etikett och switch för att aktivera skicka meddelandet Windows Notification Service om det har ställts in på navet. Se självstudien [för komma igång i Windows.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
   * **GCM**: Etikett och växla för att aktivera sändning av meddelandet till Google Cloud Messaging om det har konfigurerats på hubben. Se [Android Komma igång](notification-hubs-android-push-notification-google-gcm-get-started.md) handledning.
   * **APNS**: Etikett och switch för att möjliggöra att skicka meddelandet till Apple Platform Notification Service.
   * **Mottagare Användarnamn: En** UITextField med platshållartext, *Mottagare användarnamn tagg*, omedelbart under GCM etiketten och begränsad till vänster och höger marginaler och under GCM etiketten.

     Vissa komponenter har lagts till i [självstudien Komma igång med meddelandehubbar (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

3. **Ctrl** dra från komponenterna `ViewController.h` i vyn till och lägg till dessa nya uttag.

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. Lägg `ViewController.h`till följande `#define` efter importutdragen. Ersätta `<Enter Your Backend Endpoint>` platshållaren med den mål-URL som du använde för att distribuera appens meddel i föregående avsnitt. Till exempel `http://your_backend.azurewebsites.net`.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. Skapa en ny Cocoa Touch-klass `RegisterClient` som heter att samverka med den ASP.NET backend som du har skapat i projektet. Skapa klassen som `NSObject`ärver från . Lägg sedan till följande `RegisterClient.h`kod i .

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. I `RegisterClient.m`avsnittet uppdatera `@interface` du avsnittet:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Ersätt `@implementation` avsnittet i RegisterClient.m med följande kod:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Den här koden implementerar logiken som förklaras i vägledningsartikeln [Registrera från din app-backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) med NSURLSession för att utföra REST-anrop till appens backend och NSUserDefaults för att lagra registreringen Lokalt returneras av meddelandehubben.

    Den här klassen `authorizationHeader` kräver att dess egenskap ställs in för att fungera korrekt. Den här egenskapen `ViewController` anges av klassen efter inloggningen.

8. I `ViewController.h`lägger `#import` du `RegisterClient.h`till ett utdrag för . Lägg sedan till en deklaration för `RegisterClient` enhetstoken och referens till en instans i avsnittet: `@interface`

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. Lägg till en deklaration för en privat `@interface` metod i avsnittet i ViewController.m:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Följande kodavsnitt är inte ett säkert autentiseringsschema, `createAndSetAuthenticationHeaderWithUsername:AndPassword:` du bör ersätta implementeringen av den med din specifika autentiseringsmekanism som genererar en autentiseringstoken som ska förbrukas av registerklientklassen, t.ex.

10. Lägg sedan `@implementation` till `ViewController.m`följande kod i avsnittet , som lägger till implementeringen för att ange enhetstoken och autentiseringshuvudet.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Observera hur inställningen av enhetstoken aktiverar inloggningsknappen. Det beror på att vystyrenheten som en del av inloggningsåtgärden registrerar för push-meddelanden med appens backend. Därför vill du inte att inloggningsåtgärden ska vara tillgänglig förrän enhetstoken har konfigurerats korrekt. Du kan frikoppla inloggningen från push-registreringen så länge den förstnämnda inträffar innan den senare.

11. I ViewController.m använder du följande utdrag för att implementera åtgärdsmetoden för **inloggningsknappen** och en metod för att skicka meddelandemeddelandet med hjälp av ASP.NET serverings-serverning.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Uppdatera åtgärden för knappen **Skicka meddelande** för att använda ASP.NET-backend och skicka till alla PNS som aktiveras av en växel.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. I `ViewDidLoad` funktionen lägger du till följande `RegisterClient` för att instansiera instansiera instansiera instansiera instansieringen och ange ombud för dina textfält.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Nu `AppDelegate.m`i , ta bort `application:didRegisterForPushNotificationWithDeviceToken:` allt innehåll på metoden och ersätta den med följande (för att se till att vystyrenheten innehåller den senaste enheten token hämtas från APNs):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Slutligen `AppDelegate.m`i , se till att du har följande metod:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testa programmet

1. I XCode kör du appen på en fysisk iOS-enhet (push-meddelanden fungerar inte i simulatorn).
2. Ange samma värde för både användarnamn och lösenord i iOS-appgränssnittet. Klicka sedan på **Logga in**.

    ![iOS-testprogram][2]

3. Du bör se en pop-up informera dig om registrering framgång. Klicka på **OK**.

    ![iOS-testmeddelande visas][3]

4. I textfältet **Mottagares användarnamnstagg* anger du den användarnamnstagg som används med registreringen från en annan enhet.
5. Ange ett meddelande och klicka på **Skicka meddelande**. Endast de enheter som har en registrering med mottagarens användarnamnstagg får meddelandet. Det skickas bara till dessa användare.

    ![iOS-testtaggat meddelande][4]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om push-meddelanden till specifika användare som har taggar associerade med sina registreringar. Information om hur du skickar platsbaserade meddelanden finns i nästa självstudie: 

> [!div class="nextstepaction"]
>[Platsbaserade push-meddelanden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
