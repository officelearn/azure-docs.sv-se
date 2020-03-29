---
title: Tåg ML-modeller med skattningsgivare
titleSuffix: Azure Machine Learning
description: Lär dig hur du utför ennod och distribuerad utbildning av traditionella maskininlärnings- och djupinlärningsmodeller med azure machine learning-kalkylatorklass
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 678af1855baf52efa727444236de8a1724a7d0b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078474"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Träna modeller med Azure Machine Learning med uppskattning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Med Azure Machine Learning kan du enkelt skicka in utbildningsskriptet till [olika beräkningsmål](how-to-set-up-training-targets.md#compute-targets-for-training)med hjälp av ett [RunConfiguration-objekt](how-to-set-up-training-targets.md#whats-a-run-configuration) och ett [ScriptRunConfig-objekt](how-to-set-up-training-targets.md#submit). Det mönstret ger dig mycket flexibilitet och maximal kontroll.

För att underlätta utbildning i modeller med djupinlärning tillhandahåller Azure Machine Learning Python SDK en alternativ abstraktion på högre nivå, uppskattningsklassen, som gör det möjligt för användare att enkelt konstruera körningskonfigurationer. Du kan skapa och använda en allmän [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka utbildningsskript med alla utbildningsramverk som du väljer (till exempel scikit-learn) på alla beräkningsmål du väljer, oavsett om det är din lokala dator, en enda virtuell dator i Azure eller ett GPU-kluster i Azure. För Uppgifter om PyTorch, TensorFlow och Chainer tillhandahåller Azure Machine Learning även respektive [PyTorch-,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow-](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)och Chainer-skattningsanordningar för att förenkla med hjälp av dessa ramverk. [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)

## <a name="train-with-an-estimator"></a>Träna med en skattning

När du har skapat [din arbetsyta](concept-workspace.md) och konfigurerat [din utvecklingsmiljö](how-to-configure-environment.md)innebär utbildning av en modell i Azure Machine Learning följande steg:  
1. Skapa ett [fjärrberäkningsmål](how-to-set-up-training-targets.md) (observera att du också kan använda lokal dator som beräkningsmål)
2. Ladda upp dina [träningsdata](how-to-access-data.md) till datalager (valfritt)
3. Skapa ditt [träningsskript](tutorial-train-models-with-aml.md#create-a-training-script)
4. Skapa ett `Estimator`-objekt
5. Skicka uppskattningsverktyget till ett experimentobjekt under arbetsytan

Den här artikeln fokuserar på steg 4-5. För steg 1-3, se [tåget en modell handledning](tutorial-train-models-with-aml.md) för ett exempel.

### <a name="single-node-training"></a>Utbildning med en nod

Använd `Estimator` en för en enkelnodsutbildning som körs på fjärrberäkning i Azure för en scikit-learn-modell. Du borde redan ha skapat `compute_target` beräkningsmålobjektet och `ds` [filedataset-objektet](how-to-create-register-datasets.md) . [compute target](how-to-set-up-training-targets.md#amlcompute)

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

Det här kodavsnittet anger följande `Estimator` parametrar för konstruktorn.

Parameter | Beskrivning
--|--
`source_directory`| Lokal katalog som innehåller all kod som behövs för utbildningsjobbet. Den här mappen kopieras från den lokala datorn till fjärrberäkningen.
`script_params`| Ordlista som anger kommandoradsargumenten `entry_script`som ska `<command-line argument, value>` vidarebefordras till träningsskriptet i form av par. Om du vill ange `script_params`en `<command-line argument, "">`utförlig flagga i använder du .
`compute_target`| Fjärrberäkningsmål som utbildningsskriptet ska köras på, i det här fallet ett Azure Machine Learning Compute[(AmlCompute)](how-to-set-up-training-targets.md#amlcompute)kluster. (Observera att även om AmlCompute-klustret är det vanliga målet, är det också möjligt att välja andra beräkningsmåltyper som virtuella Azure-datorer eller till och med lokal dator.)
`entry_script`| Filsökvägen (i `source_directory`förhållande till ) för det utbildningsskript som ska köras på fjärrberäkningen. Den här filen, och eventuella ytterligare filer som den är beroende av, bör finnas i den här mappen.
`conda_packages`| Lista över Python-paket som ska installeras via conda som behövs av ditt träningsskript.  

Konstruktorn har en `pip_packages` annan parameter som kallas som du använder för alla pip paket som behövs.

Nu när du har `Estimator` skapat objektet skickar du utbildningsjobbet som ska köras på fjärrberäkningen `submit` med ett anrop till funktionen på [experimentobjektet](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Särskilda mappar** Två mappar, *utdata* och *loggar*, får särskild behandling av Azure Machine Learning. Under träningen, när du skriver filer till mappar med namnet *utdata* `./outputs` och `./logs` *loggar* som är relativa till rotkatalogen ( och , respektive), filerna kommer automatiskt att överföra till din körhistorik så att du har tillgång till dem när körningen är klar.
>
> Om du vill skapa artefakter under träningen (till exempel modellfiler, kontrollpunkter, `./outputs` datafiler eller ritade bilder) skriver du dessa till mappen.
>
> På samma sätt kan du skriva alla loggar `./logs` från din träningskörning till mappen. Om du vill använda Azure Machine Learnings [TensorBoard-integrering](https://aka.ms/aml-notebook-tb) måste du skriva dina TensorBoard-loggar till den här mappen. Medan din körning pågår kan du starta TensorBoard och strömma dessa loggar.  Senare kommer du också att kunna återställa loggarna från någon av dina tidigare körningar.
>
> Om du till exempel vill hämta en fil som skrivits till *utdatamappen* till den lokala datorn efter att fjärrträningen har körts:`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuerad utbildning och anpassade Docker-bilder

Det finns ytterligare två utbildningsscenarier `Estimator`som du kan utföra med:
* Använda en anpassad Docker-avbildning
* Distribuerad utbildning i ett kluster med flera noder

Följande kod visar hur du utför distribuerad utbildning för en Keras-modell. I stället för att använda standardavbildningarna för Azure Machine Learning anger `continuumio/miniconda` den dessutom en anpassad dockeravbildning från Docker Hub för utbildning.

Du borde redan ha skapat `compute_target` [beräkningsmålobjektet](how-to-set-up-training-targets.md#amlcompute) . Du skapar uppskattningen enligt följande:

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

Ovanstående kod exponerar följande nya `Estimator` parametrar för konstruktorn:

Parameter | Beskrivning | Default
--|--|--
`custom_docker_image`| Namnet på den bild du vill använda. Ange endast avbildningar som är tillgängliga i offentliga docker-databaser (i det här fallet Docker Hub). Om du vill använda en avbildning från en `environment_definition` privat dockerdatabas använder du konstruktorns parameter i stället. [Se exempel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Antal noder som ska användas för ditt utbildningsarbete. | `1`
`process_count_per_node`| Antal processer (eller "arbetare") som ska köras på varje nod. I det här fallet `2` använder du gpu:erna som är tillgängliga på varje nod.| `1`
`distributed_training`| [MPIKonfigureringsobjekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) för att starta distribuerad utbildning med MPI-serverdel.  | `None`


Lämna slutligen in utbildningsjobbet:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrera en modell

När du har tränat modellen kan du spara och registrera den på arbetsytan. Med modellregistrering kan du lagra och modellera dina modeller på arbetsytan för att förenkla [modellhantering och distribution](concept-model-management-and-deployment.md).

Om du kör följande kod registreras modellen på arbetsytan och den blir tillgänglig för referens med namn i fjärrberäkningskontexter eller distributionsskript. Se [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) i referensdokumenten för mer information och ytterligare parametrar.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub-spårning och -integration

När du startar en utbildningskörning där källkatalogen är en lokal Git-databas lagras information om databasen i körningshistoriken. Mer information finns i [Git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Exempel
En anteckningsbok som visar grunderna i ett uppskattningsmönster finns i:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

En bärbar dator som tränar en scikit-learn-modell med hjälp av kalkylator finns i:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

För bärbara datorer på utbildningsmodeller med hjälp av djupinlärningsramspecifika skattningsanteckningar finns i:

* [hur-till-använda-azureml/ml-ramverk](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Spåra körningsmått under träning](how-to-track-experiments.md)
* [Utbilda PyTorch-modeller](how-to-train-pytorch.md)
* [Utbilda TensorFlow-modeller](how-to-train-tensorflow.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md)