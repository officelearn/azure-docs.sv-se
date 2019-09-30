---
title: Vad är Azure-Data Science Virtual Machine
description: Viktiga analysscenarier och -komponenter för virtuella Windows- och Linux-datorer för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: f40ad01d74df26e5a6d90c348a706e8a3c666f2f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675083"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Vad är Azure-Data Science Virtual Machine för Linux och Windows?

Data Science Virtual Machine (DSVM) är en anpassad VM-avbildning på Azure Cloud Platform som skapats specifikt för data vetenskap. Många populära verktyg för datavetenskap och annat finns förinstallerade och förkonfigurerade så att du kan börja direkt med att skapa intelligenta program för avancerade analyser. 

Konfigurationerna av verktyget är rigoröst testade av data experter och utvecklare på Microsoft och av den bredare data vetenskaps gruppen. Den här testningen garanterar stabilitet och allmän lönsamhet.

DSVM finns på:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS och CentOS 7,4

> [!NOTE]
> Alla virtuella dator verktyg för djup inlärning har viktas i Data Science Virtual Machine. 


## <a name="what-can-i-do-with-the-dsvm"></a>Vad kan jag göra med DSVM?
Målet med Data Science Virtual Machine är att tillhandahålla data personal för alla kunskaps nivåer och mellan olika branscher med en friktions fri, förkonfigurerad och helt integrerad data vetenskaps miljö. I stället för att distribuera en jämförbar arbets yta på egen hand kan du etablera en DSVM. Detta val kan spara dagar eller till och med _veckor_ i installations-, konfigurations-och paket hanterings processerna. När din DSVM har allokerats kan du omedelbart börja arbeta med ditt datavetenskapsprojekt.

DSVM är utformad och konfigurerad för att arbeta med ett brett utbud av användnings scenarier. Du kan skala upp eller ned din miljö när dina projekt krav ändras. Du kan också använda det språk du föredrar för att program mera data vetenskaps uppgifter och installera andra verktyg för att anpassa systemet efter dina behov.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Förkonfigurerat analysskrivbord i molnet
DSVM innehåller en bas linje konfiguration för data vetenskaps team som vill ersätta sina lokala skriv bord med ett hanterat moln skriv bord. Den här baslinjen säkerställer att all datapersonal i ett team har en konsekvent konfiguration för att kontrollera experiment och underlätta samarbete. Det minskar också kostnaderna genom att minska sysadmin-belastningen. Den här kostnads minskningen sparar vid den tid som behövs för att utvärdera, installera och underhålla program varu paket för avancerad analys.

### <a name="data-science-training-and-education"></a>Kurser och utbildning om datavetenskap
Företags utbildare och lärare som undervisar data vetenskaps klasser brukar tillhandahålla en avbildning av en virtuell dator. Avbildningen säkerställer att eleverna har en konsekvent inställning och att exemplen fungerar förutsägbart. 

DSVM skapar en miljö på begäran med en konsekvent konfiguration som underlättar support-och inkompatibilitets utmaningarna. Det är mycket användbart i fall där de här miljöerna behöver skapas ofta, särskilt för kortare kurser.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastisk kapacitet på begäran för storskaliga projekt
Data vetenskap Hackathon kickar/tävlingar eller storskalig data modellering och utforskning kräver skalbar maskin varu kapacitet, vanligt vis för kort varaktighet. DSVM kan hjälpa till att replikera data vetenskaps miljön snabbt på begäran, på utskalade servrar som tillåter att experiment som drivs av stora data resurser kan köras.

### <a name="custom-compute-power-for-azure-notebooks"></a>Anpassad beräknings kraft för Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) är en kostnads fri värdbaserad tjänst för att utveckla, köra och dela Jupyter-anteckningsböcker i molnet utan att installera. Den kostnads fria tjänst nivån är begränsad till 4 GB minne och 1 GB data. 

Om du vill frigöra alla gränser kan du bifoga ett Notebook-projekt till en DSVM eller någon annan virtuell dator som körs på en Jupyter-Server. Om du loggar in på Azure Notebooks med ett konto genom att använda Azure Active Directory (till exempel ett företags konto), visar antecknings böcker automatiskt Dsvm i alla prenumerationer som är kopplade till det kontot. Du kan [koppla en DSVM till Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) för att utöka den tillgängliga beräknings kraften.

### <a name="short-term-experimentation-and-evaluation"></a>Kortsiktig experimentering och utvärdering
Du kan använda DSVM för att utvärdera eller lära dig verktyg som dessa, med minimal konfigurations ansträngning:

- Microsoft Machine Learning Server
- SQL Server
- Visual Studio-verktyg
- Jupyter
- Djup inlärning och Machine Learning Toolkit
- Nya verktyg populära i communityn 

Eftersom du kan konfigurera DSVM snabbt kan du använda den i andra kortsiktiga användnings scenarier. I dessa scenarier ingår att replikera publicerade experiment, köra demonstrationer och följa genom gångar i online-sessioner och konferens självstudier.

### <a name="deep-learning-with-gpus"></a>Djup inlärning med GPU: er
I DSVM kan dina utbildnings modeller använda djup inlärnings-algoritmer på maskin vara som baseras på GPU: er (Graphics Processing units). Genom att dra nytta av funktionerna för skalning av virtuella datorer i Azure-plattformen kan DSVM hjälpa dig att använda GPU-baserad maskin vara i molnet efter behov. Du kan växla till en GPU-baserad virtuell dator när du tränar stora modeller eller när du behöver snabba beräkningar samtidigt som du behåller samma OS-disk. Du kan välja någon av de N-seriens GPU-aktiverade virtuella datorer SKU: er med DSVM. Observera att de kostnads fria Azure-kontona inte stöder GPU-aktiverade virtuella datorer SKU: er.

