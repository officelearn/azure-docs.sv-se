---
title: Utlösa körningen av en ML-pipeline från en Logic App
titleSuffix: Azure Machine Learning
description: Lär dig hur du utlöser körningen av en ML-pipeline med hjälp av Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122862"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Utlösa en körning av en Machine Learning-pipeline från en logikapp

Utlösa körningen av din Azure Machine Learning Pipeline när nya data visas. Du kanske till exempel vill utlösa pipelinen för att träna en ny modell när nya data visas i blob-lagringskontot. Konfigurera utlösaren med [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Mer information finns i [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* REST-slutpunkten för en publicerad Machine Learning-pipeline. [Skapa och publicera pipelinen](how-to-create-your-first-pipeline.md). Leta sedan reda på REST-slutpunkten för din PublishedPipeline med hjälp av pipeline-ID:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Azure blob lagring](../storage/blobs/storage-blobs-overview.md) för att lagra dina data.
* [Ett datalager](how-to-access-data.md) på arbetsytan som innehåller information om ditt blob-lagringskonto.

## <a name="create-a-logic-app"></a>Skapa en logikapp

Skapa nu en [Azure Logic App-instans.](../logic-apps/logic-apps-overview.md) Om du vill [kan du använda en integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) och konfigurera en [kundhanterad nyckel](../logic-apps/customer-managed-keys-integration-service-environment.md) som ska användas av logikappen.

När logikappen har etablerats kan du konfigurera en utlösare för pipelinen:

1. [Skapa en systemtilldelad hanterad identitet](../logic-apps/create-managed-service-identity.md) för att ge appen åtkomst till din Azure Machine Learning Workspace.

1. Navigera till vyn Logikappdesigner och välj mallen Tom Logic App. 
    > [!div class="mx-imgBorder"]
    > ![Tom mall](media/how-to-trigger-published-pipeline/blank-template.png)

1. Sök efter **blob**i Designern . Välj **utlösaren När en blob läggs till eller ändras (endast egenskaper)** och lägg till den här utlösaren i logikappen.
    > [!div class="mx-imgBorder"]
    > ![Lägg till utlösare](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Fyll i anslutningsinformationen för det Blob-lagringskonto som du vill övervaka för blob-tillägg eller ändringar. Välj den behållare som ska övervakas. 
 
    Välj **det intervall** och **den frekvens** som du vill söka efter uppdateringar som fungerar åt dig.  

    > [!NOTE]
    > Den här utlösaren övervakar den valda behållaren men övervakar inte undermappar.

1. Lägg till en HTTP-åtgärd som körs när en ny eller modifierad blob identifieras. Välj **+ Nytt steg**och sök sedan efter och välj HTTP-åtgärden.

  > [!div class="mx-imgBorder"]
  > ![Sök efter HTTP-åtgärd](media/how-to-trigger-published-pipeline/search-http.png)

  Använd följande inställningar för att konfigurera åtgärden:

  | Inställning | Värde | 
  |---|---|
  | HTTP-åtgärd | POST |
  | URI |slutpunkten till den publicerade pipelinen som du hittade som en [förutsättning](#prerequisites) |
  | Autentiseringsläge | Hanterad identitet |

1. Ställ in schemat för att ange värdet för alla [DataPath PipelineParametrar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) du kan ha:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Använd `DataStoreName` den du har lagt till på arbetsytan som en [förutsättning](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![HTTP-inställningar](media/how-to-trigger-published-pipeline/http-settings.png)

1. Välj **Spara** och ditt schema är nu klart.