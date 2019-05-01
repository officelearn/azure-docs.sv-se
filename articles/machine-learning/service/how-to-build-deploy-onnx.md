---
title: Skapa och distribuera samverkande ONNX-modeller
titleSuffix: Azure Machine Learning service
description: Läs mer om ONNX och hur du använder Azure Machine Learning för att skapa och distribuera ONNX-modeller
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: 716286fdfb684a277c7147936f162089a2cdefd8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692700"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX och Azure Machine Learning: Skapa och distribuera samverkande AI-modeller

Den [öppna Neural Network Exchange](https://onnx.ai) (ONNX)-formatet är en öppen standard för att representera machine learning-modeller. ONNX stöds av en [community med partner](https://onnx.ai/supported-tools), inklusive Microsoft, som skapar kompatibelt ramverk och verktyg. Microsoft strävar efter att öppna och samverkande AI så att datatekniker och utvecklare kan:

+ Använd ramverket för valfri för att skapa och träna modeller
+ Distribuera modeller plattformsoberoende med minimal integration arbete

Microsoft stöder ONNX över dess produkter, inklusive Azure och Windows för att hjälpa dig att uppnå dessa mål.  

## <a name="why-choose-onnx"></a>Varför ska jag välja ONNX?

Du får med ONNX samverkan gör det möjligt att få bra idéer till produktionen snabbare. Dataexperter kan ONNX välja sin önskade ramverk för jobbet. På samma sätt kan utvecklare tid och förbereder modeller för produktion och distribuera i molnet och gränsen.  

Du kan skapa ONNX-modeller från många ramverk, inklusive PyTorch, Chainer, MXNet, ML.Net, TensorFlow, Keras, lär du dig SciKit, Microsoft Cognitive Toolkit, med mera.

Det finns också ett ekosystem av verktyg för att visualisera och påskynda ONNX-modeller. Det finns också ett antal förtränade ONNX-modeller för vanliga scenarier.

[ONNX-modeller kan distribueras](#deploy) till molnet med Azure Machine Learning och ONNX-Runtime. De kan också distribueras till Windows 10-enheter med hjälp av [Windows ML](https://docs.microsoft.com/windows/ai/). De kan även distribueras till andra plattformar med hjälp av konverterare som är tillgängliga från ONNX-communityn. 

[![ONNX flow diagram som visar utbildning, konverterare och distribution](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Hämta ONNX-modeller

Du kan hämta ONNX-modeller på flera olika sätt:
+ Hämta en förtränade ONNX-modell från den [ONNX modellen Zoo](https://github.com/onnx/models) (se exemplet längst ned i den här artikeln)
+ Skapa en anpassad ONNX-modell från [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Konvertera befintlig modell från ett annat format till ONNX (se exemplet längst ned i den här artikeln) 
+ Skapa en ny ONNX-modell i Azure Machine Learning-tjänsten (se exemplet längst ned i den här artikeln)

## <a name="saveconvert-your-models-to-onnx"></a>Spara/konvertera dina ONNX-modeller

Du kan konvertera befintliga modeller till ONNX eller spara dem som ONNX i slutet av utbildning.

|Ramverk för modellen|Exempel på konvertering eller verktyg|
|-----|-------|
|PyTorch|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx-konverterare](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Läs CoreML<br/>XGBoost och libSVM|[WinMLTools](/windows/ai/windows-ml/convert-model-winmltools)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|

Du hittar den senaste listan över ramverk som stöds och konverterare på den [ONNX självstudier plats](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Distribuera ONNX-modeller i Azure

Du kan använda Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka ONNX-modeller. Standarden [arbetsflöde för distribution](concept-model-management-and-deployment.md) och ONNX-Runtime som du kan skapa en REST-slutpunkt som ligger i molnet. Se ett fullständigt exempel Jupyter-anteckningsbok i slutet av den här artikeln att prova själv. 

### <a name="install-and-configure-onnx-runtime"></a>Installera och konfigurera ONNX-körning

ONNX Runtime är en högpresterande inferens motor med öppen källkod för ONNX-modeller. Den ger maskinvaruacceleration på processor- och GPU med API: er som är tillgängliga för Python, C#, och C. ONNX Runtime stöder ONNX 1.2 + modeller och körs på Linux, Windows och Mac. Python-paket är tillgängliga på [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)), och [ C# paketet](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/) på [Nuget.org](https://www.nuget.org). Mer information om projektet på [GitHub](https://github.com/Microsoft/onnxruntime). Läs [systemkrav](https://github.com/Microsoft/onnxruntime#system-requirements) före installationen.

Om du vill installera ONNX Runtime för Python, använder du:
```python
pip install onnxruntime
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

### <a name="example-deployment-steps"></a>Exempel distributionssteg

Här är ett exempel för att distribuera en ONNX-modell:

1. Initiera din arbetsyta för Azure Machine Learning-tjänsten. Om du inte har något ännu kan du lära dig hur du [skapa en arbetsyta](setup-create-workspace.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Registrera modellen med Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Skapa en avbildning med modellen och eventuella beroenden.

   ```python
   from azureml.core.image import ContainerImage

   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   > [!TIP]
   > Det föregående exemplet används standardavbildning från Azure Machine Learning-tjänsten. Du kan också använda en anpassad avbildning. Mer information finns i Konfigurera och registrera bilddelen av [distribuera modeller](how-to-deploy-and-where.md#configureimage).

   Filen `score.py` innehåller bedömnings logik och måste tas med i avbildningen. Den här filen används för att köra modellen i avbildningen. Se den här [självstudien](tutorial-deploy-models-with-aml.md#create-scoring-script) för instruktioner om hur du skapar en bedömning skript. En exempelfil för en modell för ONNX visas nedan:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global session
       model = Model.get_model_path(model_name = 'MyONNXModel')
       session = onnxruntime.InferenceSession(model)

   def preprocess(input_data_json):
       # convert the JSON data into the tensor input
       return np.array(json.loads(input_data_json)['data']).astype('float32')

   def postprocess(result):
       return np.array(result).tolist()

   def run(input_data_json):
       try:
           start = time.time()   # start timer
           input_data = preprocess(input_data_json)
           input_name = session.get_inputs()[0].name  # get the id of the first input of the model   
           result = session.run([], {input_name: input_data})
           end = time.time()     # stop timer
           return {"result": postprocess(result),
                   "time": end - start}
       except Exception as e:
           result = str(e)
           return {"error": result}
   ```

   Filen `myenv.yml` beskriver de beroenden som krävs för avbildningen. Se den här [självstudien](tutorial-deploy-models-with-aml.md#create-environment-file) anvisningar om hur du skapar en miljöfil, t.ex den här exempelfilen:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies.create(pip_packages=["numpy","onnxruntime","azureml-core"])

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Om du vill distribuera din modell, den [hur du distribuerar och var](how-to-deploy-and-where.md) dokumentet.


## <a name="examples"></a>Exempel

Se [How-to-till-användning – azureml/distribution/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) till exempel anteckningsböcker som skapar och distribuerar ONNX-modeller.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mer information

Mer information om ONNX eller bidra till projektet:
+ [ONNX projektwebbplatsen](https://onnx.ai)

+ [ONNX-kod på GitHub](https://github.com/onnx/onnx)

Mer information om ONNX Runtime eller bidra till projektet:
+ [ONNX Runtime GitHub-lagringsplatsen](https://github.com/Microsoft/onnxruntime)


