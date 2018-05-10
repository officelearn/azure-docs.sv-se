---
title: Skapa och distribuera en modell för klassificering av avbildning med hjälp av Azure Machine Learning-paketet för datorn Vision.
description: Lär dig mer om att skapa, träna, testa och distribuera en vision avbildningen klassificering datormodell med Azure Machine Learning-paketet för datorn Vision.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: bd9f01e76c68fa41616818251b5b54553059cbcc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Skapa och distribuera avbildningen klassificering modeller med Azure Machine Learning

I den här artikeln lär du dig hur du använder **Azure Machine Learning-paketet för datorn Vision** (AMLPCV) för att träna, testa och distribuera en modell för klassificering av avbildningen. 

Ett stort antal problem i datorn vision domänen kan lösas med hjälp av klassificering för avbildningen. Dessa problem är bygga modeller som besvara frågor som:
+ _Finns ett objekt i bilden? Till exempel ”hund”, ”bil”, ”levereras” och så vidare_
+ _Vilken klass av öga sjukdom allvarlighetsgrad evinced av den här tålamod retinal genomsökning?_

När du skapar och distribuerar den här modellen med AMLPCV, gå igenom följande steg:
1. Skapa en datauppsättning
2. Bild anteckningar och visualisering
3. Bild förstärkning
4. Definition av modellen för djupa Neurala nätverk (DNN)
5. Klassificerare utbildning
6. Utvärdering och visualisering
7. Webbtjänsten distribution
8. Läs in testning-webbtjänst

