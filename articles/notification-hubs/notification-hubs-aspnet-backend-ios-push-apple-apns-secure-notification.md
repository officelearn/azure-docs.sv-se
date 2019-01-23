---
title: Azure Notification Hubs Secure Push
description: Lär dig hur du skickar säker push-meddelanden till en iOS-app från Azure. Kodexempel som skrivits i Objective-C och C#.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d88bdb1eaeb95413df84bf69ed4fc763b6d4901f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449275"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs Secure Push

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

Stöd för push-meddelande i Microsoft Azure kan du komma åt en enkel att använda och utskalad push-infrastruktur, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila enheter plattformar.

På grund av föreskrifter säkerhetsbegränsningar, ibland ett program kan också vilja inkludera något i meddelandet som inte kan tillhandahållas via standard push-meddelandeinfrastruktur. Den här självstudien beskrivs hur du uppnår samma upplevelse genom att skicka känslig information via en säker och autentiserad anslutning mellan klientenheten och appserverdelen.

På en hög nivå är flödet på följande sätt:

1. App backend-server:
   * Butiker säker nyttolast i backend-databasen.
   * Skickar ID för det här meddelandet till enheten (ingen säker information skickas).
2. Appen på enheten när du tar emot meddelandet:
   * Enheten kontaktar serverdelen begär säker nyttolasten.
   * Appen kan visa nyttolasten som ett meddelande på enheten.

Det är viktigt att Observera att i föregående flödet (och i den här självstudien), förutsätter vi att enheten lagrar en autentiseringstoken i lokal lagring, när användaren loggar in. Detta garanterar en sömlös upplevelse som enheten kan hämta den meddelandets säker nyttolast använder denna token. Om ditt program lagrar inte autentiseringstoken på enheten eller om dessa token kan ha upphört att gälla, enhetsapp när tas emot meddelandet ska visa ett allmänt meddelande där användaren uppmanas att starta appen. Appen sedan autentiserar användaren och visar meddelandets nyttolast.

Den här säker Push-självstudien visar hur du skickar push-meddelanden på ett säkert sätt. Självstudiekursen bygger vidare på den [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) kursen, så du måste slutföra stegen i självstudien först.

> [!NOTE]
> Den här självstudien förutsätter att du har skapat och konfigurerat din meddelandehubb, enligt beskrivningen i [komma igång med Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Ändra på iOS-projektet

Nu när du har ändrat din appens serverdel att skicka bara *ID* av ett meddelande, du måste ändra din iOS-app för att hantera som meddelanden och Ring tillbaka din serverdel för att hämta det säkra meddelandet som ska visas.

För att åstadkomma detta måste vi skriva logik för att hämta skyddat innehåll från appens serverdel.

1. I `AppDelegate.m`, kontrollera att app-register för tyst meddelanden så att den bearbetar meddelande-ID skickas från serverdelen. Lägg till den `UIRemoteNotificationTypeNewsstandContentAvailability` alternativ i didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. I din `AppDelegate.m` lägga till en implementeringsavsnittet överst med följande försäkran:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Lägg sedan till i implementeringsavsnittet följande kod, och Ersätt platshållaren `{back-end endpoint}` med slutpunkt för din serverdel som hämtats tidigare:

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

    Den här metoden anropar din appens serverdel för att hämta meddelandeinnehållet med de autentiseringsuppgifter som lagras i delade inställningar.

4. Nu har vi och hanteringen av inkommande meddelanden som du kan använda metoden ovan för att hämta innehåll ska visas. Först måste vi aktivera din iOS-app ska köras i bakgrunden när du tar emot ett push-meddelande. I **XCode**, app-projekt i den vänstra rutan och sedan klicka på dina viktigaste app-mål i den **mål** avsnitt från fönstret i mitten.
5. Klicka sedan på din **funktioner** fliken högst upp på din centrala fönstret och kontrollera den **Remote Notifications** kryssrutan.

    ![][IOS1]

6. I `AppDelegate.m` lägger du till följande metod för att hantera push-meddelanden:

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

    Observera att det är bättre att hantera fall av saknas huvud-autentiseringsegenskapen eller nekande av backend-server. Särskild hantering av dessa fall beror huvudsakligen på din användarupplevelse för målet. Ett alternativ är att visa ett meddelande med en allmän uppmaning att autentisera användaren att hämta anmälan.

## <a name="run-the-application"></a>Kör programmet

Om du vill köra programmet måste du göra följande:

1. Kör appen på en fysisk iOS-enhet (push-meddelanden inte fungerar i simulatorn) i XCode.
2. Ange ett användarnamn och lösenord i iOS-appens användargränssnitt. Det kan vara valfri sträng, men de måste vara samma värde.
3. I iOS-appens användargränssnitt klickar du på **logga in**. Klicka sedan på **skicka push**. Du bör se säkra meddelandet som visas i meddelandecentret.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
