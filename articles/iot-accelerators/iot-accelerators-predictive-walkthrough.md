---
title: Översikt över lösningsacceleratorn Förutsägande underhåll – Azure | Microsoft Docs
description: En översikt över Solution Accelerator-lösningen för förebyggande underhåll i Azure IoT som förutsäger den tidpunkt då ett fel sannolikt uppstår för ett affärs scenario.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "73827419"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Översikt över lösningsacceleratorn Förutsägande underhåll

Lösningsacceleratorn Förutsägande underhåll är en lösning från slutpunkt till slutpunkt för ett affärsscenario som förutsäger den punkt då det är troligt att ett fel uppstår. Du kan använda lösningsacceleratorn proaktivt för att till exempel optimera underhållet. Lösningen kombinerar viktiga Azure IoT Solution Accelerator-tjänster, till exempel IoT Hub och en [Azure Machine Learning][lnk-machine-learning] arbets yta. Den här arbetsytan innehåller en modell, baserad på en offentlig provdatauppsättning, för att förutsäga återstående driftstid (RUL) för en flygplansmotor. Lösningen implementerar IoT-affärsscenariot som startpunkt när du planerar och implementerar en lösning som uppfyller dina specifika affärskrav.

Solution Accelerator-koden för förebyggande underhåll finns [på GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i lösningsacceleratorn:

![Logisk arkitektur][img-architecture]

De blå objekten är Azure-tjänster som är etablerade i den region där du etablerade lösningsacceleratorn. På [etableringssidan][lnk-azureiotsolutions] finns en lista med de regioner där du kan distribuera lösningsacceleratorn.

Det gröna objektet är en simulerad flyg Plans motor. Mer information om dessa simulerade enheter finns i avsnittet [Simulerade enheter](#simulated-devices).

De grå objekten är komponenter som implementerar funktioner för *enhets hantering* . Den aktuella versionen av lösningsacceleratorn Förebyggande underhåll etablerar inte dessa resurser. Mer information om enhets hantering finns i [lösnings Accelerator för fjärr styrning][lnk-remote-monitoring].

## <a name="azure-resources"></a>Azure-resurser

I Azure Portal navigerar du till resursgruppen med det lösningsnamn som du har valt för att visa dina etablerade resurser.

![Accelerator resurser][img-resource-group]

När du etablerar lösningsacceleratorn får du ett e-postmeddelande med en länk till Machine Learning-arbetsytan. Du kan också navigera till arbets ytan Machine Learning från [Microsoft Azure IoT Solution Accelerators][lnk-azureiotsolutions] -sidan. En panel är tillgänglig på den här sidan när lösningen har statusen **Redo**.

![Machine Learning-modell][img-machine-learning]

## <a name="simulated-devices"></a>Simulerade enheter

I Solution Accelerator är en simulerad enhet en flyg Plans motor. Lösningen etableras med två motorer som mappar till ett enda flygplan. Varje motor sänder fyra typer av telemetri: Sensor 9, Sensor 11, Sensor 14 och Sensor 15 tillhandahåller de data som behövs för att Machine Learning-modellen ska kunna beräkna motorns återstående driftstid. Varje simulerad enhet skickar följande telemetrimeddelanden till IoT Hub:

*Antal cykler*. En cykel är en slutförd flygning med en varaktighet på mellan två och tio timmar. Telemetridata samlas in varje halvtimme under flygningens längd.

*Telemetri*. Det finns fyra sensorer som registrerar motorns attribut. Sensorerna har allmänna beteckningar: Sensor 9, Sensor 11, Sensor 14 och Sensor 15. Dessa fyra sensorer skickar telemetri tillräckligt för att få användbara resultat från RUL-modellen. Modellen som används i lösningsacceleratorn har skapats från en offentlig datauppsättning som innehåller verkliga data för motorsensorer. Mer information om hur modellen har skapats från den ursprungliga datauppsättningen finns i [mallen för förebyggande underhåll i Cortana Intelligence-galleriet][lnk-cortana-analytics].

De simulerade enheterna kan hantera följande kommandon som skickas från IoT Hub i lösningen:

| Kommando | Beskrivning |
| --- | --- |
| StartTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Startar enheten som skickar telemetri |
| StopTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Stoppar enheten som skickar telemetri |

IoT Hub bekräftar enhetskommandona.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-jobb

**Jobb: telemetri** körs på den inkommande data strömmen för enheten med två instruktioner:

* Det första uttrycket väljer all telemetri från enheterna och skickar dessa data till Blob Storage. Härifrån är den visualiserad i webbappen.
* Den andra instruktionen beräknar genomsnittliga sensorvärden under en glidande tvåminutersperiod och skickar dem via händelsehubben till en **händelseprocessor**.

## <a name="event-processor"></a>Händelseprocessor
**Händelseprocessorvärden** körs i ett Azure-webbjobb. **Händelseprocessorn** tar de genomsnittliga sensorvärdena från en slutförd cykel. Den skickar sedan dessa värden till en utbildad modell som beräknar RUL för en motor. Ett API ger åtkomst till modellen i en Machine Learning-arbetsyta som ingår i lösningen.

## <a name="machine-learning"></a>Machine Learning
Machine Learning-komponenten använder en modell som härletts från de data som samlats in från verkliga luftfartygsmotorer. Du kan navigera till arbets ytan Machine Learning från din lösnings panel på sidan [azureiotsolutions.com][lnk-azureiotsolutions] . Panelen är tillgänglig när lösningen har statusen **Redo**.

Machine Learnings modellen är tillgänglig som en mall som visar hur du arbetar med telemetri som samlats in via IoT Solution Accelerator-tjänster. Microsoft har byggt en [Regressions modell][lnk_regression_model] av en flyg Plans motor baserat på offentligt tillgängliga data<sup> \[ \] 1</sup>och stegvisa anvisningar om hur du använder modellen.

Azure IoT-lösningsacceleratorn Förutsägande underhåll använder regressionsmodellen som skapats från den här mallen. Modellen distribueras till din Azure-prenumeration och görs tillgänglig via en automatiskt genererad API. Lösningen innehåller en delmängd av test data för 4 (av 100 totala) motorer och 4 (av totalt 21) data strömmar för sensor. Dessa data är tillräckliga för att tillhandahålla ett korrekt resultat från Trained Model.

*\[1 \] A. Saxena och K. Goebel (2008). Data uppsättningen "turbofan Engine degraderinging degradering", NASA Ames Prognostics data Central ( https://c3.nasa.gov/dashlink/resources/139/) , NASA Ames Research Center, Moffett Field, ca*

## <a name="next-steps"></a>Nästa steg
Nu när du har sett huvudkomponenterna i lösningsacceleratorn Förebyggande underhåll kanske du vill anpassa den.

Du kan också utforska några av de andra funktionerna i IoT-lösningens acceleratorer:

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
