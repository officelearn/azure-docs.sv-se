---
title: Jämför Azure IoT Hub och Azure Event Hubs | Microsoft Docs
description: En jämförelse av IoT Hub och Event Hubs Azure-tjänster som fokus funktionella skillnader och användningsfall. Jämförelsen innehåller protokoll som stöds, hantering, övervakning, och filöverföringar.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 830052341c4f0e3488c8e63da59cbef1f72e158a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "42061488"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Ansluta IoT-enheter till Azure: IoT Hub och Event Hubs

Azure tillhandahåller tjänster som utvecklats särskilt för olika typer av anslutningar och kommunikation för att ansluta dina data till kraften i molnet. Både Azure IoT Hub och Azure Event Hubs är molntjänster som kan mata in stora mängder data och bearbeta eller lagra dessa data för affärsinsikter. De två tjänsterna är liknande i att de båda har stöd för inmatning av data med låg fördröjning och hög tillförlitlighet, men de har utformats för olika syften. IoT Hub har utvecklats specifikt för att åtgärda de specifika behoven för att ansluta IoT-enheter, i skala till Azure-molnet medan Event Hubs har utformats för big data som strömmas. Det är därför Microsoft rekommenderar att du använder Azure IoT Hub för att ansluta IoT-enheter till Azure

Azure IoT Hub är molngatewayen som ansluter IoT-enheter för att samla in data till öka affärsinsikterna och automatisering. Dessutom innehåller IoT Hub funktioner som berikar relationen mellan dina enheter och backend-system. Dubbelriktad kommunikation funktioner innebär att även om du tar emot data från enheter du kan också skicka kommandon och principer till enheter, till exempel att uppdatera egenskaper eller anropa åtgärder för hantering av enhet.  Den här anslutningen för moln-till-enhet används också viktig funktion för att leverera intelligenta molntjänster till edge-enheter med Azure IoT Edge. Den unika identiteten på enhetsnivå som tillhandahålls av IoT Hub hjälper att bättre skydda din IoT-lösning från eventuella attacker. 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) är big data strömningstjänst i Azure. Den är utformad för stora dataflöden data strömmande scenarier där kunder kan skicka miljontals förfrågningar per dag. Händelsehubbar använder ett konsumentmönster indelat i partitioner modell för att skala ut din ström och är integrerad i stordata- och Analystjänster i Azure som Databricks, Stream Analytics, ADLS och HDInsight. Den här tjänsten är utformad att stödja dina stordata appar och lösningar med funktioner som Event Hubs Capture och automatisk ökning. Dessutom använder IoT Hub Event Hubs för telemetri flow sökvägen, så att din IoT-lösning kan också dra nytta av enorma kraften i Event Hubs.

För att sammanfatta, även om båda lösningarna är utformade för datainmatning i massiv skala, tillhandahåller IoT-hubb avancerade IoT-specifika funktioner som är utformade att maximera värdet av att ansluta dina IoT-enheter till Azure-molnet.  Om din IoT-resa du precis har börjat, garanterar från och med IoT Hub för att stödja din scenarier för inmatning av data att du har direktåtkomst till komplett IoT-funktioner när dina affärsmässiga och tekniska behov kräver att användaren.

I följande tabell innehåller information om hur de två nivåerna av IoT Hub jämfört med Event Hubs när du utvärderar dem för IoT-funktioner. Mer information om nivåerna standard och basic för IoT Hub finns i [välja rätt nivå för IoT Hub](iot-hub-scaling.md).

| IoT-funktion | Standardnivån för IoT Hub | Basic-nivån för IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Enhet-till-moln-meddelanden | ![Markera][checkmark] | ![Markera][checkmark] | ![Markera][checkmark] |
| Protokoll: HTTPS, AMQP, AMQP via webSockets | ![Markera][checkmark] | ![Markera][checkmark] | ![Markera][checkmark] |
| Protokoll: MQTT, MQTT via webSockets | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Identitet per enhet | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Ladda upp filer från enheter | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Enhetsetableringstjänst | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Meddelanden från moln till enhet | ![Markera][checkmark] |  |  |
| Enhetstvillingen och enhetshantering | ![Markera][checkmark] |  |  |
| IoT Edge | ![Markera][checkmark] |  |  |

Även om endast användningsfall är enhet till moln för datainmatning, rekommenderar vi använda IoT Hub eftersom det ger dig en tjänst som är utformad för IoT-enhetsanslutning. 

### <a name="next-steps"></a>Nästa steg

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se den [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
