---
title: Introduktion till Azure Data Science Virtual Machine för Linux och Windows | Microsoft Docs
description: Viktiga analysscenarier och -komponenter för virtuella Windows- och Linux-datorer för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 2814ad51d2f0f868833cf9c6964b7ea4a8424435
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574921"
---
# <a name="what-is-azure-data-science-virtual-machine-for-linux-and-windows"></a>Vad är Azure Data Science Virtual Machine för Linux och Windows?

Den virtuella datorn för datavetenskap (DSVM) är en anpassad VM-avbildning i Microsoft Azure-molnet som skapats specifikt för datavetenskap. Många populära verktyg för datavetenskap och annat finns förinstallerade och förkonfigurerade så att du kan börja direkt med att skapa intelligenta program för avancerade analyser. 

Verktyget konfigurationer är noggrant testade av dataexperter och utvecklare på Microsoft och av bredare dataexpertcommunityn så stabilitet och allmän fungera i framtiden.

DSVM finns på:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS och CentOS 7,4

**Alla virtuella datorer i djup inlärningen** har viks i data science VM. 


## <a name="what-can-i-do-with-dsvm"></a>Vad kan jag göra med DSVM?
Målet med den virtuella datorn för datavetenskap (DSVM) är att ge datapersonal på alla kunskapsnivåer och inom alla branscher en smidig, förkonfigurerad och helt integrerad datavetenskapsmiljö. I stället för att distribuera en jämförbar arbetsyta på egen hand kan du etablera en DSVM – och på så sätt spara dagar eller till och med _veckor_ på installation, konfiguration och pakethanteringsprocesser. När din DSVM har allokerats kan du omedelbart börja arbeta med ditt datavetenskapsprojekt.

Den virtuella datorn för datavetenskap har utformats och konfigurerats för att arbeta med en rad olika användningsscenarier. Du kan skala upp eller ned din miljö när dina projekt krav ändras. Du kan också använda det språk du föredrar för att program mera data vetenskaps uppgifter och installera andra verktyg för att anpassa systemet efter dina specifika behov.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Förkonfigurerat analysskrivbord i molnet
Den virtuella datorn för datavetenskap tillhandahåller en baslinjekonfiguration för datavetenskapsteam som vill ersätta sina lokala skrivbord med ett hanterat molnskrivbord. Den här baslinjen säkerställer att all datapersonal i ett team har en konsekvent konfiguration för att kontrollera experiment och underlätta samarbete. Det minskar också kostnaderna genom att minska sysadmin-belastningen. Den här belastnings minskningen sparar i tid för att utvärdera, installera och underhålla de olika program varu paket som behövs för avancerad analys.

### <a name="data-science-training-and-education"></a>Kurser och utbildning om datavetenskap
Företags utbildare och lärare som undervisar data vetenskaps klasser brukar tillhandahålla en avbildning av en virtuell dator. De tillhandahåller avbildningen för att säkerställa att deras studenter har en konsekvent inställning och att exemplen fungerar förutsägbart. Den virtuella datorn för datavetenskap skapar en miljö på begäran med en konsekvent konfiguration som minskar problemen med support och inkompatibilitet. Det är mycket användbart i fall där de här miljöerna behöver skapas ofta, särskilt för kortare kurser.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastisk kapacitet på begäran för storskaliga projekt
Datavetenskapstävlingar/”hackathons” eller storskalig datamodellering och -utforskning kräver utskalad maskinvarukapacitet, vanligtvis under kort tid. Data Science VM kan hjälpa till att replikera data vetenskaps miljön snabbt på begäran, på utskalade servrar som tillåter att experiment som drivs med hög prestanda körs.

### <a name="custom-compute-power-for-azure-notebooks"></a>Anpassad beräknings kraft för Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) är en kostnads fri värdbaserad tjänst för att utveckla, köra och dela Jupyter-anteckningsböcker i molnet utan att installera. Den kostnads fria tjänst nivån är dock begränsad till 4 GB minne och 1 GB data. Om du vill frigöra alla gränser kan du koppla ett antecknings projekt till en Data Science VM eller någon annan virtuell dator som kör Jupyter-servern. Om du loggar in på Azure Notebooks med ett konto som använder Azure Active Directory (till exempel ett företags konto) visar antecknings böcker automatiskt data vetenskaps datorer i alla prenumerationer som är kopplade till det kontot. Du kan [koppla en data science VM till Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) för att expandera den tillgängliga beräknings kraften.

### <a name="short-term-experimentation-and-evaluation"></a>Kortsiktig experimentering och utvärdering
Den virtuella datorn för datavetenskap kan användas för att utvärdera och lära sig verktyg, till exempel Microsoft ML Server, SQL Server, Visual Studio-verktyg, Jupyter, deep learning-/ML-verktyg och nya verktyg som är populära i communityn, med minimal konfiguration. Eftersom Data Science VM kan konfigureras snabbt, kan det användas i andra kortsiktiga användnings scenarier. I dessa scenarier ingår att replikera publicerade experiment, köra demonstrationer, efter genom gångar i online-sessioner och konferens självstudier.

