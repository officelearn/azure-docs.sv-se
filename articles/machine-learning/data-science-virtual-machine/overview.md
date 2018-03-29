---
title: Introduktion till Azure datavetenskap virtuell dator för Linux och Windows | Microsoft Docs
description: Viktiga analytics scenarier och komponenter för Windows och Linux datavetenskap virtuella datorer.
keywords: datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: f62f6c4b2679457e8aaddb81e8a37622ca878ffc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Introduktion till Azure datavetenskap virtuell dator för Linux och Windows

Den virtuella datorn på vetenskap (DSVM) är en anpassad VM-avbildning på Microsoft Azure-molnet som skapats specifikt för att göra datavetenskap. Många populära verktyg för datavetenskap och annat finns förinstallerade och förkonfigurerade så att du kan börja direkt med att skapa intelligenta program för avancerade analyser. Den är tillgänglig på Windows Server och Linux. Vi erbjuder Windows-versionen av DSVM på Server 2016 och Server 2012. Vi erbjuder Linux utgåvor av DSVM på Ubuntu 16.04 LTS och CentOS 7.4.

Det här avsnittet beskrivs vad du kan göra med den virtuella datorn vetenskapliga Data, en översikt över viktiga scenarier för att använda den virtuella datorn, specificerar de viktigaste funktionerna som är tillgängliga i Windows och Linux-versioner och innehåller instruktioner om hur du kommer igång med dem.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Vad kan jag göra med datavetenskap virtuella datorn?
Målet av datavetenskap Virtual Machine är att ge data personal på alla funktioner och roller med en kostnadsfri friktion datavetenskap miljö. Den här virtuella datorn sparar mycket tid som du tog henne om du har distribuerat en jämförbar miljö på egen hand. Starta projektet datavetenskap i stället, direkt i en nyligen skapade VM-instans. 

Den virtuella datorn datavetenskap designas och konfigurerad för att arbeta med ett brett spektrum av Användningsscenarier. Du kan skala din miljö uppåt eller nedåt när projektet behöver ändras. Du kan använda programmet datavetenskap aktiviteter önskade språk. Du kan installera andra verktyg och anpassa systemet för dina specifika behov.

## <a name="key-scenarios"></a>Viktiga scenarier
Det här avsnittet beskrivs några viktiga scenarier som den virtuella datorn datavetenskap kan distribueras.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Förkonfigurerade analytics skrivbordet i molnet
Den virtuella datorn vetenskap Data innehåller en grundläggande konfiguration för datavetenskap team vill ersätta sina lokala datorer med en hanterad dator. Den här baslinjen garanterar att alla data forskare i ett team har en konsekvent konfiguration som du vill kontrollera experiment och främja samarbete. Det minskar även kostnaderna genom att minska belastningen sysadmin och spara på den tid som behövs för att utvärdera, installera och underhålla olika programvarupaket som behövs för att göra avancerade analyser.  

### <a name="data-science-training-and-education"></a>Kurser och utbildning om datavetenskap
Enterprise lärare och lärare utbilda som datavetenskap klasser normalt innehåller en avbildning av virtuell dator för att säkerställa att deras studenter har en konsekvent konfiguration och att exemplen förutsägbart fungerar. Datavetenskap VM skapar en på-begäran-miljö med en konsekvent konfiguration som underlättar support och inkompatibilitet utmaningar. Fall där dessa miljöer behöver skapas ofta, särskilt för kortare utbildning klasser nytta avsevärt.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>På begäran elastisk kapacitet för storskaliga projekt
Datavetenskap hackathons/tävlingar eller storskaliga datamodellering och utforskning kräver utskalad maskinvarukapacitet, vanligtvis under kort tid. Den virtuella datorn vetenskapliga Data hjälper replikera datavetenskap miljö snabbt på begäran på skaländras ut servrar som tillåter experiment som kräver kraftfulla databearbetningsresurser för att köras.

### <a name="short-term-experimentation-and-evaluation"></a>Kortsiktig undersökningar och utvärdering
Den virtuella datorn datavetenskap kan vara används för att utvärdera och lära dig verktyg som Microsoft ML Server, SQL Server, Visual Studio tools, Jupyter, djupa learning / ML verktyg och nya verktyg populära i gemenskapen med minimal installation arbete. Eftersom den virtuella datorn datavetenskap kan ställas in snabbt, kan den användas i andra kortsiktig användarscenarier som replikerar publicerade experiment, köra demonstrationer följande genomgång i online sessioner eller konferens självstudier.

### <a name="deep-learning"></a>Djupgående learning
Datavetenskap VM kan användas för utbildning-modell med djup learning algoritmer på GPU (Graphics bearbetningsenheter) baserad maskinvara. Använda VM skalning maskinvarufunktioner för Azure-molnet kan DSVM du använda GPU-baserad maskinvara på molnet enligt behov. Växla till en GPU-baserad virtuell dator när utbildning stora modeller kan eller behöver snabba beräkningar samtidigt som samma OS-disk.  Windows Server 2016-versionen av DSVM finns förinstallerat med GPU drivrutiner, ramverk och GPU-versioner av djup learning ramverk. Linux, är djup learning på GPU aktiverat på både CentOS och Ubuntu DSVMs. Du kan distribuera Ubuntu och CentOS Windows 2016-versionen av datavetenskap VM till icke GPU-baserad virtuell Azure-dator i vilket fall djup learning ramverk återgår till att CPU-läge. 

