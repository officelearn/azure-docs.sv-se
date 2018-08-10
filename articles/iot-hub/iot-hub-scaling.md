---
title: Azure IoT Hub skalning | Microsoft Docs
description: Så här skalar du din IoT-hubb för att stödja din förväntade meddelandedataflöde och önskade funktioner. Innehåller en sammanfattning av alternativ för horisontell partitionering och dataflöden som stöds för varje nivå.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 01aeaee03a4cfabbda3a29cddd17febdc8a16e45
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003540"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Välja rätt nivå för IoT Hub för din lösning

Varje IoT-lösning är olika, så Azure IoT Hub erbjuder flera alternativ baserat på priser och skalning. Den här artikeln är avsedd att hjälpa dig att utvärdera din IoT Hub-behov. Prisinformation om IoT Hub-nivåer finns i [IoT Hub priser](https://azure.microsoft.com/pricing/details/iot-hub). 

För att avgöra vilken nivå för IoT Hub är rätt för din lösning, ställa dig två frågor:

**Vilka funktioner jag tänker använda?**
Azure IoT Hub har två nivåer, basic och standard, som skiljer sig åt i hur många funktioner de stöder. Om din IoT-lösning baserad kring att samla in data från enheter och analysera dem centralt är den grundläggande nivån förmodligen rätt för dig. Om du vill använda mer avancerade konfigurationer att fjärrstyra IoT-enheter eller distribuera några av dina arbetsbelastningar till själva enheterna bör du överväga standard-nivån. För en detaljerad analys på detaljnivå av vilka funktioner som ingår i varje nivå fortfarande [nivåerna Basic och standard](#basic-and-standard-tiers).

**Hur mycket data jag tänker flytta varje dag?**
Varje nivå för IoT Hub är tillgänglig i tre storlekar som baserat dataflöde för hur mycket data som de kan hantera i en viss dag. Dessa storlekar identifieras numeriskt som 1, 2 och 3. Varje enhet i en IoT-hubb på nivå 1 kan exempelvis hantera 400 tusen meddelanden per dag, medan en nivå 3-enhet kan hantera 300 miljoner. För mer information om riktlinjer för data kan fortsätta att [meddelandedataflöde](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Nivåerna Basic och standard

Standardnivån för IoT Hub kan du använda alla funktioner och är obligatoriskt för alla IoT-lösningar som vill göra användning av funktioner för dubbelriktad kommunikation. Basic-nivån kan en delmängd av funktionerna som är avsedd för IoT-lösningar som bara behöver enkelriktad kommunikation från enheter till molnet. Båda nivåerna har samma funktioner för säkerhet och autentisering.

När du har skapat din IoT-hubb som du kan uppgradera från basic-nivån till standardnivån utan att avbryta din befintliga åtgärder. Mer information finns i [uppgradera din IoT-hubb](iot-hub-upgrade.md). Observera att den maximala partitionsgränsen för basic-nivån IoT Hub är 8 och standard-nivån är 32. De flesta IoT-hubbar behöver bara 4 partitioner. Partitionsgränsen väljs när IoT-hubben har skapats och avser antalet samtidiga läsare av dessa meddelanden på meddelanden från enheten till molnet. Det här värdet ändras inte när du migrerar från basic-nivån till standardnivån. Tänk också på den enda typen av [edition](https://azure.microsoft.com/pricing/details/iot-hub/) inom en nivå kan väljas per IoT Hub. Du kan till exempel skapa en IoT-hubb med flera enheter av S1, men inte med en blandning av enheter från olika versioner, till exempel S1 och B3, eller S1 och S2.

| Funktion | Basic-nivå | Standard-nivå |
| ---------- | ---------- | ------------- |
| [Enhet-till-moln-telemetri](iot-hub-devguide-messaging.md) | Ja | Ja |
| [Identitet per enhet](iot-hub-devguide-identity-registry.md) | Ja | Ja |
| [Meddelanderoutning](iot-hub-devguide-messages-read-custom.md) och [Event Grid-integration](iot-hub-event-grid.md) | Ja | Ja |
| [Protokoll som HTTP, AMQP och MQTT](iot-hub-devguide-protocols.md) | Ja | Ja |
| [Enhetsetableringstjänst](../iot-dps/about-iot-dps.md) | Ja | Ja |
| [Övervakning och diagnostik](iot-hub-monitor-resource-health.md) | Ja | Ja |
| [Moln-till-enhet-meddelanden](iot-hub-devguide-c2d-guidance.md) |   | Ja |
| [Enhetstvillingar](iot-hub-devguide-device-twins.md), [modultvillingar](iot-hub-devguide-module-twins.md) och [enhetshantering](iot-hub-device-management-overview.md) |   | Ja |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Ja |

IoT Hub erbjuder också en kostnadsfri nivå som är avsedd för testning och utvärdering. Den har alla funktioner i standard-nivån, men begränsad meddelanden tilldelningar. Du kan inte uppgradera från den kostnadsfria nivån till basic eller standard. 

### <a name="iot-hub-rest-apis"></a>REST API:er för IoT Hub

Skillnaden i funktioner som stöds mellan nivåerna basic och standard för IoT Hub innebär att vissa API-anrop inte fungerar med nivån basic hubs. I följande tabell visas vilka API: er är tillgängliga: 

| API | Basic-nivå | Standard-nivå |
| --- | ---------- | ------------- |
| [Ta bort enhet](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Ja | Ja |
| [Få enhet](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Ja | Ja |
| Ta bort modul | Ja | Ja |
| Hämta modul | Ja | Ja |
| [Få statistik för registret](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Ja | Ja |
| [Hämta statistik för tjänster](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Ja | Ja |
| [Skapa eller uppdatera enhet](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Ja | Ja |
| PUT-modulen | Ja | Ja |
| [Fråga IoT-hubb](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Ja | Ja |
| Fråga moduler | Ja | Ja |
| [Skapa filöverföringen SAS-URI](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Ja | Ja |
| [Ta emot meddelande om enheten som är bunden](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Ja | Ja |
| [Skicka enhetshändelse](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Ja | Ja |
| Skicka modulen händelse | Ja | Ja |
| [Uppdatera filuppladdningsstatus](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Ja | Ja |
| [Bulkåtgärd för enhet](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/bulkoperation) | Ja, förutom IoT Edge-funktioner | Ja | 
| [Rensa kön för kommandot](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Ja |
| [Hämta enhetstvilling](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Ja |
| Hämta modultvilling |   | Ja |
| [Anropa enhetsmetoden](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Ja |
| [Uppdatera enhetstvillingen](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Ja | 
| Uppdatera modultvilling |   | Ja | 
| [Lämna meddelande om enheten som är bunden](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Ja |
| [Fullständig enhet bunden meddelande](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Ja |
| [Avbryt jobb](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Ja |
| [Skapa jobb](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Ja |
| [Hämta jobb](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Ja |
| [Fråga jobb](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Ja |

## <a name="message-throughput"></a>Dataflöde för meddelanden

Det bästa sättet att ändra storlek på en IoT Hub-lösning är att utvärdera trafiken på basis av per enhet. Tänk särskilt krävs högsta dataflöde för följande typer av åtgärder:

* Meddelanden från enheten till molnet
* Meddelanden från moln till enhet
* Identitetsregisteråtgärder

Trafiken mäts regelbundet per enhet, inte per hubb. En nivå 1 eller 2 IoT Hub-instansen kan ha upp till 200 enheter som är kopplade till den. En IoT-hubb på nivå 3-instans kan ha upp till 10 enheter. Du kan ändra antalet enheter eller flytta mellan 1, 2 och 3 storlekar på en särskild nivå utan att avbryta din befintliga åtgärder när du har skapat din IoT-hubb. Mer information finns i [uppgradera din IoT-hubb](iot-hub-upgrade.md).

Som ett exempel på varje nivå trafikfunktioner Tänk meddelanden från enheten till molnet på följande varaktig genomströmning:

| Nivå | Varaktig genomströmning | Varaktiga överföringshastighet |
| --- | --- | --- |
| B1 S1 |Upp till 1 111 KB per minut per enhet<br/>(1,5 GB per dag per enhet) |Medelvärde för 278 meddelanden per minut per enhet<br/>(400 000 meddelanden per dag per enhet) |
| B2, S2 |Upp till 16 MB per minut per enhet<br/>(22,8 GB per dag per enhet) |Medelvärde för 4,167 meddelanden per minut per enhet<br/>(6 miljoner meddelanden per dag per enhet) |
| B3, S3 |Upp till 814 MB per minut per enhet<br/>(1144.4 GB per dag per enhet) |Medelvärde för 208,333 meddelanden per minut per enhet<br/>(300 miljoner meddelanden per dag per enhet) |

Utöver den här dataflödesinformationen, se [IoT Hub-kvoter och begränsningar] [ IoT Hub quotas and throttles] och utforma din lösning på lämpligt sätt.

### <a name="identity-registry-operation-throughput"></a>Identitet registret åtgärden dataflöde
IoT Hub identitetsregisteråtgärder är inte ska vara körning åtgärder, eftersom de främst är relaterade till enhetsetablering.

Specifika burst prestandasiffror Se [IoT Hub-kvoter och begränsningar][IoT Hub quotas and throttles].

## <a name="auto-scale"></a>Autoskala
Om du närmar gräns för antalet tillåtna meddelanden på din IoT-hubb, kan du använda dessa [steg för att automatiskt skala](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) att räkna upp en enhet, IoT Hub i samma nivå för IoT Hub.

## <a name="sharding"></a>Horisontell partitionering
Även om en enda IoT-hubb kan skala till miljoner enheter, kräver ibland din lösning specifika prestandaegenskaper som en enda IoT-hubb inte kan garantera. I så fall kan du partitionera dina enheter över flera IoT-hubbar. Flera IoT-hubbar jämna trafikökningar och hämta nödvändiga dataflöde eller åtgärden avgifter som krävs.

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub funktioner och prestandainformation finns i [IoT Hub priser] [länk priser] eller [IoT Hub-kvoter och begränsningar][IoT Hub quotas and throttles].
* Om du vill ändra nivå för IoT Hub följer du stegen i [uppgradera din IoT-hubb](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
