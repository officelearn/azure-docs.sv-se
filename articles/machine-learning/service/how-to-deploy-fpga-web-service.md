---
title: Distribuera modeller på FPGA
titleSuffix: Azure Machine Learning service
description: Lär dig hur du distribuerar en webbtjänst med en modell som körs på en FPGA med Azure Machine Learning-tjänsten för extremt låg latens inferensjobb.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: a82c3b3aef59ef53e5054f2beb2880e8a01dc8fa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448668"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Distribuera en modell som en webbtjänst på en FPGA med Azure Machine Learning-tjänsten

Du kan distribuera en modell som en webbtjänst på [fältet programmable gate matriser (FPGA)](concept-accelerate-with-fpgas.md).  FPGA ger extremt låg latens inferensjobb, även med en enda gruppstorlek.  Dessa modeller är tillgängliga:
  - ResNet 50
  - ResNet 152
  - DenseNet 121
  - VGG-16   

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](http://aka.ms/AMLFree) i dag.

- En arbetsyta för Azure Machine Learning-tjänsten och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.
 
  - Din arbetsyta måste finnas i den *östra USA 2* region.

  - Installera contrib-tillägg:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - För närvarande endast tensorflow version < = 1.10 stöds, så installera när alla andra installationer har slutförts:

    ```shell
    pip install "tensorflow==1.10"
    ```

### <a name="get-the-notebook"></a>Hämta anteckningsboken

Självstudien finns tillgänglig som en Jupyter Notebook. Följ kod här eller kör den [Snabbstart notebook](https://github.com/Azure/aml-real-time-ai/blob/master/notebooks/project-brainwave-quickstart.ipynb).

## <a name="create-and-deploy-your-model"></a>Skapa och distribuera din modell
Skapa en pipeline för att Förbearbeta inmatad bild, blir en funktion med ResNet-50 på en FPGA och kör sedan funktionerna via en klassificerare som tränats på ImageNet-datauppsättning.

Följ anvisningarna för att:

* Definiera modellen pipelinen
* Distribuera modellen
* Använd distribuerade modell
* Ta bort distribuerade tjänster

> [!IMPORTANT]
> Din klient ska vara i samma Azure-region som slutpunkt för att optimera svarstid och dataflöde.  För närvarande skapas av API: er i regionen östra USA Azure.



### <a name="preprocess-image"></a>Förbearbeta bild
Det första steget i pipelinen är att Förbearbeta bilderna.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Lägg till Upplärda
Initiera modellen och ladda ned en TensorFlow kontrollpunkt för den quantized versionen av ResNet50 som ska användas som en upplärda.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Lägg till klassificerare
Den här klassificerare har tränats på ImageNet-datauppsättning.

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>Skapa tjänstdefinition
Nu när du har definierat bild Förbearbeta, upplärda och klassificerare som körs på tjänsten, kan du skapa en tjänstdefinition. Tjänstdefinitionen är en uppsättning filer som genereras från den modell som har distribuerats till FPGA-tjänsten. Tjänstdefinitionen består av en pipeline. Pipelinen är en serie steg som körs i ordning.  TensorFlow faser, Keras faser och steg BrainWave stöds.  Stegen körs på tjänsten, med utdata i varje fas blir indata i efterföljande steg i ordning.

Att skapa ett TensorFlow-steg, ange en session som innehåller diagrammet (i det här fallet används standard-graph) och indata och utdata tensors till den här fasen.  Den här informationen används för att spara diagrammet så att den kan köras på tjänsten.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Distribuera modell
Skapa en tjänst från tjänstdefinitionen.  Din arbetsyta måste vara på plats i USA, östra 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>Testa tjänsten
Att skicka en bild-API: et och testa svaret, lägga till en mappning från utdata klass-ID till ImageNet klassnamn.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Anropar din tjänst och Ersätt ”your image.jpg” filnamnet nedan med en avbildning från din dator. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Rensa service
Ta bort tjänsten.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Skydda FPGA-webbtjänster

Information om hur du skyddar FPGA webbtjänster finns i den [skydda webbtjänster](how-to-secure-web-service.md) dokumentet.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [förbruka en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md).
