---
title: Övervaka pipeline kör Azure Synapse Studio (förhandsversion)
description: Använd Azure Synapse Studio för att övervaka dina arbetsytepipelplatser.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430791"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Använda Azure Synapse Studio för att övervaka dina arbetsytepipellinekörningar

Med Azure Synapse Analytics kan du skapa komplexa pipelines som kan automatisera och dirigera dataförflyttningar, dataomvandling och beräkningsaktiviteter i din lösning. Du kan skapa och övervaka dessa pipelines med Hjälp av Azure Synapse Studio (förhandsversion).

I den här artikeln beskrivs hur du övervakar dina pipeline-körningar, vilket gör att du kan hålla ett öga på den senaste statusen, problemen och förloppet för dina pipelines.

## <a name="access-the-list-of-pipeline-runs"></a>Få tillgång till listan över pipelinekörningar

Om du vill se listan över pipeline-körningar på arbetsytan öppnar du först [Azure Synapse Studio](https://web.azuresynapse.net/) och väljer arbetsyta.

![Logga in på arbetsytan](./media/common/login-workspace.png)

När du har öppnat arbetsytan väljer du avsnittet **Bildskärm** till vänster.

![Välj övervakarnav](./media/common/left-nav.png)

Välj **Pipeline körs** om du vill visa listan över pipeline-körningar.

![Välj pipeline-körningar](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrera dina pipeline-körningar

Du kan filtrera listan över pipelinekörningar till de som du är intresserad av. Med filtren högst upp på skärmen kan du ange ett fält som du vill filtrera mellan.

Du kan till exempel filtrera vyn så att endast pipeline-körningarna för pipelinen med namnet "helgdag" visas:

![Knappen Filter](./media/common/filter-button.png)

![Exempelfilter](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Visa information om en viss pipelinekörning

Om du vill visa information om pipeline-körningen väljer du pipeline-körningen. Visa sedan aktivitetskörningar som är associerade med pipelinekörningen. Om pipelinen fortfarande körs kan du övervaka förloppet. 
  
## <a name="next-steps"></a>Nästa steg

Mer information om övervakningsprogram finns i artikeln [Övervaka Apache Spark-program.](how-to-monitor-spark-applications.md) 