[CNTK](https://www.microsoft.com/cognitive-toolkit/) används som djup learning-ramverk utbildning utförs lokalt på en GPU påslagen dator som den ([djup learning datavetenskap VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), och distributionen använder Azure ML Operationalization CLI.

Läs den [paketet referensdokumentationen](https://aka.ms/aml-packages/vision) för detaljerad för varje modul och klass.

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

1. Följande konton och programmet måste ställas in och installerat:
   - Ett konto för Machine Learning-experimentering 
   - Ett konto i Azure Machine Learning modellen Management
   - Azure Machine Learning Workbench installerat

   Om dessa tre är ännu inte skapats eller installerats, så den [installationen av Azure Machine Learning Quickstart och arbetsstationen](../service/quickstart-installation.md) artikel. 

1. Azure Machine Learning-paketet för datorn Vision måste installeras. Lär dig hur du [installationspaket här](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Exempeldata och bärbara datorer

### <a name="get-the-jupyter-notebook"></a>Hämta Jupyter-anteckningsbok

Ladda ned anteckningsboken för att köra exemplet beskrivs här själv.

> [!div class="nextstepaction"]
> [Hämta Jupyter-anteckningsbok](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Läs in exempeldata

I följande exempel används en datamängd som består av 63 Bords bilder. Varje avbildning är märkt som tillhör någon av fyra olika klasser (skål, cup, bestick, platta). Antalet avbildningar i det här exemplet är liten så att det här exemplet kan utföras snabbt. I praktiken måste minst 100 bilder per klass anges. Alla avbildningar finns i *”... /sample_data/imgs_recycling / ”*i underkataloger som kallas” skål ”,” cup ”,” bestick ”och” skylt ”.

![Azure Machine Learning-datamängd](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>Storage-kontexten

Storage-kontexten används för att fastställa där olika utdata, till exempel förhöjd bilder eller DNN modellfiler ska lagras. Mer information om lagring kontexter finns i [StorageContext dokumentationen](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test). 

Normalt behöver inte lagring innehållet anges explicit. Men för att undvika gränsen 25 MB på Projektstorlek som införts av Azure Machine Learning-arbetsstationen ange katalogen utdata för Azure Machine Learning-paketet för datorn Vision till en plats utanför Azure Machine Learning-projektet (”... /.. /.. /.. / cvtk_output ”). Glöm inte att ta bort katalogen ”cvtk_output” när den inte längre behövs.


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>Skapa en datamängd

När du har importerat beroenden och ange sedan kontexten för lagring, kan du skapa dataset-objekt.

Ange rotkatalogen för avbildningar för att skapa objektet med Azure Machine Learning-paket för datorn Vision, på den lokala disken. Den här katalogen måste följa samma allmänna strukturen som Bords-dataset som, innehålla underkataloger med själva bilderna:
- rot
    - Etikett 1
    - etiketten 2
    - ...
    - etikett n
  
Tränar en modell för klassificering av avbildningen till en annan dataset är lika enkelt som att ändra rotsökvägen `dataset_location` i följande kod så att den pekar på olika bilder.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

Dataset-objekt innehåller funktioner för att hämta bilder med den [Bing avbildningen Sök API](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Två typer av sökningar stöds: 
+ Vanlig textfrågor
+ Bild-URL-frågor

De här frågorna tillsammans med etiketten klass måste anges i en JSON-kodad textfil. Exempel:

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Dessutom måste du uttryckligen skapa ett kontextobjekt innehåller Bing avbildningen Sök API-nyckeln. Detta kräver en Bing avbildningen Sök API-prenumeration.

## <a name="visualize-and-annotate-images"></a>Visualisera och kommentera bilder

Du kan visualisera avbildningar och rätt etiketter i dataset-objektet med hjälp av följande widgeten. 

Om du får felet ”Widget Javascript inte identifieras” kör detta kommando för att lösa det:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Azure Machine Learning-datamängd](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Utöka bilder

Den [ `augmentation` modulen](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) innehåller funktioner för att utöka funktionerna i ett dataset-objekt med hjälp av alla omvandlingar som beskrivs i den [imgaug](https://github.com/aleju/imgaug) bibliotek. Bildfiler kan grupperas i en enkel rörledning då alla konverteringar i pipelinen används samtidigt varje avbildning. 

Om du vill använda olika förstärkning steg separat, eller på något annat sätt, kan du definiera flera pipelines och skicka dem till den *augment_dataset* funktion. Mer information och exempel på bilden förstärkning finns i [imgaug dokumentationen](https://github.com/aleju/imgaug).

Lägga till förhöjd bilder i träningsmängden det är särskilt praktiskt för små datauppsättningar. Eftersom DNN utbildning processen är långsammare på grund av ökade antalet utbildning bilder, rekommenderar vi att du börjar experiment utan förstärkning.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Definiera DNN modeller

Följande pretrained djupa Neurala nätverket modeller stöds med det här paketet: 
+ AlexNet
+ Resnet 18
+ Resnet 34
+ Resnet 50
+ Resnet 101
+ Resnet 152

Dessa DNNs kan användas som klassificerare eller som featurizer. 

Mer information om nätverk finns [här](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), och är en grundläggande introduktion till överför utbildning [här](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Standardparametrar för avbildningen klassificering för det här paketet är 224 x 224 pixelupplösning och en Resnet 18 DNN. Dessa parametrar har valts för att fungera på en mängd olika uppgifter. Precisionen kan ofta förbättras, till exempel genom att öka bildupplösningen till 500 x 500 bildpunkter och/eller välja en djupare modell (Resnet 50). Ändra parametrarna kan dock finnas på en signifikant ökning utbildning. Se artikeln på [hur vi kan förbättra noggrannhet](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Träna klassificeraren

Du kan välja något av följande metoder för före utbildade DNN.

  - **DNN Förfining**, som tränar DNN att utföra klassificeringen direkt. DNN utbildning är långsam, leder den normalt till bästa resultat eftersom alla nätverk vikterna kan förbättras under utbildning för att ge bästa noggrannhet.

  - **DNN featurization**, som körs DNN som-är att hämta en lägre endimensionell representation av en bild (512, 2048 eller 4096 flyter). Den representationen används sedan som indata för att träna en separat klassificerare. Eftersom DNN hålls oförändrade, den här metoden är mycket snabbare jämfört med DNN förfining men precisionen inte är lika bra. Dock kan tränar en extern klassificerare, till exempel en linjär SVM (som visas i följande kod) Ange en stark baslinje och hjälp med att förstå möjligheten att ett problem.
  
TensorBoard kan användas för att visualisera utbildning förloppet. Aktivera TensorBoard:
1. Lägger till parametern `tensorboard_logdir=PATH` som visas i följande kod
1. Starta TensorBoard klienten med hjälp av kommandot `tensorboard --logdir=PATH` i en ny konsol.
1. Öppna en webbläsare som finns beskrivet av TensorBoard, vilket som standard är localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![PNG](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Utvärdera och visualisera modellen prestanda

Du kan utvärdera prestanda hos den tränade modellen på ett oberoende testdata med hjälp av modulen utvärdering. Utvärderingen-mått som beräknar bland annat:
 
+ Precision (som standard ett genomsnitt av klassen)
+ PR kurva
+ ROC-kurvan
+ Området under kurva
+ Förvirring matris


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![PNG](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Azure Machine Learning-datamängd](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Azure Machine Learning-datamängd](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operationalization: distribuera och använda

Operationalization är hur du publicerar modeller och kod som webbtjänster och användningen av tjänsterna gav inga resultat för företag. 

När din modell tränas, kan du distribuera den modellen som en webbtjänst för användning med [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Modeller kan distribueras till din lokala dator eller kluster i Azure Container Service (ACS). Med ACS kan du skala webbtjänsten manuellt eller använda funktionen autoskalning.

**Logga in med Azure CLI**

Med hjälp av en [Azure](https://azure.microsoft.com/) konto med en giltig prenumeration måste du logga in med följande CLI-kommando:
<br>`az login`

+ Om du vill växla till en annan Azure-prenumeration, använder du kommandot:
<br>`az account set --subscription [your subscription name]`

+ Om du vill visa det aktuella modellen management kontot, använder du kommandot:
  <br>`az ml account modelmanagement show`

**Skapa och ange din distributionsmiljö**

Du behöver bara ange din distributionsmiljö av en gång. Om du inte har någon ännu, konfigurera din distributionsmiljö som använder [instruktionerna](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Följ lokalt eller kluster-distribution konfigurationsstegen korrekt baserat på dina behov.
+ Lokala distributioner stöds för Linux och Windows 10-datorer, men inte för Windows datavetenskap VM eller djup Learning VM. 
+ Distributioner i klustret har stöd för både Linux och Windows. 

Om du vill se din miljö för aktiv distribution, kan du använda kommandot CLI:
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
    
### <a name="manage-web-services-and-deployments"></a>Hantera webbtjänster och distributioner

Följande API: er kan användas för att distribuera modeller som webbtjänster, hantera dessa webbtjänster och hantera distributioner.

|Aktivitet|API|
|----|----|
|Skapa distributionsobjektet|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Distribuera webbtjänsten|`deploy_obj.deploy()`|
|Poäng bild|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Ta bort webbtjänst|`deploy_obj.delete()`|
|Skapa docker bild utan webbtjänst|`deploy_obj.build_docker_image()`|
|Visa en lista med befintliga distributionen|`AMLDeployment.list_deployment()`|
|Ta bort om tjänsten finns med distributionens namn|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API-dokumentationen:** kontakta den [paketet referensdokumentationen](https://aka.ms/aml-packages/vision) för detaljerad för varje modul och klass.

**CLI-referens:** för mer avancerade åtgärder som rör distribution avser den [modell management CLI referens](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Distributionshantering i Azure-portalen**: du kan spåra och hantera dina distributioner i den [Azure-portalen](https://ms.portal.azure.com/). Hitta din Machine Learning modellen Management kontosida använda dess namn från Azure-portalen. Gå till sidan för modellen konto > modellen Management > tjänster.


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Använda webbtjänsten 

När du distribuerar modellen som en webbtjänst kan poängsätta du avbildningar till webbtjänsten genom att använda någon av följande metoder:

- Poängsätta webbtjänsten direkt med distribution objekt med `deploy_obj.score_image(image_path_or_url)`

- Använd Service endpoint URL-Adressen och tjänsten nyckel (ingen för lokala distribution) med: `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Formuläret HTTP-begäranden direkt för att samla in webbtjänstslutpunkt. Det här alternativet är för avancerade användare.

### <a name="score-with-existing-deployment-object"></a>Poängsätta med befintliga distributionsobjektet

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Poängsätta med tjänsten slutpunkts-url och nyckeln för tjänsten

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Poäng slutpunkten med HTTP-begäran direkt

Följande exempelkod utgör HTTP-begäran direkt i Python. Du kan dock göra det i andra programmeringsspråk.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Parsa serialiserade resultatet från webbtjänsten

Utdata från webbtjänsten är en JSON-sträng. Du kan parsa den här JSON-strängen med olika DNN modellen klasser.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Machine Learning-paketet för datorn Vision i de här artiklarna:

+ Lär dig hur du [förbättra den här modellen](how-to-improve-accuracy-for-computer-vision-models.md).

+ Läs den [paketet översikt och lära dig hur du installerar det](https://aka.ms/aml-packages/vision).

+ Utforska den [refererar dokumentationen](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) för det här paketet.

+ Lär dig mer om [andra Python-paket för Azure Machine Learning](reference-python-package-overview.md).
