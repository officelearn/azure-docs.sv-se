---
title: Push-meddelanden till särskilda användare som använder Azure Notification Hubs | Microsoft Docs
description: Lär dig att använda Azure Notification Hubs till att skicka push-meddelanden till specifika användare.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4ecac47de08b458eac375f8f5e774c396aeb2f5d
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448116"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Självstudier: Push-meddelanden till särskilda användare som använder Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Denna självstudie visar hur du använder Azure Notification Hubs till att skicka push-meddelanden till en specifik appanvändare på en specifik enhet. En ASP.NET-WebAPI-serverdelen används att autentisera klienter och generera meddelanden, som visas i avsnittet om vägledning [registrering från appens serverdel](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

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

Den här självstudien förutsätter att du har skapat och konfigurerat din meddelandehubb, enligt beskrivningen i [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Den här självstudien är också nödvändiga till den [säker Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) självstudien.
Om du vill använda Mobile Apps som serverdelstjänsten kan se den [Mobile Apps Kom igång med Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Ändra din iOS-app

1. Öppna vyn ensidesappen du skapade i den [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) självstudien.

   > [!NOTE]
   > Det här avsnittet förutsätter att projektet har konfigurerats med en tom organisationsnamn. Om inte, du behöver lägga till åtkomstgruppen för ditt organisationsnamn till alla klassnamn.

2. I den `Main.storyboard` Lägg till de komponenter som visas i skärmbilden från objektbiblioteket.

    ![Redigera storybard i Xcode-gränssnittet builder][1]

   * **Användarnamn**: En UITextField med platshållartext, *ange användarnamn*, omedelbart under Skicka resultaten etikettera och begränsad till höger och vänster marginaler och under etiketten skicka resultaten.
   * **Lösenord**: En UITextField med platshållartext, *ange lösenord*, omedelbart under användarnamnet text fältet och begränsad till höger och vänster marginaler och under textfältet användarnamn. Kontrollera den **skydda textinmatning** alternativet i attributet-Inspector under *returnerar nyckeln*.
   * **Logga in**: En UIButton märkt direkt under lösenordsfältet och avmarkera de **aktiverad** alternativet i attribut-Inspector under *kontroll-innehåll*
   * **WNS**: Etikettera och växla om du vill aktivera avsieringen Windows Notification Service om den har ställts in på hubben. Se den [Windows har börjat](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) självstudien.
   * **GCM**: Etikettera och växla om du vill aktivera skicka aviseringen till Google Cloud Messaging om den har ställts in på hubben. Se [Android har börjat](notification-hubs-android-push-notification-google-gcm-get-started.md) självstudien.
   * **APNS**: Etikett och växel att skicka aviseringen till Apple Platform Notification Service.
   * **Mottagarens Username:A** UITextField med platshållartext, *mottagaren username-taggen*, direkt under GCM etikettera och begränsad till höger och vänster marginaler och under GCM-etikett.

    Vissa komponenter har lagts till i den [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) självstudien.

3. **CTRL** dra från komponenterna i vyn `ViewController.h` och Lägg till de här nya uttag.

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

4. I `ViewController.h`, Lägg till följande `#define` efter din importuttryck. Ersätt den `<Enter Your Backend Endpoint>` med mål-URL som du använde för att distribuera appens serverdel i föregående avsnitt. Till exempel *http://your_backend.azurewebsites.net*.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. Skapa en ny Cocoa Touch klass med namnet i projektet `RegisterClient` gränssnittet med ASP.NET backend-server du skapade. Skapa klassen ärver från `NSObject`. Lägg sedan till följande kod i den `RegisterClient.h`.

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. I den `RegisterClient.m`, uppdatera den `@interface` avsnittet:

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

7. Ersätt den `@implementation` avsnittet i RegisterClient.m med följande kod:

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

    Den här koden implementerar logiken som beskrivs i artikeln vägledning [registrering från appens serverdel](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) använder NSURLSession för att utföra REST-anrop till appens serverdel och NSUserDefaults lokalt lagra registrationId som returneras av den meddelandehubb.

    Den här klassen kräver egenskapen `authorizationHeader` anges för att fungera korrekt. Den här egenskapen anges av den `ViewController` klass efter inloggningen.

8. I `ViewController.h`, lägga till en `#import` instruktionen för `RegisterClient.h`. Sedan lägger du till en deklaration för enhetstoken och referera till en `RegisterClient` instans i den `@interface` avsnittet:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. Lägg till en privat metod förklaring i ViewController.m, den `@interface` avsnittet:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Följande kodfragment är inte ett schema för säker autentisering, bör du ersätta implementeringen av den `createAndSetAuthenticationHeaderWithUsername:AndPassword:` med specifika autentiseringsmekanism som genererar en autentiseringstoken som ska konsumeras av klassen registrera klienten, t.ex. OAuth, Active Directory.

10. I den `@implementation` delen av `ViewController.m`, Lägg till följande kod, som lägger till implementeringen för att ställa in rubriken enhet token och autentisering.

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

    Observera hur inställningen enhetstoken kan logga in knappen. Det beror på att som en del av åtgärden logga in view controller registrerar för push-meddelanden med appserverdelen. Därför vill inte logga In åtgärder för att vara tillgängliga tills enhetstoken har konfigurerats korrekt. Inloggning från push-registreringen kan frikopplar så länge som det tidigare inträffar före det senare.

11. I ViewController.m, använder du följande kodfragment för att implementera åtgärdsmetod för din **logga In** knappen och en metod för att skicka meddelandet med hjälp av ASP.NET-serverdel.

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

12. Uppdatera åtgärden för den **skicka meddelande** knappen för att använda ASP.NET-serverdel och skicka till alla PNS aktiverad som en växel.

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

13. I den `ViewDidLoad` fungera, Lägg till följande för att skapa en instans av den `RegisterClient` instans och ange ombud för din textfält.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Nu i `AppDelegate.m`, ta bort allt innehåll för metoden `application:didRegisterForPushNotificationWithDeviceToken:` och Ersätt den med följande (för att se till att view controller innehåller den senaste enhetstoken som hämtas från APN):

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

15. Slutligen i `AppDelegate.m`, kontrollera att du har följande metod:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testa programmet

1. Kör appen på en fysisk iOS-enhet (push-meddelanden inte fungerar i simulatorn) i XCode.
2. Ange samma värde för både användarnamn och lösenord i iOS-appens användargränssnitt. Klicka sedan på **logga In**.

    ![iOS testa program][2]

3. Du bör se ett popup-fönster som talar om för en lyckad registrering. Klicka på **OK**.

    ![iOS testa visas meddelandet][3]

4. I den **mottagaren username-taggen* text anger användartagg för namn som används med registreringen från en annan enhet.
5. Ange ett meddelande och klicka på **skicka meddelande**. Endast de enheter som har en registrering med taggen mottagarens användaren namn får meddelandet. Det skickas endast till dessa användare.

    ![iOS testmeddelande taggade][4]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om push-meddelanden till specifika användare som har taggar associerade med sina registreringar. Information om hur du skickar platsbaserade meddelanden finns i nästa självstudie: 

> [!div class="nextstepaction"]
>[Platsbaserade push-meddelanden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
