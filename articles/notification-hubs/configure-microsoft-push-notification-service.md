---
title: Konfigurera Microsoft Push Notification Service i Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du konfigurerar Microsoft Push Notification Service-inställningar för en Azure-meddelandehubb.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127328"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Konfigurera MPNS-inställningar (Microsoft Push Notification Service) i Azure-portalen

Den här artikeln visar hur du konfigurerar MPNS-inställningar (Microsoft Push Notification Service) för en Azure-meddelandehubb med hjälp av Azure-portalen. 

## <a name="prerequisites"></a>Krav
Om du inte redan har skapat en meddelandehubb skapar du en nu. Mer information finns [i Skapa en Azure-meddelandehubb i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurera Microsoft Push Notification Service (MPNS)

Följande procedur ger dig anvisningar för att konfigurera MPNS-inställningar (Microsoft Push Notification Service) för en meddelandehubb: 

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Windows Phone (MPNS)** på den vänstra menyn.
1. Aktivera antingen oautentiserade eller autentiserade push-meddelanden:

   a. Om du vill aktivera oautentiserade push-meddelanden väljer du **Aktivera oautentiserade push** > **Spara**.

      ![Skärmbild som visar hur du aktiverar oautentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Så här aktiverar du autentiserade push-meddelanden:
      * Välj **Ladda upp certifikat i**verktygsfältet .
      * Markera filikonen och välj sedan certifikatfilen.
      * Ange lösenordet för certifikatet.
      * Välj **OK**.
      * Välj **Spara**på sidan **Windows Phone (MPNS).**

## <a name="next-steps"></a>Nästa steg
En självstudiekurs med steg-för-steg-instruktioner för att skicka meddelanden till Windows Phone-enheter med hjälp av Azure Notification Hubs och Microsoft Push Notification Service (MPNS) finns i [Push-meddelanden till Windows Phone-appar med hjälp av Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

