---
title: Skala av Azure IoT Hub | Microsoft-dokument
description: Så här skalar du din IoT-hubb för att stödja ditt förväntade meddelandedataflöde och önskade funktioner. Innehåller en sammanfattning av det dataflöde som stöds för varje nivå och alternativ för fragmentering.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: ecc1ae5138fe5a1e42fed9be2e31b5afa8b6d5b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497499"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Välja rätt IoT Hub-nivå för din lösning

Varje IoT-lösning är annorlunda, så Azure IoT Hub erbjuder flera alternativ baserat på priser och skala. Den här artikeln är avsedd att hjälpa dig att utvärdera dina IoT Hub-behov. Prisinformation om IoT Hub-nivåer finns i [IoT Hub-priser](https://azure.microsoft.com/pricing/details/iot-hub).

Om du vill bestämma vilken IoT Hub-nivå som är rätt för din lösning ställer du dig två frågor:

**Vilka funktioner planerar jag att använda?**

Azure IoT Hub erbjuder två nivåer, grundläggande och standard, som skiljer sig åt i antalet funktioner som de stöder. Om din IoT-lösning är baserad på att samla in data från enheter och analysera den centralt, är den grundläggande nivån förmodligen rätt för dig. Om du vill använda mer avancerade konfigurationer för att fjärrstyra IoT-enheter eller distribuera några av dina arbetsbelastningar till själva enheterna bör du överväga standardnivån. För en detaljerad uppdelning av vilka funktioner som ingår i varje nivå fortsätter till [grundläggande och standardnivåer](#basic-and-standard-tiers).

**Hur mycket data planerar jag att flytta dagligen?**

Varje IoT Hub-nivå är tillgänglig i tre storlekar, baserat på hur mycket datadataflöde de kan hantera under en viss dag. Dessa storlekar identifieras numeriskt som 1, 2 och 3. Varje enhet på en nivå 1 IoT-hubb kan till exempel hantera 400 000 meddelanden per dag, medan en nivå 3-enhet kan hantera 300 miljoner. Mer information om datariktlinjerna finns i [Meddelandedataflöde](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Grund- och standardnivåer

Standardnivån för IoT Hub aktiverar alla funktioner och krävs för alla IoT-lösningar som vill använda sig av dubbelriktade kommunikationsfunktioner. Nivån Basic möjliggör en delmängd av funktionerna och är avsedd för IoT-lösningar som bara behöver enkelriktad kommunikation från enheter till molnet. Båda nivåerna har samma funktioner för säkerhet och autentisering.

Endast en typ av [utgåva](https://azure.microsoft.com/pricing/details/iot-hub/) inom en nivå kan väljas per IoT Hub. Du kan till exempel skapa en IoT Hub med flera enheter S1, men inte med en blandning av enheter från olika utgåvor, till exempel S1 och S2.

| Funktion | Basic-nivå | Gratis/standardnivå |
| ---------- | ---------- | ------------- |
| [Telemetri för enhet till moln](iot-hub-devguide-messaging.md) | Ja | Ja |
| [Identitet per enhet](iot-hub-devguide-identity-registry.md) | Ja | Ja |
| [Meddelanderoutning,](iot-hub-devguide-messages-read-custom.md) [meddelandeberikande och](iot-hub-message-enrichments-overview.md) [integrering av händelserutnät](iot-hub-event-grid.md) | Ja | Ja |
| [HTTP-, AMQP- och MQTT-protokoll](iot-hub-devguide-protocols.md) | Ja | Ja |
| [Tjänst för etablering av enhet](../iot-dps/about-iot-dps.md) | Ja | Ja |
| [Övervakning och diagnostik](iot-hub-monitor-resource-health.md) | Ja | Ja |
| [Meddelanden från molnet till enheten](iot-hub-devguide-c2d-guidance.md) |   | Ja |
| [Enhetstvillingar,](iot-hub-devguide-device-twins.md) [modultvillingar](iot-hub-devguide-module-twins.md)och [Enhetshantering](iot-hub-device-management-overview.md) |   | Ja |
| [Enhetsströmmar (förhandsgranskning)](iot-hub-device-streams-overview.md) |   | Ja |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Ja |
| [Förhandstitt på IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Ja |

IoT Hub erbjuder också en kostnadsfri nivå som är avsedd för testning och utvärdering. Den har alla funktioner på standardnivån, men begränsade meddelandemängder. Du kan inte uppgradera från den kostnadsfria nivån till antingen grundläggande eller standard.

## <a name="partitions"></a>Partitioner

Azure IoT Hubs innehåller många kärnkomponenter i [Azure Event Hubs](../event-hubs/event-hubs-features.md), inklusive [partitioner](../event-hubs/event-hubs-features.md#partitions). Händelseströmmar för IoT Hubs fylls vanligtvis med inkommande telemetridata som rapporteras av olika IoT-enheter. Partitioneringen av händelseströmmen används för att minska de konkurrenser som uppstår när du samtidigt läser och skriver till händelseströmmar.

Partitionsgränsen väljs när IoT Hub skapas och kan inte ändras. Den maximala partitionsgränsen för grundläggande nivå IoT Hub och standardnivå IoT Hub är 32. De flesta IoT-hubbar behöver bara 4 partitioner. Mer information om hur du bestämmer partitionerna finns i vanliga frågor om händelsehubbar [Hur många partitioner behöver jag?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Nivå uppgradering

När du har skapat din IoT-hubb kan du uppgradera från den grundläggande nivån till standardnivån utan att avbryta dina befintliga åtgärder. Mer information finns i [Så här uppgraderar du din IoT-hubb](iot-hub-upgrade.md).

Partitionskonfigurationen förblir oförändrad när du migrerar från grundläggande nivå till standardnivå.

> [!NOTE]
> Den kostnadsfria nivån stöder inte uppgradering till grundläggande eller standard.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST API:er

Skillnaden i funktioner som stöds mellan de grundläggande nivåerna och standardnivåerna för IoT Hub innebär att vissa API-anrop inte fungerar med grundläggande nivåhubbar. I följande tabell visas vilka API:er som är tillgängliga:

| API | Basic-nivå | Gratis/standardnivå |
| --- | ---------- | ------------- |
| [Ta bort enhet](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletedevice) | Ja | Ja |
| [Hämta enhet](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevice) | Ja | Ja |
| [Ta bort modul](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletemodule) | Ja | Ja |
| [Hämta modul](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getmodule) | Ja | Ja |
| [Hämta registerstatistik](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevicestatistics) | Ja | Ja |
| [Få statistik över tjänster](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getservicestatistics) | Ja | Ja |
| [Skapa eller uppdatera enhet](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatedevice) | Ja | Ja |
| [Skapa eller uppdatera modul](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatemodule) | Ja | Ja |
| [Fråga IoT-hubb](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/queryiothub) | Ja | Ja |
| [Skapa filuppladdning SAS URI](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Ja | Ja |
| [Ta emot enhetsbunden avisering](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Ja | Ja |
| [Skicka enhetshändelse](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Ja | Ja |
| Skicka modulhändelse | ENDAST AMQP och MQTT | ENDAST AMQP och MQTT |
| [Uppdatera status för filöverföring](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Ja | Ja |
| [Massenhetsåtgärd](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/bulkdevicecrud) | Ja, förutom IoT Edge-funktioner | Ja |
| [Avbryt importexportjobbet](https://docs.microsoft.com/rest/api/iothub/service/jobclient/cancelimportexportjob) | Ja | Ja |
| [Skapa importexportjobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createimportexportjob) | Ja | Ja |
| [Hämta importexportjobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjob) | Ja | Ja |
| [Hämta importexportjobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) | Ja | Ja |
| [Rensa kommandokö](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) |   | Ja |
| [Hämta enhetstvilling](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) |   | Ja |
| [Hämta modultvilling](https://docs.microsoft.com/rest/api/iothub/service/twin/getmoduletwin) |   | Ja |
| [Anropa enhetsmetod](https://docs.microsoft.com/rest/api/iothub/service/devicemethod/invokedevicemethod) |   | Ja |
| [Uppdatera enhetstvilling](https://docs.microsoft.com/rest/api/iothub/service/twin/updatedevicetwin) |   | Ja |
| [Uppdatera modultvilling](https://docs.microsoft.com/rest/api/iothub/service/twin/updatemoduletwin) |   | Ja |
| [Överger meddelande om enhetsbunden](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Ja |
| [Fullständigt meddelande om enhetsbunden](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Ja |
| [Avbryt jobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/canceljob) |   | Ja |
| [Skapa jobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createjob) |   | Ja |
| [Skaffa jobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getjob) |   | Ja |
| [Frågejobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/queryjobs) |   | Ja |

## <a name="message-throughput"></a>Meddelandegenomströmning

Det bästa sättet att storlek en IoT Hub-lösning är att utvärdera trafiken per enhet. Tänk särskilt på den högsta genomströmning som krävs för följande kategorier av åtgärder:

* Meddelanden från enheten till molnet
* Meddelanden från molnet till enheten
* Identitetsregisteråtgärder

Trafiken mäts för din IoT-hubb per enhet. När du skapar en IoT-hubb väljer du dess nivå och utgåva och anger antalet tillgängliga enheter. Du kan köpa upp till 200 enheter för B1-, B2-, S1- eller S2-utgåvan, eller upp till 10 enheter för B3- eller S3-utgåvan. När din IoT-hubb har skapats kan du ändra antalet enheter som är tillgängliga i dess utgåva, uppgradera eller nedgradera mellan utgåvor inom dess nivå (B1 till B2) eller uppgradera från den grundläggande till standardnivån (B1 till S1) utan att avbryta dina befintliga åtgärder. Mer information finns i [Så här uppgraderar du din IoT-hubb](iot-hub-upgrade.md).  

Som ett exempel på varje nivås trafikfunktioner följer meddelanden mellan enheter och moln dessa riktlinjer för kontinuerligt dataflöde:

| Nivå utgåva | Ihållande genomflöde | Ihållande sändningshastighet |
| --- | --- | --- |
| B1, S1 |Upp till 1111 KB/minut per enhet<br/>(1,5 GB/dag/enhet) |Genomsnitt på 278 meddelanden/minut per enhet<br/>(400 000 meddelanden/dag per enhet) |
| B2, S2 |Upp till 16 MB/minut per enhet<br/>(22,8 GB/dag/enhet) |Genomsnitt 4 167 meddelanden/minut per enhet<br/>(6 miljoner meddelanden/dag per enhet) |
| B3, S3 |Upp till 814 MB/minut per enhet<br/>(1144,4 GB/dag/enhet) |Genomsnitt på 208 333 meddelanden/minut per enhet<br/>(300 miljoner meddelanden/dag per enhet) |

Enhet-till-moln-dataflöde är bara ett av de mått du behöver tänka på när du utformar en IoT-lösning. Mer omfattande information finns i [IoT Hub-kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Dataflöde för identitetsregisteråtgärd

IoT Hub-identitetsregisteråtgärder ska inte vara körningsåtgärder, eftersom de oftast är relaterade till enhetsetablering.

Specifika burst-prestandanummer finns i [IoT Hub-kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatisk skala

Om du närmar dig den tillåtna meddelandegränsen på din IoT-hubb kan du använda dessa [steg för att automatiskt skala för](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) att öka en IoT Hub-enhet på samma IoT Hub-nivå.

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub-funktioner och prestandainformation finns i [IoT Hub-priser](https://azure.microsoft.com/pricing/details/iot-hub) eller [IoT Hub-kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

* Om du vill ändra din IoT Hub-nivå följer du stegen i [Uppgradera din IoT-hubb](iot-hub-upgrade.md).
