---
title: "Förutsäga vehicle hälsotillstånd och andra vanor - Azure | Microsoft Docs"
description: "Använda funktionerna i Cortana Intelligence och få insikter om i realtid och förutsägbara på vehicle hälsa och köra vanor."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 8584cade8e12b4f06ae455227e702844a06f512a
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Vehicle telemetri Analytics lösning playbook
Den här menyn innehåller länkar till kapitlen i den här playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Översikt
Superdatorer har flyttats från labbet och nu parkerade i garage. Dessa senaste bilar innehåller många olika sensorer, vilket ger dem möjlighet att spåra och övervaka miljontals händelser per sekund. 2020, kommer de flesta av dessa fordon vara ansluten till Internet. Genom att trycka på i denna mängd data kan ge större säkerhet, tillförlitlighet och en bättre intresseväckande upplevelse. Microsoft gör detta drömma en verkligheten med Cortana Intelligence.

Cortana Intelligence är en helt hanterad stordata och avancerade analyser suite som du kan använda för att omvandla dina data till intelligent åtgärd. Mallen Cortana Intelligence Vehicle telemetri Analytics lösningen visar hur bil hos återförsäljarna, bil tillverkare och försäkringsbolag får i realtid och förutsägande insikter om vehicle hälsa och köra vanor. 

Lösningen har implementerats som en [lambda-arkitektur mönster](https://en.wikipedia.org/wiki/Lambda_architecture), vilket visar att hela potentiella i Cortana Intelligence-plattformen för realtid och batchbearbetning.

## <a name="architecture"></a>Arkitektur
Lösningsarkitektur för Vehicle telemetri Analytics visas i diagrammet:

![Arkitekturdiagram för lösning](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Den här lösningen innehåller följande komponenter i Cortana Intelligence och visar deras slutpunkt till slutpunkt-integrering:

* **Händelsehubbar i Azure** en miljontals vehicle telemetriska händelser i Azure.
* **Azure Stream Analytics** ger realtidsinsikter på vehicle hälsa och kvarstår dessa data till långsiktig lagring för bättre batch analytics.
* **Azure Machine Learning** identifierar avvikelser i realtid och använder batch-bearbetning för att tillhandahålla förutsägbar insikter.
* **Azure HDInsight** omvandlar data i större skala.
* **Azure Data Factory** hanterar orchestration, schemaläggning, resurshantering och övervakning av batch-bearbetning-pipeline.
* **Power BI** ger den här lösningen en omfattande instrumentpanel för data i realtid och förutsägelseanalys visualiseringar.

Den här lösningen använder två olika datakällor: 

* **Simulerade vehicle signaler och diagnostik**: vehicle telematik simulator avger diagnostisk information och signalerar som motsvarar tillstånd fordon och intresseväckande mönstret vid en viss tidpunkt. 
* **Vehicle katalogen**: referens datamängden mappar VINs till modeller.

