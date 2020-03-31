---
title: Översikt över lösningsacceleratorn Förutsägande underhåll – Azure | Microsoft Docs
description: En översikt över Azure IoT Predictive Maintenance-lösningsacceleratorn som förutsäger den punkt där ett fel sannolikt kommer att inträffa för ett affärsscenario.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827419"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Översikt över lösningsacceleratorn Förutsägande underhåll

Lösningsacceleratorn Förutsägande underhåll är en lösning från slutpunkt till slutpunkt för ett affärsscenario som förutsäger den punkt då det är troligt att ett fel uppstår. Du kan använda lösningsacceleratorn proaktivt för att till exempel optimera underhållet. Lösningen kombinerar viktiga Azure IoT-lösningsacceleratorer, till exempel IoT Hub och en [Azure Machine Learning-arbetsyta.][lnk-machine-learning] Den här arbetsytan innehåller en modell, baserad på en offentlig provdatauppsättning, för att förutsäga återstående driftstid (RUL) för en flygplansmotor. Lösningen implementerar IoT-affärsscenariot som startpunkt när du planerar och implementerar en lösning som uppfyller dina specifika affärskrav.

Acceleratorkoden för snabblösningskoden för förutsägande underhåll [finns på GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i lösningsacceleratorn:

![Logisk arkitektur][img-architecture]

De blå objekten är Azure-tjänster som är etablerade i den region där du etablerade lösningsacceleratorn. På [etableringssidan][lnk-azureiotsolutions] finns en lista med de regioner där du kan distribuera lösningsacceleratorn.

Det gröna objektet är en simulerad flygplansmotor. Mer information om dessa simulerade enheter finns i avsnittet [Simulerade enheter](#simulated-devices).

De grå objekten är komponenter som implementerar *enhetshanteringsfunktioner.* Den aktuella versionen av lösningsacceleratorn Förebyggande underhåll etablerar inte dessa resurser. Mer information om enhetshantering finns i [lösningsacceleratorn för fjärrövervakning][lnk-remote-monitoring].

## <a name="azure-resources"></a>Azure-resurser

I Azure Portal navigerar du till resursgruppen med det lösningsnamn som du har valt för att visa dina etablerade resurser.

![Acceleratorresurser][img-resource-group]

När du etablerar lösningsacceleratorn får du ett e-postmeddelande med en länk till Machine Learning-arbetsytan. Du kan också navigera till arbetsytan Machine Learning från sidan [Microsoft Azure IoT Solution Accelerators.][lnk-azureiotsolutions] En panel är tillgänglig på den här sidan när lösningen har statusen **Redo**.

![Maskininlärningsmodell][img-machine-learning]

## <a name="simulated-devices"></a>Simulerade enheter

I lösningsacceleratorn är en simulerad anordning en flygplansmotor. Lösningen etableras med två motorer som mappar till ett enda flygplan. Varje motor sänder fyra typer av telemetri: Sensor 9, Sensor 11, Sensor 14 och Sensor 15 tillhandahåller de data som behövs för att Machine Learning-modellen ska kunna beräkna motorns återstående driftstid. Varje simulerad enhet skickar följande telemetrimeddelanden till IoT Hub:

*Antal cykler*. En cykel är en avslutad flygning med en varaktighet mellan två och tio timmar. Telemetridata samlas in varje halvtimme under flygningens längd.

*Telemetri*. Det finns fyra sensorer som registrerar motorattribut. Sensorerna har allmänna beteckningar: Sensor 9, Sensor 11, Sensor 14 och Sensor 15. Dessa fyra sensorer skickar telemetri tillräckligt för att få användbara resultat från RUL-modellen. Modellen som används i lösningsacceleratorn har skapats från en offentlig datauppsättning som innehåller verkliga data för motorsensorer. Mer information om hur modellen har skapats från den ursprungliga datauppsättningen finns i [mallen för förebyggande underhåll i Cortana Intelligence-galleriet][lnk-cortana-analytics].

De simulerade enheterna kan hantera följande kommandon som skickas från IoT Hub i lösningen:

| Kommando | Beskrivning |
| --- | --- |
| StartTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Startar enheten som skickar telemetri |
| StopTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Stoppar enheten som skickar telemetri |

IoT Hub bekräftar enhetskommandona.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-jobb

**Jobb: Telemetri** fungerar på telemetriströmmen för inkommande enhet med hjälp av två satser:

* Det första uttrycket väljer all telemetri från enheterna och skickar dessa data till Blob Storage. Härifrån visualiseras den i webbappen.
* Den andra instruktionen beräknar genomsnittliga sensorvärden under en glidande tvåminutersperiod och skickar dem via händelsehubben till en **händelseprocessor**.

## <a name="event-processor"></a>Händelseprocessor
**Händelseprocessorvärden** körs i ett Azure-webbjobb. **Händelseprocessorn** tar de genomsnittliga sensorvärdena från en slutförd cykel. Den skickar sedan dessa värden till en tränad modell som beräknar RUL för en motor. Ett API ger åtkomst till modellen på en Machine Learning-arbetsyta som är en del av lösningen.

## <a name="machine-learning"></a>Machine Learning
Machine Learning-komponenten använder en modell som härletts från de data som samlats in från verkliga luftfartygsmotorer. Du kan navigera till arbetsytan Maskininlärning från panelen för lösningen på [sidan azureiotsolutions.com.][lnk-azureiotsolutions] Panelen är tillgänglig när lösningen har statusen **Redo**.

Machine Learning-modellen är tillgänglig som en mall som visar hur du arbetar med telemetri som samlas in via IoT-lösningsacceleratortjänster. Microsoft har byggt en [regressionsmodell][lnk_regression_model] av en flygplansmotor baserat på offentligt tillgängliga data<sup>\[\]1</sup>och steg-för-steg-vägledning om hur du använder modellen.

Azure IoT-lösningsacceleratorn Förutsägande underhåll använder regressionsmodellen som skapats från den här mallen. Modellen distribueras till din Azure-prenumeration och görs tillgänglig via ett automatiskt genererat API. Lösningen innehåller en delmängd av testdata för 4 (av totalt 100) motorer och 4 (av totalt 21) sensordataströmmar. Dessa data är tillräckliga för att tillhandahålla ett korrekt resultat från Trained Model.

*\[1\] A. Saxena och K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Nästa steg
Nu när du har sett huvudkomponenterna i lösningsacceleratorn Förebyggande underhåll kanske du vill anpassa den.

Du kan också utforska några av de andra funktionerna i IoT-lösningsacceleratorer:

* [Vanliga frågor och svar om IoT-lösningsacceleratorer][lnk-faq]
* [IoT-säkerhet från grunden][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
