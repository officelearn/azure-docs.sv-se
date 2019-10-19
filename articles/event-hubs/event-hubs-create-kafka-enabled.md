---
title: Skapa Apache Kafka aktive rad Event Hub – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genom gång av hur du skapar ett Apache Kafka aktiverat Azure Event Hubs-namnområde med hjälp av Azure Portal.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555812"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Skapa Apache Kafka aktiverade Event Hub

Azure Event Hubs är en stor data strömnings plattform som en tjänst (PaaS) som matar in miljon tals händelser per sekund och ger låg latens och högt data flöde för analys och visualisering i real tid.

Azure Event Hubs ger dig en Kafka-slutpunkt. Den här slut punkten gör det möjligt för Event Hubs namn området att förstå [Apache Kafka](https://kafka.apache.org/intro) meddelande protokoll och API: er. Med den här funktionen kan du kommunicera med dina Event Hub på samma sätt som med Kafka-ämnen utan att ändra protokoll klienter eller köra egna kluster. Event Hubs stöder [Apache Kafka version 1,0](https://kafka.apache.org/10/documentation.html) och senare.

Den här artikeln beskriver hur du skapar en Event Hubs namnrymd och hämtar anslutnings strängen som krävs för att ansluta Kafka-program till Kafka-aktiverade Event Hub.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Skapa ett Kafka-aktiverat Event Hubs-namnområde

1. Logga in på [Azure-portalen][Azure portal] och klicka på **Skapa en resurs** högst upp till vänster på skärmen.

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

Om du vill veta mer om Event Hubs går du till följande länkar:

* [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
