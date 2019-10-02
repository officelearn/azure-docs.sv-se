---
title: Verktyg som ingår på DSVM
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
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803945"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Vilka verktyg ingår i Azure-Data Science Virtual Machine?

Nedan finns en uppdaterad lista med verktyg som finns på Data Science Virtual Machine, tillsammans med länkar till information om hur varje verktyg är konfigurerat.


| **Verktyg**                                                           | **Windows DSVM** | **Linux-DSVM** | **Användnings anmärkningar** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) med populära paket förinstallerade   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [Använda R på DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition innehåller: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) Parallel and Distributed High-Performance Framework (R och python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nya modern Machine Learning-algoritmer från Microsoft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R och python driftsättning](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus med delad aktivering: Excel, Word och PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 och 3,5 med populära paket förinstallerade    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) med populära paket för Julia-språket förinstallerade                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [Använda Julia på DSVM](./dsvm-languages.md#julia) |
| Relationsdatabaser                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-data-Platforms # SQL-Server-2016-Developer-edition.md) |
| Databasverktyg                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, SQLCMD](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-drivrutiner|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (frågeverktyg), <br />  BCP, SQLCMD <br />  ODBC/JDBC-drivrutiner| |
| Skalbar databas analys med SQL Server Machine Learning-tjänster (R, python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter Notebook Server](https://jupyter.org/) med följande kernels:                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Jupyter Notebook exempel](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [R Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Python Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Julia Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter-exempel](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Endast Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 sparker     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (Server för flera datorer)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (Server för flera datorer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Endast Ubuntu) | |
| Utvecklings verktyg, IDE: er och kod redigerare:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) med git-plugin, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)och [R Tools för Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio 2019 på DSVM](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Visual Studio Code på DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [RStudio Desktop på DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [RStudio-server på DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [pycharm med Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Pycharm med på DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Juno på DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 vim och emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 git och git bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| SDK:er för åtkomst till Azure och Cortana Intelligence Suite med tjänster |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI Skriv bordet på DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Data förflyttning och hanterings verktyg: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure Storage Explorer |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [AzCopy på DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB-säkrings driv rutin](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [blobfuse på DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AdlCopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [AdlCopy på DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB Data Migration Tool](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB på DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): flytta data mellan lokala platser och molnet |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM-användning](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 UNIX/Linux kommando rads verktyg |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache-granskning](https://drill.apache.org) för data utforskning |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Apache-granskning på DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Machine Learning-verktyg: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3-integration med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [XGBoost på DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Vowpal Wabbit på DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [wekas](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Wekas på DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Endast Ubuntu) | [LightGBM på DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Endast Ubuntu) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [mousserande vatten](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>(Endast Ubuntu) | [H20 på DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Djup inlärnings verktyg som fungerar på en GPU eller CPU: |  |  | Klicka på namnet på verktyget nedan för mer information om användning |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span>(Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>Ubuntu | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe och Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3- [kedjor](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow betjäna](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, NVIDIA-drivrutin](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
