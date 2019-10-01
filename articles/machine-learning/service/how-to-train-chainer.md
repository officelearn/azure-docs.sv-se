---
title: Träna djup inlärning neurala nätverk med kedjer
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina PyTorch-utbildnings skript i företags skala med hjälp av Azure Machine Learnings uppskattare-klass.  Exempel skriptet klassificerar handskrivna siffer bilder för att bygga ett djup inlärnings neurala nätverk med hjälp av kedje-och python-biblioteket som körs ovanpå numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: 70d6bd9507670a8846b2a79509b6b6e571f17e37
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710092"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Träna och registrera kedje modeller i skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör dina [kedje](https://chainer.org/) praktik skript i företags skala med hjälp av Azure Machine learnings uppskattare [-klass.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Exempel övnings skriptet i den här artikeln använder den populära [MNIST-datauppsättningen](http://yann.lecun.com/exdb/mnist/) för att klassificera handskrivna siffror med ett djup neurala-nätverk (DNN) som skapats med hjälp av kedje-python-biblioteket som körs ovanpå [numpy](https://www.numpy.org/).

Oavsett om du tränar en djup inlärnings kedja från grunden eller om du använder en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut utbildnings jobb med elastiska moln med hjälp av elastiska moln beräknings resurser. Du kan bygga, distribuera, hantera och övervaka modeller av produktions klass med Azure Machine Learning. 

Lär dig mer om [djup inlärning vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:

- Azure Machine Learning Notebook VM – inga hämtningar eller installationer behövs

    - Slutför [Självstudie: Konfigurera miljö och arbets](tutorial-1st-experiment-sdk-setup.md) yta för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen Samples djup inlärning på Notebook-servern hittar du en slutförd antecknings bok och filer i **azureml > ml-ramverk >-kedja > distribution > träna-parameter-finjustera-Deploy-with-Chainer** -mappen.  Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

- Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - Ladda ned exempel skript filen [chainer_mnist. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py).
     - Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) av den här guiden på sidan GitHub exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa ett experiment och ladda upp utbildnings data och utbildnings skript.

### <a name="import-packages"></a>Importera paket

Importera först azureml. Core python-biblioteket och Visa versions numret.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) ett objekt.

Skapa ett objekt för arbets ytan genom `config.json` att läsa filen som skapats i [avsnittet krav](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Skapa en projekt katalog
Skapa en katalog som ska innehålla all nödvändig kod från den lokala datorn som du behöver åtkomst till på fjär resursen. Detta inkluderar utbildnings skriptet och eventuella ytterligare filer som utbildnings skriptet är beroende av.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Förbered utbildnings skript

I den här självstudien har utbildnings skriptet **chainer_mnist. py** redan angetts. I praktiken bör du kunna ta med ett anpassat tränings skript som är och köra det med Azure ML utan att behöva ändra koden.

Om du vill använda Azure MLs spårnings-och mått funktioner lägger du till en liten del av Azure ML-koden i utbildnings skriptet.  I övnings skriptet **chainer_mnist. py** visas hur du loggar vissa mått i Azure ml-körningen med hjälp av `Run`-objektet i skriptet.

Det tillhandahållna utbildnings skriptet använder exempel data från kedje `datasets.mnist.get_mnist` funktionen.  För dina egna data kan du behöva använda steg som att [Ladda upp data uppsättning och skript](how-to-train-keras.md#data-upload) för att göra data tillgängliga under utbildningen.

Kopiera övnings skriptet **chainer_mnist. py** till projekt katalogen.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Skapa ett djup inlärnings experiment

Skapa ett experiment. I det här exemplet skapar du ett experiment med namnet "kedjeer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Skapa eller hämta ett beräknings mål

Du behöver ett [beräknings mål](concept-compute-target.md) för att träna din modell. I det här exemplet använder du Azure ML Managed Compute (AmlCompute) för din beräknings resurs för Fjärrutbildning.

**Skapandet av AmlCompute tar cirka 5 minuter**. Om AmlCompute med det namnet redan finns i din arbets yta hoppar den här koden över skapande processen.  

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

Mer information om beräknings mål finns i artikeln [Vad är en Compute Target](concept-compute-target.md) -artikel.

## <a name="create-a-chainer-estimator"></a>Skapa en uppskattare för en kedja

I uppräknings funktionen för [kedjan](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) finns ett enkelt sätt att starta kedje jobb på ditt beräknings mål.

Uppskattaren för kedjan implementeras via den generiska [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen, som kan användas för att stödja eventuella ramverk. Mer information om tränings modeller med hjälp av den generiska uppskattningen finns i [träna modeller med Azure Machine Learning med hjälp av uppskattning](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Skicka in en körning

[Kör-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereder**: En Docker-avbildning skapas enligt kedje bedömaren. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet.

- **Skalning**: Klustret försöker skala upp om Batch AI klustret kräver fler noder för att köra körningen än vad som är tillgängligt.

- **Körning**: Alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och entry_script körs. Utdata från STDOUT och./logs-mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efterbearbetning**: Mappen./outputs för körningen kopieras till körnings historiken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen kan du spara och registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).


När modell träningen har slutförts registrerar du modellen på din arbets yta med följande kod.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Om du får ett fel meddelande om att modellen inte hittas, så ge den en minut och försök igen.  Ibland finns det en liten fördröjning mellan slutet av inlärnings körningen och tillgängligheten för modellen i katalogen utdata.

Du kan också hämta en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare modell validerings arbete lokalt. I övnings skriptet `chainer_mnist.py`sparar ett sparfunktionen-objekt modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia från data lagret.

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

I den här artikeln har du tränat och registrerat en djup inlärning, neurala Network med hjälp av kedjor på Azure Machine Learning. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår [modell distributions](how-to-deploy-and-where.md) artikel.

* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)

* [Spåra kör mått vid träning](how-to-track-experiments.md)

* [Visa vår referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
