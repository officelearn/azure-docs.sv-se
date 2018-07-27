---
title: Skapa Apache Kafka aktiverat Azure Event Hubs | Microsoft Docs
description: Skapa en Kafka aktiverat Event Hubs-namnområde med Azure portal
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 79b6b879bd2332c044ce871e2c9a938c6b9c900c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285096"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Skapa Apache Kafka aktiverat händelsehubbar

Azure Event Hubs är en stor Dataströmningsplattform som en tjänst (PaaS) som matar in miljontals händelser per sekund och ger mindre fördröjning och högt dataflöde för analys i realtid och visualisering.

Händelsehubbar i Azure ger dig en Kafka-slutpunkt. Den här slutpunkten kan Event Hubs-namnområdet att förstå internt [Apache Kafka](https://kafka.apache.org/intro) meddelande protokoll och API: er. Med den här funktionen kan du kommunicera med dina event hubs precis som med Kafka-avsnitt utan att ändra protokoll-klienter eller ditt eget kluster som körs. Har stöd för Händelsehubbar [Apache Kafka versioner 1.0](https://kafka.apache.org/10/documentation.html) och senare.

Den här artikeln beskriver hur du skapar ett namnområde för Event Hubs och hämta anslutningssträngen som krävs för att ansluta Kafka-program till Kafka-aktiverade event hubs.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Skapa en Kafka aktiverat Event Hubs-namnområdet

1. Logga in på den [Azure-portalen][Azure portal], och klicka på **skapa en resurs** på upp till vänster på skärmen.

2. Sök efter Event Hubs och välj de alternativ som visas här:
    
    ![Söka efter Event Hubs i portalen](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Ange ett unikt namn och möjliggör Kafka för namnområdet. Klicka på **Skapa**.
    
    ![Skapa ett namnområde](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. När namnområdet har skapats på den **inställningar** klickar du på fliken **principer för delad åtkomst** att hämta anslutningssträngen.

    ![Klicka på principer för delad åtkomst](./media/event-hubs-create/create-event-hub7.png)

5. Du kan välja standard **RootManageSharedAccessKey**, eller lägga till en ny princip. Klicka på namnet på principen och kopiera anslutningssträngen. 
    
    ![Välj en princip](./media/event-hubs-create/create-event-hub8.png)
 
6. Lägga till den här anslutningssträngen i konfigurationen för Kafka-programmet.

Du kan nu strömma händelser från dina program som använder Kafka-protokollet till Event Hubs.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar finns i följande länkar:

* [Stream till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
