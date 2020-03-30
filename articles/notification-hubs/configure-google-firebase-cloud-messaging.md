---
title: Konfigurera Google Firebase Cloud Messaging i Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du konfigurerar en Azure-meddelandehubb med Google Firebase Cloud Messaging-inställningar.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127477"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera Google Firebase-inställningar för en meddelandehubb i Azure-portalen

Den här artikeln visar hur du konfigurerar FCM-inställningar (Google Firebase Cloud Messaging) för en Azure-meddelandehubb med hjälp av Azure-portalen.  

## <a name="prerequisites"></a>Krav
Om du inte redan har skapat en meddelandehubb skapar du en nu. Mer information finns [i Skapa en Azure-meddelandehubb i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurera Google Firebase Cloud Messaging (FCM)

Följande procedur ger dig anvisningar för att konfigurera FCM-inställningar (Google Firebase Cloud Messaging) för en meddelandehubb: 

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Google (GCM/FCM)** på den vänstra menyn. 
2. Klistra in **API-nyckeln** för FCM-projektet som du sparade tidigare. 
3. Välj **Spara**. 

   ![Skärmbild som visar hur du konfigurerar meddelandehubbar för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Nästa steg
En självstudiekurs med steg-för-steg-instruktioner för att skicka meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging finns i [Push-meddelanden till Android-enheter med hjälp av Notification Hubs och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

