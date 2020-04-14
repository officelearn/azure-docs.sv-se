---
title: Vad är FPGA - hur man distribuerar
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar en webbtjänst med en modell som körs på en FPGA med Azure Machine Learning för ultralåg latens inferens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 870f7b0ab0f1d7b247435cdbb74e21801b3b052a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257189"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Vad är fältprogrammerbara grindmatriser (FPGA) och hur du distribuerar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln innehåller en introduktion till fältprogrammerbara gate-matriser (FPGA) och visar hur du distribuerar dina modeller med Azure Machine Learning till en Azure FPGA.

En FPGA innehåller en matris med programmerbara logiska block och en hierarki med omkonfigurerbara anslutningar. Sammanlänkningarna gör att dessa block kan konfigureras på olika sätt efter tillverkningen. Jämfört med andra chips ger FPGA en kombination av programmerbarhet och prestanda.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs CPU, GPU och ASIC

Följande diagram och tabell visar hur FPGA:er jämför med andra processorer.

![Diagram över FPGA-jämförelsen för Azure Machine Learning](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processor||Beskrivning|
|---|:-------:|------|
|Applikationsspecifika integrerade kretsar|Asics|Anpassade kretsar, till exempel Googles TensorFlow-processorenheter (TPU), ger högsta effektivitet. De kan inte konfigureras om när dina behov ändras.|
|Fältprogrammerbara grindmatriser|FPGA:er|FPGA, till exempel de som är tillgängliga på Azure, ger prestanda nära ASIC. De är också flexibla och omkonfigurerbara över tiden, för att implementera ny logik.|
|Enheter för grafikbearbetning|GPU: er|Ett populärt val för AI-beräkningar. GPU:er erbjuder parallella bearbetningsfunktioner, vilket gör det snabbare vid bildåtergivning än processorer.|
|Centralprocessorer|Processorer|Generella processorer, vars prestanda inte är idealisk för grafik och videobearbetning.|

FPGA på Azure baseras på Intels FPGA-enheter, som dataforskare och utvecklare använder för att påskynda AI-beräkningar i realtid. Den här FPGA-aktiverade arkitekturen erbjuder prestanda, flexibilitet och skala och är tillgänglig på Azure.

FPGA gör det möjligt att uppnå låg latens för begäranden i realtid (eller modellbedömning). Asynkrona begäranden (batching) behövs inte. Batchbearbetning kan orsaka svarstid, eftersom mer data behöver bearbetas. Implementeringar av neurala bearbetningsenheter kräver inte batchbearbetning; Därför latens kan vara många gånger lägre, jämfört med CPU och GPU-processorer.

### <a name="reconfigurable-power"></a>Omkonfigurerbar effekt
Du kan konfigurera om FPGA för olika typer av maskininlärningsmodeller. Den här flexibiliteten gör det enklare att accelerera programmen baserat på den mest optimala numeriska precisionen och minnesmodellen som används. Eftersom FPGA-enheter är omkonfigurerbara kan du hålla dig uppdaterad med kraven på snabbt föränderliga AI-algoritmer.

## <a name="whats-supported-on-azure"></a>Vad stöds på Azure
Microsoft Azure är världens största molninvestering i FPGA. Med hjälp av den här FPGA-aktiverade maskinvaruarkitekturen körs tränade neurala nätverk snabbt och med lägre latens. Azure kan parallellisera förutbildade djupa neurala nätverk (DNN) över FPGA för att skala ut din tjänst. DNN kan förtränas, som en djup featurizer för överföring lärande, eller finjusteras med uppdaterade vikter.

FPGA på Azure stöder:

+ Scenarier för bildklassificering och igenkänning
+ TensorFlow-distribution (kräver Tensorflow 1.x)
+ Intel FPGA-maskinvara

Dessa DNN-modeller är för närvarande tillgängliga:
  - ResNet 50
  - ResNet 152
  - TätNet-121
  - VGG-16
  - SSD-VGG

FPGA är tillgängliga i dessa Azure-regioner:
  - USA, östra
  - Sydostasien
  - Europa, västra
  - USA, västra 2

> [!IMPORTANT]
> För att optimera svarstid och dataflöde bör klienten som skickar data till FPGA-modellen finnas i en av regionerna ovan (den som du distribuerade modellen till).

**PBS-familjen för virtuella Azure-datorer** innehåller Intel Arria 10 FPGAs. Det visas som "Standard PBS Family vCPUs" när du kontrollerar din Azure kvotallokering. PB6-datorn har sex virtuella processorer och en FPGA, och den etableras automatiskt av Azure ML som en del av distributionen av en modell till en FPGA. Den används bara med Azure ML och kan inte köra godtyckliga bitströmmar. Till exempel kommer du inte att kunna blinka FPGA med bitströmmar att göra kryptering, kodning, etc.

### <a name="scenarios-and-applications"></a>Scenarier och program

Azure FPGA är integrerade med Azure Machine Learning. Microsoft använder FPGA för DNN-utvärdering, Bing-sökningsrankning och programvarudefinierad nätverksacceleration (SDN) för att minska svarstiden, samtidigt som processorer frigörs för andra uppgifter.

I följande scenarier används FPGA:
+ [Automatiserat optiskt inspektionssystem](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Kartläggning av tomttäcke](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Exempel: Distribuera modeller på FPGA

Du kan distribuera en modell som en webbtjänst på FPGA med Azure Machine Learning Hardware Accelerated Models. Med hjälp av FPGA ger ultralåg latens inferens, även med en enda batchstorlek. Inferens, eller modellbedömning, är den fas där den distribuerade modellen används för förutsägelse, oftast på produktionsdata.

### <a name="prerequisites"></a>Krav

- En Azure-prenumeration.  Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

- FPGA-kvoten. Använd Azure CLI för att kontrollera om du har kvot:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > De andra möjliga ``southeastasia`` ``westeurope``platserna ``westus2``är , och .

    Kommandot returnerar text som liknar följande:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Kontrollera att du har minst 6 virtuella processorer under __CurrentValue__.

    Om du inte har kvot skickar [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)du en begäran till .

- En Azure Machine Learning-arbetsyta och Azure Machine Learning SDK för Python installerat. Mer information finns i [Skapa en arbetsyta](how-to-manage-workspace.md).
 
- Python SDK för maskinvaruaccelererad modell:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```

## <a name="1-create-and-containerize-models"></a>1. Skapa och behålla modeller

Det här dokumentet beskriver hur du skapar ett TensorFlow-diagram för att förbehandla indataavbildningen, göra den till en featurizer med ResNet 50 på en FPGA och sedan köra funktionerna via en klassificerare som tränas på ImageNet-datauppsättningen.

Följ instruktionerna för att:

* Definiera TensorFlow-modellen
* Konvertera modellen
* Distribuera modellen
* Förbruka den distribuerade modellen
* Ta bort distribuerade tjänster

Använd [Azure Machine Learning SDK för Python för](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) att skapa en tjänstdefinition. En tjänstdefinition är en fil som beskriver en pipeline med grafer (indata, featurizer och klassificerare) baserat på TensorFlow. Distributionskommandot komprimerar automatiskt definitionen och diagrammen till en ZIP-fil och överför ZIP till Azure Blob-lagring. DNN distribueras redan för att köras på FPGA.

### <a name="load-azure-machine-learning-workspace"></a>Läs in Azure Machine Learning-arbetsyta

Läs in din Azure Machine Learning-arbetsyta.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Bild av förbehandlad bearbetning

Indata till webbtjänsten är en JPEG-bild.  Det första steget är att avkoda JPEG-avbildningen och förbehandla den.  JPEG-bilderna behandlas som strängar och resultatet är tensors som kommer att vara indata till ResNet 50-modellen.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Ladda featurizer

Initiera modellen och hämta en TensorFlow-kontrollpunkt för den kvantiserade versionen av ResNet50 som ska användas som featurizer.  Du kan ersätta "QuantizedResnet50" i kodavsnittet nedan med genom att importera andra djupa neurala nätverk:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Lägg till klassificerare

Den här klassificeraren har tränats i ImageNet-datauppsättningen.  Exempel på överföring av inlärning och utbildning av anpassade vikter finns i uppsättningen [med exempeldatorböcker](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Spara modellen

Nu när förbehandlaren, ResNet 50-featurizern och klassificeraren har lästs in sparar du diagrammet och tillhörande variabler som modell.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Spara in- och utgångss tensorer
De in- och utdatatratorer som skapades under stegen för förbearbetning och klassificerare kommer att behövas för modellkonvertering och inferens.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Spara in- och utdata tensorerna eftersom du behöver dem för modellkonvertering och slutminuter.

De tillgängliga modellerna och motsvarande standardklassificerare utdata tensors är nedan, vilket är vad du skulle använda för slutledning om du använde standardklassificeraren.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrera modellen

[Registrera](concept-model-management-and-deployment.md) modellen med hjälp av SDK med ZIP-filen i Azure Blob-lagring. Genom att lägga till taggar och andra metadata om modellen kan du hålla reda på dina tränade modeller.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Om du redan har registrerat en modell och vill läsa in den kan du hämta den.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Konvertera modell

Konvertera TensorFlow-grafen till open neurala nätverksutbytesformatet[(ONNX](https://onnx.ai/)).  Du måste ange namnen på indata- och utdatatratorer, och dessa namn kommer att användas av din klient när du använder webbtjänsten.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Skapa Docker-avbildning

Den konverterade modellen och alla beroenden läggs till i en Docker-avbildning.  Den här Docker-avbildningen kan sedan distribueras och instansieras.  Distributionsmål som stöds inkluderar AKS i molnet eller en kantenhet som [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Du kan också lägga till taggar och beskrivningar för den registrerade Docker-avbildningen.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Lista bilderna efter tagg och få detaljerade loggar för felsökning.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Distribuera till molnet eller kanten

### <a name="deploy-to-the-cloud"></a>Distribuera till molnet

Om du vill distribuera din modell som en storskalig produktionswebbtjänst använder du Azure Kubernetes Service (AKS). Du kan skapa en ny med Azure Machine Learning SDK, CLI eller [Azure Machine Learning studio](https://ml.azure.com).

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS-distributionen kan ta cirka 15 minuter.  Kontrollera om distributionen lyckades.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Distribuera behållaren till AKS-klustret.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Testa molntjänsten
Docker-avbildningen stöder gRPC och TensorFlow-serveringen "förutsäga" API.  Använd exempelklienten för att anropa Docker-avbildningen för att få förutsägelser från modellen.  Exempelklientkod är tillgänglig:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Om du vill använda TensorFlow-servering kan du [hämta en exempelklient](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Eftersom den här klassificeraren [ImageNet](http://www.image-net.org/) har tränats i ImageNet-datauppsättningen mappar du klasserna till läsbara etiketter som kan läsas av människor.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Rensa tjänsten
Ta bort webbtjänsten, avbildningen och modellen (måste göras i den här ordningen eftersom det finns beroenden).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Distribuera till en lokal kantserver

Alla [Azure Data Box Edge-enheter](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) innehåller en FPGA för att köra modellen.  Endast en modell kan köras på FPGA på en gång.  Om du vill köra en annan modell distribuerar du bara en ny behållare. Instruktioner och exempelkod finns i [det här Azure-exemplet](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Säkra FPGA-webbtjänster

Om du vill skydda dina FPGA-webbtjänster läser du dokumentet [om säkra webbtjänster.](how-to-secure-web-service.md)

## <a name="next-steps"></a>Nästa steg

Kolla in dessa anteckningsböcker, videor och bloggar:

+ Flera [exempel anteckningsböcker](https://aka.ms/aml-accel-models-notebooks)

+ [Hyperskala hårdvara: ML i skala ovanpå Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Inuti det Microsoft FPGA-baserade konfigurerbara molnet (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave för AI i realtid: projektets hemsida](https://www.microsoft.com/research/project/project-brainwave/)
