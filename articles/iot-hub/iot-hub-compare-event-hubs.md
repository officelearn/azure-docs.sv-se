---
title: Jämför Azure IoT Hub med Azure Event Hubs | Microsoft-dokument
description: En jämförelse av Azure-tjänsterna i IoT Hub och Event Hubs som belyser funktionella skillnader och användningsfall. Jämförelsen omfattar protokoll som stöds, enhetshantering, övervakning och filöverföringar.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7aa59f8181ad60b3d43846a3f4f1f471a050b238
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733445"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Ansluta IoT-enheter till Azure: IoT Hub och Event Hubs

Azure tillhandahåller tjänster som är särskilt utvecklade för olika typer av anslutning och kommunikation som hjälper dig att ansluta dina data till molnets kraft. Både Azure IoT Hub och Azure Event Hubs är molntjänster som kan inta stora mängder data och bearbeta eller lagra dessa data för affärsinsikter. De två tjänsterna är likartade genom att de båda stöder inmatning av data med låg latens och hög tillförlitlighet, men de är utformade för olika ändamål. IoT Hub har utvecklats för att hantera de unika kraven för att ansluta IoT-enheter till Azure-molnet medan Event Hubs har utformats för stordatastreaming. Microsoft rekommenderar att du använder Azure IoT Hub för att ansluta IoT-enheter till Azure

Azure IoT Hub är molngatewayen som ansluter IoT-enheter för att samla in data och driva affärsinsikter och automatisering. Dessutom innehåller IoT Hub funktioner som berikar relationen mellan dina enheter och dina backend-system. Dubbelriktade kommunikationsfunktioner innebär att du även kan skicka tillbaka kommandon och principer till enheter medan du får data från enheter. Du kan till exempel använda meddelanden från molnet till enheten för att uppdatera egenskaper eller anropa enhetshanteringsåtgärder. Kommunikation mellan molnet och enheten gör det också möjligt att skicka molninformation till dina edge-enheter med Azure IoT Edge. Den unika identiteten på enhetsnivå som tillhandahålls av IoT Hub hjälper dig att bättre skydda din IoT-lösning från potentiella attacker. 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) är Azures tjänst för direktuppspelning av stordata. Den är utformad för scenarier med högt dataflöde där kunder kan skicka miljontals begäranden per dag. Event Hubs använder en partitionerad konsumentmodell för att skala ut strömmen och är integrerad i stordata- och analystjänsterna i Azure, till exempel Databricks, Stream Analytics, ADLS och HDInsight. Med funktioner som Capture Event Hubs och Auto-Inflate är den här tjänsten utformad för att stödja dina stordataappar och lösningar. Dessutom använder IoT Hub Event Hubs för sin telemetriflödessökväg, så din IoT-lösning drar också nytta av den enorma kraften i Event Hubs.

Sammanfattningsvis är båda lösningarna utformade för datainmatning i stor skala. Endast IoT Hub tillhandahåller de omfattande IoT-specifika funktioner som är utformade för att maximera affärsvärdet för att ansluta dina IoT-enheter till Azure-molnet.  Om din IoT-resa har precis börjat, kommer du från och med IoT Hub att stödja dina scenarier för datainmatning att du har omedelbar åtkomst till de fullständiga IoT-funktionerna när ditt företag och tekniska behov kräver dem.

I följande tabell finns information om hur de två nivåerna i IoT Hub jämför med eventhubbar när du utvärderar dem för IoT-funktioner. Mer information om standard- och grundläggande nivåer i IoT Hub finns i [Så här väljer du rätt IoT Hub-nivå](iot-hub-scaling.md).

| IoT-kapacitet | Standardnivå för IoT Hub | Grundläggande nivå för IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Meddelanden från enhet till moln | ![Markera][checkmark] | ![Markera][checkmark] | ![Markera][checkmark] |
| Protokoll: HTTPS, AMQP, AMQP över webSockets | ![Markera][checkmark] | ![Markera][checkmark] | ![Markera][checkmark] |
| Protokoll: MQTT, MQTT över webSockets | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Identitet per enhet | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Filuppladdning från enheter | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Tjänst för etablering av enhet | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Meddelanden från molnet till enheten | ![Markera][checkmark] |  |  |
| Hantering av enhetstvilling och enhet | ![Markera][checkmark] |  |  |
| Enhetsströmmar (förhandsgranskning) | ![Markera][checkmark] |  |  |
| IoT Edge | ![Markera][checkmark] |  |  |

Även om det enda användningsfallet är datainmatning från enhet till moln rekommenderar vi starkt att du använder IoT Hub eftersom det tillhandahåller en tjänst som är utformad för IoT-enhetsanslutning. 

### <a name="next-steps"></a>Nästa steg

Mer information om hur du utforskar funktionerna i IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
