---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060520"
---
### <a name="configure-required-ios-packages"></a>Konfigurera nödvändiga iOS-paket

Paketet [länkas automatiskt](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) när appen skapas. Allt du behöver göra är att installera den inbyggda poddar:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Konfigurera info. plist och rättigheter. plist

1. Gå till mappen "PushDemo/iOS" och öppna arbets ytan "PushDemo. xcworkspace", Välj det översta projektet "PushDemo" och välj fliken "signerings & funktioner".

1. Uppdatera paket identifieraren så att den matchar det värde som används i etablerings profilen.

1. Lägg till två nya funktioner med hjälp av knappen-"+":

    - Bakgrunds läge funktion och skalbaserade fjärraviseringar.
    - Funktioner för push-meddelanden

### <a name="handle-push-notifications-for-ios"></a>Hantera push-meddelanden för iOS

1. Öppna "AppDelegate. h" och Lägg till följande import:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Uppdatera listan över protokoll som stöds av "AppDelegate" genom att lägga till `UNUserNotificationCenterDelegate` :

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Öppna "AppDelegate. m" och konfigurera alla obligatoriska iOS-motanrop:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