## <a name="whats-included-in-the-data-science-vm"></a>Vad ingår i den virtuella datorn vetenskap Data?
Datavetenskap virtuell dator har många populära datavetenskap och djup lära redan har installerats och konfigurerats. Den omfattar också verktyg som gör det enkelt att arbeta med olika Azure-data och analyser produkter. Du kan utforska och skapa förutsägelsemodeller i stora datauppsättningar genom att använda Microsoft ML Server (R, Python) eller SQL Server 2017. En mängd andra verktyg från community för öppen källkod och från Microsoft finns också inkluderade som exempel koden och bärbara datorer. I följande tabell specificerar och jämför huvudkomponenterna som ingår i Windows och Linux-versioner av datavetenskap Virtual Machine.


| **Verktyget**                                                           | **Windows-version** | **Linux-version** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R öppna](https://mran.microsoft.com/open/) med populära paket förinstallerat   |Y                      | Y             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition innehåller <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) parallella och distribuerade högpresterande framework (R och Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) -nya den senaste ML algoritmer från Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R och Python Operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus med delade aktivering - Excel, Word och PowerPoint   |Y                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 med populära paket förinstallerat    |Y                      |Y              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) med populära paket för Julia språket förinstallerat                         |Y                      |Y              |
| Relationsdatabaser                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(CentOS) |
| Databasverktyg                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [BCP sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC-drivrutiner| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (frågar verktyget), <br /> * bcp sqlcmd <br /> * ODBC/JDBC-drivrutiner|
| Skalbar i databasen analytics med SQL Server ML-tjänster (R, Python) | Y     |N              |
| **[Jupyter-anteckningsbok Server](http://jupyter.org/) med följande kärnor**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 & 3.5 | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub (flera användares anteckningsböcker server)| N | Y |
| **Utvecklingsverktyg, IDEs och kod redigerare**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) > med Git Plugin, Azure HDInsight (Hadoop), Data Lake, SQL Server Data tools [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs), och [R-verktyg för Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim och Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git och GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Y | N |
| PowerBI Desktop | Y | N |
| SDK: er för åtkomst till Azure och Cortana Intelligence Suite av tjänster | Y | Y |
| **Dataförflyttning och hanteringsverktyg** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Lagringsutforskaren | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [BLOB SÄKRAD drivrutin](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB Datamigreringsverktyg](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): flytta data mellan OnPrem och moln | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux-kommandoradsverktyg | Y | Y |
| [Apache ökad](http://drill.apache.org) för datagranskning | Y | Y |
| **Machine Learning-verktyg** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integrering med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | Y (Ubuntu) |
| **GPU-baserad djup lära** |Windows Server 2016 edition  | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft kognitiva Toolkit (kallades tidigare CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Y | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia siffror](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model-Server](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow fungerar](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA CUDNN, Nvidia drivrutin](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **Stordataplattform (Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokala [Spark](http://spark.apache.org/) fristående | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Local [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | N | Y |



## <a name="get-started-with-the-windows-data-science-vm"></a>Kom igång med Windows datavetenskap VM
* Skapa en instans av önskad Windows DSVM edition genom att gå till
  * [Windows Server 2016 baserat DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  eller 
  * [Windows Server 2012 baserad DSVM](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* Klicka på den **hämta IT nu** knappen.
* Logga in på den virtuella datorn från fjärrskrivbordet med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
* För att identifiera och starta verktyg som finns tillgängliga, klickar du på den **starta** menyn.

## <a name="get-started-with-the-linux-data-science-vm"></a>Kom igång med Linux datavetenskap VM
* Skapa en instans av önskade Linux DSVM edition genom att gå till 
  * [Ubuntu baserat DSVM](http://aka.ms/dsvm/ubuntu)

  eller

  * [CentOS baserat DSVM](http://aka.ms/dsvm/centos)

  
* Klicka på den **blir det nu** knappen.
* Logga in på den virtuella datorn från en SSH-klienten, till exempel Putty eller SSH-kommando, med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
* Ange dsvm mer information i Kommandotolken-gränssnittet.
* Hämta X2Go klienten för din klientplattform för ett grafiskt skrivbord [här](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) och följ instruktionerna i Linux datavetenskap VM dokumentet [etablera Linux datavetenskap virtuell dator](linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Nästa steg
### <a name="for-the-windows-data-science-vm"></a>För Windows-datavetenskap VM
* Mer information om hur du kör specifika verktyg som är tillgängliga på Windows-versionen finns [etablera Microsoft datavetenskap Virtual Machine](provision-vm.md) och
* Mer information om hur du utför olika uppgifter som behövs för projektet datavetenskap på Windows-VM finns [tio saker du kan göra på datavetenskap virtuella](vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>För Linux datavetenskap VM
* Mer information om hur du kör specifika verktyg som är tillgängliga på Linux-versionen finns [etablera Linux datavetenskap virtuell dator](linux-dsvm-intro.md).
* En genomgång som visar hur du utför flera gemensamma datavetenskap med Linux VM finns [datavetenskap på den Linux datavetenskap virtuella](linux-dsvm-walkthrough.md).

