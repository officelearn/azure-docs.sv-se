---
title: Konfigurera Google Firebase Cloud Messaging i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar en Azure Notification Hub med Google Firebase Cloud Messaging-inställningar.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 803289476d4d8869f7828b2843a983ee0c86bfbd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212496"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera inställningar för Google Firebase Cloud Messaging (FCM) för en Notification Hub i Azure Portal
Den här artikeln visar hur du konfigurerar inställningar för Google Firebase Cloud Messaging (FCM) för en Azure Notification Hub med hjälp av Azure Portal.  

## <a name="prerequisites"></a>Förutsättningar
Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurera Google Firebase Cloud Messaging (FCM)

Följande procedur visar hur du konfigurerar inställningar för Google Firebase Cloud Messaging (FCM) för en Notification Hub: 

1. I Azure Portal på sidan **Notification Hub** väljer du **Google (GCM/FCM)** på den vänstra menyn. 
2. Klistra in **API-nyckeln** för det FCM-projekt som du sparade tidigare. 
3. Välj **Spara**. 

   ![Skärm bild som visar hur du konfigurerar Notification Hubs för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Nästa steg
En själv studie kurs med stegvisa instruktioner för att skicka meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging finns i [push-meddelanden till Android-enheter med Notification Hubs och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

