---
title: Konfigurera Microsoft Push Notification Service i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar Microsoft Push Notification Service-inställningar för en Azure notification hub.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488435"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera inställningar för Microsoft Push Notification Service (MPNS) för en notification hub i Azure portal
Den här artikeln visar hur du konfigurerar inställningar för Microsoft Push Notification Service (MPNS) för en Azure notification hub med hjälp av Azure portal. 

## <a name="prerequisites"></a>Förutsättningar
Om du inte redan har skapat en meddelandehubb, ska du skapa en nu. Mer information finns i [skapar en Azure notification hub i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurera Microsoft Push Notification Service (MPNS)

Följande procedur ger dig steg för att konfigurera inställningar för Microsoft Push Notification Service (MPNS) för en notification hub: 

1. I Azure-portalen på den **Meddelandehubb** väljer **Windows Phone (MPNS)** på den vänstra menyn.
1. Aktivera oautentiserad eller autentiserade push-meddelanden:

   a. Om du vill aktivera ej autentiserade push-meddelanden, Välj **aktivera ej autentiserade push** > **spara**.

      ![Skärmbild som visar hur du aktiverar ej autentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. För att aktivera autentiserade push-meddelanden:
      * I verktygsfältet väljer **överför certifikat**.
      * Välj filikonen och välj sedan certifikatfilen.
      * Ange lösenordet för certifikatet.
      * Välj **OK**.
      * På den **Windows Phone (MPNS)** väljer **spara**.

## <a name="next-steps"></a>Nästa steg
Se en självstudie med stegvisa instruktioner för push-meddelanden till Windows Phone-enheter med hjälp av Azure Notification Hubs och Microsoft Push Notification Service (MPNS) [Push-meddelanden till Windows Phone-appar med hjälp av meddelande Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

