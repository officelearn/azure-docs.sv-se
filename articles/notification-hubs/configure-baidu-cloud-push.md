---
title: Konfigurera Baidu Cloud push i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar Baidu-inställningar för en Azure Notification Hub.
services: notification-hubs
author: jwargo
manager: patniko
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9feb5f66c5ae979a239c3349b2b1347ad307ce49
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884646"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Föråldrad Konfigurera Baidu Cloud push-inställningar för en Notification Hub i Azure Portal

Den här artikeln visar hur du konfigurerar Baidu Cloud push-inställningar för en Azure Notification Hub med hjälp av Azure Portal.

> [!IMPORTANT]
> Den här självstudien är föråldrad. 

## <a name="prerequisites"></a>Förutsättningar
Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurera Baidu Cloud Push
Med följande procedur kan du konfigurera Baidu Cloud push-inställningar för en Notification Hub:

1. I Azure Portal på sidan **Notification Hub** väljer du **Baidu (Android China)** på den vänstra menyn. 
2. Ange den **API-nyckel** som du fick från Baidu-konsolen i Baidu Cloud push projektet. 
3. Ange den **hemliga nyckeln** som du fick från Baidu-konsolen i Baidu Cloud push projektet. 
4. Välj **Spara**. 

    ![Skärm bild av Notification Hubs som visar Baidu-konfigurationen (Android China) för push-meddelanden](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Nästa steg
En själv studie kurs med stegvisa instruktioner för att skicka meddelanden till Baidu med hjälp av Azure Notification Hubs och Baidu Cloud push finns i [Kom igång med Notification Hubs med hjälp av Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
