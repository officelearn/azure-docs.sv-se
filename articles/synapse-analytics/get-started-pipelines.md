---
title: 'Självstudie: kom igång med att integrera med pipelines'
description: I den här självstudien får du lära dig hur du integrerar pipelines och aktiviteter med Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744920"
---
# <a name="integrate-with-pipelines"></a>Integrera med pipelines

I den här självstudien får du lära dig hur du integrerar pipelines och aktiviteter med Synapse Studio. 

## <a name="overview"></a>Översikt

Du kan integrera en mängd olika uppgifter i Azure Synapse.

1. Gå till **integrerings** hubben i Synapse Studio.
1. Välj **+**  >  **pipeline** för att skapa en ny pipeline.
1. Gå till **utveckla** hubben och välj en av de antecknings böcker som du skapade tidigare.
1. Dra den bärbara datorn till pipelinen ( **Obs!** Lägg till import moduler i antecknings boken som anges i [dokument](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace), vilket krävs när du kör från pipelinen)
1. I pipelinen väljer du **Lägg till utlösare**  >  **ny/redigera** .
1. I **Välj utlösare** väljer du **ny** och ställer in **upprepningen** på "var 1 timme".
1. Välj **OK** . 
1. Välj **Publicera alla** .
1. Om du vill att pipelinen ska köras omedelbart utan att vänta på nästa timma väljer du **Lägg till** utlösare för utlösare  >  **nu** .



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visualisera data med Power BI](get-started-visualize-power-bi.md)
                                 
