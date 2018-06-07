---
title: Genomgång av lösningsacceleratorn Förutsägande underhåll – Azure | Microsoft Docs
description: En genomgång av lösningsacceleratorn Förutsägande underhåll i Azure IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: e29975558801b4ffccd38d4485306d25ecaec0aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655503"
---
# <a name="predictive-maintenance-solution-accelerator-walkthrough"></a>Genomgång av lösningsacceleratorn Förutsägande underhåll

Lösningsacceleratorn Förutsägande underhåll är en lösning från slutpunkt till slutpunkt för ett affärsscenario som förutsäger den punkt då det är troligt att ett fel uppstår. Du kan använda lösningsacceleratorn proaktivt för att till exempel optimera underhållet. Lösningen kombinerar viktiga tjänster i Azure IoT-lösningsaccelerator, till exempel IoT Hub, Stream Analytics och en [Azure Machine Learning][lnk-machine-learning]-arbetsyta. Den här arbetsytan innehåller en modell, baserad på en offentlig provdatauppsättning, för att förutsäga återstående driftstid (RUL) för en flygplansmotor. Lösningen implementerar IoT-affärsscenariot som startpunkt när du planerar och implementerar en lösning som uppfyller dina specifika affärskrav.

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i lösningsacceleratorn:

![][img-architecture]

De blå objekten är Azure-tjänster som är etablerade i den region där du etablerade lösningsacceleratorn. På [etableringssidan][lnk-azureiotsuite] finns en lista med de regioner där du kan distribuera lösningsacceleratorn.

Det gröna objektet är en simulerad enhet som representerar en flygplansmotor. Mer information om dessa simulerade enheter finns i avsnittet [Simulerade enheter](#simulated-devices).

De grå objekten representerar komponenter som implementerar funktioner för *enhetshantering*. Den aktuella versionen av lösningsacceleratorn Förebyggande underhåll etablerar inte dessa resurser. Mer information om enhetshantering finns i den [Fjärrövervaknings lösningsaccelerator][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Simulerade enheter

I lösningsacceleratorn representerar en simulerad enhet en flygplansmotor. Lösningen etableras med två motorer som mappar till ett enda flygplan. Varje motor sänder fyra typer av telemetri: Sensor 9, Sensor 11, Sensor 14 och Sensor 15 tillhandahåller de data som behövs för att Machine Learning-modellen ska kunna beräkna motorns återstående driftstid. Varje simulerad enhet skickar följande telemetrimeddelanden till IoT Hub:

*Antal cykler*. En cykel representerar en slutförd flygning med varierande längd på mellan 2–10 timmar. Telemetridata samlas in varje halvtimme under flygningens längd.

*Telemetri*. Det finns fyra sensorer som representerar motorattribut. Sensorerna har allmänna beteckningar: Sensor 9, Sensor 11, Sensor 14 och Sensor 15. Dessa fyra sensorer representerar den mängd telemetri som krävs för att få fram användbara resultat från RUL-modellen. Modellen som används i lösningsacceleratorn har skapats från en offentlig datauppsättning som innehåller verkliga data för motorsensorer. Mer information om hur modellen har skapats från den ursprungliga datauppsättningen finns i [mallen för förebyggande underhåll i Cortana Intelligence-galleriet][lnk-cortana-analytics].

De simulerade enheterna kan hantera följande kommandon som skickas från IoT Hub i lösningen:

| Kommando | Beskrivning |
| --- | --- |
| StartTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Startar enheten som skickar telemetri |
| StopTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Stoppar enheten som skickar telemetri |

IoT Hub bekräftar enhetskommandona.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-jobb

**Jobb: Telemetri** körs på den inkommande telemetriströmmen för enheten med hjälp av två uttryck:

* Det första uttrycket väljer all telemetri från enheterna och skickar dessa data till Blob Storage. Härifrån kan de sedan visualiseras i webbappen.
* Den andra instruktionen beräknar genomsnittliga sensorvärden under en glidande tvåminutersperiod och skickar dem via händelsehubben till en **händelseprocessor**.

## <a name="event-processor"></a>Händelseprocessor
**Händelseprocessorvärden** körs i ett Azure-webbjobb. **Händelseprocessorn** tar de genomsnittliga sensorvärdena från en slutförd cykel. Den skickar dessa värden till en API som får den tränade modellen att beräkna RUL för en motor. API: et exponeras av en Machine Learning-arbetsyta som har etablerats som en del av lösningen.

## <a name="machine-learning"></a>Machine Learning
Machine Learning-komponenten använder en modell som härletts från de data som samlats in från verkliga luftfartygsmotorer. Du kan navigera till Machine Learning-arbetsytan från din lösningsikon på sidan [azureiotsuite.com][lnk-azureiotsuite]. Panelen är tillgänglig när lösningen har statusen **Redo**.


## <a name="next-steps"></a>Nästa steg
Nu när du har sett huvudkomponenterna i lösningsacceleratorn Förebyggande underhåll kanske du vill anpassa den.

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Vanliga frågor och svar om IoT-lösningsacceleratorer][lnk-faq]
* [IoT-säkerhet från grunden][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-accelerators-remote-monitoring-explore.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/