---
title: Lösningsarkitektur
description: Lär dig mer om informations flödet i Azure Security Center för IoT-tjänsten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311757"
---
# <a name="azure-security-center-for-iot-architecture"></a>Arkitektur för Azure Security Center for IoT

I den här artikeln beskrivs den funktionella system arkitekturen i Azure Security Center för IoT-lösningen.

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center för IoT-komponenter

Azure Security Center for IoT består av följande komponenter:

- IoT Hub-integrering
- Enhets agenter (valfritt)
- Skicka Security Message SDK
- Analytics-pipeline

### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center för IoT-arbetsflöden

Azure Security Center for IoT fungerar i ett av två funktions arbets flöden: inbyggda och förbättrade

### <a name="built-in"></a>Inbyggd

I det **inbyggda** läget är Azure Security Center for IoT aktiverat när du väljer att aktivera **säkerhets** alternativet i IoT Hub. Vi erbjuder övervakning i real tid, rekommendationer och aviseringar, det inbyggda läget erbjuder en synlighet för enskilda steg och en oöverträffad säkerhet. Det krävs ingen agent installation på några enheter och det går inte att använda avancerad analys av loggade aktiviteter för att analysera och skydda fält enheten.

### <a name="enhanced"></a>Optimerad

I **utökat** läge, när du aktiverar **säkerhets** alternativet i din IoT Hub och installerat Azure Security Center för IoT-enhetens agenter på dina enheter, samlar agenterna samman, sammanställer och analyserar säkerhets händelser från dina enheter. Rå säkerhets händelser kan omfatta IP-anslutningar, process skapande, användar inloggningar och annan säkerhetsrelaterad information. Azure Security Center för IoT-enhets agenter hanterar också händelse agg regering för att undvika hög nätverks data flöde. Agenterna är mycket anpassningsbara, så att du kan använda dem för olika uppgifter, t. ex. genom att bara skicka viktig information till det snabbaste service avtalet, eller för att samla in omfattande säkerhets information och kontext i större segment, vilket kan undvika högre tjänste kostnader.

![Arkitektur för Azure Security Center for IoT](./media/architecture/azure-iot-security-architecture.png)

Enhets agenter och andra program använder Azure-funktionen för att **Skicka säkerhets meddelande-SDK** för att skicka säkerhets information till Azure IoT Hub. IoT Hub hämtar informationen och vidarebefordrar den till Azure Security Center för IoT-tjänsten.

När Azure Security Center för IoT-tjänsten är aktive rad, förutom vidarebefordrade data, så skickar IoT Hub även alla interna data för analys med Azure Security Center för IoT. Dessa data omfattar enhets moln åtgärds loggar, enhets identiteter och nav konfiguration. All den här informationen hjälper dig att skapa Azure Security Center för IoT Analytics-pipeline.

Azure Security Center för IoT Analytics-pipeline tar också emot ytterligare hot informations strömmar från olika källor i Microsoft-och Microsoft-partner. Pipelinen Azure Security Center for IoT hela Analytics fungerar med varje kund konfiguration som gjorts på tjänsten (till exempel anpassade aviseringar och användning av SDK: n för säkerhets meddelanden).

Med hjälp av en Analytics-pipeline kombinerar Azure Security Center for IoT alla data strömmar för att skapa rekommendationer och aviseringar som kan åtgärdas. Pipelinen innehåller både anpassade regler som skapats av säkerhets forskare och experter, samt Machine Learning-modeller som söker efter avvikelser från standardenhets beteende och risk analys.

Azure Security Center för IoT-rekommendationer och-varningar (analys av pipeline-utdata) skrivs till arbets ytan Log Analytics för varje kund. Att inkludera rå händelser i arbets ytan samt aviseringar och rekommendationer möjliggör djupgående undersökning och frågor med hjälp av exakt information om de misstänkta aktiviteter som upptäckts.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om den grundläggande arkitekturen och arbets flödet för Azure Security Center för IoT-lösningen. Mer information om förutsättningar, hur du kommer igång och aktiverar din säkerhetslösning i IoT Hub finns i följande artiklar:

- [Förutsättningar för tjänsten](service-prerequisites.md)
- [Komma igång](getting-started.md)
- [Konfigurera lösningen](quickstart-configure-your-solution.md)
- [Aktivera säkerhet i IoT Hub](quickstart-onboard-iot-hub.md)
- [Vanliga frågor och svar om Azure Security Center för IoT](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT-säkerhetsaviseringar](concept-security-alerts.md)
