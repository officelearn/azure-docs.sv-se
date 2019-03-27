---
title: Konfigurera Apple Push Notification Service i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar en Azure notification hub med inställningar för Apple Push Notification Service (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488448"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera inställningar för Apple Push Notification Service (APNS) för en notification hub i Azure portal
Den här artikeln visar hur du konfigurerar inställningar för Apple Push Notification Service (APNS) för en Azure notification hub med hjälp av Azure portal. 

## <a name="prerequisites"></a>Förutsättningar
Om du inte redan har skapat en meddelandehubb, ska du skapa en nu. Mer information finns i [skapar en Azure notification hub i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurera Apple Push Notification Service

Följande procedur ger dig steg för att konfigurera inställningar för Apple Push Notification Service (APNS) för en notification hub:

1. I Azure-portalen på den **Meddelandehubb** väljer **Apple (APNS)** på den vänstra menyn.

1. För **autentiseringsläge**, väljer du antingen **certifikat** eller **Token**.

   a. Om du väljer **certifikat**:
   * Välj filikonen och välj sedan den *.p12* fil du vill överföra.
   * Ange ett lösenord.
   * Välj **Sandbox**-läge. Eller om du vill skicka push-meddelanden till användare som har köpt din app från butiken, Välj **produktion** läge.

     ![Skärmbild av ett APN-certifikatet har konfigurerats i Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Om du väljer **Token**:

   * Ange värden för **nyckel-ID**, **paket-ID**, **lag-ID**, och **Token**.
   * Välj **Sandbox**-läge. Eller om du vill skicka push-meddelanden till användare som har köpt din app från butiken, Välj **produktion** läge.

     ![Skärmbild av en APN token konfiguration i Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Nästa steg
En självstudie med stegvisa instruktioner för push-meddelanden till iOS-enheter finns i följande artikel: [Push-meddelanden till iOS-enheter med hjälp av Meddelandehubbar och APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
