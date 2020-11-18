---
title: Organiserade miljöer
titleSuffix: Azure Machine Learning
description: Lär dig mer om Azure Machine Learning granskade miljöer, en uppsättning förkonfigurerade miljöer som bidrar till att minska experiment-och distributions tider.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701406"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning granskade miljöer

Den här artikeln innehåller de granskade miljöerna i Azure Machine Learning och de paket och kanaler som är förinstallerade i dem. Granskade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga i arbets ytan som standard. De backas upp av cachelagrade Docker-avbildningar, vilket minskar kostnaden för att köra förberedelser och möjliggör snabbare distributions tid. Använd de här miljöerna för att snabbt komma igång med olika ramverk för maskin inlärning.

> [!NOTE]
> Den här listan uppdateras från och med september 2020. Använd python SDK för att hämta den senast uppdaterade listan. Mer information finns i artikeln om [miljöer](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azureml-automl"></a>AzureML-AutoML

**Paket kanaler:**

* Anaconda
* Conda – falska
* pytorch

**Conda-paket:**

* python
* numpy
* scikit-learn
* Pandas
* PY-xgboost
* fbprophet
* högtid
* installations verktyg – git
* psutil

**PIP-paket:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – standardvärden
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* härledning – schema
* PY-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Paket kanaler:**

* Anaconda
* Conda – falska
* pytorch

**Conda-paket:**

* python
* numpy
* scikit-learn
* Pandas
* PY-xgboost
* fbprophet
* högtid
* installations verktyg – git
* pytorch
* cudatoolkit
* psutil

**PIP-paket:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – standardvärden
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* härledning – schema
* pytorch-transformatorer
* avstånd
* en_core_web_sm
* PY-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Paket kanaler:**

* Anaconda
* Conda – falska
* pytorch

**Conda-paket:**

* python
* numpy
* scikit-learn
* Pandas
* fbprophet
* högtid
* installations verktyg – git
* pytorch
* cudatoolkit
* psutil

**PIP-paket:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – standardvärden
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* härledning – schema
* horovod
* pytorch-transformatorer
* avstånd
* en_core_web_sm
* PY-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-vision-GPU

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml-data uppsättning-runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml – standardvärden
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-DNN-vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**Paket kanaler:**

* Anaconda
* Conda – falska
* pytorch

**Conda-paket:**

* python
* numpy
* scikit-learn
* Pandas
* fbprophet
* högtid
* installations verktyg – git
* psutil

**PIP-paket:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – standardvärden
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* härledning – schema
* PY-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* Chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* Chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-dask-CPU

**Paket kanaler:**

* Conda – falska
* pytorch
* standardvärden

**Conda-paket:**

* python

**PIP-paket:**

* adlfs
* azureml-core
* azureml-data uppsättning-runtime
* dask [Slutför]
* dask-ml [complete]
* distribuera
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-dask-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python
* matplotlib

**PIP-paket:**

* azureml – standardvärden
* adlfs
* azureml-core
* dask [Slutför]
* dask-ml [complete]
* distribuera
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-HyperDrive-ForecastDNN

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml – standardvärden
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-DNN-Forecasting

## <a name="azureml-minimal"></a>AzureML-Minimal

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0,15

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1,0-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1,0-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1,1-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1,1-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1,2-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1,2-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1,3-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1,3-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1,4-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1,4-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1,5-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1,5-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1,6-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1,6-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* längre

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-lär-0.20.3

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1,10-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1,10-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1,12-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1,12-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1,13-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1,13-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2,0-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2,0-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2,1-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2,1-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2,2-CPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2,2-GPU

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* tensorflow – GPU
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**Paket kanaler:**

* Anaconda
* Conda – falska

**Conda-paket:**

* python
* Pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-telemetry
* azureml-träna-restclients-HyperDrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-träna
* azureml – SDK
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn – Pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Paket kanaler:**

* Conda – falska

**Conda-paket:**

* python

**PIP-paket:**

* azureml-core
* azureml – standardvärden
* azureml-dataset-runtime [säkring, Pandas]
