---
title: Konfigurera Google Firebase Cloud Messaging i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar en Azure notification hub med Google Firebase Cloud Messaging-inställningar.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239185"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera inställningarna för Google Firebase Cloud Messaging (FCM) för en notification hub i Azure portal
Den här artikeln visar hur du konfigurerar inställningarna för Google Firebase Cloud Messaging (FCM) för en Azure notification hub med hjälp av Azure portal.  

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du inte redan har skapat en meddelandehubb, ska du skapa en nu. Mer information finns i [skapar en Azure notification hub i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurera Google Firebase Cloud Messaging (FCM)

Följande procedur ger dig steg för att konfigurera inställningarna för Google Firebase Cloud Messaging (FCM) för en notification hub: 

1. I Azure-portalen på den **Meddelandehubb** väljer **Google (GCM/FCM)** på den vänstra menyn. 
2. Klistra in den **API-nyckel** för FCM-projektet som du sparade tidigare. 
3. Välj **Spara**. 

   ![Skärmbild som visar hur du konfigurerar Meddelandehubbar för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Nästa steg
Se en självstudie med stegvisa instruktioner för push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging [Push-meddelanden till Android-enheter med hjälp av Meddelandehubbar och Google FCM ](notification-hubs-android-push-notification-google-fcm-get-started.md).

