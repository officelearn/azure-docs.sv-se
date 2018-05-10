---
title: Skapa Kafka aktiverat Azure Event Hubs | Microsoft Docs
description: Skapa en Kafka aktiverat Azure Event Hubs namnområde med Azure-portalen
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 4f1d21be3c19dfbc764485fea47b6d4cb2171b3c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="create-kafka-enabled-event-hubs"></a>Skapa Kafka aktiverad Händelsehubbar

Händelsehubbar i Azure är en Big Data strömning plattform som en tjänst (PaaS) som en miljontals händelser per sekund och innehåller låg latens och hög genomströmning för analys i realtid och visualisering.

Händelsehubbar i Azure för Kafka ekosystem ger dig en slutpunkt. Den här slutpunkten gör Händelsehubbar namnområdet till internt förstå [Apache Kafka](https://kafka.apache.org/intro) protokoll och API: er. Med den här funktionen kan du kommunicera med dina Händelsehubbar precis som med Kafka avsnitt utan att ändra protokoll-klienter eller ditt eget kluster som körs. Händelsehubbar för Kafka ekosystem stöder [Apache Kafka versioner 1.0](https://kafka.apache.org/10/documentation.html) och senare.

Den här artikeln beskriver hur du skapar ett namnområde för Händelsehubbar och ansluta till den sträng som krävs för att ansluta Kafka program till Kafka aktiverat Händelsehubbar.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Skapa en Kafka aktiverat Händelsehubbar namnområde

1. Logga in på den [Azure-portalen][Azure portal], och klicka på **skapar du en resurs** på upp till vänster på skärmen.

2. Sök efter Händelsehubbar och markera de alternativ som visas här:
    
    ![Sök efter Händelsehubbar i portalen](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. **Skapa namnområdet**, genom att ange ett unikt namn och aktivera Kafka på namnområdet. Klicka på **Skapa**.
    
    ![Skapa ett namnområde](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. När namnområdet har skapats på den **inställningar** klickar du på **principer för delad åtkomst** att hämta anslutningssträngen.

    ![Klicka på principer för delad åtkomst](./media/event-hubs-create/create-event-hub7.png)

5. Du kan välja standard **RootManageSharedAccessKey**, eller Lägg till en ny princip. Klicka på namnet på principen och kopiera anslutningssträngen. 
    
    ![Välj en princip](./media/event-hubs-create/create-event-hub8.png)
 
6. Lägg till den här anslutningssträngen konfigurationen Kafka programmet.

Du kan nu strömma händelser från ditt program som använder protokollet Kafka i Händelsehubbar.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar finns i följande länkar:

* [Dataströmmen i Händelsehubbar från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Läs mer om Händelsehubbar för Kafka ekosystem](event-hubs-for-kafka-ecosystem-overview.md)
* [Lär dig mer om Händelsehubbar](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
