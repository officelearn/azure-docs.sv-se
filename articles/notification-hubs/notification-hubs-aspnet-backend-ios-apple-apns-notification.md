---
title: "Meddela användare för iOS med .NET-serverdel i Azure Notification Hubs"
description: "Lär dig hur du skickar push-meddelanden till användare i Azure. Kodexempel som skrivits i Objective-C och .NET-API: et för serverdelen."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 0fa7a886e1ecb0a90b6aebc1dbf9ef0c6ce1acf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-notify-users-for-ios-with-net-backend"></a>Meddela användare för iOS med .NET-serverdel i Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Översikt
Stöd för push-meddelanden i Azure kan du få ett enkelt att använda och multiplatform skalats ut push-infrastruktur, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila plattformar. Denna självstudie visar hur du använder Azure-meddelandehubbar för att skicka push-meddelanden till en specifik app-användare på en viss enhet. En ASP.NET-WebAPI-serverdel används för att autentisera klienter och att generera meddelanden, som visas i avsnittet vägledning [registrering från din Apps serverdel](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

> [!NOTE]
> Den här kursen förutsätter att du har skapat och konfigurerat din meddelandehubb enligt beskrivningen i [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Den här kursen är också nödvändigt för att den [Secure Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) kursen.
> Om du vill använda Mobile Apps som serverdelstjänsten kan se den [Mobile Apps Kom igång med Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Ändra din iOS-app
1. Öppna en sida visa app som du skapade i den [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kursen.
   
   > [!NOTE]
   > I det här avsnittet förutsätter att projektet har konfigurerats med ett tomt organisationsnamn. Om inte, måste du har lagt till ditt organisationsnamn till alla klassnamn.
   > 
   > 
2. Lägg till de komponenter som visas i skärmbilden nedan från objektbiblioteket i din Main.storyboard.
   
    ![][1]
   
   * **Användarnamnet**: A UITextField med platshållartext, *ange användarnamn*omedelbart under Skicka resultaten etikett och begränsad till höger och vänster marginaler och under Skicka resultaten etikett.
   * **Lösenordet**: A UITextField med platshållartext, *ange lösenord för*, omedelbart under användarnamnet text fältet och begränsad till höger och vänster marginaler och under textfältet användarnamn. Kontrollera den **Secure textinmatning** alternativet i attributet-Inspector under *returnera nyckeln*.
   * **Logga in**: A UIButton etikett direkt under lösenordsfältet och avmarkerar de **aktiverad** alternativet i attribut-Inspector under *kontrollens innehåll*
   * **WNS**: etikett och växel för att skicka meddelande Windows Notification Service om den har konfigurerats på hubben. Finns det [Windows komma igång](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kursen.
   * **GCM**: etikett och växel för att skicka aviseringen till Google Cloud Messaging om den har konfigurerats på hubben. Se [Android igång](notification-hubs-android-push-notification-google-gcm-get-started.md) kursen.
   * **APN**: etikett och växel för att skicka aviseringen till Apple Platform Notification Service.
   * **Recipent Username**: A UITextField med platshållartext, *mottagaren användarnamn taggen*direkt under GCM etikett och begränsad till höger och vänster marginaler och under GCM-etiketten.

    Vissa komponenter har lagts till i den [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) kursen.

1. **CTRL** dra från komponenter i vyn till ViewController.h och Lägg till dessa nya uttag.
   
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
2. Lägg till följande i ViewController.h, `#define` under import-instruktioner. Ersätt den *< Endpoint ange Backend\>*  med mål-URL som du använde för att distribuera din Apps serverdel i föregående avsnitt. Till exempel *http://you_backend.azurewebsites.net*.
   
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
3. I projektet, skapa en ny **Cocoa Touch klassen** med namnet **RegisterClient** gränssnittet med ASP.NET serverdel du skapat. Skapa klassen som ärver från `NSObject`. Lägg sedan till följande kod i RegisterClient.h.
   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
4. I uppdateringen RegisterClient.m i `@interface` avsnitt:
   
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
5. Ersätt den `@implementation` avsnitt i RegisterClient.m med följande kod.

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

    Koden ovan implementerar logik som beskrivs i artikeln vägledning [registrering från din Apps serverdel](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) använda NSURLSession för REST-anrop till din Apps serverdel och NSUserDefaults lokalt lagra registrationId som returneras av meddelandehubben.

    Observera att den här klassen kräver egenskapen **authorizationHeader** ställs in för att fungera korrekt. Den här egenskapen anges av den **ViewController** klassen efter inloggningen.

1. ViewController.h, lägga till en `#import` -instruktion för RegisterClient.h. Sedan lägger du till en deklaration för enhetstoken och hänvisar till en `RegisterClient` instans i den `@interface` avsnitt:
   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
2. ViewController.m, lägga till en privat metoddeklaration i den `@interface` avsnitt:
   
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end

> [!NOTE]
> Följande kodavsnitt är inte en säker autentiseringsschema, ska du ersätta att genomföra den **createAndSetAuthenticationHeaderWithUsername:AndPassword:** med specifika autentiseringsmekanism som genererar en autentiseringstoken som ska konsumeras av registrera klient-klassen, t.ex. OAuth Active Directory.
> 
> 

1. I den `@implementation` delen av ViewController.m Lägg till följande kod som lägger till implementeringen för att ange rubriken enhet token och autentisering.
   
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
   
    Observera hur enhetstoken kan logga in knappen. Det beror på att som en del av åtgärden inloggningen view controller registrerar för push-meddelanden med appens serverdel. Därför kan vill vi inte logga In åtgärd som ska vara tillgängliga förrän enhetstoken har konfigurerats korrekt. Logga in från push-registrering kan frikopplar så länge som det tidigare sker innan de senare.
2. I ViewController.m, använder du följande kodavsnitt för att implementera åtgärdsmetod för din **loggar In** knapp och en metod för att skicka meddelandet med ASP.NET-serverdel.
   
       - (IBAction) LogInAction: (id) avsändaren {/ / skapa autentiseringshuvud och ange den registrera klienten NSString * användarnamn = self. UsernameField.text;   NSString * lösenord = self. PasswordField.text;
   
           [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController * selfie = self;   [self.registerClient registerWithDeviceToken:self.deviceToken taggar: nil andCompletion:^(NSError* error) {Om (! fel) {dispatch_async(dispatch_get_main_queue(), ^ {selfie. SendNotificationButton.enabled = Ja.               [self MessageBox:@"Success" message:@"Registered har”!];});}}];}

        -(void) SendNotificationASPNETBackend: (NSString*) pns UsernameTag: (NSString*) usernameTag meddelande: (NSString*) meddelande {NSURLSession* session = [NSURLSession sessionWithConfiguration: [NSURLSessionConfiguration defaultSessionConfiguration] ombud: nil delegateQueue:nil];

            Skicka taggen pns och användarnamnet som parametrar med REST-URL: en till ASP.NET-serverdel NSURL * requestURL = [NSURL URLWithString: [NSString stringWithFormat:@"%@/api/notifications? pns = % @& to_tag = % @”, BACKEND_ENDPOINT pns, usernameTag]];

            NSMutableURLRequest * begäran = [NSMutableURLRequest requestWithURL:requestURL];    [begära setHTTPMethod:@"POST"];

            Hämta fingerad authenticationheader från registrera klienten NSString * authorizationHeaderValue = [NSString stringWithFormat:@"Basic % @”, self.registerClient.authenticationHeader];    [begära setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            Lägg till avisering brödtext [begära setValue:@"application/json;charset=utf-8” forHTTPHeaderField:@"Content-Type"];    [begära setHTTPBody: [meddelande dataUsingEncoding:NSUTF8StringEncoding]];

            Köra skicka meddelandet REST API för ASP.NET-serverdel NSURLSessionDataTask * dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) svar;        Om (fel || httpResponse.statusCode! = 200) {NSString* status = [NSString stringWithFormat:@"Error Status för % @: % d\nError: %@\n”, pns, httpResponse.statusCode, fel];            dispatch_async(dispatch_get_main_queue(), ^ {/ / lägga till text eftersom alla 3 PNS-anrop kan också ha information för att visa [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];            });            NSLog(status);        }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];    [dataTask återuppta]; }


1. Uppdatera åtgärden för den **skicka meddelande** knappen för att använda ASP.NET-serverdel och skicka till alla PNS aktiveras med en växel.

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



1. I funktionen **ViewDidLoad**, Lägg till följande för att skapa en instans av RegisterClient instansen och ange ombud för textfält.
   
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
2. Nu i **AppDelegate.m**, ta bort allt innehåll för metoden **program: didRegisterForPushNotificationWithDeviceToken:** och Ersätt den med följande för att se till att view controller innehåller den senaste enhetstoken som hämtas från APN:
   
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
3. Slutligen i **AppDelegate.m**, kontrollera att du har följande metod:
   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }

## <a name="test-the-application"></a>Testa programmet
1. Kör appen i XCode på en fysisk iOS-enhet (push-meddelanden inte fungerar i simulatorn).
2. Ange ett användarnamn och lösenord i iOS-app Användargränssnittet. Det kan vara valfri sträng, men de måste båda vara samma strängvärde. Klicka på **loggar In**.
   
    ![][2]
3. Du bör se ett popup-fönster som informerar dig om registreringen lyckades. Klicka på **OK**.
   
    ![][3]
4. I den **mottagaren användarnamn taggen* text, ange namnet användartaggen används med registrering från en annan enhet.
5. Ange ett meddelande och klicka på **skicka meddelande**.  Endast de enheter som har en registrering med taggen mottagande användaren namnet ta emot meddelandet.  Endast skickas till användare.
   
    ![][4]

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
