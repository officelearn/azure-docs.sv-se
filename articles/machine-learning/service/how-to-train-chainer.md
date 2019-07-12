---
title: Träna och registrera Chainer modeller
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du tränar och registrerar en modell för Chainer med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 8ecefccbdf5f02652e935858b6ae8fb4cdfde640
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840029"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Träna och registrera Chainer modeller i hög skala med Azure Machine Learning-tjänsten

Den här artikeln visar hur du tränar och registrerar en modell för Chainer med hjälp av Azure Machine Learning-tjänsten. Den använder de populära [MNIST datauppsättning](http://yann.lecun.com/exdb/mnist/) att klassificera handskriven siffror med hjälp av ett djupa neurala nätverk (DNN) som skapats med den [Chainer Python-bibliotek](https://Chainer.org) som körs ovanpå [numpy](https://www.numpy.org/).

Chainer är ett övergripande neurala nätverk API kan köra längst upp på andra populära DNN-ramverk för att förenkla utvecklingen. Med Azure Machine Learning-tjänsten kan du snabbt skala ut utbildningsjobb med hjälp av elastisk molnberäkningsresurser. Du kan också spåra ditt träningskörningar, version modeller, distribuera modeller och mycket mer.

Om du utvecklar en modell för Chainer från grunden eller du behöver en befintlig modell i molnet, Azure Machine Learning-tjänsten kan hjälpa dig att skapa modeller för produktionsklart.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden på något av dessa miljöer:

- Azure Machine Learning Notebook VM - inga nedladdningar eller installeras

    - Slutför den [molnbaserade notebook Snabbstart](quickstart-run-cloud-notebook.md) att skapa en dedikerad anteckningsboksserver förinstallerade med SDK: N och exempellagringsplatsen.
    - Mappen samples på notebook-server innehåller en slutförd notebook och filer i den **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** mapp.  Den bärbara datorn innehåller utökade avsnitt som täcker intelligent finjustering av hyperparametrar, distribution av modeller och notebook widgetar.

- En egen Jupyter Notebook-server

    - [Installera Azure Machine Learning SDK för Python](setup-create-workspace.md#sdk)
    - [Skapa en konfigurationsfil för arbetsyta](setup-create-workspace.md#write-a-configuration-file)
    - Hämta skriptfilen [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) i den här guiden på sidan med GitHub kodexempel. Den bärbara datorn innehåller utökade avsnitt som täcker intelligent finjustering av hyperparametrar, distribution av modeller och notebook widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

Det här avsnittet konfigurerar träningsexperimentet av inläsning av nödvändiga python-paket, initierar en arbetsyta, skapa ett experiment och laddar upp den utbildningsinformationen och skripten för utbildning.

### <a name="import-packages"></a>Importera paket

Importera först azureml.core Python-biblioteket ad Visa versionsnumret.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

Den [Azure Machine Learning-tjänstens arbetsyta](concept-workspace.md) är den översta resursen för tjänsten. Den ger dig en centraliserad plats för att arbeta med alla artefakter som du skapar. I Python-SDK kan du komma åt arbetsytan artefakter genom att skapa en [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objekt.

Skapa en arbetsyta objekt från den `config.json` fil som skapats i den [krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Skapa en projektkatalog
Skapa en katalog som innehåller alla nödvändiga koden från din lokala dator som du behöver åtkomst till fjärresursen. Detta inkluderar skriptet utbildning och eventuella ytterligare filer som dina utbildningsskript beror på.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Förbereda inlärningsskript

I de här självstudierna utbildning skriptet **chainer_mnist.py** redan tillhandahålls åt dig. I praktiken, bör du kunna ta alla anpassade inlärningsskript eftersom är och kör den med Azure ML utan att behöva ändra din kod.

För att använda Azure ML spårning och funktioner för mått, måste du lägga till en viss Azure ML-kod i skriptet utbildning.  Skriptet utbildning **chainer_mnist.py** visar hur du loggar in några mått till din Azure ML kör. Åtkomst till den Azure ML gör du `Run` objekt i skriptet.

Kopiera skriptet utbildning **chainer_mnist.py** i projektkatalogen.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment och en mapp för att lagra dina utbildningsskript. I det här exemplet skapar du ett experiment som kallas ”chainer mnist”.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Skapa eller hämta ett beräkningsmål

Du behöver en [beräkningsmålet](concept-compute-target.md) för att träna din modell. I den här självstudien använder du Azure ML hanteras beräkning (AmlCompute) för remote utbildning beräkning.

**Skapandet av AmlCompute tar cirka 5 minuter**. Om AmlCompute med det namnet finns redan i din arbetsyta, kommer den här koden hoppa över skapandeprocessen.  

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

Mer information om beräkningsmål finns i den [vad är ett beräkningsmål](concept-compute-target.md) artikeln.

## <a name="create-a-chainer-estimator"></a>Skapa en Chainer kostnadsuppskattning

Den [Chainer kostnadsuppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) ger ett enkelt sätt att starta Chainer upplärningsjobb på din beräkningsmål.

Chainer kostnadsuppskattning implementeras via ett allmänt [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klass, som kan användas för att stödja valfritt ramverk. Läs mer om träna modeller med hjälp av generiska kostnadsuppskattning [träna modeller med Azure Machine Learning med hjälp av kostnadsuppskattning](how-to-train-ml-models.md)

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

Den [köra objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnitt för körningshistoriken medan jobbet körs och när den är slutförd.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Eftersom körningen körs passerar i följande steg:

- **Förbereda**: En docker-avbildning skapas enligt Chainer kostnadsuppskattning. Avbildningen överförs till den arbetsytan container registry och cachelagras för senare körningar. Loggarna strömmas också till körningshistoriken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker att skala upp om Batch AI-kluster kräver fler noder att köra körningen än vad som är tillgängliga.

- **Körning**: Alla skript i mappen skript överförs till beräkningsmål datalager monterad eller kopieras och entry_script körs. Utdata från stdout och. / loggkatalogen strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Det. / matar ut mappen för körningen kopieras till körningshistoriken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen, kan du spara och registrera den till din arbetsyta. Modellen kan du lagra och version dina modeller i din arbetsyta för att förenkla [modellera hantering och distribution](concept-model-management-and-deployment.md).

Lägg till följande kod i skriptet utbildning **chainer_mnist.py**, för att spara modellen. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Registrera modellen till din arbetsyta med följande kod.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Nästa steg

I den här artikeln tränas en Chainer modell på Azure Machine Learning-tjänsten. 

* Om du vill veta hur du distribuerar en modell kan du fortsätta till vår [modellera distribution](how-to-deploy-and-where.md) artikeln.

* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)

* [Spåra kör mått vid träning](how-to-track-experiments.md)
