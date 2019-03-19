---
title: Konfigurera pushmeddelanden med Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du ställer in Azure Notification Hubs i Azure-portalen med hjälp av plattformsinställningarna notification system (PNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 8d2b54e65c1409c01a4c38ca8fcdfeca5f031277
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113341"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Konfigurera push-meddelanden i en notification hub i Azure portal

Azure Meddelandehubbar ger en push-motor som är lätt att använda och som skalas ut. Du kan använda Meddelandehubbar för att skicka meddelanden till alla plattformar (iOS, Android, Windows, Kindle, Baidu) och från valfri serverdel (i molnet eller lokalt). Mer information finns i [vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabbstarten ska du använda plattformsinställningarna notification system (PNS) i Meddelandehubbar för att ställa in push-meddelanden på flera plattformar. Snabbstarten visar hur du fortsätter i Azure-portalen.

Om du inte redan har skapat en meddelandehubb, ska du skapa en nu. Mer information finns i [skapar en Azure notification hub i Azure-portalen](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Så här ställer upp Apple Push Notification Service (APNS):

1. I Azure-portalen i den **Meddelandehubb**väljer **Apple (APNS)**.

1. För **autentiseringsläge**, väljer du antingen **certifikat** eller **Token**.

   a. Om du väljer **certifikat**:
   * Välj filikonen och välj sedan den *.p12* fil du vill överföra.
   * Ange ett lösenord.
   * Välj **Sandbox**-läge. Eller om du vill skicka push-meddelanden till användare som har köpt din app från butiken, Välj **produktion** läge.

     ![Skärmbild av ett APN-certifikatet har konfigurerats i Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Om du väljer **Token**:

   * Ange värden för **nyckel-Id**, **paket-Id**, **lag-Id**, och **Token**.
   * Välj **Sandbox**-läge. Eller om du vill skicka push-meddelanden till användare som har köpt din app från butiken, Välj **produktion** läge.

     ![Skärmbild av en APN token konfiguration i Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Mer information finns i [Push-meddelanden till iOS med Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase Cloud Messaging

Konfigurera push-meddelanden för Google Firebase Cloud Messaging (FCM):

1. I Azure-portalen i den **Meddelandehubb**väljer **Google (GCM/FCM)**. 
2. Klistra in den **API-nyckel** för FCM-projektet som du sparade tidigare. 
3. Välj **Spara**. 

   ![Skärmbild som visar hur du konfigurerar Meddelandehubbar för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

När du har slutfört de här stegen kan en varning som anger att meddelandehubben har uppdaterats. Knappen **Spara** är inaktiverad. 

Mer information finns i [Push-meddelanden till Android-enheter med hjälp av Meddelandehubbar och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

Så här ställer in Windows Push Notification Service (WNS):

1. I Azure-portalen i den **Meddelandehubb**väljer **Windows (WNS)**.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärmbild som visar rutorna paket-SID och Säkerhetsnyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Mer information finns i [skicka meddelanden till UWP-appar med Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service för Windows Phone

Så här upp Microsoft Push Notification Service (MPNS) för Windows Phone: 

1. I Azure-portalen i den **Meddelandehubb**väljer **Windows Phone (MPNS)**.
1. Aktivera oautentiserad eller autentiserade push-meddelanden:

   a. Om du vill aktivera ej autentiserade push-meddelanden, Välj **aktivera ej autentiserade push** > **spara**.

      ![Skärmbild som visar hur du aktiverar ej autentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. För att aktivera autentiserade push-meddelanden:
      * I verktygsfältet väljer **överför certifikat**.
      * Välj filikonen och välj sedan certifikatfilen.
      * Ange lösenordet för certifikatet.
      * Välj **OK**.
      * På den **Windows Phone (MPNS)** väljer **spara**.

Mer information finns i [Push-meddelanden till Windows Phone-appar med hjälp av Meddelandehubbar](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging"></a>Amazon Device Messaging

Konfigurera push-meddelanden för Amazon Device Messaging (ADM):

1. I Azure-portalen i den **Meddelandehubb**väljer **Amazon (ADM)**.
2. Ange värden för **klient-ID** och **Klienthemlighet**.
3. Välj **Spara**.
    
   ![Skärmbild av ADM-inställningar i Azure portal](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Mer information finns i [Kom igång med Notification Hubs för Kindle-appar](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Konfigurera push-meddelanden för Baidu:

1. I Azure-portalen i den **Meddelandehubb**väljer **Baidu (Android China)**. 
2. Ange den **Api-nyckel** som du fick från Baidu-konsolen i Baidu cloud push-projekt. 
3. Ange den **hemlig nyckel** som du fick från Baidu-konsolen i Baidu cloud push-projekt. 
4. Välj **Spara**. 

    ![Skärmbild av Notification Hubs som visar Baidu (Android China)-konfigurationen för push-meddelanden](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

När du har slutfört de här stegen kan en varning som anger att meddelandehubben har uppdaterats. Knappen **Spara** är inaktiverad. 

Mer information finns i [Kom igång med Notification Hubs med hjälp av Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten lärde du dig att konfigurera platform notification-systeminställningar för en meddelandehubb i Azure-portalen. 

Läs mer om hur du push-meddelanden till olika plattformar i de här självstudierna:

- [Push-meddelanden till iOS-enheter med hjälp av Meddelandehubbar och APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Push-meddelanden till Android-enheter med hjälp av Meddelandehubbar och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Push-meddelanden till en UWP-app som körs på en Windows-enhet](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Push-meddelanden till en Windows Phone 8-app med hjälp av MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Push-meddelanden till en Kindle-App](notification-hubs-kindle-amazon-adm-push-notification.md)
- [Push-meddelanden med hjälp av Meddelandehubbar och Baidu cloud push](notification-hubs-baidu-china-android-notifications-get-started.md)
