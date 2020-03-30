---
title: Träna djupinlärning Chainer modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina PyTorch-utbildningsskript i företagsskala med hjälp av azure Machine Learning Chainer-uppskattningsklassen.  Exempelskriptet klassificerar handskrivna sifferbilder för att skapa ett neuralt nätverk med djupinlärning med hjälp av Chainer Python-biblioteket som körs ovanpå numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536621"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Träna och registrera Chainer-modeller i stor skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du kör dina [Chainer-utbildningsskript](https://chainer.org/) i företagsskala med hjälp av azure Machine Learning [Chainer-uppskattningsklassen.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Exempelutbildningsskriptet i den här artikeln använder den populära [MNIST-datauppsättningen](http://yann.lecun.com/exdb/mnist/) för att klassificera handskrivna siffror med hjälp av ett djupt neuralt nätverk (DNN) byggt med Chainer Python-biblioteket som körs ovanpå [numpy](https://www.numpy.org/).

Oavsett om du tränar en Chainer-modell med djupinlärning från grunden eller om du tar in en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut utbildningsjobb med öppen källkod med hjälp av elastiska molnberäkningsresurser. Du kan skapa, distribuera, version och övervaka modeller i produktionsklass med Azure Machine Learning. 

Läs mer om [djupinlärning vs maskininlärning](concept-deep-learning-vs-machine-learning.md).

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="prerequisites"></a>Krav

Kör den här koden på någon av dessa miljöer:

- Azure Machine Learning-beräkningsinstans – inga hämtningar eller installation nödvändiga

    - Slutför [självstudiekursen: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad anteckningsboksserver som är förinstallerad med SDK och exempeldatabasen.
    - I mappen med djupinlärning i exemplen på den bärbara servern hittar du en slutförd anteckningsbok och filer i mappen **how-to-use-azureml > ml-frameworks > chainer >-distribution > train-hyperparameter-tune-deploy-with-chainer-folder.**  Anteckningsboken innehåller utökade avsnitt som täcker intelligent hyperparameterjustering, modelldistribution och widgetar för bärbara datorer.

- Din egen Jupyter Notebook server

    - [Installera SDK för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).
    - Hämta exempelskriptfilen [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Du kan också hitta en ifylld [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) av denna guide på GitHub exempel sida. Anteckningsboken innehåller utökade avsnitt som täcker intelligent hyperparameterjustering, modelldistribution och widgetar för bärbara datorer.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

Det här avsnittet ställer in utbildningsexperimentet genom att läsa in de python-paket som krävs, initiera en arbetsyta, skapa ett experiment och ladda upp träningsdata och utbildningsskript.

### <a name="import-packages"></a>Importera paket

Importera först azureml.core Python-biblioteket och visa versionsnumret.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

[Arbetsytan Azure Machine Learning](concept-workspace.md) är resursen på den högsta nivån för tjänsten. Det ger dig en centraliserad plats att arbeta med alla artefakter du skapar. I Python SDK kan du komma åt arbetsytans [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) artefakter genom att skapa ett objekt.

Skapa ett arbetsyteobjekt `config.json` genom att läsa filen som skapats i [förutsättningarna:](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Skapa en projektkatalog
Skapa en katalog som innehåller all nödvändig kod från den lokala datorn som du behöver åtkomst till på fjärrresursen. Detta inkluderar utbildningsskriptet och eventuella ytterligare filer som utbildningsskriptet är beroende av.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Förbered utbildningsskript

I den här självstudien finns redan utbildningsskriptet **chainer_mnist.py** för dig. I praktiken bör du kunna ta alla anpassade utbildningsskript som de är och köra det med Azure ML utan att behöva ändra koden.

Om du vill använda Azure ML-spårnings- och måttfunktionerna lägger du till en liten mängd Azure ML-kod i ditt utbildningsskript.  Utbildningsskriptet **chainer_mnist.py** visar hur du loggar vissa mått till din `Run` Azure ML-körning med objektet i skriptet.

Det medföljande utbildningsskriptet använder `datasets.mnist.get_mnist` exempeldata från chainerfunktionen.  För dina egna data kan du behöva använda steg som [Ladda upp datauppsättning och skript](how-to-train-keras.md#data-upload) för att göra data tillgängliga under träningen.

Kopiera utbildningsskriptet **chainer_mnist.py** till projektkatalogen.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Skapa ett djupinlärningsexperiment

Skapa ett experiment. Skapa i det här exemplet ett experiment som kallas "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Skapa eller hämta ett beräkningsmål

Du behöver ett [beräkningsmål](concept-compute-target.md) för att träna din modell. I det här exemplet använder du Azure ML-hanterad beräkning (AmlCompute) för din beräkningsresurs för fjärrutbildning.

**Skapandet av AmlCompute tar cirka 5 minuter.** Om AmlCompute med det namnet redan finns på arbetsytan hoppar den här koden över skapandeprocessen.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Mer information om beräkningsmål finns i [vad som är en beräkningsmålsartikel.](concept-compute-target.md)

## <a name="create-a-chainer-estimator"></a>Skapa en chainer-uppskattning

[Chainer-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) är ett enkelt sätt att starta Chainer-utbildningsjobb på ditt beräkningsmål.

Chainer-kalkylatorn implementeras genom [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) den generiska klassen, som kan användas för att stödja alla ramverk. Mer information om träningsmodeller med hjälp av den allmänna uppskattningen finns i [tågmodeller med Azure Machine Learning med uppskattning](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Skicka en körning

[Run-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körningshistoriken medan jobbet körs och efter att det har slutförts.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereda**: En dockeravbildning skapas enligt Chainer-uppskattningen. Avbildningen överförs till arbetsytans behållarregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI-klustret kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Kör:** Alla skript i skriptmappen överförs till beräkningsmålet, datalager monteras eller kopieras och entry_script körs. Utdata från stdout och mappen ./logs strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen ./output i körningen kopieras över till körningshistoriken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen kan du spara och registrera den på arbetsytan. Med modellregistrering kan du lagra och modellera dina modeller på arbetsytan för att förenkla [modellhantering och distribution](concept-model-management-and-deployment.md).


När modellutbildningen har slutförts registrerar du modellen på din arbetsyta med följande kod.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Modellen du just registrerade distribueras på exakt samma sätt som alla andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning du använde för utbildning. Distributionen innehåller ett avsnitt om hur du registrerar modeller, men du kan hoppa direkt till att [skapa ett beräkningsmål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

Du kan också ladda ner en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare modellvalideringsarbete lokalt. I utbildningsskriptet `chainer_mnist.py`beständigs modellen i en lokal mapp (lokal till beräkningsmålet). Du kan använda run-objektet för att hämta en kopia från datalager.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat ett neuralt nätverk med Deep learning med Chainer på Azure Machine Learning. Om du vill veta hur du distribuerar en modell fortsätter du till vår [artikel om modelldistribution.](how-to-deploy-and-where.md)

* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)

* [Spåra körningsmått under träning](how-to-track-experiments.md)

* [Visa vår referensarkitektur för distribuerad djupinlärningsutbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
