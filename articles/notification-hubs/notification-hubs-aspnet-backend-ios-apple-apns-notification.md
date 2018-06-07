---
title: Push-meddelanden till specifika användare använder Azure Notification Hubs | Microsoft Docs
description: Lär dig att använda Azure Notification Hubs till att skicka push-meddelanden till specifika användare.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/13/2018
ms.author: dimazaid
ms.openlocfilehash: 36d70c40e3de7bd38cdfc566da37060cdcea9060
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "33777543"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Självstudier: Push-meddelanden till specifika användare använder Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Denna självstudie visar hur du använder Azure Notification Hubs till att skicka push-meddelanden till en specifik appanvändare på en specifik enhet. En ASP.NET-WebAPI-serverdel används för att autentisera klienter och att generera meddelanden, som visas i avsnittet vägledning [registrering från din Apps serverdel](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett WebAPI-projekt
> * Autentisera klienter mot WebAPI-serverdelen
> * Registrera för meddelanden med hjälp av WebAPI-serverdelen
> * Skicka meddelanden från WebAPI-serverdelen
> * Publicera den nya WebAPI-serverdelen
> * Ändra din iOS-app
> * Testa programmet

## <a name="prerequisites"></a>Förutsättningar
Den här kursen förutsätter att du har skapat och konfigurerat din meddelandehubb enligt beskrivningen i [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Den här kursen är också nödvändigt för att den [Secure Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) kursen.
Om du vill använda Mobile Apps som serverdelstjänsten kan se den [Mobile Apps Kom igång med Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Ändra din iOS-app
1. Öppna en sida visa app som du skapade i den [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kursen.
   
   > [!NOTE]
   > Det här avsnittet förutsätter att projektet har konfigurerats med ett tomt organisationsnamn. Om inte, måste du har lagt till ditt organisationsnamn till alla klassnamn.
   > 
   > 
2. I den `Main.storyboard`, lägga till komponenter som visas i skärmbilden från objektbiblioteket.
   
    ![][1]
   
   * **Användarnamnet**: A UITextField med platshållartext, *ange användarnamn*omedelbart under Skicka resultaten etikett och begränsad till höger och vänster marginaler och under Skicka resultaten etikett.
   * **Lösenordet**: A UITextField med platshållartext, *ange lösenord för*, omedelbart under användarnamnet text fältet och begränsad till höger och vänster marginaler och under textfältet användarnamn. Kontrollera den **Secure textinmatning** alternativet i attributet-Inspector under *returnera nyckeln*.
   * **Logga in**: A UIButton etikett direkt under lösenordsfältet och avmarkerar de **aktiverad** alternativet i attribut-Inspector under *kontrollens innehåll*
   * **WNS**: etikett och växel för att skicka meddelande Windows Notification Service om den har ställts in på hubben. Finns det [Windows komma igång](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kursen.
   * **GCM**: etikett och växel för att skicka aviseringen till Google Cloud Messaging om den har ställts in på hubben. Se [Android igång](notification-hubs-android-push-notification-google-gcm-get-started.md) kursen.
   * **APN**: etikett och växel för att skicka aviseringen till Apple Platform Notification Service.
   * **Mottagaren Username:A** UITextField med platshållartext, *mottagaren användarnamn taggen*direkt under GCM etikett och begränsad till höger och vänster marginaler och under GCM-etiketten.

    Vissa komponenter har lagts till i den [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kursen.

1. **CTRL** dra från komponenter i vyn till ViewController.h och Lägg till dessa nya uttag.
   
    ```obj-c
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
2. Lägg till följande i ViewController.h, `#define` efter import-instruktioner. Ersätt den *< Endpoint ange Backend\>*  med mål-URL som du använde för att distribuera din Apps serverdel i föregående avsnitt. Till exempel *http://you_backend.azurewebsites.net*.
   
    ```obj-c
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```
3. I projektet, skapa en ny **Cocoa Touch klassen** med namnet **RegisterClient** gränssnittet med ASP.NET serverdel du skapat. Skapa klassen som ärver från `NSObject`. Lägg sedan till följande kod i RegisterClient.h.

    ```obj-c   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
    ```
4. I den `RegisterClient.m`, uppdatera det `@interface` avsnitt:

    ```obj-c   
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
5. Ersätt den `@implementation` avsnitt i RegisterClient.m med följande kod:

    ```obj-c
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

    Den här koden implementerar logik som beskrivs i artikeln vägledning [registrering från din Apps serverdel](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) använda NSURLSession för REST-anrop till din Apps serverdel och NSUserDefaults lokalt lagra registrationId som returneras av den meddelandehubben.

    Den här klassen kräver egenskapen **authorizationHeader** ställs in för att fungera korrekt. Den här egenskapen anges av den **ViewController** klassen efter inloggningen.

1. ViewController.h, lägga till en `#import` -instruktion för RegisterClient.h. Sedan lägger du till en deklaration för enhetstoken och hänvisar till en `RegisterClient` instans i den `@interface` avsnitt:

    ```obj-c   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
    ```
2. ViewController.m, lägga till en privat metoddeklaration i den `@interface` avsnitt:
   
    ```obj-c
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end
    ```

    > [!NOTE]
    > Följande kodavsnitt är inte en säker autentiseringsschema, ska du ersätta att genomföra den **createAndSetAuthenticationHeaderWithUsername:AndPassword:** med specifika autentiseringsmekanism som genererar en autentiseringstoken som ska konsumeras av registrera klient-klassen, t.ex. OAuth Active Directory.
1. I den `@implementation` avsnitt i `ViewController.m`, Lägg till följande kod, som lägger till implementeringen för att ange rubriken enhet token och autentisering.
   
    ```obj-c
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
   
    Observera hur enhetstoken kan logga in knappen. Det beror på att som en del av åtgärden inloggningen view controller registrerar för push-meddelanden med appens serverdel. Därför vill inte logga In åtgärden ska vara tillgänglig tills enhetstoken har konfigurerats korrekt. Så länge som det tidigare sker innan de senare kan du frikoppla inloggningen från registreringen av push.
2. I ViewController.m, använder du följande kodavsnitt för att implementera åtgärdsmetod för din **loggar In** knapp och en metod för att skicka meddelandet med ASP.NET-serverdel.
   
    ```obj-c
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

1. Uppdatera åtgärden för den **skicka meddelande** knappen för att använda ASP.NET-serverdel och skicka till alla PNS aktiveras med en växel.

    ```obj-c
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

1. I funktionen **ViewDidLoad**, Lägg till följande för att skapa en instans av RegisterClient instansen och ange ombud för textfält.
   
    ```obj-c
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```
2. Nu i **AppDelegate.m**, ta bort allt innehåll för metoden `application:didRegisterForPushNotificationWithDeviceToken:` och Ersätt den med följande för att se till att view controller innehåller den senaste enhetstoken som hämtas från APN:
   
    ```obj-c
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
    ```
3. Slutligen i **AppDelegate.m**, kontrollera att du har följande metod:

    ```obj-c   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }
    ```

## <a name="test-the-application"></a>Testa programmet
1. Kör appen i XCode på en fysisk iOS-enhet (push-meddelanden inte fungerar i simulatorn).
2. Ange samma värde för både användarnamn och lösenord i iOS-app Användargränssnittet. Klicka på **loggar In**.
   
    ![][2]
3. Du bör se ett popup-fönster som informerar dig om registreringen lyckades. Klicka på **OK**.
   
    ![][3]
4. I den **mottagaren användarnamn taggen* text, ange namnet användartaggen används med registrering från en annan enhet.
5. Ange ett meddelande och klicka på **skicka meddelande**. Endast de enheter som har en registrering med taggen mottagande användaren namnet ta emot meddelandet. Endast skickas till användare.
   
    ![][4]

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig mer om push-meddelanden till specifika användare som har taggar associerade med sina registreringar. Information om hur du skickar platsbaserade meddelanden finns i nästa självstudie: 

> [!div class="nextstepaction"]
>[Platsbaserade push-meddelanden](notification-hubs-push-bing-spartial-data-geofencing-notification.md)


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
