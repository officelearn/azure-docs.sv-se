---
title: "Skapa en Azure-händelsehubb | Microsoft Docs"
description: "Skapa ett namnområde för Azure Event Hubs och en händelsehubb med hjälp av Azure portal"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: sethm
ms.openlocfilehash: 816bf1426704d3391550e80c0700f1b011683a94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal

## <a name="create-an-event-hubs-namespace"></a>Skapa ett namnområde för Händelsehubbar
1. Logga in på [Azure Portal][Azure portal] och klicka på **Ny** högst upp till vänster på skärmen.
1. Klicka på **Sakernas Internet**, och klicka sedan på **Händelsehubbar**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. I bladet **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic eller Standard). Välj även en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen. 
1. Klicka på **Skapa** för att skapa namnområdet. Du kan behöva vänta några minuter för systemet att fullständigt etablera resurser.
2. Klicka på det nyligen skapade namnområden i portalen listan över namnområden.
2. Klicka på **principer för delad åtkomst**, och klicka sedan på **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. Klicka på kopieringsknappen för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. Spara den här anslutningssträngen i en tillfällig plats, till exempel Anteckningar för senare användning.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. Klicka på det nya namnområdet i listan Händelsehubbar namnområde.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Klicka på namnområdesbladet och på **Händelsehubbar**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. Klicka på **Lägg till händelsehubb** överst på bladet.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Skriv ett namn för din händelsehubb, och klicka sedan på **skapa**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)

Din händelsehubb har nu skapats och du har anslutningssträngar för måste du skicka och ta emot händelser.

## <a name="next-steps"></a>Nästa steg
Mer information om Händelsehubbar finns i följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Event Hubs API-översikt](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/