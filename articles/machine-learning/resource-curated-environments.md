---
title: Granskade miljöer
titleSuffix: Azure Machine Learning
description: Samling av granskade miljöer som är tillgängliga i Azure Machine Learning
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 72ccf2a765f50358635e4a803ed0b92e60bd7d19
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012220"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning granskade miljöer

Den här artikeln innehåller de granskade miljöerna i Azure Machine Learning och de paket och kanaler som är förinstallerade i dem. Granskade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga i arbets ytan som standard. De backas upp av cachelagrade Docker-avbildningar, vilket minskar kostnaden för att köra förberedelser och möjliggör snabbare distributions tid. Använd de här miljöerna för att snabbt komma igång med olika ramverk för maskin inlärning.

> [!NOTE]
> Den här listan uppdateras från och med juni 2020. Använd python SDK för att hämta den senast uppdaterade listan. Mer information finns i artikeln om [miljöer](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azure-automl"></a>Azure-AutoML

- [AzureML AutoML](#azureml-automl)
- [AzureML AutoML-GPU](#azureml-automl-gpu)
- [AzureML AutoML-DNN](#azureml-automl-dnn)
- [AzureML AutoML DNN-GPU](#azureml-automl-dnn-gpu)
- [Azure AutoML DNN vision GPU](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AzureML AutoML

paket kanaler:
- Anaconda
- Conda – falska
- pytorch

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-pipeline-core = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-tolka = = 1.8.0
  - azureml – förklara-Model = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-träna-automl-client = = 1.8.0
  - azureml-träna-automl-Runtime = = 1.8.0. post1
  - härledning – schema
  - pyarrow = = 0.17.0
  - PY-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- PY-xgboost<= 0,90
- fbprophet = = 0,5
- installations verktyg – git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>AzureML AutoML-GPU

paket kanaler:
- Anaconda
- Conda – falska
- pytorch

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-pipeline-core = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-tolka = = 1.8.0
  - azureml – förklara-Model = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-träna-automl-client = = 1.8.0
  - azureml-träna-automl-Runtime = = 1.8.0. post1
  - härledning – schema
  - pyarrow = = 0.17.0
  - PY-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- fbprophet = = 0,5
- installations verktyg – git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>AzureML AutoML-DNN

paket kanaler:
- Anaconda
- Conda – falska
- pytorch

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-pipeline-core = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-tolka = = 1.8.0
  - azureml – förklara-Model = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-träna-automl-client = = 1.8.0
  - azureml-träna-automl-Runtime = = 1.8.0. post1
  - härledning – schema
  - pytorch-transformatorer = = 1.0.0
  - blank steg = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - PY-cpuinfo = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- PY-xgboost<= 0,90
- fbprophet = = 0,5
- installations verktyg – git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>AzureML AutoML DNN-GPU

paket kanaler:
- Anaconda
- Conda – falska
- pytorch

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-pipeline-core = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-tolka = = 1.8.0
  - azureml – förklara-Model = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-träna-automl-client = = 1.8.0
  - azureml-träna-automl-Runtime = = 1.8.0. post1
  - härledning – schema
  - horovod = = 0.19.4
  - pytorch-transformatorer = = 1.0.0
  - blank steg = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - PY-cpuinfo = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- fbprophet = = 0,5
- installations verktyg – git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>AzureML AutoML DNN vision GPU

relation
- python = 3.7
- –
  - azureml-automl-core = = 1.8.0
  - azureml-core = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-automl-client = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-tolka = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml – förklara-Model = = 1.8.0
  - azureml-träna-automl-Runtime = = 1.8.0. post1
  - azureml-träna-automl = = 1.8.0
  - azureml-contrib-dataset = = 1.8.0
  - azureml-pipeline-core = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - azureml – pipeline – steg = = 1.8.0
  - azureml-pipeline = = 1.8.0
  - azureml-träna = = 1.8.0
  - azureml – SDK = = 1.8.0
  - azureml-contrib-automl-DNN-vision = = 1.8.0

## <a name="azure-ml-designer"></a>Azure ML-designer

- [AzureML designer](#azureml-designer)
- [AzureML designer ka](#azureml-designer-cv)
- [AzureML designer ka-transformering](#azureml-designer-cv-transform)
- [AzureML designer-IO](#azureml-designer-io)
- [AzureML designer-NLP](#azureml-designer-nlp)
- [AzureML designer-PyTorch](#azureml-designer-pytorch)
- [AzureML designer PyTorch-träna](#azureml-designer-pytorch-train)
- [AzureML designer R](#azureml-designer-r)
- [AzureML designer-rekommendation](#azureml-designer-recommender)
- [AzureML designer-Poäng](#azureml-designer-score)
- [AzureML designer-transformering](#azureml-designer-transform)

### <a name="azureml-designer"></a>AzureML designer

paket kanaler:
- Conda – falska

relation
- python = 3.6.8
- scikit – överraskning = 1.0.6
- –
  - azureml-designer-klassisk-modules = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - blank steg = = 2.1.7

### <a name="azureml-designer-cv"></a>AzureML designer ka

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-designer-ka-modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>AzureML designer ka-transformering

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-designer-ka-modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>AzureML designer-IO

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-nu>= 1,6
  - azureml-designer-dataio-modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>AzureML designer-NLP

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-designer-klassisk-modules = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - blank steg = = 2.1.7

### <a name="azureml-designer-pytorch"></a>AzureML designer-PyTorch

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>AzureML designer PyTorch-träna

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-r"></a>AzureML designer R

paket kanaler:
- Conda – falska

relation
- python = 3.6.8
- r-cirkumflex = 6.0
- r-catools = 1.17.1
- r-Cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-Forcats = 0.5.0
- r-Forecast = 8.12
- r-glmnet = 2.0
- r-igraph = 1.2.4
- r-Matrix = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1.8
- r-nlme = 3.1
- r-vasslepulver = 7.3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-Reticulate = 1.12
- r-rocr = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-strängare = 1.4.0
- r-tidyverse = 1.2.1
- r-timedate = 3043.102
- r-tseries = 0,10
- r = 3.5.1
- –
  - azureml-designer-klassisk-modules = = 0.0.124

### <a name="azureml-designer-recommender"></a>AzureML designer-rekommendation

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-designer-rekommenderare-modules = = 0.0.5

### <a name="azureml-designer-score"></a>AzureML designer-Poäng

paket kanaler:
- standardvärden

relation
- Conda
- python = 3.6.8
- –
  - azureml-designer-score-modules = = 0.0.5

### <a name="azureml-designer-transform"></a>AzureML designer-transformering

paket kanaler:
- standardvärden

relation
- python = 3.6.8
- –
  - azureml-designer-DataTransform-modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML HyperDrive-ForecastDNN

relation
- python = 3.7
- –
  - azureml-core = = 1.8.0
  - azureml-pipeline-core = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-träna-automl-client = = 1.8.0
  - azureml-träna-automl-Runtime = = 1.8.0. post1
  - azureml-contrib-automl-DNN-Forecasting = = 1.8.0

## <a name="azureml-minimal"></a>AzureML minimal

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0

## <a name="azureml-sidecar"></a>AzureML sidvagn

paket kanaler:
- Conda – falska

relation
- python = 3.6.2

## <a name="azureml-tutorial"></a>AzureML-självstudie

paket kanaler:
- Anaconda
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - azureml-widgetar = = 1.8.0
  - azureml-pipeline-core = = 1.8.0
  - azureml – pipeline – steg = = 1.8.0
  - azureml-OpenData uppsättningar = = 1.8.0
  - azureml-automl-core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-träna-automl-client = = 1.8.0
  - azureml-träna-automl-Runtime = = 1.8.0. post1  
  - azureml-träna-automl = = 1.8.0
  - azureml-träna = = 1.8.0
  - azureml – SDK = = 1.8.0
  - azureml-tolka = = 1.8.0
  - azureml-tensorboard = = 1.8.0
  - azureml-mlflow = = 1.8.0
  - mlflow
  - sklearn – Pandas
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>AzureML VowpalWabbit-8.8.0

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-nu [säkring, Pandas]

## <a name="dask"></a>Dask

- [AzureML dask-CPU](#azureml-dask-cpu)
- [AzureML dask-GPU](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>AzureML dask-CPU

paket kanaler:
- Conda – falska
- pytorch
- standardvärden

relation
- python = 3.6.9
- –
  - adlfs
  - azureml-core = = 1.8.0
  - azureml-dataprep
  - dask [Slutför]
  - dask-ml [complete]
  - distribuera
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - 1150
  - pyarrow

### <a name="azureml-dask-gpu"></a>AzureML dask-GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.9
- –
  - azureml-standardvärden = = 1.8.0
  - adlfs
  - azureml-core = = 1.8.0
  - azureml-dataprep
  - dask [Slutför]
  - dask-ml [complete]
  - distribuera
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - 1150
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [AzureML-kedja 5.1.0 CPU](#azureml-chainer-510-cpu)
- [AzureML-kedja 5.1.0 GPU](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>AzureML-kedja 5.1.0 CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - kedjor = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>AzureML-kedja 5.1.0 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - kedjor = = 5.1.0
  - cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0,15

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [AzureML PyTorch 1,0-CPU](#azureml-pytorch-10-cpu)
- [AzureML PyTorch 1,0 GPU](#azureml-pytorch-10-gpu)
- [AzureML PyTorch 1,1-CPU](#azureml-pytorch-11-cpu)
- [AzureML PyTorch 1,1 GPU](#azureml-pytorch-11-gpu)
- [AzureML PyTorch 1,2-CPU](#azureml-pytorch-12-cpu)
- [AzureML PyTorch 1,2 GPU](#azureml-pytorch-12-gpu)
- [AzureML PyTorch 1,3-CPU](#azureml-pytorch-13-cpu)
- [AzureML PyTorch 1,3 GPU](#azureml-pytorch-13-gpu)
- [AzureML PyTorch 1,4-CPU](#azureml-pytorch-14-cpu)
- [AzureML PyTorch 1,4 GPU](#azureml-pytorch-14-gpu)
- [AzureML PyTorch 1,5-CPU](#azureml-pytorch-15-cpu)
- [AzureML PyTorch 1,5 GPU](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>AzureML PyTorch 1,0-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>AzureML PyTorch 1,0 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>AzureML – PyTorch 1,1-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>AzureML PyTorch 1,1 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>AzureML PyTorch 1,2-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>AzureML PyTorch 1,2 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>AzureML PyTorch 1,3-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>AzureML PyTorch 1,3 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>AzureML PyTorch 1,4-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>AzureML PyTorch 1,4 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>AzureML PyTorch 1,5-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>AzureML PyTorch 1,5 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - Future = = 0.17.1

## <a name="scikit-learn"></a>Scikit-information

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit – lär dig 0.20.3

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - scikit – lära = = 0.20.3
  - scipy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [Azure ML TensorFlow 1,10-processor](#azureml-tensorflow-110-cpu)
- [Azure ML TensorFlow 1,10 GPU](#azureml-tensorflow-110-gpu)
- [Azure ML TensorFlow 1,12-processor](#azureml-tensorflow-112-cpu)
- [Azure ML TensorFlow 1,12 GPU](#azureml-tensorflow-112-gpu)
- [Azure ML TensorFlow 1,13-processor](#azureml-tensorflow-113-cpu)
- [Azure ML TensorFlow 1,13 GPU](#azureml-tensorflow-113-gpu)
- [Azure ML TensorFlow 2,0-processor](#azureml-tensorflow-20-cpu)
- [Azure ML TensorFlow 2,0 GPU](#azureml-tensorflow-20-gpu)
- [Azure ML TensorFlow 2,1-processor](#azureml-tensorflow-21-cpu)
- [Azure ML TensorFlow 2,1 GPU](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>AzureML TensorFlow 1,10-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow = = 1.10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>AzureML TensorFlow 1,10 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow – GPU = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>AzureML TensorFlow 1,12-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow = = 1.12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>AzureML TensorFlow 1,12 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow – GPU = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>AzureML – TensorFlow 1,13-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>AzureML TensorFlow 1,13 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow – GPU = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>AzureML TensorFlow 2,0-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>AzureML TensorFlow 2,0 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow – GPU = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>AzureML TensorFlow 2,1-CPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>AzureML TensorFlow 2,1 GPU

paket kanaler:
- Conda – falska

relation
- python = 3.6.2
- –
  - azureml-core = = 1.8.0
  - azureml-standardvärden = = 1.8.0
  - azureml-telemetri = = 1.8.0
  - azureml-träna-restclients-HyperDrive = = 1.8.0
  - azureml-träna-core = = 1.8.0
  - tensorflow – GPU = = 2.1.0
  - horovod = = 0.19.1

