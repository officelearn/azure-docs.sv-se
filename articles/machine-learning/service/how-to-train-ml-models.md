---
title: Träna machine learning-modeller med Azure Machine Learning
description: Lär dig hur du utför en nod och distribuerade utbildning av traditionella machine learning och deep learning-modeller med Azure Machine Learning-tjänster
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983590"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Så här att träna modeller med Azure Machine Learning

Utbildning machine learning-modeller, särskilt djupa neurala nätverk är ofta en tid - och beräkningsintensiva uppgift. När du är klar skriver utbildning-skriptet och körs på en liten delmängd av data på din lokala dator, vill du förmodligen att skala upp din arbetsbelastning.

För att underlätta utbildning, ger Python SDK för Azure Machine Learning en övergripande abstraktion klassen kostnadsuppskattning, vilket gör att användare kan enkelt skapa sina modeller i Azure-ekosystemet. Du kan skapa och använda en kostnadsuppskattning-objektet för att skicka någon kod för utbildning som du vill köra på fjärranslutna beräkningar, oavsett om det är en nod kör eller distribuerade utbildning i ett GPU-kluster. Azure Machine Learning ger också respektive anpassade PyTorch och TensorFlow Estimators som gör det enkelt att använda de här ramverken för PyTorch och TensorFlow-jobb.

## <a name="train-with-an-estimator"></a>Träna med en kostnadsuppskattning

När du har skapat din [arbetsytan](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace) och Ställ in din [utvecklingsmiljö](how-to-configure-environment.md), träna en modell i Azure Machine Learning omfattar följande steg:  
1. [Skapa fjärransluten beräkningsmål](how-to-set-up-training-targets.md)
2. [Ladda upp dina utbildningsdata](how-to-access-data.md) (valfritt)
3. Skapa skriptet utbildning
4. Skapa ett objekt för kostnadsuppskattning
5. Skicka jobbet utbildning

Den här artikeln handlar om steg 4 – 5. Steg 1 – 3, finns i det här [självstudien](tutorial-train-models-with-aml.md) ett exempel.

### <a name="single-node-training"></a>Nod-utbildning

Följande kod visar hur en nod utbildning som körs på fjärranslutna beräkning i Azure för en scikit-Läs modellen. Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#batch) objekt `compute_target` och din [datalager](how-to-access-data.md) objektet `ds`.

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

Ovanstående kodfragmentet anger följande parametrar till konstruktorn kostnadsuppskattning:
* `source_directory`: Den lokala katalogen som innehåller hela din kod som behövs för utbildningsjobbet. Den här mappen kopieras från din lokala dator till den fjärranslutna beräkningen 
* `script_params`: En ordlista som att ange kommandoradsargument för att dina utbildningsskript `entry_script`, i form av < kommandoradsargumentet, värde > par
* `compute_target`: Den fjärranslutna beräkning som utbildning skriptet ska köras på, i det här fallet en [Batch AI](how-to-set-up-training-targets.md#batch) kluster
* `entry_script`: Filepath (relativt till den `source_directory`) för utbildning-skriptet som ska köras på den fjärranslutna databearbetning. Den här filen och eventuella ytterligare filer som den är beroende av, bör finnas i den här mappen
* `conda_packages`: Lista över Python-paket installeras via conda som krävs för dina utbildningsskript.  
Konstruktorn har en annan parameter med namnet `pip_packages` som du kan använda några pip-paket som behövs

Nu när du har skapat ditt kostnadsuppskattning-objekt kan du skicka utbildningsjobbet som ska köras i beräkningen som fjärråtkomst via ett anrop till den `submit` fungerar på din [Experiment](concept-azure-machine-learning-architecture.md#experiment) objektet `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Specialmappar** två mappar *matar ut* och *loggar*, ta emot särskild behandling av Azure Machine Learning. Under utbildning, om du skriver filer till mappar för *matar ut* och *loggar* som är i förhållande till rotkatalogen (`./outputs` och `./logs`respektive), får de här filerna överförs automatiskt till din körningshistoriken så att du har åtkomst till dem när din körning är klar. 
>
> Åtkomst till artefakter som skapats vid träning (till exempel modellfiler kontrollpunkter, filer eller ritade bilder) skriva dem till den `./outputs` mapp.
>
> På samma sätt du kan skriva loggar från utbildning kör till den `./logs` mapp. Använda Azure Machine Learning [TensorBoard integrering](https://aka.ms/aml-notebook-tb) se till att du skriver TensorBoard loggarna till den här mappen. När din körning pågår, kommer du att kunna starta TensorBoard och strömma dessa loggar.  Senare, kan du också återställa loggarna från någon av dina tidigare körningar.
>
> Till exempel för att hämta en fil som skrivs till den *matar ut* mappen till den lokala datorn efter remote utbildning kör: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuerad utbildning och anpassad Docker-avbildningar

Det finns två ytterligare scenarier som du kan utföra med kostnadsuppskattning:
1. Med en anpassad dockeravbildning
2. Distribuerad utbildning i ett kluster med flera noder

Följande kod visar hur du utför distribuerad utbildning för ett CNTK-modellen. Dessutom istället för att använda standard Azure Machine Learning-avbildningar, det förutsätter att du har en egen anpassad docker-avbildning som du vill använda för träning.

Du bör redan har skapat din [beräkningsmålet](how-to-set-up-training-targets.md#batch) objektet `compute_target`. Du kan skapa kostnadsuppskattning på följande sätt:

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

Koden ovan visar följande nya parametrar till konstruktorn kostnadsuppskattning:
* `custom_docker_base_image`: Namnet på avbildningen som du vill använda. Du kan bara ange-avbildningarna i offentliga docker-databaser (i det här fallet Docker Hub). Om du vill använda en avbildning från ett privat docker-lagringsplats, Använd konstruktorn `environment_definition` parameter i stället
* `node_count`: Antalet noder för utbildning-jobbet. Det här argumentet som standard `1`
* `process_count_per_node`: Antalet processer (eller ”anställda”) för att köra på varje nod. I sådana fall kan du använda den `2` GPU: er tillgängliga på varje nod. Det här argumentet som standard `1`
* `distributed_backend`: Serverdelen för att starta distribuerade utbildning som kostnadsuppskattning erbjuder via MPI. Det här argumentet som standard `None`. Om du vill utföra parallella eller distribuerade utbildning (t.ex. `node_count`> 1 eller `process_count_per_node`> 1 eller båda), ange `distributed_backend='mpi'`. MPI-implementering som används av AML [öppna MPI](https://www.open-mpi.org/).

Slutligen skicka utbildningsjobbet:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Exempel
En självstudiekurs om hur du tränar en modell för sklearn finns:
* `tutorials/01.train-models.ipynb`

En självstudiekurs om distribuerade CNTK med anpassade docker finns:
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

Hämta dessa anteckningsböcker:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Skapa PyTorch-modeller](how-to-train-pytorch.md)
* [Skapa TensorFlow-modeller](how-to-train-tensorflow.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
