---
title: Vanliga frågor och svar – Azure Event Hubs för Apache Kafka
description: Den här artikeln visar hur konsumenter och producenter som använder olika protokoll (AMQP, Apache Kafka och HTTPS) kan utbyta händelser när de använder Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: shvija
ms.openlocfilehash: cd4306fd4a3b6dd308b0d62945ae264dfb6ce8ff
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85298335"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Vanliga frågor och svar – Event Hubs för Apache Kafka 
Den här artikeln innehåller svar på några vanliga frågor om att migrera till Event Hubs för Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Kör du Apache Kafka?

Nej.  Vi kör Kafka API-åtgärder mot Event Hubs-infrastrukturen.  Eftersom det finns en nära korrelation mellan Apache Kafka och Event Hubs AMQP-funktioner (det vill säga, ta emot, hantera, så vidare), kan vi ta den kända tillförlitligheten för Event Hubs till Kafka PaaS-utrymmet.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs konsument grupp eller konsument grupp för Kafka
Vad är skillnaden mellan en konsument grupp för Event Hub och en Kafka-konsument grupp på Event Hubs? Kafka konsument grupper på Event Hubs är helt skilda från standard Event Hubs konsument grupper.

**Event Hubs konsument grupper**

- De hanteras med åtgärder för att skapa, Hämta, uppdatera och ta bort (CRUD) via portal, SDK eller Azure Resource Manager mallar. Event Hubs konsument grupper kan inte skapas autoskapade.
- De är underordnade entiteter för en Event Hub. Det innebär att samma konsument grupp namn kan återanvändas mellan Event Hub i samma namnrymd eftersom de är separata entiteter.
- De används inte för att lagra förskjutningar. Den dirigerade AMQP-förbrukningen görs med hjälp av extern offset Storage, till exempel Azure Storage.

**Kafka konsument grupper**

- De skapas Autoskapas.  Kafka-grupper kan hanteras via Kafka-konsument grupps-API: er.
- De kan lagra förskjutningar i Event Hubss tjänsten.
- De används som nycklar i vad som effektivt är ett förskjutnings nyckel värdes lager. För ett unikt par av `group.id` och `topic-partition` lagrar vi en förskjutning i Azure Storage (3x-replikering). Event Hubs användare debiteras inte extra lagrings kostnader för lagring av Kafka-förskjutningar. Förskjutningar är manipulable via Kafka konsument grupps-API: er, men de förskjutnings lagrings *kontona* är inte direkt synliga eller Manipulable för Event Hub-användare.  
- De sträcker sig över ett namn område. Om du använder samma Kafka grupp namn för flera program i flera ämnen innebär det att alla program och deras Kafka-klienter kommer att ombalanseras när endast ett enda program behöver ombalansera.  Välj grupp namn med en bra idé.
- De är helt åtskilda från Event Hubs konsument grupper. Du behöver **inte** använda "$default" eller behöver inte oroa dig om Kafka-klienter som stör AMQP-arbetsbelastningar.
- De visas inte i Azure Portal. Konsument grupp information kan nås via Kafka-API: er.

## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs och Event Hubs för Kafka finns i följande artiklar:  

- [Apache Kafka Developer Guide för Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka migrations guide för Event Hubs](apache-kafka-migration-guide.md)
- [Apache Kafka fel söknings guide för Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Rekommenderade konfigurationer](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

