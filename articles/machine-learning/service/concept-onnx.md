---
title: Hög prestanda, skriptkörning över flera plattformar inferens med ONNX
titleSuffix: Azure Machine Learning service
description: Läs mer om ONNX och ONNX-Runtime för att påskynda modeller
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6bda1af8095e7026c79b7d8ffe45b39f6cbb3508
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028702"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX och Azure Machine Learning: Skapa och påskynda ML-modeller

Lär dig hur med hjälp av den [öppna Neural Network Exchange](https://onnx.ai) (ONNX) hjälpa dig att optimera dina maskininlärningsmodeller.

Optimera maskininlärningsmodeller för inferens är svårt eftersom du behöver justera modellen och inferens-biblioteket för att få ut det mesta av maskinvarukapaciteten. Problemet blir mycket svårt att om du vill få bästa möjliga prestanda på olika typer av plattformar (moln/edge, CPU/GPU osv), eftersom varje har olika funktioner och egenskaper. Komplexiteten ökar om du har modeller från en mängd ramverk som måste köras på en rad olika plattformar. Det tar lång tid att optimera alla olika kombinationer av ramverk och maskinvara. En lösning för att träna en gång i din prioriterade framework och kör var som helst i molnet eller edge krävs. Det här är här ONNX kommer in.

Microsoft och en community med partner skapade ONNX som en öppen standard för att representera machine learning-modeller. Modeller från [många ramverk](https://onnx.ai/supported-tools) inklusive TensorFlow, PyTorch, lär du dig SciKit, Keras, Chainer, MXNet och MATLAB kan exporteras eller konverteras till standard ONNX-formatet. När modeller finns i ONNX-formatet, kan de köras på en rad olika plattformar och enheter.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) är en högpresterande inferens motor för att distribuera ONNX-modeller till produktion. Det är optimerad för både i molnet och gränsen och fungerar på Linux, Windows och Mac. Skrivna i C++, har också C, Python, och C# API: er. ONNX Runtime tillhandahåller support för alla ONNX-ML-specifikationen och är integrerat med acceleratorer på en annan maskinvara, till exempel TensorRT på NVidia GPU: er.

ONNX-Runtime används i hög skala Microsoft-tjänster som Bing, Office och Cognitive Services. Prestandavinster är beroende av ett antal faktorer, men dessa Microsoft-tjänster har sett en __genomsnittlig 2 x prestandaökning på processor__. ONNX Runtime används också som en del av Windows-ML på hundratals miljoner enheter. Du kan använda körningen med Azure Machine Learning-tjänster. Med ONNX-körning kan dra du nytta av omfattande produktionsnivå optimeringar, testning och pågående förbättringar.

[![ONNX flow diagram som visar utbildning, konverterare och distribution](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Hämta ONNX-modeller

Du kan hämta ONNX-modeller på flera olika sätt:
+ Skapa en ny ONNX-modell i Azure Machine Learning-tjänsten (se exempel längst ned i den här artikeln)
+ Konvertera befintlig modell från ett annat format till ONNX (se den [självstudier](https://github.com/onnx/tutorials)) 
+ Hämta en förtränade ONNX-modell från den [ONNX modellen Zoo](https://github.com/onnx/models) (se exempel längst ned i den här artikeln)
+ Skapa en anpassad ONNX-modell från [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Många modeller, inklusive bildklassificering, objektidentifiering och bearbetning av text kan representeras ONNX-modeller. Vissa modeller kan dock inte att konvertera har. Om du får den här situationen kan du rapportera problemet i GitHub för respektive converter som du använde. Du kan fortsätta använda din befintliga format-modell tills problemet åtgärdas.

## <a name="deploy-onnx-models-in-azure"></a>Distribuera ONNX-modeller i Azure

Du kan använda Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka ONNX-modeller. Standarden [arbetsflöde för distribution](concept-model-management-and-deployment.md) och ONNX-Runtime som du kan skapa en REST-slutpunkt som ligger i molnet. Se exempel Jupyter-anteckningsböcker i slutet av den här artikeln att prova själv. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installera och använda ONNX körning med Python

Python-paket för ONNX Runtime är tillgängliga på [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Läs [systemkrav](https://github.com/Microsoft/onnxruntime#system-requirements) före installationen. 

 Installera ONNX Runtime Python genom att använda något av följande kommandon: 
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
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
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
+ [ONNX Runtime GitHub-lagringsplatsen](https://github.com/Microsoft/onnxruntime)


