---
title: Konfigurera Apple Push Notification Service i Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du konfigurerar en Azure-meddelandehubb med APNS-inställningar (Apple Push Notification Service).
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
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127521"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera inställningar för Apple Push Notification Service för en meddelandehubb i Azure-portalen

Den här artikeln visar hur du konfigurerar APNS-inställningar (Apple Push Notification Service) för en Azure-meddelandehubb med hjälp av Azure-portalen. 

## <a name="prerequisites"></a>Krav
Om du inte redan har skapat en meddelandehubb skapar du en nu. Mer information finns [i Skapa en Azure-meddelandehubb i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurera Apple Push Notification Service

Följande procedur ger dig anvisningar för att konfigurera APNS-inställningar (Apple Push Notification Service) för en meddelandehubb:

1. På sidan **Meddelandehubben** på sidan Meddelandehubben på sidan Meddelandehubben väljer du **Apple (APNS)** på den vänstra menyn.

1. För **autentiseringsläge**väljer du antingen **Certifikat** eller **Token**.

   a. Om du väljer **Certifikat:**
   * Markera filikonen och välj sedan den *p12-fil* som du vill ladda upp.
   * Ange ett lösenord.
   * Välj **Sandbox**-läge. Om du vill skicka push-meddelanden till användare som köpt appen från butiken väljer du **Produktionsläge.**

     ![Skärmbild av en APNS-certifikatkonfiguration i Azure-portalen](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Om du väljer **Token:**

   * Ange värdena för **nyckel-ID,** **bunt-ID,** **grupp-ID**och **token**.
   * Välj **Sandbox**-läge. Om du vill skicka push-meddelanden till användare som köpt appen från butiken väljer du **Produktionsläge.**

     ![Skärmbild av en APNS-tokenkonfiguration i Azure-portalen](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Nästa steg
En självstudiekurs med steg-för-steg-instruktioner för att skicka meddelanden till iOS-enheter finns i följande artikel: [Push-meddelanden till iOS-enheter med hjälp av Meddelandehubbar och APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
