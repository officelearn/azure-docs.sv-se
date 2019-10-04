---
title: Verktyg som ingår med DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: En lista med verktyg som ingår i Windows-och Ubuntu DSVM-avbildningarna
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: e8f0eeabd4e869b3812f2d690d2665a8ec31cd14
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950150"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Vilka verktyg ingår i Azure-Data Science Virtual Machine?

Nedan finns en uppdaterad lista med verktyg som finns på Data Science Virtual Machine, tillsammans med länkar till information om hur varje verktyg är konfigurerat.


| **Verktyg**                                                           | **Windows DSVM** | **Linux-DSVM** | **Användnings anmärkningar** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) med populära paket förinstallerade   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [R på DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition innehåller: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) Parallel and Distributed High-Performance Framework (R och python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nya modern Machine Learning-algoritmer från Microsoft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R och python driftsättning](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus med delad aktivering: Excel, Word och PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 och 3,5 med populära paket förinstallerade    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) med populära paket för Julia-språket förinstallerade                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia på DSVM](./dsvm-languages.md#julia) |
| Relationsdatabaser                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server på DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Databasverktyg                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, SQLCMD](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-drivrutiner|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (frågeverktyg), <br />  BCP, SQLCMD <br />  ODBC/JDBC-drivrutiner| |
| Skalbar databas analys med SQL Server Machine Learning-tjänster (R, python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter Notebook Server](https://jupyter.org/) med följande kernels:                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Jupyter Notebook exempel](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [R Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 python |<span class='green-check'>&#9989;</span></br> (3) |<span class='green-check'>&#9989;</span> | [Python Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Julia Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Endast Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 sparker     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (Server för flera datorer)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (Server för flera datorer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Endast Ubuntu) | |
| Utvecklings verktyg, IDE: er och kod redigerare:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) med git-plugin, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)och [R Tools för Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio på DSVM](./dsvm-tools-development.md#visual-studio-2017) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [Visual Studio Code på DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.1.463) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio Desktop på DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio-server på DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [pycharm med Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (2018.1.4)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [Pycharm med på DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno på DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [git](https://git-scm.com/) och git bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [openjdk](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0_201) |<span class='green-check'>&#9989;</span></br> (1.8.0_222) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework |<span class='green-check'>&#9989;</span></br> (4.7.2) |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/en-us/) |<span class='green-check'>&#9989;</span></br> (2.65.5313.1381) |<span class='red-x'>&#10060;</span>   |
| SDK:er för åtkomst till Azure och Cortana Intelligence Suite med tjänster |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI Skriv bordet på DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Data förflyttning och hanterings verktyg: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> 1\.6.2 |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [AzCopy på DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB-säkrings driv rutin](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [blobfuse på DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AdlCopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [AdlCopy på DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB Data Migration Tool](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB på DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 UNIX/Linux kommando rads verktyg |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache-granskning](https://drill.apache.org) för data utforskning |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Apache-granskning på DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Machine Learning-verktyg: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3-integration med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [XGBoost på DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [Vowpal Wabbit på DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [wekas](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> (5.1.0) |<span class='green-check'>&#9989;</span></br> 5\.2.2 | [Wekas på DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> punkt | [LightGBM på DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [mousserande vatten](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 på DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Djup inlärnings verktyg som fungerar på en GPU eller CPU: |  |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe](https://github.com/BVLC/caffe) och [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) och [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) på DSVM |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3- [kedjor](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (5.2.0) | [Kedjor på DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, NVIDIA-drivrutin på DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod på DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.2.4) | [Keras på DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/en-us/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [CNTK på DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [PyTorch på DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [TensorFlow på DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow betjäna](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [TensorFlow servar på DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [Theano på DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