### <a name="deep-learning"></a>Deep learning
Den virtuella datorn för datavetenskap kan användas som utbildningsmodeller med hjälp av deep learning-algoritmer på GPU-baserad maskinvara (grafikprocessorer). Genom att utnyttja Azure-molnets VM-skalningsfunktioner hjälper DSVM dig att använda GPU-baserad maskinvara i molnet enligt behov. Det går att växla till en GPU-baserad virtuell dator vid utbildning av stora modeller eller behov av snabba beräkningar samtidigt som samma OS-disk behålls.  Windows Server 2016-versionen av DSVM levereras förinstallerad med GPU-drivrutiner, -ramverk och GPU-versioner av deep learning-ramverk. I Linux-utgåvan är deep learning på GPU aktiverat på DSVM för både CentOS och Ubuntu. Du kan distribuera Ubuntu-, CentOS-eller Windows 2016-versionen av Data Science VM till en icke-GPU-baserad virtuell Azure-dator. I det här fallet kommer alla ramverk för djup inlärning att återgå till CPU-läget. Lär dig mer om [tillgängliga djup inlärnings-och AI-ramverk](dsvm-deep-learning-ai-frameworks.md).
 
Lär dig mer om [tillgängliga djup inlärnings-och AI-ramverk](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-dsvm"></a>Vad ingår i DSVM?
Många populära datavetenskaps- och deep learning-verktyg har redan installerats och konfigurerats på den virtuella datorn för datavetenskap. Den innehåller också verktyg som gör det enkelt att arbeta med olika Azure-data och analys-produkter, till exempel Microsoft ML Server (R, Python) för att skapa prediktiva modeller eller SQL Server 2017 för utforskning av storskaliga datauppsättningar. Data Science VM innehåller en värd för andra verktyg från communityn med öppen källkod och från Microsoft, samt [exempel kod och antecknings böcker](dsvm-samples-and-walkthroughs.md). 

Verktyg och plattformar:
+ [Programmeringsspråk som stöds](dsvm-languages.md)

+ [Data plattformar som stöds](dsvm-data-platforms.md)

+ [Utvecklingsverktyg och IDE: er](dsvm-tools-development.md)

+ [Djup inlärning och AI-ramverk](dsvm-deep-learning-ai-frameworks.md).

+ [Machine Learning och data science-verktyg](dsvm-ml-data-science-tools.md)

+ [Verktyg för data inmatning](dsvm-tools-ingestion.md)

+ [Verktyg för data utforskning och visualisering](dsvm-tools-explore-and-visualize.md)

I följande tabell specificeras och jämförs huvudkomponenterna som ingår i Windows- och Linux-versionerna av den virtuella datorn för datavetenskap.

| **Verktyg**                                                           | **Windows-version** | **Linux-version** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) med populära paket förinstallerade   |J                      | J             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition innehåller <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) – parallellt och distribuerat högpresterande ramverk (R och Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) – nya avancerade ML-algoritmer från Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R och Python – driftsättning](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |J                      | J |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) Pro-Plus med delad aktivering – Excel, Word och PowerPoint   |J                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 med populära paket förinstallerade    |J                      |J              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) med populära paket för Julia-språket förinstallerade                         |J                      |J              |
| Relationsdatabaser                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databasverktyg                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC-/JDBC-drivrutiner| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (frågeverktyg) <br /> * bcp, sqlcmd <br /> * ODBC-/JDBC-drivrutiner|
| Skalbar databasanalys med SQL Server ML-tjänster (R, Python) | J     |N              |
| **[Jupyter Notebook Server](https://jupyter.org/) med följande kärnor**                                  | J     | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | J (endast Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | J |
| JupyterHub (anteckningsboksserver för flera användare)| N | J |
| JupyterLab (anteckningsboksserver för flera användare) | N | J (endast Ubuntu) |
| **Utvecklingsverktyg, IDE: er och kod redigerare**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual studio 2019 (Community Edition)](https://www.visualstudio.com/community/) med git-plugin, Azure HDInsight (Hadoop), Data Lake, SQL Server data verktyg, [Node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)och [R Tools för Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia-IDE)](https://junolab.org/)| J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim och Emacs | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git och GitBash | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | J | N |
| Power BI Desktop | J | N |
| SDK:er för åtkomst till Azure och Cortana Intelligence Suite med tjänster | J | J |
| **Verktyg för dataförflyttning och -hantering** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob FUSE-drivrutin](https://github.com/Azure/azure-storage-fuse) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB-datamigreringsverktyg](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): Flytta data mellan OnPrem och molnet | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix-/Linux-kommandoradsverktyg | J | J |
| [Apache Drill](https://drill.apache.org) för datautforskning | J | J |
| **Machine Learning-verktyg** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integrering med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | J (endast Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | J (endast Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | J (endast Ubuntu) |
| **Deep Learning-verktyg** <br>Alla verktyg fungerar på en GPU eller CPU |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | J (Windows 2016) | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | J (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | J (Windows 2016) | J|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe och Caffe2](https://github.com/caffe2/caffe2) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA-, cuDNN-, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit) | J | J |

## <a name="next-steps"></a>Nästa steg

Lär dig mer med de här artiklarna:

+ Windows:
  + [Konfigurera en Windows-DSVM](provision-vm.md)
  + [Tio saker du kan göra på en Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Konfigurera en Linux-DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Konfigurera en Linux-DSVM (CentOS)](linux-dsvm-intro.md)
  + [Data vetenskap i en Linux-DSVM](linux-dsvm-walkthrough.md)
