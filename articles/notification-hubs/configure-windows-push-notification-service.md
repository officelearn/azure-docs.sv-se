---
title: Konfigurera Windows Push Notification Service i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Windows Push Notification Service för en Azure Notification Hub.
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
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212405"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera inställningar för Windows Push Notification Service (WNS) för en Notification Hub i Azure Portal
Den här artikeln visar hur du konfigurerar inställningar för Windows Notification Service (WNS) för en Azure Notification Hub med hjälp av Azure Portal.  

## <a name="prerequisites"></a>Förutsättningar
Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurera Windows Push Notification Service (WNS)

Följande procedur visar hur du konfigurerar inställningar för Windows Push Notification Service (WNS) för en Notification Hub: 

1. I Azure Portal på sidan **Notification Hub** väljer du **Windows (WNS)** på den vänstra menyn.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärm bild som visar rutorna paket-SID och säkerhets nyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Nästa steg
En själv studie kurs med stegvisa instruktioner för att skicka meddelanden till Universell Windows-plattform program med hjälp av Azure Notification Hubs och Windows Push Notification Service (WNS) finns i [skicka meddelanden till UWP-appar med hjälp av Azure Notification Hubbar](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


