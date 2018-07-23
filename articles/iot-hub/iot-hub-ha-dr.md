---
title: Azure IoT Hub hög tillgänglighet och katastrofåterställning recovery | Microsoft Docs
description: Beskriver de funktioner för Azure och IoT Hub som hjälper dig att bygga högtillgängliga Azure IoT-lösningar med disaster recovery-funktioner.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 992c42511f7bc9e9af71ff552ee91bc2472ebcf8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185711"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub hög tillgänglighet och disaster recovery
Som en Azure-tjänst ger IoT Hub hög tillgänglighet (HA) med hjälp av uppsägningar på nivån Azure-region utan ytterligare plattformsbelastning krävs av lösningen. Microsoft Azure-plattformen innehåller också funktioner för att hjälpa dig att skapa lösningar med funktioner för katastrofåterställning (DR) eller interregionala tillgänglighet. Om du vill ange globala kan utnyttja interregionala hög tillgänglighet för enheter eller användare, dessa Azure-DR-funktioner. Artikeln [Azure Business Continuity teknisk vägledning](../resiliency/resiliency-technical-guidance.md) beskrivs de inbyggda funktionerna i Azure för affärskontinuitet och Katastrofåterställning. Den [haveriberedskap och hög tillgänglighet för Azure-program] [ Disaster recovery and high availability for Azure applications] dokumentet innehåller vägledning för arkitektur om strategier för Azure-program att uppnå hög tillgänglighet och Katastrofåterställning.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Förutom intra-region hög tillgänglighet implementerar IoT Hub redundans mekanismer för haveriberedskap som kräver ingen åtgärd från användaren. IoT Hub DR initieras lokal och har en återställningstid (RTO) 2-26 timmar och följande återställningspunktmål (Rpo):

| Funktioner | RPO-MÅL |
| --- | --- |
| Tjänsttillgänglighet för registret och kommunikation |CName förlust |
| Identitetsdata i identitetsregistret |0 – 5 minuter dataförlust |
| Meddelanden från enheten till molnet |Försvinner alla olästa meddelanden |
| Åtgärdsövervakning meddelanden |Försvinner alla olästa meddelanden |
| Meddelanden från moln till enhet |0 – 5 minuter dataförlust |
| Moln till enhet någon feedbackkö |Försvinner alla olästa meddelanden |
| Enhetsdata för enhetstvilling |0 – 5 minuter dataförlust |
| Överordnade och enheten jobb |0 – 5 minuter dataförlust |

## <a name="regional-failover-with-iot-hub"></a>Regional redundans med IoT Hub
En fullständig behandling av distributionstopologier i IoT-lösningar som ligger utanför omfånget för den här artikeln. Artikeln beskriver den *regional redundans* distributionsmodell för hög tillgänglighet och katastrofåterställning återställning.

Lösningen tillbaka slutet körs huvudsakligen i en datacenterplats i en regional redundans-modell. En sekundär IoT-hubb och backend-servrar distribueras på en annan datacenterplats. Om IoT hub i det primära datacentret drabbas av ett avbrott eller nätverksanslutning från enheten till det primära datacentret avbryts, enheter att använda en sekundär slutpunkt. Du kan förbättra tillgängligheten för lösningen genom att implementera en modell för redundans över regioner i stället för dig inom en region. 

På en hög nivå för att implementera en regional redundans-modell med IoT Hub, behöver du följande:

* **En sekundär IoT hub och enhet routning logic**: om tjänsten i din primära region avbryts enheter måste börja ansluta till din sekundära region. Den tillståndsmedveten naturen för de flesta tjänster som ingår, är det vanligt för administratörer av lösning att utlösa mellan regioner redundansprocessen. Det bästa sättet att kommunicera med den nya slutpunkten till enheter, samtidigt som de behåller kontrollen över processen är att de regelbundet kontrollera en *concierge* för den aktuella aktiva slutpunkten. Tjänsten concierge kan vara ett webbprogram som ska replikeras och sparas kan nås med hjälp av tekniker för DNS-omdirigering (till exempel [Azure Traffic Manager][Azure Traffic Manager]).
* **Replikering för Identity-registry**: kan användas, sekundära IoT-hubben måste innehålla alla enhetsidentiteter som kan ansluta till lösningen. Lösningen ska hålla geo-replikerade säkerhetskopior av enhetsidentiteter och överföra dem till den sekundära IoT-hubben innan du byter aktiv slutpunkt för enheter. Enhetens identitet exportfunktionen för IoT Hub är användbart i den här kontexten. Mer information finns i [utvecklarhandboken för en IoT Hub - identitetsregistret][IoT Hub developer guide - identity registry].
* **Sammanslagning av logic**: när den primära regionen blir tillgänglig igen, alla tillstånd och data som har skapats på den sekundära platsen måste flyttas tillbaka till den primära regionen. Den här tillstånd och data främst är relaterade till enhetsidentiteter och programmetadata, som måste slås samman med den primära IoT hub och andra programspecifika butiker i den primära regionen. För att förenkla det här steget ska du använda idempotenta åtgärder. Idempotenta åtgärder minimera sidoeffekter från eventuell konsekvent distribution av händelser och dubbletter eller out ordning leverans av händelser. Dessutom bör programlogiken utformas ska kunna hanteras eventuella inkonsekvenser eller ”något” inaktuella tillstånd. Den här situationen kan inträffa på grund av den ytterligare tid det tar för systemet kan ”reparera” baserat på återställningspunktmål (RPO).

## <a name="next-steps"></a>Nästa steg
Du kan följa dessa länkar om du vill veta mer om Azure IoT Hub:

* [Kom igång med IoT-hubbar (självstudier)][lnk-get-started]
* [Vad är Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[What is Azure IoT Hub?]: about-iot-hub.md
