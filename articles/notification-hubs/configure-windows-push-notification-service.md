---
title: Konfigurera Windows Push Notification Service i Azure Notification Hubs | Microsoft-dokument
description: Lär dig hur du konfigurerar Windows Push Notification Service-inställningar för en Azure-meddelandehubb.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127317"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Konfigurera inställningar för Windows Push Notification Service i Azure-portalen

Den här artikeln visar hur du konfigurerar WNS-inställningar (Windows Notification Service) för en Azure-meddelandehubb med hjälp av Azure-portalen.  

## <a name="prerequisites"></a>Krav
Om du inte redan har skapat en meddelandehubb skapar du en nu. Mer information finns [i Skapa en Azure-meddelandehubb i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurera Windows Push Notification Service (WNS)

Följande procedur ger dig anvisningar för att konfigurera WNS-inställningar (Windows Push Notification Service) för en meddelandehubb: 

1. På sidan **Meddelandehubben** på sidan Meddelandehubben väljer du **Windows (WNS)** på den vänstra menyn.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärmbild som visar rutorna Package SID och Security Key](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Nästa steg
En självstudiekurs med steg-för-steg-instruktioner för att skicka meddelanden till Universella Windows-plattformsprogram med hjälp av Azure Notification Hubs och Windows Push Notification Service (WNS) finns i [Skicka meddelanden till UWP-appar med hjälp av Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


