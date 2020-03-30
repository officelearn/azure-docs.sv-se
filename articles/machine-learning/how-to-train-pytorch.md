---
title: Träna djupinlärning PyTorch modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina PyTorch-utbildningsskript i företagsskala med azure machine learnings PyTorch-uppskattningsklass.  Exemplet skript klassificera kyckling och kalkon bilder för att bygga en djupinlärning neurala nätverk baserat på PyTorch's transfer lärande handledning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834866"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Träna Pytorch deep learning-modeller i stor skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du kör dina [PyTorch-utbildningsskript](https://pytorch.org/) i företagsskala med azure machine learnings [pytorch-uppskattningsklass.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)  

Exemplet skript i den här artikeln används för att klassificera kyckling och kalkon bilder för att bygga en djupinlärning neurala nätverk baserat på PyTorch överföring lärande [handledning](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html). 

Oavsett om du tränar en PyTorch-modell med djupinlärning från grunden eller om du tar in en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut utbildningsjobb med öppen källkod med elastiska molnberäkningsresurser. Du kan skapa, distribuera, version och övervaka modeller i produktionsklass med Azure Machine Learning. 

Läs mer om [djupinlärning vs maskininlärning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Krav

Kör den här koden på någon av dessa miljöer:

- Azure Machine Learning-beräkningsinstans – inga hämtningar eller installation nödvändiga

    - Slutför [självstudiekursen: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad anteckningsboksserver som är förinstallerad med SDK och exempeldatabasen.
    - I mappen med djupinlärning i exemplen på den bärbara servern hittar du en slutförd och utökad anteckningsbok genom att navigera till den här katalogen: **hur du ska använda azureml-> utbildning med djupinlärning > tåg-hyperparameter-tune-deploy-with-pytorch-mappen.** 
 
 - Din egen Jupyter Notebook server

    - [Installera SDK för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).
    - [Hämta exempelskriptfilerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Du kan också hitta en ifylld [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) av den här guiden på GitHub exempelsidan. Anteckningsboken innehåller utökade avsnitt som täcker intelligent hyperparameterjustering, modelldistribution och widgetar för bärbara datorer.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

Det här avsnittet ställer in utbildningsexperimentet genom att läsa in de python-paket som krävs, initiera en arbetsyta, skapa ett experiment och ladda upp träningsdata och utbildningsskript.

### <a name="import-packages"></a>Importera paket

Importera först nödvändiga Python-bibliotek.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

[Arbetsytan Azure Machine Learning](concept-workspace.md) är resursen på den högsta nivån för tjänsten. Det ger dig en centraliserad plats att arbeta med alla artefakter du skapar. I Python SDK kan du komma åt arbetsytans [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefakter genom att skapa ett objekt.

Skapa ett arbetsyteobjekt från `config.json` filen som [skapats](#prerequisites)i avsnittet förutsättningar .

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Skapa ett djupinlärningsexperiment

Skapa ett experiment och en mapp för att hålla dina träningsskript. Skapa i det här exemplet ett experiment som kallas "pytorchfåglar".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Hämta data

Datauppsättningen består av cirka 120 träningsbilder vardera för kalkoner och kycklingar, med 100 valideringsbilder för varje klass. Vi kommer att ladda ner och extrahera datauppsättningen som en del av vår utbildning skript `pytorch_train.py`. Bilderna är en delmängd av [open images v5-datauppsättningen](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Förbereda utbildningsskript

I den här självstudien finns redan utbildningsskriptet. `pytorch_train.py` I praktiken kan du ta alla anpassade utbildningsskript, som det är, och köra det med Azure Machine Learning.

Ladda upp Pytorch `pytorch_train.py`utbildningsskript, .

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Men om du vill använda Azure Machine Learning spårning och mått funktioner, måste du lägga till en liten mängd kod i din utbildning skript. Exempel på måttspårning finns `pytorch_train.py`i .

## <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräkningsmål för ditt PyTorch-jobb som ska köras på. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning-beräkningskluster.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Mer information om beräkningsmål finns i [vad som är en beräkningsmålsartikel.](concept-compute-target.md)

## <a name="create-a-pytorch-estimator"></a>Skapa en PyTorch-uppskattning

[PyTorch-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) är ett enkelt sätt att starta ett PyTorch-utbildningsjobb på ett beräkningsmål.

PyTorch-uppskattningen implementeras genom den [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) allmänna klassen, som kan användas för att stödja alla ramverk. Mer information om träningsmodeller med hjälp av den allmänna uppskattningen finns i [tågmodeller med Azure Machine Learning med uppskattning](how-to-train-ml-models.md)

Om ditt träningsskript behöver ytterligare pip- eller conda-paket för att köras kan du ha `pip_packages` paketen installerade på den resulterande docker-avbildningen genom att skicka deras namn genom argumenten och. `conda_packages`

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

Mer information om hur du anpassar pythonmiljön finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

## <a name="submit-a-run"></a>Skicka en körning

[Run-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körningshistoriken medan jobbet körs och efter att det har slutförts.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereda**: En docker avbildning skapas enligt PyTorch-uppskattningen. Avbildningen överförs till arbetsytans behållarregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI-klustret kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Kör:** Alla skript i skriptmappen överförs till beräkningsmålet, datalager monteras eller kopieras och entry_script körs. Utdata från stdout och mappen ./logs strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen ./output i körningen kopieras över till körningshistoriken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ner en modell

När du har tränat modellen kan du registrera den på din arbetsyta. Med modellregistrering kan du lagra och modellera dina modeller på arbetsytan för att förenkla [modellhantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Modellen du just registrerade distribueras på exakt samma sätt som alla andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning du använde för utbildning. Distributionen innehåller ett avsnitt om hur du registrerar modeller, men du kan hoppa direkt till att [skapa ett beräkningsmål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

Du kan också hämta en lokal kopia av modellen med hjälp av run-objektet. I utbildningsskriptet beständigs modellen i en lokal mapp i utbildningsskriptet. `pytorch_train.py` Du kan använda run-objektet för att hämta en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuerad träning

Kalkylatorn [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) stöder också distribuerad utbildning över CPU- och GPU-kluster. Du kan enkelt köra distribuerade PyTorch-jobb och Azure Machine Learning hanterar orkestrering för dig.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är en öppen källkod, alla minska ram för distribuerad utbildning som utvecklats av Uber. Det erbjuder en enkel väg till distribuerade GPU PyTorch jobb.

Om du vill använda [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) Horovod `distributed_training` anger du ett objekt för parametern i PyTorch-konstruktorn. Den här parametern säkerställer att Horovod-biblioteket är installerat så att du kan använda i ditt träningsskript.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod och dess beroenden kommer att installeras för dig, `train.py` så att du kan importera den i din utbildning skript enligt följande:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportera till ONNX

Om du vill optimera inferensen med [ONNX Runtime](concept-onnx.md)konverterar du din tränade PyTorch-modell till ONNX-formatet. Inferens, eller modellbedömning, är den fas där den distribuerade modellen används för förutsägelse, oftast på produktionsdata. Se [handledningen](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) för ett exempel.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat ett neuralt nätverk med djupinlärning med PyTorch på Azure Machine Learning. Om du vill veta hur du distribuerar en modell fortsätter du till vår artikel om modelldistribution.

> [!div class="nextstepaction"]
> [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körningsmått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referensarkitektur för distribuerad djupinlärningsutbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

