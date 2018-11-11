---
title: Förutsäga fordonshälsa och driver på vanor – Azure | Microsoft Docs
description: Använda funktionerna i Cortana Intelligence insikter i realtid och förutsägande på fordonshälsa och vanor.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: 0594b9b40fdf8647f99946a31dbb47a95c9694ac
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300361"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Vehicle Telemetry Analytics-lösning, playbook

Superdatorer finns har flyttat ut ur labbet och nu i garage. Dessa placeras nu på banbrytande bilar som innehåller många sensorer. Dessa sensorer som ger dem möjlighet att spåra och övervaka miljoner händelser varje sekund. De flesta av dessa fordon ska anslutas till Internet 2020. Att den stora mängden data ger större säkerhet, tillförlitlighet, och därför en bättre Driver upplevelse. Microsoft gör detta drömma verklighet med Cortana Intelligence.

Cortana Intelligence är en fullständigt hanterad stordata och avancerad analys som hjälper som du kan använda för att omvandla data till smarta åtgärder. Lösningsmallen Cortana Intelligence Vehicle Telemetry Analytics visar hur bilhandlare, bilfabrikanter och försäkringsbolag kan hämta i realtid och förutsägande insikter om fordonshälsa och körvanor.

Lösningen har implementerats som en [lambda-arkitekturmönster](https://en.wikipedia.org/wiki/Lambda_architecture), som visar hela potential för Cortana Intelligence-plattformens i realtid och batchbearbetning.

## <a name="architecture"></a>Arkitektur
Arkitektur för Vehicle Telemetry Analytics-lösningen illustreras i följande diagram:

![Diagram över lösningsarkitektur](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Den här lösningen innehåller följande komponenter i Cortana Intelligence och visar sina integrering:

* **Azure Event Hubs** mata in miljontals händelser för fordonstelemetri i Azure.
* **Azure Stream Analytics** ger insikter i realtid om fordonshälsa och kvarstår dessa data till långsiktig lagring för mer omfattande batchanalyser.
* **Azure Machine Learning** identifierar avvikelser i realtid och använder batch-bearbetning för att lämna förutsägelser.
* **Azure HDInsight** transformerar data i stor skala.
* **Azure Data Factory** hanterar orkestrering, schemaläggning, resurshantering samt övervakning av din pipeline för batchbearbetning.
* **Power BI** ger den här lösningen en omfattande instrumentpanel för data i realtid och visualisering för förutsägelseanalys.

Den här lösningen använder två olika datakällor: 

* **Simulerade vehicle signaler och diagnostik**: en vehicle telematics simulator genererar diagnostisk information och signaler som relaterar till tillståndet för fordon och utveckla mönster vid en viss tidpunkt. 
* **Vehicle catalog**: den här referensdatauppsättning mappar VIN siffror till modeller.

## <a name="next-steps"></a>Nästa steg

Om du vill utforska ytterligare i den här lösningen, se [Vehicle Telemetry Analytics-lösning, playbook: fördjupa dig i lösningen](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

Läs hur du konfigurerar Power BI-rapporter och instrumentpaneler för den här lösningen i [Vehicle Telemetry Analytics-lösningen mall Power BI-instrumentpanel, konfigurationsanvisningar](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
