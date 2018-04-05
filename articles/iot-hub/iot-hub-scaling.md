---
title: Azure IoT-hubb skalning | Microsoft Docs
description: Så här skalar du din IoT-hubb för att stödja din förväntade genomströmningen och önskade funktioner. Innehåller en sammanfattning av dataflödet som stöds för varje nivå och alternativ för delning.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3237ee41f8596ed3ce508857adf7dc29cee1ada
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Välj rätt IoT-hubb nivån för din lösning

Alla IoT-lösningen är olika, så Azure IoT Hub erbjuder flera alternativ baserat på priser och skalning. Den här artikeln är avsedd att hjälpa dig att utvärdera dina behov för IoT-hubb. Prisinformation om IoT-hubb nivåer finns i [IoT-hubb priser](https://azure.microsoft.com/pricing/details/iot-hub). 

Om du vill välja vilken nivå av IoT-hubb som passar din lösning, fråga dig själv två:

**Vilka funktioner planerar för att använda?**
Azure IoT-hubb erbjuder två nivåer, basic och standard, som skiljer sig åt i antalet funktioner de stöder. Om din IoT-lösning baserad runt samla in data från enheter och analysera den centralt är den grundläggande nivån förmodligen rätt för dig. Om du vill använda mer avancerade konfigurationer att fjärrstyra IoT-enheter eller distribuera vissa av dina arbetsbelastningar till själva enheterna bör du överväga standardnivån. En detaljerad analys av vilka funktioner som ingår i varje nivå fortsätta [nivåerna Basic och standard](#basic-and-standard-tiers).

**Hur mycket data I tänker flytta dagligen?**
Varje nivå i IoT-hubb finns i tre storlekar, baserade runt dataflöde för hur mycket data som de kan hantera i en viss dag. Dessa storlekar identifieras numeriskt som 1, 2 och 3. Varje enhet av en nivå 1 IoT-hubb kan hantera 400 tusen meddelanden per dag, medan en nivå 3-enhet kan hantera 300 miljoner. Mer information om riktlinjerna som data kan fortsätta [genomströmningen](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Nivåerna Basic och standard

Standardnivån för IoT-hubb kan du använda alla funktioner och krävs för IoT-lösningar som vill kunna använda funktioner för dubbelriktad kommunikation. Den grundläggande nivån kan en delmängd av funktionerna och är avsedd för IoT-lösningar som behöver bara enkelriktad kommunikation från enheter till molnet. Båda nivåerna har samma funktioner för säkerhet och autentisering.

När du har skapat din IoT-hubb som du kan uppgradera från den grundläggande nivån till standardnivån om utan att avbryta din befintliga drift. Mer information finns i [uppgradera din IoT-hubb](iot-hub-upgrade.md).

| Funktion | Basic-nivå | Standardnivå |
| ---------- | ---------- | ------------- |
| [Enhet till moln telemetri](iot-hub-devguide-messaging.md) | Ja | Ja |
| [Identitet per enhet](iot-hub-devguide-identity-registry.md) | Ja | Ja |
| [Meddelanderoutning](iot-hub-devguide-messages-read-custom.md) och [händelse rutnätet integrering](iot-hub-event-grid.md) | Ja | Ja |
| [HTTP- och AMQP MQTT protokoll](iot-hub-devguide-protocols.md) | Ja | Ja |
| [Etablering av tjänst](../iot-dps/about-iot-dps.md) | Ja | Ja |
| [Övervakning och diagnostik](iot-hub-monitor-resource-health.md) | Ja | Ja |
| [Meddelanden moln till enhet](iot-hub-devguide-c2d-guidance.md) |   | Ja |
| [Enheten twins](iot-hub-devguide-device-twins.md) och [enhetshantering](iot-hub-device-management-overview.md) |   | Ja |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Ja |

IoT-hubb erbjuder också en kostnadsfri nivå som är avsedd för testning och utvärdering. Det har alla funktioner av standardnivån, men begränsad meddelanden ersättning. Du kan inte uppgradera från den kostnadsfria nivån till basic eller standard. 

### <a name="iot-hub-rest-apis"></a>REST API:er för IoT Hub

Skillnaden i funktioner som stöds mellan nivåerna basic och standard IoT-hubb innebär att vissa API-anrop inte fungerar med nav grundläggande nivån. I följande tabell visas vilka API: er är tillgängliga: 

| API | Basnivå | Standardnivå |
| --- | ---------- | ------------- |
| [Ta bort enheten](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) | Ja | Ja |
| [Hämta enhet](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) | Ja | Ja |
| [Få statistik för registret](https://docs.microsoft.com/rest/api/iothub/deviceapi/getregistrystatistics) | Ja | Ja |
| [Hämta statistik för tjänster](https://docs.microsoft.com/rest/api/iothub/deviceapi/getservicestatistics) | Ja | Ja |
| [Placera enheten](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | Ja | Ja |
| [Fråga om enheter](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | Ja | Ja |
| [Skapa filöverföringen SAS-URI](https://docs.microsoft.com/rest/api/iothub/httpruntime/createfileuploadsasuri) | Ja | Ja |
| [Enheten bunden meddelande](https://docs.microsoft.com/rest/api/iothub/httpruntime/receivedeviceboundnotification) | Ja | Ja |
| [Skicka enhetshändelse](https://docs.microsoft.com/rest/api/iothub/httpruntime/senddeviceevent) | Ja | Ja |
| [Uppdatera filen Överföringsstatusen](https://docs.microsoft.com/rest/api/iothub/httpruntime/updatefileuploadstatus) | Ja | Ja |
| [Enheten massåtgärd](https://docs.microsoft.com/rest/api/iothub/deviceapi/bulkdeviceoperation) | Ja, förutom IoT kant-funktioner | Ja | 
| [Rensa kommandot kön](https://docs.microsoft.com/rest/api/iothub/deviceapi/purgecommandqueue) |   | Ja |
| [Hämta enheten dubbla](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/getdevicetwin) |   | Ja |
| [Anropa metoden för enhet](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/invokedevicemethod) |   | Ja |
| [Uppdatera enheten dubbla](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/updatedevicetwin) |   | Ja | 
| [Avbryt enheten bunden meddelande](https://docs.microsoft.com/rest/api/iothub/httpruntime/abandondeviceboundnotification) |   | Ja |
| [Fullständig enhet bunden meddelande](https://docs.microsoft.com/rest/api/iothub/httpruntime/completedeviceboundnotification) |   | Ja |
| [Avbryta jobb](https://docs.microsoft.com/rest/api/iothub/jobapi/canceljob) |   | Ja |
| [Skapa jobb](https://docs.microsoft.com/rest/api/iothub/jobapi/createjob) |   | Ja |
| [Hämta jobb](https://docs.microsoft.com/rest/api/iothub/jobapi/getjob) |   | Ja |
| [Frågan jobb](https://docs.microsoft.com/rest/api/iothub/jobapi/queryjobs) |   | Ja |

## <a name="message-throughput"></a>Genomströmningen

Det bästa sättet att ändra storlek på en IoT-hubb-lösning är att utvärdera trafiken på grundval av per enhet. I synnerhet Tänk krävs belastning genomströmning för följande typer av åtgärder:

* Meddelanden från enheten till molnet
* Meddelanden moln till enhet
* Identitetsregisteråtgärder

Trafiken mäts på grundval av per enhet, inte per hubb. En instans för nivå 1 eller 2 IoT-hubb kan ha upp till 200 enheter som är kopplade till den. En IoT-hubb nivå 3-instans kan ha upp till 10 enheter. När du har skapat din IoT-hubb kan du ändra antalet enheter eller flytta mellan 1, 2 och 3 storlekar inom en specifik nivå utan att avbryta din befintliga drift. Mer information finns i [uppgradera din IoT-hubb](iot-hub-upgrade.md).

Meddelanden från enhet till moln följa riktlinjerna varaktigt dataflöde som ett exempel på varje nivå trafik funktioner:

| Nivå | Varaktigt dataflöde | Varaktiga överföringshastighet |
| --- | --- | --- |
| B1, S1 |Upp till 1 111 KB per minut per enhet<br/>(1,5 GB/dag/unit) |Medelvärde för 278 meddelanden per minut per enhet<br/>(400 000 meddelanden/dag per enhet) |
| B2, S2 |Upp till 16 MB per minut per enhet<br/>(22,8 GB/dag/unit) |Medelvärde för 4,167 meddelanden per minut per enhet<br/>(6 miljoner meddelanden/dag per enhet) |
| B3, S3 |Upp till 814 MB per minut per enhet<br/>(1144.4 GB/dag/unit) |Medelvärde för 208,333 meddelanden per minut per enhet<br/>(300 miljoner meddelanden/dag per enhet) |

Förutom informationen genomströmning Se [IoT-hubb kvoter och begränsningar] [ IoT Hub quotas and throttles] och därefter utforma din lösning.

### <a name="identity-registry-operation-throughput"></a>Identitet registret åtgärden genomflöde
IoT-hubb identitet registret åtgärder är inte ska vara körning åtgärder, som de främst är relaterade till enhetsetableringen.

Specifika burst prestanda siffror finns [IoT-hubb kvoter och begränsningar][IoT Hub quotas and throttles].

## <a name="sharding"></a>Horisontell partitionering
När en enda IoT-hubb kan skalas för miljoner enheter, kräver ibland din lösning specifika prestandaegenskaper som en enda IoT-hubb inte kan garantera. I så fall kan du partitionera dina enheter över flera IoT-hubbar. Flera IoT-hubbar Utjämna trafiken belastning och hämta nödvändiga genomströmning eller åtgärden priser som krävs.

## <a name="next-steps"></a>Nästa steg

* Mer information om funktioner för IoT-hubb och prestandainformation finns [IoT-hubb priser] [länk priser] eller [IoT-hubb kvoter och begränsningar][IoT Hub quotas and throttles].
* Om du vill ändra din IoT-hubb-nivå, följer du stegen i [uppgradera din IoT-hubb](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
