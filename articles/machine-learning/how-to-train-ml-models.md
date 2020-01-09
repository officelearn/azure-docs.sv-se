---
title: Träna ML-modeller med uppskattningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du utför en enda nod och distribuerad utbildning av traditionella maskin inlärnings-och djup inlärnings modeller med hjälp av Azure Machine Learning uppskattnings klass
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: 3205f853088245461e854562999164f9813f3bd5
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536634"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Träna modeller med Azure Machine Learning med hjälp av uppskattning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Med Azure Machine Learning kan du enkelt skicka ditt utbildnings skript till [olika beräknings mål](how-to-set-up-training-targets.md#compute-targets-for-training), med hjälp av ett [RunConfiguration-objekt](how-to-set-up-training-targets.md#whats-a-run-configuration) och ett ScriptRunConfig- [objekt](how-to-set-up-training-targets.md#submit). Det här mönstret ger dig mycket flexibilitet och maximal kontroll.

För att under lätta djup inlärnings modellen är Azure Machine Learning python SDK en alternativ högre abstraktion, klassen uppskattning, som gör det möjligt för användarna att enkelt konstruera kör konfigurationer. Du kan skapa och använda en generisk [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka utbildnings skript med hjälp av valfritt ramverk som du väljer (till exempel scikit-information) på alla beräknings mål du väljer, oavsett om det är en lokal dator, en enskild virtuell dator i Azure eller ett GPU-kluster i Azure. För PyTorch-, TensorFlow-och kedje uppgifter-aktiviteter tillhandahåller Azure Machine Learning även de olika uppskattningarna [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)och [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) för att förenkla användningen av dessa ramverk.

## <a name="train-with-an-estimator"></a>Träna med en kostnadsuppskattning

När du har skapat din [arbetsytan](concept-workspace.md) och Ställ in din [utvecklingsmiljö](how-to-configure-environment.md), träna en modell i Azure Machine Learning omfattar följande steg:  
1. Skapa ett [fjärran slutet beräknings mål](how-to-set-up-training-targets.md) (Observera att du även kan använda lokal dator som beräknings mål)
2. Ladda upp dina [utbildnings data](how-to-access-data.md) till data lagret (valfritt)
3. Skapa din [inlärningsskript](tutorial-train-models-with-aml.md#create-a-training-script)
4. Skapa en `Estimator` objekt
5. Skicka uppskattningen till ett experiment objekt under arbets ytan

Den här artikeln handlar om steg 4 – 5. Steg 1 – 3, finns i den [träna en modellsjälvstudie](tutorial-train-models-with-aml.md) ett exempel.

### <a name="single-node-training"></a>Nod-utbildning

Använd en `Estimator` för en nod utbildning som körs på fjärranslutna beräkning i Azure för en scikit-Läs modellen. Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#amlcompute) objekt `compute_target` och din [datalager](how-to-access-data.md) objektet `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Det här kodfragmentet anger följande parametrar till den `Estimator` konstruktor.

Parameter | Beskrivning
--|--
`source_directory`| Lokal katalog som innehåller hela din kod som behövs för utbildningsjobbet. Den här mappen kopieras från den lokala datorn till fjärrdatorn.
`script_params`| Ord lista som anger de kommando rads argument som ska skickas till ditt utbildnings skript `entry_script`, i form av `<command-line argument, value>` par. Om du vill ange en utförlig flagga i `script_params`använder du `<command-line argument, "">`.
`compute_target`| Fjärrberäknings mål som ditt utbildnings skript ska köras på, i det här fallet ett Azure Machine Learning Compute-kluster ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Observera även om AmlCompute-kluster är det vanligaste målet, är det också möjligt att välja andra beräknings mål typer, till exempel virtuella Azure-datorer eller till och med lokal dator.)
`entry_script`| FilePath (relativt till den `source_directory`) för utbildning-skriptet som ska köras på den fjärranslutna databearbetning. Den här filen och eventuella ytterligare filer som den är beroende av bör finnas i den här mappen.
`conda_packages`| Lista över Python-paket installeras via conda som krävs för dina utbildningsskript.  

Konstruktorn har en annan parameter med namnet `pip_packages` som du använder för alla pip-paket som behövs.

Nu när du har skapat din `Estimator` objekt, skicka utbildningsjobbet som ska köras på den fjärranslutna beräkningen med ett anrop till den `submit` fungerar på din [Experiment](concept-azure-machine-learning-architecture.md#experiments) objektet `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Specialmappar** två mappar *matar ut* och *loggar*, ta emot särskild behandling av Azure Machine Learning. Vid träning, när du skriver filer till mappar för *matar ut* och *loggar* som är i förhållande till rotkatalogen (`./outputs` och `./logs`respektive), filerna kommer automatiskt ladda upp till din körningshistoriken så att du har åtkomst till dem när din körning är klar.
>
> Skapa artefakter vid träning (till exempel modellfiler kontrollpunkter, filer eller ritade bilder) skriva dem till den `./outputs` mapp.
>
> På samma sätt kan du skriva loggar från din utbildning som körs till mappen `./logs`. Använda Azure Machine Learning [TensorBoard integrering](https://aka.ms/aml-notebook-tb) se till att du skriver TensorBoard loggarna till den här mappen. När din körning pågår, kommer du att kunna starta TensorBoard och strömma dessa loggar.  Senare, kan du också återställa loggarna från någon av dina tidigare körningar.
>
> Till exempel för att hämta en fil som skrivs till den *matar ut* mappen till den lokala datorn efter remote utbildning kör: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuerad utbildning och anpassad Docker-avbildningar

Det finns två ytterligare scenarier som du kan utföra med den `Estimator`:
* Med en anpassad dockeravbildning
* Distribuerad utbildning i ett kluster med flera noder

Följande kod visar hur du utför distribuerad utbildning för en keras-modell. I stället för att använda standard Azure Machine Learning avbildningar anges dessutom en anpassad Docker-avbildning från Docker Hub `continuumio/miniconda` för utbildning.

Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#amlcompute) objektet `compute_target`. Du skapar kostnadsuppskattning på följande sätt:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Koden ovan visar följande nya parametrar till den `Estimator` konstruktorn:

Parameter | Beskrivning | Default
--|--|--
`custom_docker_image`| Namnet på avbildningen som du vill använda. Ange endast-avbildningarna i offentliga docker-databaser (i det här fallet Docker Hub). Använd konstruktorns `environment_definition` parameter i stället om du vill använda en avbildning från en privat Docker-lagringsplats. [Se exempel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Antalet noder som ska användas för utbildning-jobbet. | `1`
`process_count_per_node`| Antal processer (eller ”anställda”) för att köra på varje nod. I sådana fall kan du använda den `2` GPU: er tillgängliga på varje nod.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) -objekt för att starta distribuerad utbildning med MPI-backend.  | `None`


Slutligen skicka utbildningsjobbet:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrera en modell

När du har tränat modellen kan du spara och registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

Genom att köra följande kod registrerar du modellen på din arbets yta och gör den tillgänglig för referens med namn i fjärrstyrda beräknings kontexter eller distributions skript. Mer information och ytterligare parametrar finns i [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) i referens dokumenten.

```python
model = run.register_model(model_name='sklearn-sample')
```

## <a name="github-tracking-and-integration"></a>GitHub spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Exempel
För en bärbar dator som visar grunderna i ett uppskattnings mönster, se:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

För en bärbar dator som tränar en scikit-modell med hjälp av en uppskattning kan du läsa:
* [självstudier/img-klassificering – del 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

För antecknings böcker i utbildnings modeller med hjälp av detaljerade uppskattningar för djup inlärnings ramverk, se:

* [How-to-use-azureml/ml-Framework](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Skapa PyTorch-modeller](how-to-train-pytorch.md)
* [Skapa TensorFlow-modeller](how-to-train-tensorflow.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
