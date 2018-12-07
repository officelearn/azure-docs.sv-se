---
title: Utbilda ML-modeller med Azure Machine Learning-tjänsten en kostnadsuppskattning-klass
description: Lär dig hur du utför en nod och distribuerade utbildning av traditionella machine learning och deep learning-modeller med hjälp av Azure Machine Learning services kostnadsuppskattning-klass
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec12
ms.openlocfilehash: 53462fc0aecbb8f5aeef0bb9208264c714ce8394
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011428"
---
# <a name="train-models-with-azure-machine-learning"></a>Träna modeller med Azure Machine Learning

Utbildning machine learning-modeller, särskilt djupa neurala nätverk är ofta en tid - och beräkningsintensiva uppgift. När du är klar skriver utbildning-skriptet och körs på en liten delmängd av data på din lokala dator, vill du förmodligen att skala upp din arbetsbelastning.

För att underlätta utbildning, ger Python SDK för Azure Machine Learning en övergripande abstraktion klassen kostnadsuppskattning, vilket gör att användare kan enkelt skapa sina modeller i Azure-ekosystemet. Du kan skapa och använda en `Estimator` objekt att skicka någon kod för utbildning som du vill köra på fjärranslutna beräkningar, oavsett om det är en nod kör eller distribuerade utbildning i ett GPU-kluster. För PyTorch och TensorFlow-jobb, ger Azure Machine Learning också respektive anpassad `PyTorch` och `TensorFlow` estimators att förenkla med hjälp av de här ramverken.

## <a name="train-with-an-estimator"></a>Träna med en kostnadsuppskattning

När du har skapat din [arbetsytan](concept-azure-machine-learning-architecture.md#workspace) och Ställ in din [utvecklingsmiljö](how-to-configure-environment.md), träna en modell i Azure Machine Learning omfattar följande steg:  
1. Skapa en [remote beräkningsmål](how-to-set-up-training-targets.md)
2. Ladda upp din [träningsdata](how-to-access-data.md) (valfritt)
3. Skapa din [inlärningsskript](tutorial-train-models-with-aml.md#create-a-training-script)
4. Skapa en `Estimator` objekt
5. Skicka jobbet utbildning

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
`script_params`| Ordlista att ange kommandoradsargument för att dina utbildningsskript `entry_script`, i form av < kommandoradsargumentet, värde > par
`compute_target`| Remote beräkningsmål som utbildning skriptet ska köras på, i det här fallet en Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) kluster
`entry_script`| FilePath (relativt till den `source_directory`) för utbildning-skriptet som ska köras på den fjärranslutna databearbetning. Den här filen och eventuella ytterligare filer som den är beroende av, bör finnas i den här mappen
`conda_packages`| Lista över Python-paket installeras via conda som krävs för dina utbildningsskript.  
Konstruktorn har en annan parameter med namnet `pip_packages` som du använder för eventuella pip-paket som behövs

Nu när du har skapat din `Estimator` objekt, skicka utbildningsjobbet som ska köras på den fjärranslutna beräkningen med ett anrop till den `submit` fungerar på din [Experiment](concept-azure-machine-learning-architecture.md#experiment) objektet `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Specialmappar** två mappar *matar ut* och *loggar*, ta emot särskild behandling av Azure Machine Learning. Vid träning, när du skriver filer till mappar för *matar ut* och *loggar* som är i förhållande till rotkatalogen (`./outputs` och `./logs`respektive), filerna kommer automatiskt ladda upp till din körningshistoriken så att du har åtkomst till dem när din körning är klar.
>
> Skapa artefakter vid träning (till exempel modellfiler kontrollpunkter, filer eller ritade bilder) skriva dem till den `./outputs` mapp.
>
> På samma sätt kan du skriva loggar från utbildning kör till den `./logs` mapp. Använda Azure Machine Learning [TensorBoard integrering](https://aka.ms/aml-notebook-tb) se till att du skriver TensorBoard loggarna till den här mappen. När din körning pågår, kommer du att kunna starta TensorBoard och strömma dessa loggar.  Senare, kan du också återställa loggarna från någon av dina tidigare körningar.
>
> Till exempel för att hämta en fil som skrivs till den *matar ut* mappen till den lokala datorn efter remote utbildning kör: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuerad utbildning och anpassad Docker-avbildningar

Det finns två ytterligare scenarier som du kan utföra med den `Estimator`:
* Med en anpassad dockeravbildning
* Distribuerad utbildning i ett kluster med flera noder

Följande kod visar hur du utför distribuerad utbildning för ett CNTK-modellen. Dessutom istället för att använda standard Azure Machine Learning-avbildningar, det förutsätter att du använder en egen anpassad docker-avbildning för utbildning.

Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#amlcompute) objektet `compute_target`. Du skapar kostnadsuppskattning på följande sätt:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

Koden ovan visar följande nya parametrar till den `Estimator` konstruktorn:

Parameter | Beskrivning | Standard
--|--|--
`custom_docker_base_image`| Namnet på avbildningen som du vill använda. Ange endast-avbildningarna i offentliga docker-databaser (i det här fallet Docker Hub). Om du vill använda en avbildning från ett privat docker-lagringsplats, Använd konstruktorn `environment_definition` parameter i stället | `None`
`node_count`| Antalet noder som ska användas för utbildning-jobbet. | `1`
`process_count_per_node`| Antal processer (eller ”anställda”) för att köra på varje nod. I sådana fall kan du använda den `2` GPU: er tillgängliga på varje nod.| `1`
`distributed_backend`| Serverdelen för att starta distribuerade utbildning som kostnadsuppskattning erbjuder via MPI.  Att utföra parallella eller distribuerade utbildning (t.ex. `node_count`> 1 eller `process_count_per_node`> 1 eller båda), ange `distributed_backend='mpi'`. MPI-implementering som används av AML [öppna MPI](https://www.open-mpi.org/).| `None`

Slutligen skicka utbildningsjobbet:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Exempel
För en bärbar dator som tränar en modell för sklearn, se:
* [självstudier/img-klassificering – del 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Notebooks på distribuerade djupinlärning, finns här:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Skapa PyTorch-modeller](how-to-train-pytorch.md)
* [Skapa TensorFlow-modeller](how-to-train-tensorflow.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
