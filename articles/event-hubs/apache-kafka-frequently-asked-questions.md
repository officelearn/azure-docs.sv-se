---
title: Vanliga frågor och svar - Azure Event Hubs för Apache Kafka
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
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606748"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Vanliga frågor och svar - Event Hubs för Apache Kafka 
Den här artikeln innehåller svar på några av de vanligaste frågorna om att migrera till Event Hubs för Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Driver du Apache Kafka?

Nej.  Vi kör Kafka API-åtgärder mot Event Hubs-infrastruktur.  Eftersom det finns ett snävt samband mellan Apache Kafka och Event Hubs AMQP-funktioner (det vill säga producera, ta emot, hantera, så vidare.), kan vi föra den kända tillförlitligheten hos Event Hubs till Kafka PaaS-utrymmet.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Konsumentgrupp för Event Hubs jämfört med Kafka konsumentgrupp
Vad är skillnaden mellan en Event Hub-konsumentgrupp och en Kafka-konsumentgrupp på Event Hubs? Kafka-konsumentgrupper på Event Hubs skiljer sig helt från vanliga konsumentgrupper för Event Hubs.

**Konsumentgrupper för eventhubbar**

- De hanteras med mallar för att skapa, hämta, uppdatera och ta bort (CRUD) via mallar för portal, SDK eller Azure Resource Manager. Händelsehubbar konsumentgrupper kan inte skapas automatiskt.
- De är underordnade entiteter i en händelsehubb. Det innebär att samma konsumentgruppsnamn kan återanvändas mellan händelsehubbar i samma namnområde eftersom de är separata entiteter.
- De används inte för att lagra förskjutningar. Orkestrerad AMQP-förbrukning görs med extern förskjutningslagring, till exempel Azure Storage.

**Kafka konsumentgrupper**

- De är autoskapade.  Kafka-grupper kan hanteras via Kafka konsumentgrupp API: er.
- De kan lagra förskjutningar i tjänsten Event Hubs.
- De används som nycklar i vad som i praktiken är en offset nyckel-värde butik. För ett unikt `group.id` `topic-partition`par och lagrar vi en förskjutning i Azure Storage (3x replication). Event Hubs-användare ådrar sig inte extra lagringskostnader från att lagra Kafka-förskjutningar. Förskjutningar kan hanteras via Kafka-konsumentgrupp-API:erna, men *motlagringskontona* är inte direkt synliga eller kan hanteras för Event Hub-användare.  
- De sträcker sig över ett namnområde. Använda samma Kafka gruppnamn för flera program på flera ämnen innebär att alla program och deras Kafka klienter kommer att balanseras när endast ett enda program behöver ombalansering.  Välj dina gruppnamn klokt.
- De skiljer sig helt från Event Hubs konsumentgrupper. Du behöver **inte** använda "$Default", inte heller behöver du oroa dig för Kafka-klienter som stör AMQP-arbetsbelastningar.
- De kan inte visas i Azure-portalen. Konsumentgruppsinformation är tillgänglig via Kafka API:er.

## <a name="next-steps"></a>Nästa steg
Mer information om eventhubbar och händelsehubbar för Kafka finns i följande artiklar:  

- [Utvecklarguide för Apache Kafka för Event Hubs](apache-kafka-developer-guide.md)
- [Guiden Apache Kafka-migrering för eventhubbar](apache-kafka-migration-guide.md)
- [Felsökningsguide för Apache Kafka för eventhubbar](apache-kafka-troubleshooting-guide.md)
- [Rekommenderade konfigurationer](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

