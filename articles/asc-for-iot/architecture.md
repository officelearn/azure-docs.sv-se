---
title: Så här fungerar Azure Security Center för IoT-lösningsarkitektur| Microsoft-dokument
description: Lär dig mer om informationsflödet i Azure Security Center for IoT-tjänsten.
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
ms.openlocfilehash: 2e55f98f8c7b6ddbc21f7ea8633467461ea5be29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922168"
---
# <a name="azure-security-center-for-iot-architecture"></a>Arkitektur för Azure Security Center for IoT

I den här artikeln beskrivs den funktionella systemarkitekturen för Azure Security Center for IoT-lösningen. 

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center för IoT-komponenter

Azure Security Center för IoT består av följande komponenter:
- Integrering av IoT-hubb
- Enhetsagenter (tillval)
- Skicka säkerhetsmeddelande SDK
- Analytics-pipeline
 
### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center för IoT-arbetsflöden

Azure Security Center för IoT fungerar i ett av två funktionsarbetsflöden: Inbyggd och Förbättrad  

### <a name="built-in"></a>Inbyggd
I **inbyggt** läge aktiveras Azure Security Center för IoT när du väljer att aktivera **alternativet Säkerhet** i din IoT Hub. I realtidsövervakning, rekommendationer och varningar erbjuder inbyggt läge synlighet i ett steg och oöverträffad säkerhet. Inbyggt läge kräver inte agentinstallation på några enheter och använder avancerad analys av loggade aktiviteter för att analysera och skydda din fältenhet. 

### <a name="enhanced"></a>Optimerad 
I **utökat** läge, efter att ha aktiverat **alternativet Säkerhet** i din IoT-hubb och installerat Azure Security Center för IoT-enhetsagenter på dina enheter, samlar agenterna in, sammanställer och analyserar råa säkerhetshändelser från dina enheter. Råa säkerhetshändelser kan omfatta IP-anslutningar, processskapande, användarinloggningar och annan säkerhetsrelevant information. Azure Security Center för IoT-enhetsagenter hanterar också händelseaggregering för att undvika högt nätverksdataflöde. Agenterna är mycket anpassningsbara, så att du kan använda dem för specifika uppgifter, till exempel att bara skicka viktig information på det snabbaste SLA,eller för att samla omfattande säkerhetsinformation och kontext i större segment, vilket undviker högre servicekostnader.

![Arkitektur för Azure Security Center for IoT](./media/architecture/azure-iot-security-architecture.png)
 
Enhetsagenter och andra program använder **Azure skicka säkerhetsmeddelande SDK** för att skicka säkerhetsinformation till Azure IoT Hub. IoT Hub hämtar den här informationen och vidarebefordrar den till Azure Security Center for IoT-tjänsten.

När Azure Security Center for IoT-tjänsten är aktiverad, förutom vidarebefordrade data, skickar IoT Hub också ut alla sina interna data för analys av Azure Security Center för IoT. Dessa data omfattar enhetsmolnsoperationsloggar, enhetsidentiteter och hubbkonfiguration. All den här informationen hjälper till att skapa Azure Security Center för IoT-analyspipeline.
 
Azure Security Center för IoT analytics pipeline får också ytterligare hotinformationsströmmar från olika källor inom Microsoft och Microsoft-partner. Azure Security Center för IoT hela analytics pipeline fungerar med varje kundkonfiguration som görs på tjänsten (till exempel anpassade aviseringar och användning av skicka säkerhetsmeddelandeT SDK).
 
Med hjälp av analytics pipeline, Azure Security Center för IoT kombinerar alla strömmar av information för att generera användbara rekommendationer och aviseringar. Pipelinen innehåller både anpassade regler som skapats av säkerhetsforskare och experter samt maskininlärningsmodeller som söker efter avvikelser från standardenhetens beteende och riskanalys.
 
Azure Security Center för IoT-rekommendationer och aviseringar (analyspipellineutdata) skrivs till log analytics-arbetsytan för varje kund. Inklusive råhändelser på arbetsytan samt aviseringar och rekommendationer möjliggör djupdykning undersökningar och frågor med hjälp av exakta uppgifter om misstänkta aktiviteter som identifierats.  

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om den grundläggande arkitekturen och arbetsflödet för Azure Security Center for IoT-lösning. Mer information om förutsättningar finns i följande artiklar:

- [Förutsättningar för tjänsten](service-prerequisites.md)
- [Komma igång](getting-started.md)
- [Konfigurera lösningen](quickstart-configure-your-solution.md)
- [Aktivera säkerhet i IoT Hub](quickstart-onboard-iot-hub.md)
- [Vanliga frågor och svar om Azure Security Center för IoT](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT-säkerhetsaviseringar](concept-security-alerts.md)
