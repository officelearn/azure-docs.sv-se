---
title: 'ONNX: hög perf, plattformsoberoende inferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder ONNX (Open Neural Network Exchange) kan hjälpa till att optimera inferensen av din maskininlärningsmodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270178"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX och Azure Machine Learning: Skapa och påskynda ML-modeller

Lär dig hur du använder [ONNX (Open Neural Network Exchange)](https://onnx.ai) kan hjälpa till att optimera inferensen av din maskininlärningsmodell. Inferens, eller modellbedömning, är den fas där den distribuerade modellen används för förutsägelse, oftast på produktionsdata. 

Det är svårt att optimera maskininlärningsmodeller för slutledning (eller modellbedömning) eftersom du måste justera modellen och inferensbiblioteket för att få ut det mesta av maskinvarufunktionerna. Problemet blir extremt svårt om du vill få optimal prestanda på olika typer av plattformar (moln / kant, CPU / GPU, etc.), eftersom var och en har olika funktioner och egenskaper. Komplexiteten ökar om du har modeller från en mängd olika ramar som behöver köras på en mängd olika plattformar. Det är mycket tidskrävande att optimera alla olika kombinationer av ramverk och hårdvara. En lösning för att träna en gång i ditt ramverk och köra var som helst i molnet eller kanten behövs. Det är här ONNX kommer in.

Microsoft och en grupp partner skapade ONNX som en öppen standard för att representera maskininlärningsmodeller. Modeller från [många ramverk,](https://onnx.ai/supported-tools) inklusive TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet och MATLAB kan exporteras eller konverteras till standard ONNX-formatet. När modellerna är i ONNX-format kan de köras på en mängd olika plattformar och enheter.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) är en högpresterande inferensmotor för att distribuera ONNX-modeller till produktion. Den är optimerad för både molnet och kanten och fungerar på Linux, Windows och Mac. Den är skriven i C++och har även C-, Python- och C#-API:er. ONNX Runtime ger stöd för alla ONNX-ML-specifikationer och integrerar även med acceleratorer på olika maskinvara som TensorRT på NVidia GPU:er.

ONNX Runtime används i avancerade Microsoft-tjänster som Bing, Office och Cognitive Services. Prestandavinster är beroende av ett antal faktorer, men dessa Microsoft-tjänster har sett en __genomsnittlig 2x prestandavinst på CPU__. ONNX Runtime används också som en del av Windows ML på hundratals miljoner enheter. Du kan använda körningen med Azure Machine Learning. Genom att använda ONNX Runtime kan du dra nytta av omfattande optimeringar, testning och pågående förbättringar av produktionskvalitet.

[![ONNX-flödesdiagram som visar utbildning, konverterare och distribution](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Skaffa ONNX-modeller

Du kan hämta ONNX-modeller på flera sätt:
+ Träna en ny ONNX-modell i Azure Machine Learning (se exempel längst ned i den här artikeln)
+ Konvertera befintlig modell från ett annat format till ONNX (se [självstudierna)](https://github.com/onnx/tutorials) 
+ Skaffa en förtränad ONNX-modell från [ONNX Model Zoo](https://github.com/onnx/models) (se exempel längst ner i den här artikeln)
+ Generera en anpassad ONNX-modell från [Azure Custom Vision-tjänsten](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Många modeller, inklusive bildklassificering, objektidentifiering och textbearbetning, kan representeras som ONNX-modeller. Vissa modeller kanske dock inte kan konverteras. Om du stöter på den här situationen kan du lämna in ett problem i GitHub för respektive konverterare som du använde. Du kan fortsätta använda din befintliga formatmodell tills problemet åtgärdas.

## <a name="deploy-onnx-models-in-azure"></a>Distribuera ONNX-modeller i Azure

Med Azure Machine Learning kan du distribuera, hantera och övervaka dina ONNX-modeller. Med hjälp av [standarddistributionsarbetsflödet](concept-model-management-and-deployment.md) och ONNX Runtime kan du skapa en REST-slutpunkt som finns i molnet. Se exempel Jupyter anteckningsböcker i slutet av den här artikeln för att prova det själv. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installera och använda ONNX Runtime med Python

Python-paket för ONNX Runtime finns på [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Läs [systemkraven](https://github.com/Microsoft/onnxruntime#system-requirements) före installationen. 

 Om du vill installera ONNX Runtime för Python använder du något av följande kommandon: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Om du vill anropa ONNX Runtime i python-skriptet använder du:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Dokumentationen som medföljer modellen visar vanligtvis in- och utgångarna för att använda modellen. Du kan också använda ett visualiseringsverktyg som [Netron](https://github.com/lutzroeder/Netron) för att visa modellen. ONNX Runtime kan du också fråga modellens metadata, indata och utdata:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Om du vill dra `run` slutsatser av din modell använder och skickar du in listan över utdata som du vill returnera (lämna tom om du vill ha alla) och en karta över indatavärdena. Resultatet är en lista över utdata.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Den fullständiga Python API-referensen finns i [REFERENSdokumenten ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exempel

Se [hur du använder azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) till exempel anteckningsböcker som skapar och distribuerar ONNX-modeller.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mer information

Läs mer om ONNX eller bidra till projektet:
+ [ONNX-projektets webbplats](https://onnx.ai)
+ [ONNX-kod på GitHub](https://github.com/onnx/onnx)

Läs mer om ONNX Runtime eller bidra till projektet:
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)


