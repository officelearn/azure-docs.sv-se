---
title: Träna ML-modeller med uppskattningar
titleSuffix: Azure Machine Learning service
description: Lär dig hur du utför en nod och distribuerade utbildning av traditionella machine learning och deep learning-modeller med hjälp av Azure Machine Learning services kostnadsuppskattning-klass
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 0080c8ac5e957912c5fd59a7051619ee60bd914c
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260071"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Träna modeller med Azure Machine Learning med hjälp av uppskattning

Med Azure Machine Learning kan du enkelt skicka ditt utbildnings skript till [olika beräknings mål](how-to-set-up-training-targets.md#compute-targets-for-training), med hjälp av [RunConfiguration-objekt](how-to-set-up-training-targets.md#whats-a-run-configuration) och ScriptRunConfig- [objekt](how-to-set-up-training-targets.md#submit). Det här mönstret ger dig mycket flexibilitet och maximal kontroll.

För att under lätta djup inlärnings modellen är Azure Machine Learning python SDK en alternativ högre abstraktion, klassen uppskattning, som gör det möjligt för användarna att enkelt konstruera kör konfigurationer. Du kan skapa och använda en generisk [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka ett utbildnings skript med hjälp av valfritt ramverk som du väljer (till exempel scikit-information) som du vill köra på alla beräknings mål, oavsett om det är en lokal dator, en enskild virtuell dator i Azure eller ett GPU-kluster i Azure. För PyTorch-, TensorFlow-och kedje uppgifter-aktiviteter tillhandahåller Azure Machine Learning också respektive [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) och [kedje](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) bedömare för att förenkla användningen av dessa ramverk.

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
`script_params`| Ord lista som anger kommando rads argument för ditt utbildnings skript `entry_script`, i form av `<command-line argument, value>` par. Om du vill ange en utförlig flagga `script_params`i använder `<command-line argument, "">`du.
`compute_target`| Fjärrberäknings mål som ditt utbildnings skript ska köras på, i det här fallet ett Azure Machine Learning Compute-kluster ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Observera även om AmlCompute-kluster är det vanligaste målet, är det också möjligt att välja andra beräknings mål typer, till exempel virtuella Azure-datorer eller till och med lokal dator.)
`entry_script`| FilePath (relativt till den `source_directory`) för utbildning-skriptet som ska köras på den fjärranslutna databearbetning. Den här filen och eventuella ytterligare filer som den är beroende av, bör finnas i den här mappen
`conda_packages`| Lista över Python-paket installeras via conda som krävs för dina utbildningsskript.  

Konstruktorn har en annan parameter med namnet `pip_packages` som du använder för eventuella pip-paket som behövs

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
> På samma sätt kan du skriva loggar från din utbildning som körs till `./logs` mappen. Använda Azure Machine Learning [TensorBoard integrering](https://aka.ms/aml-notebook-tb) se till att du skriver TensorBoard loggarna till den här mappen. När din körning pågår, kommer du att kunna starta TensorBoard och strömma dessa loggar.  Senare, kan du också återställa loggarna från någon av dina tidigare körningar.
>
> Till exempel för att hämta en fil som skrivs till den *matar ut* mappen till den lokala datorn efter remote utbildning kör: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuerad utbildning och anpassad Docker-avbildningar

Det finns två ytterligare scenarier som du kan utföra med den `Estimator`:
* Med en anpassad dockeravbildning
* Distribuerad utbildning i ett kluster med flera noder

Följande kod visar hur du utför distribuerad utbildning för en keras-modell. I stället för att använda standard Azure Machine Learning avbildningar anges dessutom en anpassad Docker-avbildning från Docker Hub `continuumio/miniconda` för träning.

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
`custom_docker_base_image`| Namnet på avbildningen som du vill använda. Ange endast-avbildningarna i offentliga docker-databaser (i det här fallet Docker Hub). Använd konstruktorns `environment_definition` parameter i stället om du vill använda en avbildning från en privat Docker-lagringsplats. [Se exempel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Antalet noder som ska användas för utbildning-jobbet. | `1`
`process_count_per_node`| Antal processer (eller ”anställda”) för att köra på varje nod. I sådana fall kan du använda den `2` GPU: er tillgängliga på varje nod.| `1`
`distributed_backend`| Serverdelen för att starta distribuerade utbildning som kostnadsuppskattning erbjuder via MPI.  Att utföra parallella eller distribuerade utbildning (t.ex. `node_count`> 1 eller `process_count_per_node`> 1 eller båda), ange `distributed_backend='mpi'`. MPI-implementering som används av AML [öppna MPI](https://www.open-mpi.org/).| `None`

Slutligen skicka utbildningsjobbet:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>GitHub spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Till exempel loggas det aktuella inchecknings-ID: t för databasen som en del av historiken.

## <a name="examples"></a>Exempel
För en bärbar dator som visar grunderna i ett uppskattnings mönster, se:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

För en bärbar dator som tränar en scikit-modell med hjälp av en uppskattning kan du läsa:
* [självstudier/img-klassificering – del 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

För antecknings böcker i utbildnings modeller med hjälp av detaljerade uppskattningar för djup inlärnings ramverk, se:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Skapa PyTorch-modeller](how-to-train-pytorch.md)
* [Skapa TensorFlow-modeller](how-to-train-tensorflow.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
