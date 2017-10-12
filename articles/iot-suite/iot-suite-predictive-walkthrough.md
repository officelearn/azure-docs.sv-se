---
title: "Genomgång av förebyggande underhåll | Microsoft Docs"
description: "En genomgång av den förkonfigurerade lösningen för förebyggande underhåll i Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: a68a8fdc3976ade0d1036d5ed58c8b2eb6d32a5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Genomgång av den förkonfigurerade lösningen för förebyggande underhåll

Den förkonfigurerade lösningen för förutsägande underhåll är en lösning från slutpunkt till slutpunkt för ett affärsscenario som beräknar den punkt då det är troligt att ett fel uppstår. Du kan använda denna förkonfigurerade lösning proaktivt för aktiviteter, till exempel för att optimera underhåll. Lösningen kombinerar viktiga tjänster i Azure IoT Suite, till exempel IoT Hub, Stream analytics och en [Azure Machine Learning][lnk-machine-learning]-arbetsyta. Den här arbetsytan innehåller en modell, baserad på en offentlig provdatauppsättning, för att förutsäga återstående driftstid (RUL) för en flygplansmotor. Lösningen implementerar IoT-affärsscenariot som startpunkt när du planerar och implementerar en lösning som uppfyller dina specifika affärskrav.

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i den förkonfigurerade lösningen:

![][img-architecture]

De blå objekten är Azure-tjänster som är etablerade i den region där du etablerade den förkonfigurerade lösningen. På [etableringssidan][lnk-azureiotsuite] finns en lista över de regioner där du kan distribuera den förkonfigurerade lösningen.

Det gröna objektet är en simulerad enhet som representerar en flygplansmotor. Mer information om dessa simulerade enheter finns i följande avsnitt.

De grå objekten representerar komponenter som implementerar funktioner för *enhetshantering*. Den aktuella versionen av den förkonfigurerade lösningen för förebyggande underhåll etablerar inte dessa resurser. Mer information om enhetsadministration finns i dokumentationen för [den förkonfigurerade lösningen för fjärrövervakning][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Simulerade enheter

I den förkonfigurerade lösningen representerar en simulerad enhet en flygplansmotor. Lösningen etableras med två motorer som mappar till ett enda flygplan. Varje motor sänder fyra typer av telemetri: Sensor 9, Sensor 11, Sensor 14 och Sensor 15 tillhandahåller de data som behövs för att Machine Learning-modellen ska kunna beräkna motorns återstående driftstid. Varje simulerad enhet skickar följande telemetrimeddelanden till IoT Hub:

*Antal cykler*. En cykel representerar en slutförd flygning med varierande längd på mellan 2–10 timmar. Telemetridata samlas in varje halvtimme under flygningens längd.

*Telemetri*. Det finns fyra sensorer som representerar motorattribut. Sensorerna har allmänna beteckningar: Sensor 9, Sensor 11, Sensor 14 och Sensor 15. Dessa fyra sensorer representerar den mängd telemetri som krävs för att få fram användbara resultat från RUL-modellen. Modellen som används i den förkonfigurerade lösningen har skapats från en offentlig datauppsättning som innehåller verkliga data från motorsensorer. Mer information om hur modellen har skapats från den ursprungliga datauppsättningen finns i [mallen för förebyggande underhåll i Cortana Intelligence-galleriet][lnk-cortana-analytics].

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
Machine Learning-komponenten använder en modell som härletts från de data som samlats in från verkliga luftfartygsmotorer. Du kan navigera till Machine Learning-arbetsytan från ikonen på sidan [azureiotsuite.com][lnk-azureiotsuite] för din etablerade lösning. Panelen är tillgänglig när lösningen har statusen **Redo**.


## <a name="next-steps"></a>Nästa steg
Nu när du har sett huvudkomponenterna i den förkonfigurerade lösningen för förebyggande underhåll kanske du vill anpassa den. Se [Vägledning för anpassning av förkonfigurerade lösningar][lnk-customize].

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

* [Vanliga frågor och svar om IoT Suite][lnk-faq]
* [IoT-säkerhet från grunden][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/