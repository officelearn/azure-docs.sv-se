---
title: Skapa en Azure-händelsehubb | Microsoft Docs
description: Skapa ett namnområde för Azure Event Hubs och en händelsehubb med hjälp av Azure portal
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: sethm
ms.openlocfilehash: 9b466d4e727c1511ca2318c0da3ec2807a965a5d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625550"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal

## <a name="create-an-event-hubs-namespace"></a>Skapa ett namnområde för Händelsehubbar

1. Logga in på den [Azure-portalen][Azure portal], och klicka på **skapar du en resurs** på upp till vänster på skärmen.
2. Klicka på **Sakernas Internet**, och klicka sedan på **Händelsehubbar**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. I **skapa namnområdet**, ange ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.  

4. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic eller Standard). Välj även en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen.
 
5. Klicka på **Skapa** för att skapa namnområdet. Du kan behöva vänta några minuter på att systemet blir klar med att etablera resurserna.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. Klicka på det nyligen skapade namnområden i portalen listan över namnområden.

7. Klicka på **principer för delad åtkomst**, och klicka sedan på **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Klicka på kopieringsknappen för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. Spara den här anslutningssträngen i en tillfällig plats, till exempel Anteckningar för senare användning.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. Klicka på det nya namnområdet i listan Händelsehubbar namnområde.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Klicka på namnområdesbladet och på **Händelsehubbar**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Överst på bladet klickar du på **+ Event Hub**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Skriv ett namn för din händelsehubb, och klicka sedan på **skapa**. 

Din händelsehubb har nu skapats och du har anslutningssträngar för måste du skicka och ta emot händelser.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar finns i följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Event Hubs API-översikt](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/