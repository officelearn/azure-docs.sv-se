---
title: Konfigurera Baidu Cloud Push i Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du konfigurerar Baidu-inställningar för en Azure-meddelandehubb.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212516"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Inaktuella: Konfigurera Baidu Cloud Push-inställningar för en meddelandehubb i Azure-portalen

Den här artikeln visar hur du konfigurerar Baidu Cloud Push-inställningar för en Azure-meddelandehubb med hjälp av Azure-portalen.

> [!IMPORTANT]
> Den här självstudien är föråldrad. 

## <a name="prerequisites"></a>Krav
Om du inte redan har skapat en meddelandehubb skapar du en nu. Mer information finns [i Skapa en Azure-meddelandehubb i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurera Baidu Cloud Push
Följande procedur ger dig steg för att konfigurera Baidu Cloud Push-inställningar för en meddelandehubb:

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Baidu (Android Kina)** på den vänstra menyn. 
2. Ange **Api-nyckeln** som du har fått från Baidu-konsolen i Baidu-molndush-projektet. 
3. Ange **den hemliga nyckeln** som du fick från Baidu-konsolen i Baidu-molnets push-projekt. 
4. Välj **Spara**. 

    ![Skärmbild av Meddelandehubbar som visar Baidu-konfigurationen (Android Kina) för push-meddelanden](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Nästa steg
En självstudiekurs med steg-för-steg-instruktioner för att skicka meddelanden till Baidu med hjälp av Azure Notification Hubs och Baidu Cloud Push finns [i Komma igång med Meddelandehubbar med Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
