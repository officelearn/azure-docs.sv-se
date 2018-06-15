---
title: Azure Notification Hubs säker Push
description: Lär dig mer om att skicka säkra push-meddelanden till en iOS-app från Azure. Kodexempel som skrivits i Objective-C och C#.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: d3ba967a164a35af5bf66f7e74d5f95b5dc2a37f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776210"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs säker Push
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Översikt
Stöd för push-meddelanden i Microsoft Azure kan du få en lätt att använda flera plattformar, skalats ut push-infrastruktur, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila plattformar.

På grund av reglerande säkerhetsbegränsningar, ibland ett program kan också innehålla något i meddelandet inte kan överföras via de standard push-infrastrukturen. Den här självstudiekursen beskrivs hur du kan uppnå samma upplevelse genom att skicka känslig information via en säker och autentiserad anslutning mellan klientenheten och appens serverdel.

På en hög nivå är flödet:

1. På appens serverdel:
   * Lagrar säker nyttolast i backend-databas.
   * Skickar ID för det här meddelandet till enheten (ingen säker information skickas).
2. Appen på enheten när du tar emot meddelandet:
   * Enheten kontaktar serverdelen begär säker nyttolasten.
   * Appen kan du visa nyttolasten som ett meddelande på enheten.

Det är viktigt att Observera att i det föregående flödet (och i den här självstudiekursen) antar vi att enheten lagrar en autentiseringstoken i lokal lagring när en användare loggar in. Detta garanterar en integrerad upplevelse som enheten kan hämta den anmälan säker nyttolast med denna token. Om ditt program inte kan lagra autentiseringstoken på enheten, eller om dessa token kan ha gått, visas vid mottagning av meddelanden i appen enhet ett allmänt meddelande där användaren uppmanas att starta appen. Appen sedan autentiserar användaren och visar nyttolasten för meddelanden.

Den här säkra Push-kursen visar hur du skickar push-meddelanden på ett säkert sätt. Kursen bygger på den [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) självstudier, så du måste slutföra stegen i den här självstudiekursen först.

> [!NOTE]
> Den här kursen förutsätter att du har skapat och konfigurerat din meddelandehubb enligt beskrivningen i [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Ändra iOS-projektet
Nu när du har ändrat din appens serverdel att skicka bara den *ID* av ett meddelande som du behöver ändra din iOS-app för att hantera detta meddelande och ringa upp din serverdel för att hämta det säkra meddelandet som ska visas.

Vi har skriva logik för att hämta skyddat innehåll från appens serverdel för att åstadkomma detta.

1. I **AppDelegate.m**, kontrollera att app-register för tyst meddelanden så att den bearbetar meddelande-ID skickas från serverdelen. Lägg till den **UIRemoteNotificationTypeNewsstandContentAvailability** alternativ i didFinishLaunchingWithOptions:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. I din **AppDelegate.m** lägga till en implementeringsavsnittet överst med följande deklaration:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. Lägg sedan till i implementeringsavsnittet följande kod, ersätter platshållaren `{back-end endpoint}` med slutpunkten för din serverdel hämtas tidigare:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. Nu har vi att hantera inkommande meddelande och använda metoden ovan för att hämta innehåll ska visas. Vi måste först aktivera din iOS-app att köras i bakgrunden när du får ett push-meddelande. I **XCode**, Välj app-projekt i den vänstra rutan och klicka på mål-huvudsakliga app i den **mål** avsnittet från fönstret i mitten.
2. Klicka på din **funktioner** längst upp i din centrala rutan och kontrollera den **Remote Notifications** kryssrutan.
   
    ![][IOS1]
3. I **AppDelegate.m** lägger du till följande metod för att hantera push-meddelanden:
   
        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);
   
            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];
   
        }
   
    Observera att det är bättre att hantera de egenskap som saknas autentisering sidhuvud eller underkännande av serverdelen. Särskild hantering av dessa fall beror huvudsakligen på användarupplevelsen mål. Ett alternativ är att visa ett meddelande med en allmän fråga att autentisera användaren att hämta anmälan.

## <a name="run-the-application"></a>Köra programmet
Om du vill köra programmet, gör du följande:

1. Kör appen i XCode på en fysisk iOS-enhet (push-meddelanden inte fungerar i simulatorn).
2. Ange ett användarnamn och lösenord i iOS-app Användargränssnittet. Det kan vara valfri sträng, men de måste ha samma värde.
3. Klicka på iOS-app UI **logga in**. Klicka på **skicka push**. Du bör se säkra meddelandet som visas i din meddelandecentret.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