Windows Server 2016-versionen av DSVM levereras i förväg med GPU-drivrutiner, ramverk och GPU-versioner av djup inlärnings ramverk. I Linux-versionen har djup inlärning på GPU: er Aktiver ATS på både CentOS-och Ubuntu-Dsvm. 

Du kan också distribuera Ubuntu-, CentOS-eller Windows 2016-versionen av DSVM till en virtuell Azure-dator som inte baseras på GPU: er. I det här fallet kommer alla ramverk för djup inlärning att återgå till CPU-läget.
 
[Lär dig mer om tillgängliga djup inlärnings-och AI-ramverk](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Vad ingår i DSVM?
Många populära datavetenskaps- och deep learning-verktyg har redan installerats och konfigurerats på den virtuella datorn för datavetenskap. Den innehåller också verktyg som gör det enkelt att arbeta med olika Azure-produkter för data och analys. Dessa produkter omfattar Microsoft Machine Learning Server (R, python) för att skapa förutsägande modeller och SQL Server 2017 för storskalig utforskning av data uppsättningar. DSVM innehåller andra verktyg från communityn med öppen källkod och från Microsoft, tillsammans med [exempel kod och antecknings böcker](dsvm-samples-and-walkthroughs.md). 

Här är en lista över verktyg och plattformar:
+ [Programmeringsspråk som stöds](dsvm-languages.md)

+ [Data plattformar som stöds](dsvm-data-platforms.md)

+ [Utvecklingsverktyg och IDE: er](dsvm-tools-development.md)

+ [Ramverk för djup inlärning och AI](dsvm-deep-learning-ai-frameworks.md)

+ [Machine Learning och data science-verktyg](dsvm-ml-data-science-tools.md)

+ [Verktyg för data inmatning](dsvm-tools-ingestion.md)

+ [Verktyg för data utforskning och visualisering](dsvm-tools-explore-and-visualize.md)

I följande tabell specificeras och jämförs huvudkomponenterna som ingår i Windows- och Linux-versionerna av den virtuella datorn för datavetenskap.

| **Verktyg**                                                           | **Windows-utgåva** | **Linux-utgåva** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) med populära paket förinstallerade   |J                      | J             |
| [Microsoft Machine Learning Server (R, python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition innehåller: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) Parallel and Distributed High-Performance Framework (R och python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nya modern Machine Learning-algoritmer från Microsoft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R och python driftsättning](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |J                      | J |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus med delad aktivering: Excel, Word och PowerPoint   |J                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2,7 och 3,5 med populära paket förinstallerade    |J                      |J              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) med populära paket för Julia-språket förinstallerade                         |J                      |J              |
| Relationsdatabaser                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databasverktyg                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, SQLCMD](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC-drivrutiner|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (frågeverktyg), <br />  BCP, SQLCMD <br />  ODBC/JDBC-drivrutiner|
| Skalbar databas analys med SQL Server Machine Learning-tjänster (R, python) | J     |N              |
| [Jupyter Notebook Server](https://jupyter.org/) med följande kernels:                                  | J     | J |
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 python | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark | J | J |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | J (endast Ubuntu) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 sparker     | N | J |
| JupyterHub (Server för flera datorer)| N | J |
| JupyterLab (Server för flera datorer) | N | J (endast Ubuntu) |
| Utvecklings verktyg, IDE: er och kod redigerare:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) med git-plugin, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)och [R Tools för Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio-Server](https://www.rstudio.com/products/rstudio/#Server) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [pycharm med Community Edition](https://www.jetbrains.com/pycharm/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)| J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 vim och emacs | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 git och git bash | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 OpenJDK | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework | J | N |
| Power BI Desktop | J | N |
| SDK:er för åtkomst till Azure och Cortana Intelligence Suite med tjänster | J | J |
| Data förflyttning och hanterings verktyg: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure Storage Explorer | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure CLI](https://docs.microsoft.com/cli/azure) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure PowerShell | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB-säkrings driv rutin](https://github.com/Azure/azure-storage-fuse) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AdlCopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB Data Migration Tool](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): flytta data mellan lokala platser och molnet | J | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 UNIX/Linux kommando rads verktyg | J | J |
| [Apache-granskning](https://drill.apache.org) för data utforskning | J | J |
| Machine Learning-verktyg: |||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3-integration med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, python) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [wekas](https://www.cs.waikato.ac.nz/ml/weka/) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) | N | J (endast Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) | N | J (endast Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [mousserande vatten](https://www.h2o.ai/sparkling-water/) | N | J (endast Ubuntu) |
| Djup inlärnings verktyg som fungerar på en GPU eller CPU: |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | J | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](https://www.tensorflow.org/) | J (Windows 2016) | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/uber/horovod) | N | J (Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet](https://mxnet.io/) | J (Windows 2016) | J|
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe och Caffe2](https://github.com/caffe2/caffe2) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3- [kedjor](https://chainer.org/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Torch](http://torch.ch/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Theano](https://github.com/Theano/Theano) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [keras](https://keras.io/)| N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org/)| N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [NVIDIA-siffror](https://github.com/NVIDIA/DIGITS) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet-modell Server](https://github.com/awslabs/mxnet-model-server) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow betjäna](https://www.tensorflow.org/serving/) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorRT](https://developer.nvidia.com/tensorrt) | N | J |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit) | J | J |

## <a name="next-steps"></a>Nästa steg

Lär dig mer med de här artiklarna:

+ Windows:
  + [Konfigurera en Windows DSVM](provision-vm.md)
  + [Tio saker du kan göra på en Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Konfigurera en Linux-DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Konfigurera en Linux-DSVM (CentOS)](linux-dsvm-intro.md)
  + [Data vetenskap i en Linux-DSVM](linux-dsvm-walkthrough.md)
