---
title: "Registrera den aktuella användaren för push-meddelanden med hjälp av Web API | Microsoft Docs"
description: "Lär dig mer om att begära registreringen av push-meddelanden i en iOS-app med Azure Notification Hubs vid registrering utförs av ASP.NET Web API."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: fd56bb2dd627b31f00363851a4e76484aa382988
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrera den aktuella användaren för push-meddelanden med hjälp av ASP.NET
> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
> 
> 

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du begär registreringen av push-meddelanden med Azure Notification Hubs när registreringen utförs av ASP.NET Web API. Det här avsnittet utökar kursen [meddela användare med Notification Hubs]. Du måste redan har slutfört nödvändiga steg i att kursen hjälper dig att skapa autentiserade mobiltjänsten. Läs mer på meddela användare scenario [meddela användare med Notification Hubs].

## <a name="update-your-app"></a>Uppdatera ditt program
1. Lägg till följande komponenter från objektbiblioteket i din MainStoryboard_iPhone.storyboard:
   
   * **Etiketten**: ”Push till användare med Notification Hubs”
   * **Etiketten**: ”InstallationId”
   * **Etiketten**: ”användare”
   * **Textfält**: ”användare”
   * **Etiketten**: ”Password”
   * **Textfält**: ”Password”
   * **Knappen**: ”inloggning”
     
     Nu storyboard ser ut ungefär så här:
     
      ![][0]
2. I Redigeraren för Installationsassistenten skapa uttag av avstängda kontrollerna och anropa dem ansluta textfält med View-Controller (ombud) och skapa en **åtgärd** för den **inloggning** knappen.
   
       ![][1]
   
       Your BreakingNewsViewController.h file should now contain the following code:
   
        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;
   
        - (IBAction)login:(id)sender;
3. Skapa en klass som heter **DeviceInfo**, och kopiera följande kod i avsnittet gränssnitt i filen DeviceInfo.h:
   
        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;
4. Kopiera följande kod i implementeringsavsnittet av filen DeviceInfo.m:
   
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
5. Lägg till följande egenskapen singleton PushToUserAppDelegate.h:
   
        @property (strong, nonatomic) DeviceInfo* deviceInfo;
6. I den **didFinishLaunchingWithOptions** metod i PushToUserAppDelegate.m, Lägg till följande kod:
   
        self.deviceInfo = [[DeviceInfo alloc] init];
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
   
    Den första raden initierar den **DeviceInfo** singleton. De andra raden startar registreringen av push-meddelanden som redan finns är du redan har slutfört den [Kom igång med Notification Hubs] kursen.
7. I PushToUserAppDelegate.m, implementerar du metoden **didRegisterForRemoteNotificationsWithDeviceToken** i din AppDelegate och Lägg till följande kod:
   
        self.deviceInfo.deviceToken = deviceToken;
   
    Detta anger enhetens token för begäran.
   
   > [!NOTE]
   > Nu är får det inte vara någon annan kod i den här metoden. Om du redan har ett anrop till den **registerNativeWithDeviceToken** metod som har lagts till när du slutfört den [Kom igång med Notification Hubs](/manage/services/notification-hubs/get-started-notification-hubs-ios/) självstudier, måste du kommenterar ut eller ta bort detta anrop.
   > 
   > 
8. Lägg till följande hanterarmetoden i filen PushToUserAppDelegate.m:
   
   * (void) programmet:(UIApplication *) program didReceiveRemoteNotification:(NSDictionary *) användarinformationen {NSLog (@ ”% @”, användarinformationen);   UIAlertView * avisering = [[UIAlertView allokeringsenhets] initWithTitle:@"Notification” meddelande: [användarinformationen objectForKey:@"inAppMessage”] ombud: nil cancelButtonTitle: @ ”OK” otherButtonTitles:nil, nil];   [varning visa]; }
   
   Den här metoden visas ett varningsmeddelande i Användargränssnittet när appen tar emot meddelanden när den körs.
9. Öppna filen PushToUserViewController.m och returnera tangentbordet i följande implementering:
   
        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }
10. I den **viewDidLoad** metod i filen PushToUserViewController.m initiera installationId etiketten på följande sätt:
    
         DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
         Self.installationId.text = deviceInfo.installationId;
11. Lägg till följande egenskaper i gränssnittet i PushToUserViewController.m:
    
        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;
12. Lägg sedan till följande implementering:
    
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
13. Kopiera följande kod i den **inloggning** hanterarmetoden som skapats av XCode:
    
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
    
    Den här metoden hämtar ett installations-ID och ett kanal för push-meddelanden och skickar den, tillsammans med typ av enhet på den autentiserade Web API-metod som skapar en registrering i Notification Hubs. Webb-API har definierats i [meddela användare med Notification Hubs].

Nu när klientappen har uppdaterats, gå tillbaka till den [meddela användare med Notification Hubs] och uppdatera mobiltjänsten för att skicka meddelanden med Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[meddela användare med Notification Hubs]: /manage/services/notification-hubs/notify-users-aspnet

[Kom igång med Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios
