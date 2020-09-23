---
title: Lösningsarkitekturen
description: Lär dig mer om informations flödet i Azure Defender for IoT-tjänsten.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d26d9e3d686ad7c34e7493dc1413b7a9e7a2f6b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90942209"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender för IoT-arkitektur

I den här artikeln beskrivs den funktionella system arkitekturen i Defender for IoT-lösningen.

## <a name="defender-for-iot-components"></a>Defender för IoT-komponenter

Defender för IoT består av följande komponenter:

- IoT Hub-integrering
- Enhets agenter (valfritt)
- Skicka Security Message SDK
- Analytics-pipeline

### <a name="defender-for-iot-workflows"></a>Defender för IoT-arbetsflöden

Defender för IoT fungerar i ett av två funktions arbets flöden: inbyggda och förbättrade

### <a name="built-in"></a>Inbyggd

I det **inbyggda** läget är Defender för IoT aktiverat när du väljer att aktivera **säkerhets** alternativet i IoT Hub. Vi erbjuder övervakning i real tid, rekommendationer och aviseringar, det inbyggda läget erbjuder en synlighet för enskilda steg och en oöverträffad säkerhet. Det krävs ingen agent installation på några enheter och det går inte att använda avancerad analys av loggade aktiviteter för att analysera och skydda fält enheten.

### <a name="enhanced"></a>Optimerad

I **Avancerat** läge, när du aktiverar **säkerhets** alternativet i din IoT Hub och installerat Defender för IoT-enhets agenter på dina enheter, samlar agenterna samman, sammanställer och analyserar säkerhets händelser från dina enheter. Rå säkerhets händelser kan omfatta IP-anslutningar, process skapande, användar inloggningar och annan säkerhetsrelaterad information. Defender för IoT-enhets agenter hanterar även händelse agg regering för att undvika hög nätverks data flöde. Agenterna är mycket anpassningsbara, så att du kan använda dem för olika uppgifter, t. ex. genom att bara skicka viktig information till det snabbaste service avtalet, eller för att samla in omfattande säkerhets information och kontext i större segment, vilket kan undvika högre tjänste kostnader.

![Defender för IoT-arkitektur](./media/architecture/azure-iot-security-architecture.png)

Enhets agenter och andra program använder Azure-funktionen för att **Skicka säkerhets meddelande-SDK** för att skicka säkerhets information till Azure IoT Hub. IoT Hub hämtar informationen och vidarebefordrar den till tjänsten Defender for IoT.

När tjänsten Defender for IoT har Aktiver ATS, förutom vidarebefordrade data, skickar IoT Hub även alla interna data för analys av Defender för IoT. Dessa data omfattar enhets moln åtgärds loggar, enhets identiteter och nav konfiguration. All den här informationen hjälper dig att skapa en pipeline för Defender för IoT Analytics.

Defender för IoT Analytics-pipeline tar också emot ytterligare hot informations strömmar från olika källor i Microsoft-och Microsoft-partner. Pipelinen Defender for IoT hela Analytics fungerar med varje kundkonfiguration som gjorts på tjänsten (till exempel anpassade aviseringar och användning av SDK: n för att skicka Security-meddelanden).

Med Analytics-pipeline kombinerar Defender för IoT alla data strömmar för att generera åtgärds bara rekommendationer och aviseringar. Pipelinen innehåller både anpassade regler som skapats av säkerhets forskare och experter, samt Machine Learning-modeller som söker efter avvikelser från standardenhets beteende och risk analys.

Defender för IoT-rekommendationer och-varningar (analys av pipeline-utdata) skrivs till Log Analytics arbets ytan för varje kund. Att inkludera rå händelser i arbets ytan samt aviseringar och rekommendationer möjliggör djupgående undersökning och frågor med hjälp av exakt information om de misstänkta aktiviteter som upptäckts.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om den grundläggande arkitekturen och arbets flödet för Defender för IoT-lösningen. Mer information om förutsättningar, hur du kommer igång och aktiverar din säkerhetslösning i IoT Hub finns i följande artiklar:

- [Förutsättningar för tjänsten](service-prerequisites.md)
- [Komma igång](getting-started.md)
- [Konfigurera lösningen](quickstart-configure-your-solution.md)
- [Aktivera säkerhet i IoT Hub](quickstart-onboard-iot-hub.md)
- [Vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)
- [Defender för IoT-säkerhetsaviseringar](concept-security-alerts.md)
