---
title: 'Självstudie: kom igång med pipelines'
description: I den här självstudien får du lära dig hur du dirigerar pipeliner och aktiviteter med hjälp av Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329891"
---
# <a name="orchestrate-with-pipelines"></a>Dirigera med pipelines

I den här självstudien får du lära dig hur du dirigerar pipeliner och aktiviteter med hjälp av Synapse Studio. 

## <a name="overview"></a>Översikt

Du kan dirigera en mängd olika uppgifter i Azure Synapse.

1. Gå till **integrerings** hubben i Synapse Studio.
1. Välj **+**  >  **pipeline** för att skapa en ny pipeline.
1. Gå till **utveckla** hubben och välj en av de antecknings böcker som du skapade tidigare.
1. Dra den bärbara datorn till pipelinen (**Obs!** Lägg till importer i en antecknings bok som anges i [dokument](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace) som krävs vid körning från pipeline)
1. I pipelinen väljer du **Lägg till utlösare**  >  **ny/redigera**.
1. I **Välj utlösare**väljer du **ny**och ställer in **upprepningen** på "var 1 timme".
1. Välj **OK**. 
1. Välj **Publicera alla**.
1. Om du vill att pipelinen ska köras omedelbart utan att vänta på nästa timma väljer du **Lägg till**utlösare för utlösare  >  **nu**.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visualisera data med Power BI](get-started-visualize-power-bi.md)
                                 
