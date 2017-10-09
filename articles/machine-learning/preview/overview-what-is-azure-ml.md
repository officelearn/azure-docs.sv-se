---
title: "Vad är Azure Machine Learning? | Microsoft Docs"
description: "Översikt över Azure Machine Learning-experimentering och -modellhantering, en integrerad lösning från slutpunkt till slutpunkt som gör att dataforskare kan utveckla, experimentera och distribuera avancerade analysprogram i molnskala."
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c4919fb679eeb4d25eb0066b9bf617b057d44354
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="what-is-azure-machine-learning"></a>Vad är Azure Machine Learning?

Azure Machine Learning är en integrerad lösning från slutpunkt till slutpunkt för avancerade analyser inom dataforskning. Med lösningen kan dataforskare förbereda data, utveckla experiment och distribuera modeller i molnskala.

Huvudkomponenter i Azure Machine Learning:
- Azure Machine Learning Workbench
- Tjänst för Azure Machine Learning-experimentering
- Tjänst för Azure Machine Learning-modellhantering
- Microsoft Machine Learning-bibliotek för Apache Spark (MMLSpark-biblioteket)
- Visual Studio Code Tools för AI

Med dessa program och tjänster går det mycket snabbare att utveckla och distribuera dataforskningsprojekt. 

![Begrepp för Azure Machine Learning](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>Kompatibilitet med öppen källkod

Azure Machine Learning har fullständigt stöd för tekniker med öppen källkod. Du kan använda tiotusentals Python-paket som är baserade på öppen källkod, till exempel följande ramverk för maskininlärning:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Du kan köra dina experiment i hanterade miljöer som Docker-behållare och Spark-kluster. Du kan också använda avancerad maskinvara som [GPU-aktiverade virtuella datorer på Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) för att få snabbare körningar.

Azure Machine Learning bygger på följande tekniker som är baserade på öppen källkod:

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Även Microsofts egna tekniker som är baserade på öppen källkod ingår, till exempel [Microsoft Machine Learning Library för Apache Spark](https://github.com/Azure/mmlspark) och Cognitive Toolkit.

Dessutom kan du dra nytta av några av de mest avancerade, beprövade maskininlärningsteknikerna som utvecklats av Microsoft och som är avsedda att lösa storskaliga problem. De har testats i skarpt läge i många Microsoft-produkter, till exempel:

- Windows
- Bing
- Xbox
- Office
- SQL Server

Exempel på Microsofts maskininlärningstekniker som ingår i Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench är ett skrivbordsprogram som också har kommandoradsverktyg. Det kan användas i både Windows och macOS. Med programmet kan du hantera maskininlärningslösningar genom hela forskningsprojektets livscykel:

- Förberedelse och inhämtning av data
- Modellutveckling och experimenthantering
- Modelldistribution i olika målmiljöer

Huvudfunktioner i Azure Machine Learning Workbench:

- Verktyg för att förbereda data som kan lära sig dataomvandlingslogik med exempel.
- Abstraktion av datakälla (tillgängligt via UX och Python-kod).
- Python SDK för att anropa visuellt konstruerade paket för förberedelse av data.
- Inbyggd Jupyter Notebook-tjänst och klient-UX.
- Experimentövervakning och -hantering via vyer över körningshistorik.
- Rollbaserad åtkomstkontroll som möjliggör delning och samarbete via Azure Active Directory.
- Automatiska projektögonblicksbilder för varje körning och explicit versionskontroll med inbyggd Git-integrering. 
- Integrering med populära Python-IDE:er.

Mer information finns i följande artiklar:
- [Användarhandbok för förberedelse av data](data-prep-user-guide.md)
- [Använda Git med Azure Machine Learning](using-git-ml-project.md)
- [Använda Jupyter Notebook i Azure Machine Learning](how-to-use-jupyter-notebooks.md)
- Roaming och delning
- [Guide för körningshistorik](how-to-use-run-history-model-metrics.md)
- [IDE-integrering](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Tjänst för Azure Machine Learning-experimentering
Experimenteringstjänsten hanterar körningen av maskininlärningsexperiment. Tjänsten stöder även Wordbench med projekthantering, Git-integrering, åtkomstkontroll, roaming och delning. 

Genom en enkel konfiguration kan du köra experiment i flera olika beräkningsmiljöer:

- Lokalt internt
- Lokal Dockerbehållare
- Dockerbehållare på en fjärransluten virtuell dator
- Skala ut Spark-kluster i Azure

Experimenteringstjänsten skapar virtuella miljöer för att säkerställa att ditt skript kan köras isolerat med reproducerbara resultat. Tjänsten registrerar information om körningshistorik och visar historiken visuellt. Du kan enkelt välja den bästa modellen utifrån dina experimentkörningar. 

Mer information finns i [Experimentation Execution Configuration](experiment-execution-configuration.md) (Konfiguration av experimentkörning).

## <a name="azure-machine-learning-model-management-service"></a>Tjänst för Azure Machine Learning-modellhantering

Med modellhanteringstjänsten kan dataforskare och DevOps-team distribuera prediktiva modeller i en mängd olika miljöer. Modellernas version och härkomst spåras från träningskörningar till distributioner. Modellerna lagras, registreras och hanteras i molnet. 

Med hjälp av enkla CLI-kommandon kan du paketera modeller, bedömningsskript och beroenden i Docker-avbildningar. Dessa avbildningar registreras i ditt eget Docker-register som lagras i Azure (Azure Container Registry). De kan distribueras på ett tillförlitligt sätt till följande:

- Lokala datorer
- Lokala servrar
- Molnet
- IoT Edge-enheter

Kubernetes som körs i Azure Container Service (ACS) används för utskalningsdistribution i molnet. Modellkörningstelemetri registreras i AppInsights för visuella analyser. 

Mer information om modellhanteringstjänsten finns i [Model Management Overview](model-management-overview.md) (Översikt över modellhantering)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Microsoft Machine Learning-biblioteket för Apache Spark

[MMLSpark](https://github.com/Azure/mmlspark)(Microsoft Machine Learning-biblioteket för Apache Spark) är ett Spark-paket baserat på öppen källkod som tillhandahåller djupinlärnings- och dataforskningsverktyg för Apache Spark. Det integrerar [Spark Machine Learning Pipelines](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) med [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) och [OpenCV](http://opencv.org/)-biblioteket. Med biblioteket kan du snabbt skapa kraftfulla, mycket skalbara, förutsägande och analytiska modeller för stora bild- och textdatauppsättningar. Exempel på viktiga funktioner:

- Enkel inmatning av avbildningar från HDFS till Spark DataFrame
- Förbearbeta avbildningsdata med transformeringar från OpenCV
- Få funktionalitet för avbildningar med hjälp av förtränade djupa neurala nät med hjälp av Microsoft Cognitive Toolkit 
- Använd förtränade dubbelriktade LSTM:er från Keras för extrahering av medicinska entiteter
- Träna DNN-baserade avbildningsklassificeringsmodeller i virtuella datorer i N-serien (GPU) på Azure
- Funktionalisera friformstextdata med praktiska API:er ovanpå primitiver i SparkML via en enda omvandlare
- Träna klassificerings- och regressionsmodeller enkelt via implicit funktionalisering av data
- Beräkna en omfattande uppsättning utvärderingsmått inklusive mått per instans

Mer information finns i [Använda MMLSpark i Azure Machine Learning](how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools för AI
Visual Studio Code Tools för AI är ett tillägg i Visual Studio Code för att skapa, testa och distribuera djupinlärnings- och AI-lösningar. Tillägget har flera integreringspunkter med Azure Machine Learning, inklusive:
- En vy för körningshistorik som visar prestanda för träningskörningar och loggade mått.
- En gallerivy där användarna kan söka efter och starta nya projekt med Microsoft Cognitive Toolkit, TensorFlow och många andra ramverk för djupinlärning. 
- En utforskarvy för att välja beräkningsmål för körning av dina skript.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Vilka maskininlärningsalternativ har Microsoft?
Utöver Azure Machine Learning finns en mängd olika alternativ i Azure för att skapa, distribuera och hantera maskininlärningsmodeller. 
* Microsoft Machine Learning-tjänster i SQL Server
* Microsoft Machine Learning Server
* Datavetenskap – virtuella datorer
* Spark MLLib i HDInsight
* Batch AI Training Service
* Microsoft Cognitive Toolkit
* Microsoft Cognitive Services


### <a name="microsoft-machine-learning-services-in-sql-server"></a>Microsoft Machine Learning-tjänster i SQL Server
Med [Microsoft Machine Learning-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) kan du köra, träna och distribuera maskininlärningsmodeller med R eller Python. Du kan använda data som finns lokalt och i SQL Server-databaser. 

Använd Microsoft Machine Learning-tjänster när du behöver träna eller distribuera modeller lokalt eller i Microsoft SQL Server. Modeller som skapats med Machine Learning-tjänster kan distribueras med Azure Machine Learning-modellhantering. 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) är en företagsserver som gör det möjligt att vara värd för och hantera parallella och distribuerade arbetsbelastningar för R- och Python-processer. Microsoft Machine Learning Server körs på Linux, Windows, Hadoop och Apache Spark. Och är även tillgängligt i [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Microsoft Machine Learning Server ger en körningsmotor för lösningar som skapats med [Microsoft Machine Learning-paket](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) och utökar R och Python (öppen källkod) med stöd för följande scenarier:

- Analyser med höga prestanda
- statistiska analyser
- maskininlärning
- massiva datauppsättningar

Mervärdesfunktioner tillhandahålls via egna paket som installeras med servern. Du kan använda IDE:er för utveckling, till exempel [R Tools för Visual Studio](https://www.visualstudio.com/vs/rtvs/) och [Python Tools för Visual Studio](https://www.visualstudio.com/vs/python/).

Använd Microsoft Machine Learning Server när du behöver:

- Skapa och distribuera modeller som skapats med R och Python på en server
- Distribuera R- och Python-träning i stor skala i ett Hadoop- eller Spark-kluster

### <a name="data-science-virtual-machine"></a>Virtuell dator för datavetenskap
[Virtuell dator för datavetenskap (DSVM) ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) är en anpassad VM-avbildning i Microsoft Azure-molnet som skapats specifikt för dataforskning. Många populära verktyg för datavetenskap och annat finns förinstallerade och förkonfigurerade så att du kan börja direkt med att skapa intelligenta program för avancerade analyser. Den är tillgänglig på Windows Server och Linux. Vi erbjuder Windows-versionen av DSVM på Server 2016 och Server 2012. Vi erbjuder Linux-versionen av DSVM på Ubuntu 16.04 LTS och på OpenLogic 7.2 CentOS-baserade Linux-distributioner. 

Använd den virtuella datorn för datavetenskap när du behöver köra eller vara värd för jobb på en enda nod. Eller om du behöver skala upp din bearbetning via fjärranslutning på en enskild dator. Den virtuella datorn för datavetenskap kan vara mål för både Azure Machine Learning- experimentering och Azure Machine Learning-modellhantering. 

### <a name="spark-mllib-in-hdinsight"></a>Spark MLLib i HDInsight
Med [Spark MLLib i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) kan du skapa modeller som en del i Spark-jobb som körs på stordata. Med Spark kan du enkelt omvandla och förbereda data och sedan skala ut modellskapandet i ett enskilt jobb. Modeller som skapas via Spark MLLib kan distribueras, hanteras och övervakas via Azure Machine Learning-modellhantering. Träningskörningar kan skickas och hanteras med Azure Machine Learning-experimentering. Spark kan också användas för att skala ut dataförberedelsejobb som skapats i Machine Learning Workbench. 

Använd Spark när du behöver skala ut din databearbetning och skapa modeller som en del av en datapipeline. Du kan skapa Spark-jobb i Scala, Java, Python eller R. 

### <a name="batch-ai-training"></a>Batch AI Training 
[Azure Batch AI Training](https://aka.ms/batchaitraining) hjälper dig att experimentera parallellt med dina AI-modeller med valfritt ramverk och sedan träna dem i stor skala över klustrade GPU:er. Beskriv jobbkraven och konfigurationen som ska köras så hanterar vi resten. 

Med Batch AI Training kan du skala ut djupinlärningsjobb över klustrade GPU:er med ramverk som:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning-modellhantering kan användas för att hämta modeller från Batch AI Training för att distribuera, hantera och övervaka dem.  Batch AI Training kommer att integreras med Azure Machine Learning-experimentering i framtiden. 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) är ett enhetligt djupinlärningsverktyg som beskriver neurala nätverk som beräkningssteg i en riktad graf. I den riktade grafen representerar lövnoder indatavärden eller nätverksparametrar, medan andra noder representerar matrisåtgärder vid inmatning. Med Cognitive Toolkit kan du enkelt realisera och kombinera populära modelltyper, till exempel feed-forward DNN:er, konvolutionella nät (CNN:er) och återkommande nätverk (RNN:er/LSTM:er). Cognitive Toolkit implementerar SGD-inlärning (Stochastic Gradient Descent, error backpropagation) med automatisk differentiering och pallallerisering över flera GPU:er och servrar.

Använd Cognitive Toolkit när du vill skapa en modell med djupinlärning.  Cognitive Toolkit kan användas i alla tjänster som nämns ovan.

### <a name="microsoft-cognitive-services"></a>Microsoft Cognitive Services
Microsoft Cognitive Services består av en uppsättning på 30 API:er som gör att du kan skapa appar som använder naturliga kommunikationsmetoder. Dessa API: er gör att dina appar kan se, höra, tala, förstå och tolka våra behov med bara ett par rader med kod. Lägg enkelt till smarta funktioner i dina appar, till exempel: 

- Identifiering av känslor och attityder
- Syn- och taligenkänning
- Språkförståelse
- Kunskap och sökning

Med Microsoft Cognitive Services kan du utveckla appar för flera enheter och plattformar. API:erna förbättras ständigt och är enkla att konfigurera. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Nästa steg
* [Installera och skapa Azure Machine Learning](quickstart-installation.md)

