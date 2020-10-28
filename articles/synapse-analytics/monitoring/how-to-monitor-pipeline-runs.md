---
title: Övervaka pipeline-körningar med Synapse Studio
description: Använd Synapse Studio för att övervaka pipeline-körningar i arbets ytan.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 10/27/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 61d860def7209908e65e9456a4bcde87eed522fc
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746381"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Använd Synapse Studio för att övervaka körnings pipelinen för arbets ytan

Med Azure Synapse Analytics kan du skapa komplexa pipelines som kan automatisera och integrera data flytt, data omvandling och beräknings aktiviteter i din lösning. Du kan skapa och övervaka dessa pipelines med Synapse Studio (för hands version).

Den här artikeln förklarar hur du övervakar dina pipeline-körningar, vilket gör att du kan hålla koll på den senaste statusen, problemen och förloppet för dina pipeliner.

## <a name="access-pipeline-runs-list"></a>Åtkomst till pipeline-körningar

Om du vill se en lista över pipelines körs i din arbets yta [öppnar du först Synapse Studio](https://web.azuresynapse.net/) och väljer din arbets yta.

![Logga in på arbets ytan](./media/common/login-workspace.png)

När du har öppnat din arbets yta väljer du avsnittet **övervaka** till vänster.

![Välj övervaka hubb](./media/common/left-nav.png)

Välj **pipeline-körningar** om du vill visa listan över pipeliner som körs.

![Välj pipeline-körningar](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>Filtrera dina pipelines körningar

Du kan filtrera listan över pipelines som körs till de som du är intresse rad av. Med filtren längst upp på skärmen kan du ange ett fält som du vill filtrera.

Du kan till exempel filtrera vyn om du bara vill se pipelinen för pipelinen med namnet "helgdag":

![Knappen Filtrera](./media/common/filter-button.png)

![Exempel filter](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>Visa information om en speciell pipeline-körning

Om du vill visa information om din pipeline-körning väljer du pipeline-körningen. Visa sedan de aktivitets körningar som är associerade med pipeline-körningen. Om pipelinen fortfarande körs kan du övervaka förloppet. 
  
## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av program finns i artikeln [övervaka Apache Spark program](how-to-monitor-spark-applications.md) . 
