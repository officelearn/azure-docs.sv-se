---
title: Jämföra Azure IoT-hubb till Azure Event Hubs | Microsoft Docs
description: En jämförelse av tjänsterna IoT-hubb och Event Hubs Azure syntaxmarkering funktionella skillnader och användningsfall. Jämförelse innehåller protokoll som stöds, hantering, övervakning, och filöverföringar.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 9ad95071de07777e38533ecec9e8558841d8b1ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633969"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT-enheter ansluter till Azure: IoT-hubb och Händelsehubbar

Azure tillhandahåller tjänster som har utvecklats specifikt för olika typer av anslutningen och kommunikation som hjälper dig att ansluta dina data till kraften i molnet. Både Azure IoT Hub och Azure Event Hubs är molntjänster som kan mata in stora mängder data och bearbeta eller lagra data för affärsinsikter. De två tjänsterna är liknande eftersom de båda stöder införandet av data med låg latens och hög tillförlitlighet, men de har utformats för olika ändamål. IoT-hubben har utvecklats specifikt för att åtgärda de specifika behoven för IoT-enheter, med skalning, ansluta till Azure-molnet medan Händelsehubbar har utformats för stordata strömning. Det är därför som Microsoft rekommenderar att du använder Azure IoT Hub ansluta IoT-enheter till Azure

Azure IoT-hubb är molngatewayen som ansluter IoT-enheter för att samla in data till enheten affärsinsikter och automatisering. Dessutom innehåller IoT-hubb funktioner som utöka relationen mellan dina enheter och backend-system. Dubbelriktad kommunikation funktionerna kan tillhandahålla innebär att när du tar emot data från enheter du kan också skicka kommandon och principer till enheter, till exempel till uppdatera egenskaper eller anropa åtgärder för hantering av enheter.  Den här anslutningen moln till enhet används också viktig funktion för att leverera molnet intelligence till dina enheter med Azure IoT kant. Unik enhetsnivå identitet tillhandahålls av IoT-hubb hjälper dig bättre skydda din IoT-lösning från angrepp. 

[Händelsehubbar i Azure] [ Azure Event Hubs] är stordata streaming-tjänsten för Azure. Den är utformad för hög genomströmning data strömmande scenarier där kunder kan skicka miljarder förfrågningar per dag. Händelsehubbar använder en modell för partitionerade konsumenten för att skala upp din ström och är integrerad i stordata och Analystjänster Azure inklusive Databricks, Stream Analytics, ADLS och HDInsight. Tjänsten är utformad att stödja dina stordata appar och lösningar med funktioner som Event Hubs avbilda och ökar automatiskt. IoT-hubb utnyttjar dessutom Händelsehubbar för telemetri flödet sökvägen, så IoT-lösningen även fördelar från enorm kraften i Händelsehubbar.

Sammanfattningsvis, när båda lösningarna är utformade för datapåfyllning i massiv skala, tillhandahåller IoT-hubb omfattande IoT-specifika funktioner som är utformade att maximera värdet av IoT-enheter som ansluter till Azure-molnet.  Om din IoT-transporten bara börjar garanterar från och med IoT-hubb för att stödja dina data införandet scenarier att du har tillgång till komplett IoT-funktioner när du affärsmässiga och tekniska behöver dem.

Följande tabell innehåller information om hur två nivåer av IoT-hubb Jämför med Händelsehubbar när du utvärderar dem för IoT-funktioner. Mer information om standard- och basic-nivåerna för IoT-hubb finns [hur du väljer rätt IoT-hubb nivån][lnk-scaling].

| IoT-funktion | Standardnivån för IoT-hubb | IoT-hubb grundläggande nivån | Event Hubs |
| --- | --- | --- | --- |
| Meddelanden enhet till moln | ![Markera][1] | ![Markera][1] | ![Markera][1] |
| Protokoll: HTTPS, AMQP, AMQP över webSockets | ![Markera][1] | ![Markera][1] | ![Markera][1] |
| Protokoll: MQTT, MQTT över webSockets | ![Markera][1] | ![Markera][1] |  |
| Identitet per enhet | ![Markera][1] | ![Markera][1] |  |
| Ladda upp filer från enheter | ![Markera][1] | ![Markera][1] |  |
| Enhetsetableringstjänst | ![Markera][1] | ![Markera][1] |  |
| Meddelanden från moln till enhet | ![Markera][1] |  |  |
| Enheten dubbla och enhetshantering | ![Markera][1] |  |  |
| IoT Edge | ![Markera][1] |  |  |

Även om det enda användningsfallet enhet till moln datapåfyllning, rekommenderar vi med IoT-hubb som den innehåller en tjänst som är avsedd för IoT-enhetsanslutning. 

### <a name="next-steps"></a>Nästa steg

Om du vill utforska ytterligare funktionerna i IoT-hubb, finns det [utvecklarhandboken för IoT-hubb][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png
