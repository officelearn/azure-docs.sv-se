---
title: Utbilda ML-modeller med estimators
titleSuffix: Azure Machine Learning service
description: Lär dig hur du utför en nod och distribuerade utbildning av traditionella machine learning och deep learning-modeller med hjälp av Azure Machine Learning services kostnadsuppskattning-klass
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 7b479556543c6a9dff88643fdc587dec3f832f39
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548178"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Träna modeller med Azure Machine Learning med hjälp av kostnadsuppskattning

Med Azure Machine Learning kan du enkelt skicka dina utbildningsskript till [olika beräkningsmål](how-to-set-up-training-targets.md#compute-targets-for-training)med hjälp av [RunConfiguration objekt](how-to-set-up-training-targets.md#whats-a-run-configuration) och [ScriptRunConfig objektet](how-to-set-up-training-targets.md#submit). Mönstret ger stor flexibilitet och maximalt kontroll.

För att underlätta deep learning modellträning, Azure Machine Learning Python SDK tillhandahåller en alternativ på högre nivå abstraktion klassen kostnadsuppskattning, som tillåter användare att enkelt konstruktion som kör konfigurationer. Du kan skapa och använda en allmän [kostnadsuppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) att skicka inlärningsskript med valfritt learning-ramverk som du väljer (till exempel scikit-Läs) du vill köra på alla beräkningsmål, oavsett om det är den lokala datorn, en enskild virtuell dator i Azure eller en GPU kluster i Azure. För PyTorch, TensorFlow och Chainer uppgifter, Azure Machine Learning ger också respektive [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) och [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) estimators att förenkla med hjälp av dessa ramverk.

## <a name="train-with-an-estimator"></a>Träna med en kostnadsuppskattning

När du har skapat din [arbetsytan](concept-azure-machine-learning-architecture.md#workspace) och Ställ in din [utvecklingsmiljö](how-to-configure-environment.md), träna en modell i Azure Machine Learning omfattar följande steg:  
1. Skapa en [remote beräkningsmål](how-to-set-up-training-targets.md) (Observera att du kan också använda lokal dator som beräkningsmål)
2. Ladda upp din [träningsdata](how-to-access-data.md) till datalagret (valfritt)
3. Skapa din [inlärningsskript](tutorial-train-models-with-aml.md#create-a-training-script)
4. Skapa en `Estimator` objekt
5. Skicka kostnadsuppskattning till ett experiment objekt under arbetsytan

Den här artikeln handlar om steg 4 – 5. Steg 1 – 3, finns i den [träna en modellsjälvstudie](tutorial-train-models-with-aml.md) ett exempel.

### <a name="single-node-training"></a>Nod-utbildning

Använd en `Estimator` för en nod utbildning som körs på fjärranslutna beräkning i Azure för en scikit-Läs modellen. Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#amlcompute) objekt `compute_target` och din [datalager](how-to-access-data.md) objektet `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
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
`source_directory`| Lokal katalog som innehåller hela din kod som behövs för utbildningsjobbet. Den här mappen kopieras från din lokala dator till den fjärranslutna beräkningen 
`script_params`| Ordlista att ange kommandoradsargument för att dina utbildningsskript `entry_script`, i form av < kommandoradsargumentet, värde > par. Ange en utförlig flagga i `script_params`, använda `<command-line argument, "">`.
`compute_target`| Remote beräkningsmål som utbildning skriptet ska köras på, i det här fallet en Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) klustret. (Observera att även om AmlCompute kluster är vanliga mål, det är också möjligt att välja andra beräkning måltyper, till exempel virtuella datorer i Azure eller till och med lokala datorn.)
`entry_script`| FilePath (relativt till den `source_directory`) för utbildning-skriptet som ska köras på den fjärranslutna databearbetning. Den här filen och eventuella ytterligare filer som den är beroende av, bör finnas i den här mappen
`conda_packages`| Lista över Python-paket installeras via conda som krävs för dina utbildningsskript.  

Konstruktorn har en annan parameter med namnet `pip_packages` som du använder för eventuella pip-paket som behövs

Nu när du har skapat din `Estimator` objekt, skicka utbildningsjobbet som ska köras på den fjärranslutna beräkningen med ett anrop till den `submit` fungerar på din [Experiment](concept-azure-machine-learning-architecture.md#experiment) objektet `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Specialmappar** två mappar *matar ut* och *loggar*, ta emot särskild behandling av Azure Machine Learning. Vid träning, när du skriver filer till mappar för *matar ut* och *loggar* som är i förhållande till rotkatalogen (`./outputs` och `./logs`respektive), filerna kommer automatiskt ladda upp till din körningshistoriken så att du har åtkomst till dem när din körning är klar.
>
> Skapa artefakter vid träning (till exempel modellfiler kontrollpunkter, filer eller ritade bilder) skriva dem till den `./outputs` mapp.
>
> På samma sätt du kan skriva loggar från utbildning kör till den `./logs` mapp. Använda Azure Machine Learning [TensorBoard integrering](https://aka.ms/aml-notebook-tb) se till att du skriver TensorBoard loggarna till den här mappen. När din körning pågår, kommer du att kunna starta TensorBoard och strömma dessa loggar.  Senare, kan du också återställa loggarna från någon av dina tidigare körningar.
>
> Till exempel för att hämta en fil som skrivs till den *matar ut* mappen till den lokala datorn efter remote utbildning kör: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuerad utbildning och anpassad Docker-avbildningar

Det finns två ytterligare scenarier som du kan utföra med den `Estimator`:
* Med en anpassad dockeravbildning
* Distribuerad utbildning i ett kluster med flera noder

Följande kod visar hur du utför distribuerad utbildning för en Keras-modell. Dessutom kan i stället för standard Azure Machine Learning-avbildningar, anger en anpassad docker-avbildning från Docker Hub `continuumio/miniconda` för utbildning.

Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#amlcompute) objektet `compute_target`. Du skapar kostnadsuppskattning på följande sätt:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_base_image='continuumio/miniconda')
```

Koden ovan visar följande nya parametrar till den `Estimator` konstruktorn:

Parameter | Beskrivning | Standard
--|--|--
`custom_docker_base_image`| Namnet på avbildningen som du vill använda. Ange endast-avbildningarna i offentliga docker-databaser (i det här fallet Docker Hub). Om du vill använda en avbildning från ett privat docker-lagringsplats, Använd konstruktorn `environment_definition` parameter i stället. [Se exempel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Antalet noder som ska användas för utbildning-jobbet. | `1`
`process_count_per_node`| Antal processer (eller ”anställda”) för att köra på varje nod. I sådana fall kan du använda den `2` GPU: er tillgängliga på varje nod.| `1`
`distributed_backend`| Serverdelen för att starta distribuerade utbildning som kostnadsuppskattning erbjuder via MPI.  Att utföra parallella eller distribuerade utbildning (t.ex. `node_count`> 1 eller `process_count_per_node`> 1 eller båda), ange `distributed_backend='mpi'`. MPI-implementering som används av AML [öppna MPI](https://www.open-mpi.org/).| `None`

Slutligen skicka utbildningsjobbet:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="examples"></a>Exempel
En bärbar dator som visar grunderna i mönstret för kostnadsuppskattning, finns här:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

För en bärbar dator som tränar en scikit-Läs modellen använder kostnadsuppskattning, se:
* [självstudier/img-klassificering – del 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Anteckningsböcker på utbildning modeller med ramverk för djup inlärning specifika estimators finns här:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Skapa PyTorch-modeller](how-to-train-pytorch.md)
* [Skapa TensorFlow-modeller](how-to-train-tensorflow.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
