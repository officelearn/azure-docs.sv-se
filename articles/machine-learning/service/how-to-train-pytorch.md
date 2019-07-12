---
title: Träna och registrera PyTorch-modeller
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du tränar och registrera en PyTorch-modellen med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: d9c953eeecedf14a8f3fae43c5d4713252d58b4c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840086"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Träna och registrera PyTorch modeller i hög skala med Azure Machine Learning-tjänsten

Den här artikeln visar hur du tränar och registrera en PyTorch-modellen med hjälp av Azure Machine Learning-tjänsten. Den är baserad på [Pytorchs induktiv inlärning självstudien](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) som skapar en klassificerare för djupa neurala nätverk (DNN) efter bilder på kycklingar och kalkoner.

[PyTorch](https://pytorch.org/) är en öppen källkod och databaserad ramverk som ofta används för att skapa djupa neurala nätverk (DNN). Med Azure Machine Learning-tjänsten kan du snabbt skala ut öppen källkod-utbildningsjobb med hjälp av elastisk molnberäkningsresurser. Du kan också spåra ditt träningskörningar, version modeller, distribuera modeller och mycket mer.

Om du utvecklar en modell för PyTorch från grunden eller du behöver en befintlig modell i molnet, Azure Machine Learning-tjänsten kan hjälpa dig att skapa modeller för produktionsklart.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden på något av dessa miljöer:

 - Azure Machine Learning Notebook VM - inga nedladdningar eller installeras

    - Slutför den [molnbaserade notebook Snabbstart](quickstart-run-cloud-notebook.md) att skapa en dedikerad anteckningsboksserver förinstallerade med SDK: N och exempellagringsplatsen.
    - I mappen samples på notebook-server kan du hitta en slutförd och utökade anteckningsbok genom att navigera till den här katalogen: **How-to-till-användning – azureml > utbildning med deep learning > train-hyperparameter-tune-deploy-with-pytorch** mappen. 
 
 - En egen Jupyter Notebook-server

    - [Installera Azure Machine Learning SDK för Python](setup-create-workspace.md#sdk)
    - [Skapa en konfigurationsfil för arbetsyta](setup-create-workspace.md#write-a-configuration-file)
    - [Ladda ned exempelfilerna för skript](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) i den här guiden på sidan med kodexempel på GitHub. Den bärbara datorn innehåller utökade avsnitt som täcker intelligent finjustering av hyperparametrar, distribution av modeller och notebook widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

Det här avsnittet konfigurerar träningsexperimentet av inläsning av nödvändiga python-paket, initierar en arbetsyta, skapa ett experiment och laddar upp den utbildningsinformationen och skripten för utbildning.

### <a name="import-packages"></a>Importera paket

Först importera de nödvändiga Python-biblioteken.

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

Den [Azure Machine Learning-tjänstens arbetsyta](concept-workspace.md) är den översta resursen för tjänsten. Den ger dig en centraliserad plats för att arbeta med alla artefakter som du skapar. I Python-SDK kan du komma åt arbetsytan artefakter genom att skapa en [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objekt.

Skapa en arbetsyta objekt från den `config.json` fil som skapats i den [krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment och en mapp för att lagra dina utbildningsskript. I det här exemplet skapar du ett experiment som kallas ”pytorch-namnet”.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Hämta data

Datauppsättningen består av cirka 120 utbildning avbildningar för kalkoner och kycklingar, med 100 verifiering bilder för varje klass. Vi ska ladda ned och extrahera datauppsättningen som en del av vår inlärningsskript `pytorch_train.py`. Bilderna är en delmängd av den [öppna bilder v5 datauppsättning](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Förbereda utbildningsskript

I de här självstudierna skriptet utbildning `pytorch_train.py`, har redan angetts. I praktiken kan du ta alla anpassade utbildningsskript, som är och köra det med Azure Machine Learning-tjänsten.

Ladda upp inlärningsskript Pytorch `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Om du vill använda Azure Machine Learning-tjänsten spårning och funktioner för mått, kommer du måste lägga till en viss kod i skriptet utbildning. Exempel på mått spårning finns i `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräkningsmål för PyTorch jobbet ska köras på. I det här exemplet skapar du ett GPU-aktiverade Azure Machine Learning-beräkningskluster.

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

Mer information om beräkningsmål finns i den [vad är ett beräkningsmål](concept-compute-target.md) artikeln.

## <a name="create-a-pytorch-estimator"></a>Skapa en PyTorch kostnadsuppskattning

Den [PyTorch kostnadsuppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) ger ett enkelt sätt att starta ett PyTorch-utbildningsjobb i ett beräkningsmål.

Kostnadsuppskattning för PyTorch implementeras via ett allmänt [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klass, som kan användas för att stödja valfritt ramverk. Läs mer om träna modeller med hjälp av generiska kostnadsuppskattning [träna modeller med Azure Machine Learning med hjälp av kostnadsuppskattning](how-to-train-ml-models.md)

Om dina utbildningsskript behöver ytterligare pip eller conda-paket för att köra, kan du använda de paket som är installerad på den resulterande docker-avbildningen genom att skicka deras namn via den `pip_packages` och `conda_packages` argument.

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

## <a name="submit-a-run"></a>Skicka en körning

Den [köra objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnitt för körningshistoriken medan jobbet körs och när den är slutförd.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Eftersom körningen körs passerar i följande steg:

- **Förbereda**: En docker-avbildning skapas enligt PyTorch kostnadsuppskattning. Avbildningen överförs till den arbetsytan container registry och cachelagras för senare körningar. Loggarna strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker att skala upp om Batch AI-kluster kräver fler noder att köra körningen än vad som är tillgängliga.

- **Körning**: Alla skript i mappen skript överförs till beräkningsmål datalager monterad eller kopieras och entry_script körs. Utdata från stdout och. / loggkatalogen strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Det. / matar ut mappen för körningen kopieras till körningshistoriken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen, kan du registrera den till din arbetsyta. Modellen kan du lagra och version dina modeller i din arbetsyta för att förenkla [modellera hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Du kan också hämta en lokal kopia av modellen med hjälp av kör-objektet. I skriptet utbildning `pytorch_train.py`, en PyTorch spara objekt kvarstår modellen till en lokal mapp (lokal till beräkningsmål-). Du kan använda objektet kör för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuerad utbildning

Den [ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) kostnadsuppskattning stöder också distribuerad utbildning i Processorn och GPU-kluster. Du kan enkelt köra distribuerade PyTorch-jobb och Azure Machine Learning-tjänsten kommer att hantera dirigering åt dig.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är en öppen källkod, minska ramverk för distribuerad utbildning har utvecklats av Uber. Den erbjuder ett enkelt sätt att distribuerade GPU PyTorch-jobb.

Om du vill använda Horovod, ange ett [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objekt för den `distributed_training` parametern i konstruktorn PyTorch. Den här parametern säkerställer att Horovod biblioteket är installerat som du kan använda i dina utbildningsskript.


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
Horovod och dess beroenden kommer att installeras åt dig, så kan du importera det i dina utbildningsskript `train.py` på följande sätt:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportera till ONNX

Att optimera inferens med den [ONNX Runtime](concept-onnx.md), konvertera din träningsmodell PyTorch till ONNX-formatet. Inferens eller modell bedömning är fasen där distribuerade modellen används för förutsägelse oftast på produktionsdata. Se den [självstudien](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) ett exempel.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du tränas och registrerat en PyTorch-modell i Azure Machine Learning-tjänsten. Lär dig hur du distribuerar en modell, Fortsätt till vår artikel för distribution av modellen.

> [!div class="nextstepaction"]
> [Hur och var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Spåra kör mått vid träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
