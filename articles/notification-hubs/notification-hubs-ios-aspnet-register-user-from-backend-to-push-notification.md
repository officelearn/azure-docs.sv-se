---
title: Registrera den aktuella användaren för push-meddelanden med hjälp av webb-API | Microsoft Docs
description: Lär dig mer om att begära registrering av push-meddelanden i en iOS-app med Azure Notification Hubs vid registreringen utförs av ASP.NET Web API.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: ff77a955c34941d87a1f653726ab3f19e84aa440
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101983"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrera den aktuella användaren för push-meddelanden med hjälp av ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Översikt

Det här avsnittet visar hur du begär push-meddelande registrering med Azure Notification Hubs när registreringen utförs av ASP.NET Web API. Det här ämnet utvidgar självstudien [meddela användare med Meddelandehubbar]. Du måste redan har slutfört de nödvändiga stegen i självstudien att skapa den autentiserade mobiltjänsten. Läs mer på meddela användare scenariot [meddela användare med Meddelandehubbar].

## <a name="update-your-app"></a>Uppdatera din app

1. Lägg till följande komponenter från objektbiblioteket i din MainStoryboard_iPhone.storyboard:

   * **Etiketten**: ”Push till användare med Meddelandehubbar”
   * **Etiketten**: "InstallationId"
   * **Etiketten**: ”Användare”
   * **Textfält**: ”Användare”
   * **Etiketten**: ”Lösenord”
   * **Textfält**: ”Lösenord”
   * **Button**: ”Inloggning”

     Din storyboard nu ser ut som följande:

     ![][0]

2. Skapa kanaler för alla växlade kontroller i assistant-redigeraren och anropa dem, ansluta textfält med View-Controller (ombud) och skapa en **åtgärd** för den **inloggning** knappen.

    ![][1]

    Filen BreakingNewsViewController.h ska nu innehålla följande kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Skapa en klass med namnet `DeviceInfo`, och kopiera följande kod till gränssnittet i DeviceInfo.h-fil:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Kopiera följande kod i implementeringsavsnittet av filen DeviceInfo.m:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. Lägg till följande egenskap singleton i PushToUserAppDelegate.h:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. I den `didFinishLaunchingWithOptions` -metod i PushToUserAppDelegate.m, Lägg till följande kod:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Den första raden initierar den `DeviceInfo` singleton. Den andra raden startar registreringen av push-meddelanden som redan finns om du redan har slutfört den [Kom igång med Notification Hubs] självstudien.
7. Implementera metoden i PushToUserAppDelegate.m, `didRegisterForRemoteNotificationsWithDeviceToken` i AppDelegate och Lägg till följande kod:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Detta anger enhetstoken för begäran.

   > [!NOTE]
   > Då får det inte vara all annan kod i den här metoden. Om du redan har ett anrop till den `registerNativeWithDeviceToken` metod som lades till när du har slutfört den [Kom igång med Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md) självstudier, måste du kommentera eller ta bort anropet.

8. I den `PushToUserAppDelegate.m` Lägg till följande metod som hanterare:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Den här metoden visar ett meddelande i Användargränssnittet när appen tar emot meddelanden när den körs.

9. Öppna den `PushToUserViewController.m` filen och returnerar tangentbordet i följande implementering:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. I den `viewDidLoad` -metod i den `PushToUserViewController.m` filen, initiera den `installationId` etiketten på följande sätt:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Lägg till följande egenskaper i gränssnittet i `PushToUserViewController.m`:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Lägg sedan till följande implementering:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Kopiera följande kod till den `login` hanteraren metod som skapats av XCode:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Den här metoden hämtar både ett installations-ID och kanal för push-meddelanden och skickar den, tillsammans med typ av enhet, till den autentiserade webb-API-metoden som skapar en registrering i Meddelandehubbar. Den här webb-API har definierats i [meddela användare med Meddelandehubbar].

Nu när klientappen har uppdaterats, gå tillbaka till den [meddela användare med Meddelandehubbar] och uppdatera den mobila tjänsten för att skicka meddelanden med hjälp av Meddelandehubbar.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Meddela användare med Meddelandehubbar]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Kom igång med Notification Hubs]: notification-hubs-ios-apple-push-notification-apns-get-started.md
