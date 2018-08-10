---
title: Skapa en Azure-händelsehubb | Microsoft Docs
description: Skapa ett Azure Event Hubs-namnområde och en event hub med Azure-portalen
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: shvija
ms.openlocfilehash: fb2020f7bfc8521e141adb4eefbc227e8123a269
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002628"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Skapa ett Event Hubs-namnområde och en event hub med Azure-portalen

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

1. Logga in på den [Azure-portalen][Azure portal], och klicka på **skapa en resurs** på upp till vänster på skärmen.
2. Klicka på **Sakernas Internet** och sedan på **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. Under **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.  

4. När du har kontrollerat att namnområdet är tillgängligt väljer du prisnivå (Basic eller Standard). Välj även en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen.
 
5. Klicka på **Skapa** för att skapa namnområdet. Du kan behöva vänta några minuter på att systemet ska bli klart med att etablera resurserna.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. Klicka på det nyligen skapade namnområdet i listan med namnområden i Portal.

7. Klicka på **Principer för delad åtkomst** och sedan på **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Klicka på kopieringsknappen för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. Spara den här anslutningssträngen på en tillfällig plats, till exempel i Anteckningar, för senare användning.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. I listan över händelsehubbarnas namnområden klickar du på det nyligen skapade namnområdet.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Klicka på namnområdesbladet och på **Händelsehubbar**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Överst på bladet klickar du på **+ Event Hub**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Skriv ett namn för din händelsehubb och klicka sedan på **Skapa**. 

Din händelsehubb har nu skapats och du har anslutningssträngar för måste du skicka och ta emot händelser.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar finns i följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Event Hubs API-översikt](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/