---
title: Konfigurera Azure-meddelandehubb med PNS-inställningar | Microsoft Docs
description: I den här snabbstarten lär du dig att konfigurera en meddelandehubb i Azure-portalen med inställningar för plattformsspecifikt meddelandesystem (PNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314029"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>Konfigurera en Azure-meddelandehubb med inställningar för plattformsspecifikt meddelandesystem i Azure-portalen 
Azure Notification Hubs innehåller en lättanvänd och uppskalad push-motor som gör det möjligt för dig att skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle, Baidu osv) från valfri serverdel (molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

[Skapa en Azure-meddelandehubb med hjälp av Azure-portalen](create-notification-hub-portal.md) om du inte gjort det redan. I den här snabbstarten lär du dig att konfigurera en meddelandehubb i Azure-portalen med inställningar för plattformsspecifikt meddelandesystem (PNS).

## <a name="apple-push-notification-service-apns"></a>Apple Push Notification Service (APNS)
1. På sidan **Notification Hub** (Meddelandehubb) i Azure-portalen väljer du **Apple (APNS)** under **Inställningar** på den vänstra menyn.
2. Om du väljer **Certifikat** utför du följande åtgärder:
    1. Välj **filikonen** och välj den **.p12**-fil som ska laddas upp. 
    2. Ange **lösenordet**.
    3. Välj **Sandbox**-läge. Använd bara **Produktion** om du vill skicka push-meddelanden till användare som har köpt din app i butiken.

        ![Konfigurera APNS-certifikat i Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. Om du väljer **Token** gör du följande: 
    1. Ange värdena för **key ID** (nyckel-ID), **bundle ID** (bunt-ID), **team ID** (team-ID) och **token**.
    2. Välj **Sandbox**-läge. Använd bara **Produktion** om du vill skicka push-meddelanden till användare som har köpt din app i butiken.

        ![Konfigurera APNS-token i Azure-portalen](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

En fullständig självstudie om att skicka push-meddelanden till iOS-enheter med hjälp av Azure Notification Hubs och Apple Push Notification Service (APNS) finns i [den här självstudien](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)
1. På sidan **Notification Hub** (Meddelandehubb) i Azure-portalen väljer du **Google (GCM/FCM)** under **Inställningar** på den vänstra menyn. 
2. Klistra in **servernyckeln** för FCM-projektet som du sparade tidigare. 
3. Välj **Spara** i verktygsfältet. 

    ![Azure Notification Hubs - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Du ser ett meddelande i aviseringar att meddelandehubbar har uppdaterats. Knappen **Spara** är inaktiverad. 

En fullständig självstudie om att skicka push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging finns i [den här självstudien](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service-wns"></a>Windows Push Notification Service (WNS)
1. På sidan **Notification Hub** (Meddelandehubb) i Azure-portalen väljer du **Windows (WNS)** under **Inställningar** på den vänstra menyn.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara** i verktygsfältet.

    ![Rutorna Paket-SID och Säkerhetsnyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


En fullständig självstudie om att skicka push-meddelanden till en UWP-app (Universell Windows-plattform) som körs på en Windows-enhet finns i [den här självstudien](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone – Microsoft Push Notification Service
1. På sidan **Notification Hub** (Meddelandehubb) i Azure-portalen väljer du **Windows Phone (MPNS)** under **Inställningar**.
2. Om du vill aktivera ej autentiserade push-meddelanden väljer du **Aktivera ej autentiserade push-meddelanden** och därefter **Spara** i verktygsfältet.

    ![Azure Portal – Aktivera ej autentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. Om du vill använda **autentiserade** push-meddelanden följer du dessa steg:
    1. Välj **Ladda upp certifikat** i verktygsfältet.
    2. Välj **filikonen** och välj certifikatfilen.
    3. Ange **lösenordet** för certifikatet. 
    4. Välj **OK** för att stäng sidan **Ladda upp certifikat**. 
    5. På sidan **Windows Phone (MPNS)** väljer du **Spara** i verktygsfältet.

En fullständig självstudie om att skicka push-meddelanden till en Windows Phone 8-app med hjälp av Microsoft Push Notification Service (MPNS) finns i [den här självstudien](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging-adm"></a>Amazon Device Messaging (ADM)
1. På sidan **Notification Hub** (Meddelandehubb) i Azure-portalen väljer du **Amazon (ADM)** under **Inställningar** på den vänstra menyn.
2. Ange värden för **Klient-ID** och **Klienthemlighet**.
3. Välj **Spara** i verktygsfältet.
    
    ![Azure Notification Hubs – ADM-inställningar](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

En fullständig självstudie om att skicka push-meddelanden med Azure Notification Hubs till ett Kindle-program finns i [den här självstudien](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)
1. På sidan **Notification Hub** (Meddelandehubb) i Azure-portalen väljer du **Baidu (Android China)** under **Inställningar** på den vänstra menyn. 
2. Ange den **API-nyckel** som du hämtade från Baidu-konsolen i Baidu Cloud Push-projektet. 
3. Ange den **hemliga nyckel** som du hämtade från Baidu-konsolen i Baidu Cloud Push-projektet. 
4. Välj **Spara** i verktygsfältet. 

    ![Azure Notification Hubs – Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. Du ser ett meddelande i aviseringar att meddelandehubbar har uppdaterats. Knappen **Spara** är inaktiverad. 

En fullständig självstudie om att skicka push-meddelanden med hjälp av Azure Notification Hubs och Baidu Cloud Push finns i [den här självstudien](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten lärde du dig att konfigurera olika plattformsmeddelandesystem för en meddelandehubb i Azure-portalen. 

Fullständiga stegvisa instruktioner för att skicka push-meddelanden de här olika plattformarna finns i självstudierna i avsnittet **Självstudier**.

- [Skicka push-meddelanden till iOS-enheter med hjälp av Azure Notification Hubs och Apple Push Notification Service (APNS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
- [Skicka push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).
- [Skicka push-meddelanden till en UWP-app (Universell Windows-plattform) som körs på en Windows-enhet](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
- [Skicka push-meddelanden till en Windows Phone 8-app med hjälp av Microsoft Push Notification Service (MPNS)](notification-hubs-windows-mobile-push-notifications-mpns.md).
- [Skicka push-meddelanden till ett Kindle-program](notification-hubs-kindle-amazon-adm-push-notification.md).
- [Skicka push-meddelanden med hjälp av Azure Notification Hubs och Baidu Cloud Push](notification-hubs-baidu-china-android-notifications-get-started.md).
