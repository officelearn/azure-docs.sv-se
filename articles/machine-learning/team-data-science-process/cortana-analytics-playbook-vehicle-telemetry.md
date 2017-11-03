---
title: "Förutsäga vehicle hälsotillstånd och andra vanor - Azure | Microsoft Docs"
description: "Använda funktionerna i Cortana Intelligence och få insikter om i realtid och förutsägbara på vehicle hälsa och köra vanor."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: af8b3d5bf891c93c30a05c5f02d86639a466dde5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Fordonstelemetrianalys, lösning, playbook
Detta **menyn** länkar till kapitlen i den här playbook. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Översikt
Super datorer har flyttats från labbet och nu parkerade i vår garage! Dessa senaste bilar innehåller en mängd sensorer, ger dem möjlighet att spåra och övervaka miljontals händelser per sekund. Vi räknar med att 2020, de flesta av dessa bilar kommer har anslutits till Internet. Anta att trycka på i denna mängd data för att ge större säkerhet, tillförlitlighet och en bättre intresseväckande upplevelse! Microsoft har gjort detta drömma en verkligheten med Cortana Intelligence.

Microsofts Cortana Intelligence är en helt hanterad stordata och avancerade analyser suite som gör att du kan omvandla dina data till intelligent åtgärd. Vi vill introduktion till Lösningsmall Cortana Intelligence Vehicle telemetri Analytics. Den här lösningen visar hur bil hos återförsäljarna, bil tillverkare och försäkringsbolag kan använda funktionerna i Cortana Intelligence för att få realtid och förutsägbara insikter om vehicle hälsa och köra vanor. 

Lösningen har implementerats som en [lambda-arkitektur mönster](https://en.wikipedia.org/wiki/Lambda_architecture) visar hela potentiella i Cortana Intelligence-plattformen för realtid och batchbearbetning. Lösning: 

* ger en Vehicle telematik simulator
* utnyttjar Händelsehubbar för att föra in miljontals simulerade vehicle telemetriska händelser till Azure 
* använder Stream Analytics för att få realtidsinsikter på vehicle hälsa
* sparar data till långsiktig lagring för bättre batch analytics. 
* drar nytta av Machine Learning för identifiering av avvikelse i realtid och batch-bearbetning för att få förutsägande insikter.
* utnyttjar HDInsight för att omvandla data i större skala och Data Factory för att hantera orchestration, schemaläggning, resurshantering och övervakning av batch-bearbetning pipeline 
* ger den här lösningen en omfattande instrumentpanel för data i realtid och förutsägelseanalys visualiseringar med Power BI

## <a name="architecture"></a>Arkitektur
![Arkitekturdiagram för lösningen](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*bild 1 – Vehicle telemetri Analytics lösningsarkitektur*

Den här lösningen innehåller följande **Cortana Intelligence-komponenterna** och visar deras slutpunkt till slutpunkt-integrering:

* **Händelsehubbar** för att föra in miljontals vehicle telemetriska händelser i Azure.
* **Strömma Analytics** för att få realtidsinsikter på vehicle hälsa och kvarstår dessa data till långsiktig lagring för bättre batch analytics.
* **Machine Learning** för identifiering av avvikelse i realtid och batchbearbetning och få förutsägande insikter.
* **HDInsight** utnyttjas för att omvandla data i skala
* **Data Factory** hanterar orchestration, schemaläggning, resurshantering och övervakning av batch-bearbetning-pipeline.
* **Power BI** ger den här lösningen en omfattande instrumentpanel för data i realtid och förutsägelseanalys visualiseringar.

Den här lösningen använder två olika **datakällor**: 

* **Simulerade vehicle signaler och diagnostik**: vehicle telematik simulator avger diagnostisk information och signalerar som motsvarar tillstånd fordon och intresseväckande mönstret vid en viss tidpunkt. 
* **Vehicle katalogen**: en referens datamängd som innehåller Registreringsnumret för Modellmappning.

