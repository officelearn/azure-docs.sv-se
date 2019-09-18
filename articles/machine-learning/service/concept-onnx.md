---
title: Hög prestanda, plattforms oberoende störningar med ONNX
titleSuffix: Azure Machine Learning
description: Lär dig mer om ONNX och ONNX runtime för att påskynda modeller
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 4f6e9e6b44e4a8fcc52f6d8ae19af60d64972b3a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035408"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX och Azure Machine Learning: Skapa och påskynda ML-modeller

Lär dig hur du använder [Open neurala Network Exchange](https://onnx.ai) (ONNX) för att optimera dator inlärnings modellens härledning. Härlednings-eller modell poängsättning är den fas där den distribuerade modellen används för förutsägelse, oftast på produktions data. 

Optimering av maskin inlärnings modeller för härledning (eller modell poängsättning) är svår eftersom du behöver justera modellen och ett bibliotek för att få ut mesta möjliga av maskin varu funktionerna. Problemet blir mycket hårt om du vill få optimala prestanda på olika typer av plattformar (Cloud/Edge, CPU/GPU osv.), eftersom var och en har olika funktioner och egenskaper. Komplexiteten ökar om du har modeller från en mängd olika ramverk som måste köras på olika plattformar. Det är mycket tids krävande att optimera alla olika kombinationer av ramverk och maskin vara. En lösning att träna en gång i det ramverk som du föredrar och köra var som helst i molnet eller i kanten behövs. Det är där ONNX kommer in.

Microsoft och en community för partners skapade ONNX som en öppen standard för att representera maskin inlärnings modeller. Modeller från [många ramverk](https://onnx.ai/supported-tools) , inklusive TensorFlow, PyTorch, SciKit-lära, keras, kedjor, MXNET och MATLAB, kan exporteras eller konverteras till standard ONNX-formatet. När modellerna är i ONNX-formatet kan de köras på olika plattformar och enheter.

[ONNX runtime](https://github.com/Microsoft/onnxruntime) är en härlednings motor med hög prestanda för att distribuera ONNX-modeller till produktion. Den är optimerad för både molnet och Edge och fungerar på Linux, Windows och Mac. Som har C++skrivits i innehåller den även C, python och C# API: er. ONNX runtime ger stöd för all ONNX-ML-specifikationen och integreras också med acceleratorer på olika maskin vara, till exempel TensorRT på NVidia GPU: er.

ONNX runtime används i storskaliga Microsoft-tjänster som Bing, Office och Cognitive Services. Prestanda vinster är beroende av ett antal faktorer, men dessa Microsoft-tjänster har sett en __genomsnittlig dubbel prestanda vinst på CPU__. ONNX runtime används också som en del av Windows ML på hundratals miljon tals enheter. Du kan använda körnings miljön med Azure Machine Learning. Genom att använda ONNX runtime kan du dra nytta av de omfattande optimeringar av produktions klass, testning och pågående förbättringar.

[![Flödes diagram för ONNX som visar utbildning, konverterare och distribution](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Hämta ONNX-modeller

Du kan hämta ONNX-modeller på flera olika sätt:
+ Träna en ny ONNX-modell i Azure Machine Learning (se exempel längst ned i den här artikeln)
+ Konvertera en befintlig modell från ett annat format till ONNX (se [självstudierna](https://github.com/onnx/tutorials)) 
+ Hämta en förtränad ONNX-modell från [ONNX-modellen Zoo](https://github.com/onnx/models) (se exemplen längst ned i den här artikeln)
+ Skapa en anpassad ONNX-modell från [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Många modeller, inklusive bild klassificering, objekt identifiering och text bearbetning kan representeras som ONNX-modeller. Vissa modeller kanske inte kan konverteras korrekt. Om du stöter på den här situationen kan du ange ett problem i GitHub för respektive konverterare som du använde. Du kan fortsätta att använda din befintliga format modell tills problemet har åtgärd ATS.

## <a name="deploy-onnx-models-in-azure"></a>Distribuera ONNX-modeller i Azure

Med Azure Machine Learning kan du distribuera, hantera och övervaka dina ONNX-modeller. Standarden [arbetsflöde för distribution](concept-model-management-and-deployment.md) och ONNX-Runtime som du kan skapa en REST-slutpunkt som ligger i molnet. Se exempel på Jupyter Notebooks i slutet av den här artikeln för att prova själv. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installera och använda ONNX runtime med python

Python-paket för ONNX runtime finns tillgängliga på [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Läs [system kraven](https://github.com/Microsoft/onnxruntime#system-requirements) innan du installerar. 

 Om du vill installera ONNX runtime för python använder du något av följande kommandon: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

För att anropa ONNX-körning i Python-skriptet, använder du:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

I dokumentationen som medföljer modellen vanligtvis visar indata och utdata för användning av modellen. Du kan också använda ett visualiseringsverktyg som [Netron](https://github.com/lutzroeder/Netron) att visa modellen. Du kan också fråga modellmetadata, in- och utdata ONNX Runtime:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Att inferens din modell, Använd `run` och skicka in listan över utdata du vill ha returnerade (lämna tomt om du vill att alla) och en karta över indatavärdena. Resultatet är en lista över utdata.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Läs den fullständiga Python API-referensen i [ONNX Runtime referensdokument](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exempel

Se [How-to-till-användning – azureml/distribution/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) till exempel anteckningsböcker som skapar och distribuerar ONNX-modeller.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mer information

Mer information om ONNX eller bidra till projektet:
+ [ONNX projektwebbplatsen](https://onnx.ai)
+ [ONNX-kod på GitHub](https://github.com/onnx/onnx)

Mer information om ONNX Runtime eller bidra till projektet:
+ [ONNX runtime GitHub lagrings platsen](https://github.com/Microsoft/onnxruntime)


