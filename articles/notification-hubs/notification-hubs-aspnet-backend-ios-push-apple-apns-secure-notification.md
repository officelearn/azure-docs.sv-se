---
title: Azure Notification Hubs Secure Push för iOS
description: Läs om hur du skickar säkra push-meddelanden till en iOS-app från Azure. Kodexempel skrivna i Objective-C och C#.
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 96d1dd514f6fb9c11d7194714337583d6b4387cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530756"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs Secure Push

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

Med push-meddelandestöd i Microsoft Azure kan du komma åt en lättanvänd push-infrastruktur med flera plattformar, vilket förenklar implementeringen av push-meddelanden för både konsument- och företagsprogram för mobila Plattformar.

På grund av regel- eller säkerhetsbegränsningar kan ibland ett program vilja inkludera något i meddelandet som inte kan överföras via standardinfrastrukturen för push-meddelanden. Den här självstudien beskriver hur du uppnår samma upplevelse genom att skicka känslig information via en säker, autentiserat anslutning mellan klientenheten och appens backend.

På en hög nivå är flödet följande:

1. Appens baksida:
   * Lagrar säker nyttolast i backend-databas.
   * Skickar ID:t för det här meddelandet till enheten (ingen säker information skickas).
2. Appen på enheten, när du tar emot meddelandet:
   * Enheten kontaktar backend-begäran om säker nyttolast.
   * Appen kan visa nyttolasten som ett meddelande på enheten.

Det är viktigt att notera att i föregående flöde (och i den här självstudien) antar vi att enheten lagrar en autentiseringstoken i lokal lagring, när användaren loggar in. Detta garanterar en sömlös upplevelse, eftersom enheten kan hämta meddelandets säkra nyttolast med den här token. Om ditt program inte lagrar autentiseringstoken på enheten, eller om dessa token kan komma att gälla, bör enhetsappen när du tar emot meddelandet visa ett allmänt meddelande som uppmanar användaren att starta appen. Appen autentiserar sedan användaren och visar aviseringsnyttolasten.

Den här självstudien för säker push visar hur du skickar ett push-meddelande på ett säkert sätt. Självstudien bygger på instruktionen [Meddela användare,](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) så du bör slutföra stegen i den självstudien först.

> [!NOTE]
> Den här självstudien förutsätter att du har skapat och konfigurerat meddelandehubben enligt beskrivningen i [Komma igång med Meddelandehubbar (iOS).](notification-hubs-ios-apple-push-notification-apns-get-started.md)

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Ändra iOS-projektet

Nu när du har ändrat appens serverdel för att bara skicka *ID:t* för ett meddelande måste du ändra din iOS-app för att hantera meddelandet och ringa tillbaka din serverdel för att hämta det säkra meddelandet som ska visas.

För att uppnå detta mål måste vi skriva logiken för att hämta det säkra innehållet från appens backend.

1. I `AppDelegate.m`kontrollerar du att appen registrerar för tysta meddelanden så att den bearbetar meddelande-ID som skickas från serverda. Lägg `UIRemoteNotificationTypeNewsstandContentAvailability` till alternativet i didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. I `AppDelegate.m` ditt tillägg till ett implementeringsavsnitt högst upp med följande deklaration:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Lägg sedan till följande kod i implementeringsavsnittet och ersätt platshållaren `{back-end endpoint}` med slutpunkten för den backend som tidigare erhållits:

    ```objc
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

    Den här metoden anropar appens backend för att hämta meddelandeinnehållet med hjälp av de autentiseringsuppgifter som lagras i de delade inställningarna.

4. Nu måste vi hantera inkommande anmälan och använda metoden ovan för att hämta innehållet för att visa. Först måste vi göra det möjligt för din iOS-app att köras i bakgrunden när du får ett push-meddelande. Välj appprojektet på den vänstra panelen i **XCode**och klicka sedan på huvudappmålet i avsnittet **Mål** i det centrala fönstret.
5. Klicka sedan på fliken **Funktioner** högst upp i det centrala fönstret och markera kryssrutan **Fjärrmeddelanden.**

    ![][IOS1]

6. I `AppDelegate.m` lägg till följande metod för att hantera push-meddelanden:

    ```objc
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
    ```

    Observera att det är bättre att hantera fall av saknade autentiseringshuvudegenskap eller avslag av backend. Den specifika hanteringen av dessa ärenden beror främst på din målanvändarupplevelse. Ett alternativ är att visa ett meddelande med en allmän uppmaning för användaren att autentisera för att hämta det faktiska meddelandet.

## <a name="run-the-application"></a>Kör programmet

Så här kör du programmet:

1. I XCode kör du appen på en fysisk iOS-enhet (push-meddelanden fungerar inte i simulatorn).
2. Ange ett användarnamn och lösenord i iOS-appgränssnittet. Dessa kan vara vilken sträng som helst, men de måste vara samma värde.
3. Klicka på **Logga in i**iOS-appgränssnittet . Klicka sedan på **Skicka push**. Du bör se det säkra meddelandet visas i ditt aviseringscenter.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
