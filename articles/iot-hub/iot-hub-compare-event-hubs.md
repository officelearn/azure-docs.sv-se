---
title: Jämföra Azure IoT-hubb till Azure Event Hubs | Microsoft Docs
description: En jämförelse av tjänsterna IoT-hubb och Event Hubs Azure syntaxmarkering funktionella skillnader och användningsfall. Jämförelse innehåller protokoll som stöds, hantering, övervakning, och filöverföringar.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Jämförelse mellan Azure IoT-hubb och Azure Event Hubs

Både Azure IoT Hub och Azure Event Hubs är molntjänster som kan mata in stora mängder data och bearbeta eller lagra data för affärsinsikter. De två tjänsterna är liknande eftersom de båda stöder bearbeta händelse- och data med låg latens och hög tillförlitlighet. IoT-hubb utvecklades men med specifika funktioner som behövs för att stödja med skalning internet saker scenarier. 

Azure IoT-hubb är molngatewayen som ansluter enheter och samlar in data för affärsinsikter och automatisering. Gör det lätt att strömma data i molnet och hantera dina enheter i större skala. En viktiga fördelar mellan IoT-hubb och andra tjänster för införandet av data är att IoT-hubb innehåller funktioner som utöka relationen mellan dina enheter och backend-system. Funktioner för dubbelriktad kommunikation innebär att när du tar emot data från enheter du kan också skicka tillbaka till enheter för att uppdatera egenskaper eller anropa en åtgärd. Enhetsnivå identitet hjälper till att skydda datorn. Distribuerad databehandling flyttar cloud service logik på enheter.

[Händelsehubbar i Azure] [ Azure Event Hubs] är en tjänst för införandet av händelse som kan bearbeta och lagra stora mängder data och telemetri. Händelsehubbar är avsedd för händelsen införandet i massiv skala, både i samband med bland datacentret och intra-datacenter scenarier men innehåller inte omfattande IoT-specifika funktioner som är tillgängliga med IoT-hubben. Därför rekommenderar vi inte Händelsehubbar för IoT-lösningar. 

Följande tabell innehåller information om hur två nivåer av IoT-hubb Jämför med Händelsehubbar när du utvärderar dem för IoT-funktioner. Mer information om standard- och basic-nivåerna för IoT-hubb finns [hur du väljer rätt IoT-hubb nivån][lnk-scaling].

| IoT-funktion | Standardnivån för IoT-hubb | IoT-hubb grundläggande nivån | Event Hubs |
| --- | --- | --- | --- |
| Meddelanden enhet till moln | ![Kontrollera][1] | ![Kontrollera][1] | ![Kontrollera][1] |
| Protokoll: HTTPS, AMQP, AMQP över websockets | ![Kontrollera][1] | ![Kontrollera][1] | ![Kontrollera][1] |
| Protokoll: MQTT, MQTT över websockets | ![Kontrollera][1] | ![Kontrollera][1] |  |
| Identitet per enhet | ![Kontrollera][1] | ![Kontrollera][1] |  |
| Ladda upp filer från enheter | ![Kontrollera][1] | ![Kontrollera][1] |  |
| Enhetsetableringstjänst | ![Kontrollera][1] | ![Kontrollera][1] |  |
| Meddelanden moln till enhet | ![Kontrollera][1] |  |  |
| Enheten dubbla och enhetshantering | ![Kontrollera][1] |  |  |
| IoT Edge | ![Kontrollera][1] |  |  |

Även om det enda användningsfallet enhet till moln datapåfyllning, rekommenderar vi med IoT-hubb som den innehåller en tjänst som är avsedd för IoT-enhetsanslutning. 

### <a name="next-steps"></a>Nästa steg

Om du vill utforska ytterligare funktionerna i IoT-hubb, finns det [utvecklarhandboken för IoT-hubb][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png