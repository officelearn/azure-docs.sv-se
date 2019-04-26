---
title: Konfigurera Windows Push Notification Service i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar Windows Push Notification Service-inställningar för en Azure notification hub.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240284"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurera inställningar för Windows Push Notification Service (WNS) för en notification hub i Azure portal
Den här artikeln visar hur du konfigurerar inställningar för Windows Notification Service (WNS) för en Azure notification hub med hjälp av Azure portal.  

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du inte redan har skapat en meddelandehubb, ska du skapa en nu. Mer information finns i [skapar en Azure notification hub i Azure-portalen](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurera Windows Push Notification Service (WNS)

Följande procedur ger dig steg för att konfigurera inställningar för Windows Push Notification Service (WNS) för en notification hub: 

1. I Azure-portalen på den **Meddelandehubb** väljer **Windows (WNS)** på den vänstra menyn.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärmbild som visar rutorna paket-SID och Säkerhetsnyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Nästa steg
Se en självstudie med stegvisa instruktioner för push-meddelanden Universal Windows Platform-program med hjälp av Azure Notification Hubs och Windows Push Notification Service (WNS) [skicka meddelanden till UWP-appar med hjälp av Azure Meddelandehubbar](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


