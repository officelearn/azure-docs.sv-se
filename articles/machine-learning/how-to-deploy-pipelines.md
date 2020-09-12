---
title: Publicera ML-pipeliner
titleSuffix: Azure Machine Learning
description: Kör Machine Learning-arbetsflöden med maskin inlärnings pipeliner och Azure Machine Learning SDK för python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 08529d1bb50a1a5d5b3c7d0296aa36f021f45e98
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646093"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publicera och spåra maskin inlärnings pipeliner

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du kan dela en pipeline för maskin inlärning med dina kollegor eller kunder.

Maskin inlärnings pipeliner är återanvändbara arbets flöden för Machine Learning-uppgifter. En fördel med pipelines har ökat samarbete. Du kan också versions pipelines, så att kunderna kan använda den aktuella modellen när du arbetar med en ny version. 

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Machine Learning arbets yta](how-to-manage-workspace.md) för att lagra alla dina pipeline-resurser

* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning beräknings instans](concept-compute-instance.md) med SDK redan installerad

* Skapa och kör en pipeline för maskin inlärning, till exempel i följande [självstudie: skapa en Azure Machine Learning pipeline för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md). Andra alternativ finns i [skapa och köra Machine Learning-pipeliner med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>Publicera en pipeline

När du har en pipeline igång kan du publicera en pipeline så att den körs med olika indata. För REST-slutpunkten för en redan publicerad pipeline för att acceptera parametrar måste du konfigurera din pipeline så att `PipelineParameter` den använder objekt för argumenten som varierar.

1. Om du vill skapa en pipeline-parameter använder du ett [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) -objekt med ett standardvärde.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Lägg till det här `PipelineParameter` objektet som en parameter till något av stegen i pipelinen enligt följande:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publicera den här pipelinen som ska ta emot en parameter när den anropas.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Köra en publicerad pipeline

Alla publicerade pipeliner har en REST-slutpunkt. Med pipeline-slutpunkten kan du utlösa en körning av pipelinen från alla externa system, inklusive icke-python-klienter. Den här slut punkten aktiverar "hanterad repeterbarhet" i batch-poängsättning och ominlärnings scenarier.

Om du vill anropa körningen av föregående pipeline behöver du en Azure Active Directory Authentication Head-token. Att hämta en sådan token beskrivs i [klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) referensen AzureCliAuthentication och i [autentiseringen i Azure Machine Learning](https://aka.ms/pl-restep-auth) Notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Skapa en slut punkt för en versions pipeline

Du kan skapa en pipeline-slutpunkt med flera publicerade pipeliner bakom den. Detta ger dig en fast REST-slutpunkt när du itererar och uppdaterar dina ML-pipeliner.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Skicka ett jobb till en pipeline-slutpunkt

Du kan skicka ett jobb till standard versionen av en pipeline-slutpunkt:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Du kan också skicka ett jobb till en angiven version:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Samma kan åstadkommas med hjälp av REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Använda publicerade pipelines i Studio

Du kan också köra en publicerad pipeline från Studio:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visa din arbets yta](how-to-manage-workspace.md#view).

1. Välj **slut punkter**till vänster.

1. Välj **pipeline-slutpunkter**högst upp.
 ![lista över de publicerade pipelinen för Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Välj en pipeline för att köra, använda eller Granska resultat från tidigare körningar av pipelinens slut punkt.

## <a name="disable-a-published-pipeline"></a>Inaktivera en publicerad pipeline

Om du vill dölja en pipeline från listan över publicerade pipeliner inaktiverar du den, antingen i Studio eller från SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Du kan aktivera den igen med `p.enable()` . Mer information finns i klass referens för [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true) .

## <a name="next-steps"></a>Nästa steg

- Använd [dessa Jupyter-anteckningsböcker på GitHub](https://aka.ms/aml-pipeline-readme) för att utforska maskin inlärnings pipeliner ytterligare.
- Se SDK Reference-hjälpen för [azureml-pipeline-Core-](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) paketet och AzureML- [pipeline-steg-](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) paketet.
- Se tips om [hur du](how-to-debug-pipelines.md) felsöker och felsöker pipeliner.
