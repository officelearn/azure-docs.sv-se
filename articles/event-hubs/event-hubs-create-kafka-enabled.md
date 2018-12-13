---
title: Skapa Apache Kafka aktiverade event hub - Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa en Apache Kafka aktiverat Event Hubs-namnområde med hjälp av Azure-portalen.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: bf78ac6bce23b739ed661929f6fe90eddc9b4d94
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090789"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Skapa Apache Kafka aktiverat händelsehubbar

Azure Event Hubs är en stor Dataströmningsplattform som en tjänst (PaaS) som matar in miljontals händelser per sekund och ger mindre fördröjning och högt dataflöde för analys i realtid och visualisering.

Händelsehubbar i Azure ger dig en Kafka-slutpunkt. Den här slutpunkten kan Event Hubs-namnområdet att förstå internt [Apache Kafka](https://kafka.apache.org/intro) meddelande protokoll och API: er. Med den här funktionen kan du kommunicera med dina event hubs precis som med Kafka-avsnitt utan att ändra protokoll-klienter eller ditt eget kluster som körs. Har stöd för Händelsehubbar [Apache Kafka versioner 1.0](https://kafka.apache.org/10/documentation.html) och senare.

Den här artikeln beskriver hur du skapar ett namnområde för Event Hubs och hämta anslutningssträngen som krävs för att ansluta Kafka-program till Kafka-aktiverade event hubs.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Skapa ett Kafka-aktiverat Event Hubs-namnområde

1. Logga in på den [Azure-portalen][Azure portal], och klicka på **skapa en resurs** på upp till vänster på skärmen.

2. Sök efter Event Hubs och välj de alternativ som visas här:
    
    ![Sök efter Event Hubs på portalen](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Ange ett unikt namn och aktivera Kafka för namnområdet. Klicka på **Skapa**.
    
    ![Skapa ett namnområde](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. När namnområdet har skapats går du till fliken **Inställningar** och klickar på **Policyer för delad åtkomst** för att hämta anslutningssträngen.

    ![Klicka på Policyer för delad åtkomst](./media/event-hubs-create/create-event-hub7.png)

5. Du kan välja standardprincipen **RootManageSharedAccessKey** eller lägga till en ny princip. Klicka på principnamnet och kopiera anslutningssträngen. 
    
    ![Välj en princip](./media/event-hubs-create/create-event-hub8.png)
 
6. Lägg till den här anslutningssträngen i Kafka-programkonfigurationen.

Nu kan du strömma händelser från program som använder Kafka-protokollet till Event Hubs.

## <a name="next-steps"></a>Nästa steg

Mer information om Händelsehubbar finns i följande länkar:

* [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
