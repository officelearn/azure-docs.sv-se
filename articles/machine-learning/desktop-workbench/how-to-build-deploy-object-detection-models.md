---
title: Skapa och distribuera en modell för identifiering av objekt med hjälp av Azure Machine Learning-paket för visuellt innehåll.
description: Lär dig mer om att skapa, träna, testa och distribuera en objektet identifiering av modellen för visuellt innehåll med hjälp av Azure Machine Learning-paketet för visuellt innehåll.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 1451cdc5efcb62cff5c5d3725d5b15eb44be1755
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613672"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Skapa och distribuera objekt identifiering av modeller med Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


I den här artikeln lär du dig hur du använder **Azure Machine Learning-paket för visuellt innehåll** för att träna, testa och distribuera en [snabbare R-CNN](https://arxiv.org/abs/1506.01497) objektmodellen för identifiering. 

Ett stort antal problem i datordomän för visuellt innehåll kan lösas med hjälp av objektidentifiering. Dessa problem är att skapa modeller som hittar ett varierande antal objekt i en bild. 

När du skapar och distribuerar den här modellen med det här paketet, gå igenom följande steg:
1.  Skapa en datauppsättning
2.  Modell-Definition för djupa Neurala nätverk (DNN)
3.  Modellträning
4.  Utvärdering och visualisering
5.  Webbtjänsten distribution
6.  Webbtjänsten belastningstest

I det här exemplet används TensorFlow som ramverk för djupinlärning, utbildning utförs lokalt på en GPU-baserade dator som den [Deep learning virtuell dator för datavetenskap](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), och distributionen använder Azure ML driftsättning CLI.

Läs den [paketera referensdokumentation](https://aka.ms/aml-packages/vision) för detaljerade referenser för varje modul och klass.

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

1. Följande konton och programmet måste ställas in och installerad:
   - Ett konto för Machine Learning-experimentering 
   - Ett konto i Azure Machine Learning-modellhantering
   - Azure Machine Learning Workbench installerat

   Om dessa tre är ännu inte skapats eller installerats följer den [installationen av Azure Machine Learning-Quickstart och Workbench](../desktop-workbench/quickstart-installation.md) artikeln. 

1. Azure Machine Learning-paket för visuellt innehåll måste installeras. Lär dig hur du [installera det här paketet här](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Exempeldata och notebook

### <a name="get-the-jupyter-notebook"></a>Hämta Jupyter-anteckningsbok

Ladda ned anteckningsboken för att köra exemplet som beskrivs här själv.

> [!div class="nextstepaction"]
> [Hämta Jupyter-anteckningsbok](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Läsa in exempeldata

Den här demonstrationen tillhandahålls en datauppsättning med livsmedelskedja objekt i kylskåp, som består av 30 bilder och 8 klasser (eggBox, joghurt, ketchup, mushroom, Karlsson, orange, glasögon för squash och water). Det finns en anteckning xml-fil med liknande namn för varje jpg-bild. 

Följande bild visar rekommenderade mappstrukturen. 

![mappstruktur](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Bild-anteckning

Kommenterade objektet platser som krävs för att träna och utvärdera en detektor för objektet. [LabelImg](https://tzutalin.github.io/labelImg) är ett verktyg med öppen källkod anteckning som kan användas för att kommentera bilder. LabelImg skriver en XML-fil per bild i Pascal-Flyktig format, som kan läsas av det här paketet. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Skapa en datamängd

Skapa en CVTK datauppsättning som består av en uppsättning bilder med deras respektive omgivande rutan kommentarer. I det här exemplet kylskåp-avbildningar som finns i den ”.. / sample_data-livsmedel-utbildning ”mappen används. Endast JPEG-bilder stöds.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![PNG](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Definiera en modell

I det här exemplet används snabbare R-CNN-modellen. Olika parametrar som kan anges när du definierar den här modellen. Enligt dessa parametrar, samt de parametrar som används för att träna (se nästa avsnitt) finns i antingen CVTK API-dokumentation eller på den [Tensorflow objekt identifiering av webbplats](https://github.com/tensorflow/models/tree/master/research/object_detection). Mer information om snabbare R-CNN: S modell kan hittas på [den här länken](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Den här modellen är baserad på snabb R-CNN och du hittar mer information om den [här](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Träna modellen

Modellen tränas med COCO snabbare R-CNN med ResNet50 används som startpunkt för utbildning. 

För att träna detektorn anges antalet utbildning steg i koden som 350, så att utbildningen körs snabbare (~ 5 minuter med GPU). I praktiken är att ange den till minst 10 gånger antalet bilder i träningsmängden.

I det här exemplet anges antalet detektor utbildning steg till 350 för snabb utbildning. I praktiken är dock en bra tumregel att ställa in stegen på minst 10 gånger antalet bilder i träningsmängden.

Två viktiga parametrar för träning är:
- Antal steg för att öva med modellen, representeras av argumentet num_seps. Varje steg träna modellen med en minibatch av batchstorlek något.
- Learning/bidragssatserna, som kan anges av initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard kan användas för att visualisera utbildning förloppet. TensorBoard händelser finns i mappen som specificerats av model-objektet train_dir attribut. Följ dessa steg om du vill visa TensorBoard:
1. Kopiera utskriften som börjar med ”tensorboard--logdir' till Kommandotolken och kör den. 
2. Kopiera returnerade Webbadressen från kommandoraden till en webbläsare för att visa TensorBoard. 

TensorBoard bör se ut som på följande skärmbild. Det tar en liten stund för utbildning-mappen ska fyllas i. Så om TensorBoard inte visar steg in korrekt första gången försök upprepande 1 – 2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Utvärdera modellen

Metoden 'utvärdera' används för att utvärdera modellen. Den här funktionen kräver ett ObjectDetectionDataset-objekt som indata. Utvärderingen datauppsättningen kan skapas med samma funktion som den som används för datauppsättning för träning. Stöds mått är genomsnittliga Precision som definierats för den [PASCAL Flyktig utmaning](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Utvärderingsresultatet kan skrivas ut i en ren format.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

På samma sätt kan du beräkna det arbete du utfört modellen på träningsmängden. Om du gör detta kan du kontrollera utbildning konvergerat till en mycket bra lösning. Det arbete du utfört på träningsmängden efter lyckad utbildning är ofta nära 100%.

Utvärderingsresultat av visas även i TensorBoard, inklusive mappa värden och bilder med förväntade omgivande rutorna. Kopiera utskriften från följande kod i ett kommandoradsfönster att starta TensorBoard-klienten. Ett portvärde 8008 används här för att undvika konflikt med standardvärdet 6006, som har använt för att visa status för utbildning.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Bedöma en avbildning

När du är nöjd med prestanda av den tränade modellen kan model-objektet ”poäng” funktionen användas kan bedöma de nya bilderna. Returnerade resultat kan illustreras med funktionen ”visualisera”. 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![PNG](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Spara modellen

Den tränade modellen kan sparas till disk och läsa in tillbaka till minnet, som visas i följande kodexempel.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Sparade modellen för bedömning

Läsa in modellen i minnet med funktionen ”effekt” om du vill använda sparade modellen. Du behöver bara att läsa in en gång. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

När modellen har lästs in, kan den användas för att poängsätta en avbildning eller en lista över avbildningar. För en enda avbildning returneras en ordlista med nycklar som ”detection_boxes', 'detection_scores' och 'num_detections'. Om indata är en lista över avbildningar, en lista över ordlista returneras en ordlista som motsvarar en bild. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Identifierade objekt med poäng ovan 0,5, inklusive etiketter, poäng och koordinater kan skrivas ut.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Visualisera poängen precis som innan.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![PNG](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>Driftsättning: distribuera och använda

Driftsättning är hur du publicerar modeller och kod som webbtjänster och användningen av dessa tjänster för att producera affärsresultat. 

När din tränas, du kan distribuera den modellen som en webbtjänst för användning med hjälp av [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Modeller kan distribueras till din lokala dator eller kluster i Azure Container Service (ACS). Med ACS kan du skala din webbtjänst manuellt eller använda funktionen för automatisk skalning.

**Logga in med Azure CLI**

Med hjälp av en [Azure](https://azure.microsoft.com/) konto med en giltig prenumeration, logga in med hjälp av följande CLI-kommando:
<br>`az login`

+ Om du vill växla till en annan Azure-prenumeration, använder du kommandot:
<br>`az account set --subscription [your subscription name]`

+ Om du vill se aktuella modellhanteringskontot, använder du kommandot:
  <br>`az ml account modelmanagement show`

**Skapa och ange klustermiljön för distribution**

Du behöver bara ange en distributionsmiljö en gång. Om du inte har något ännu kan ställa in en distributionsmiljö nu med [instruktionerna](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Visa miljön aktiv distribution, använda följande CLI-kommando:
<br>`az ml env show`
   
Exempel på Azure CLI-kommando för att skapa och ställa in distributionsmiljön

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Hantera webbtjänster och -distributioner

Följande API: er kan användas för att distribuera modeller som webbtjänster, hantera dessa webbtjänster och hantera distributioner.

|Aktivitet|API|
|----|----|
|Skapa distributionsobjekt|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Distribuera webbtjänst|`deploy_obj.deploy()`|
|Poäng bild|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Ta bort webbtjänst|`deploy_obj.delete()`|
|Skapa docker-avbildning utan webbtjänst|`deploy_obj.build_docker_image()`|
|Lista över befintlig distribution|`AMLDeployment.list_deployment()`|
|Ta bort om tjänsten finns med distributionens namn|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API-dokumentation:** läser den [paketera referensdokumentation](https://aka.ms/aml-packages/vision) för detaljerade referenser för varje modul och klass.

**CLI-referens:** mer avancerade åtgärder relaterade till distribution, finns i den [modellhantering CLI-referensen](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Distributionshantering av i Azure-portalen**: du kan spåra och hantera dina distributioner i den [Azure-portalen](https://ms.portal.azure.com/). Hitta din kontosida för Machine Learning-modellhantering använda dess namn i Azure Portal. Gå sedan till sidan för modellhantering > modellhantering > tjänster.

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Förbruka webbtjänsten

När du har skapat webbtjänsten kan bedöma du bilder med den distribuerade webbtjänsten. Har du flera alternativ:

   - Du kan bedöma webbtjänsten med distributionsobjektet med direkt: deploy_obj.score_image(image_path_or_url) 
   - Du kan också använda slutpunkts-url och nyckel för tjänstens (ingen för lokal distribution) med: AMLDeployment.score_existing_service_with_image (image_path_or_url service_endpoint_url, service_key = ingen)
   - Utgör din http-begäranden direkt till score webservice-slutpunkten (för avancerade användare).

### <a name="score-with-existing-deployment-object"></a>Poäng med befintliga distributionsobjektet
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Poäng med slutpunkts-url och nyckel för tjänstens
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Poäng slutpunkt med http-begäran direkt
Följande är några exempel på kod för att skapa http-begäran direkt i Python. Du kan göra det i andra programmeringsspråk.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request parameters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Parsa serialiserade resultat från webbtjänsten
Resultatet från webbtjänsten är i json-sträng som kan parsas.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Machine Learning-paket för visuellt innehåll i de här artiklarna:

+ Läs den [paketera översikt](https://aka.ms/aml-packages/vision).

+ Utforska den [referensdokumentation](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) för det här paketet.

+ Lär dig mer om [andra Python-paket för Azure Machine Learning](reference-python-package-overview.md).
