---
title: Konfigurera Baidu Cloud Push i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar Baidu-inställningar för en Azure notification hub.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234273"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera Baidu Cloud Push-inställningarna för en notification hub i Azure-portalen
Den här artikeln visar hur du konfigurerar Baidu Cloud Push-inställningar för en Azure notification hub med hjälp av Azure portal. 

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du inte redan har skapat en meddelandehubb, ska du skapa en nu. Mer information finns i [skapar en Azure notification hub i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurera Baidu Cloud Push
Följande procedur ger dig steg för att konfigurera Baidu Cloud Push-inställningar för en notification hub:

1. I Azure-portalen på den **Meddelandehubb** väljer **Baidu (Android China)** på den vänstra menyn. 
2. Ange den **Api-nyckel** som du fick från Baidu-konsolen i Baidu cloud push-projekt. 
3. Ange den **hemlig nyckel** som du fick från Baidu-konsolen i Baidu cloud push-projekt. 
4. Välj **Spara**. 

    ![Skärmbild av Notification Hubs som visar Baidu (Android China)-konfigurationen för push-meddelanden](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Nästa steg
Se en självstudie med stegvisa instruktioner för push-meddelanden Baidu med hjälp av Azure Notification Hubs och Baidu Cloud Push [Kom igång med Notification Hubs med hjälp av Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
