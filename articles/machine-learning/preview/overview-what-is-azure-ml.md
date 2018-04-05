---
title: Vad är Azure Machine Learning? | Microsoft Docs
description: Förklarar grundläggande begrepp relaterade till maskininlärning i molnet och vad du kan använda det till, samt definierar viktiga termer inom maskininlärning. Översikt över Azure Machine Learning – en integrerad lösning från slutpunkt till slutpunkt som gör att dataforskare kan utveckla, experimentera och distribuera avancerade analysprogram i molnskala.
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 8a92f42ecee926042e9e0662f6b0bd9438024248
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="what-is-machine-learning"></a>Vad är Machine Learning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Med maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket gör det lättare att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="what-is-azure-machine-learning"></a>Vad är Azure Machine Learning?
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

Mer information finns i [Konfiguration av experimenteringstjänst](experimentation-service-configuration.md).

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
Utöver Azure Machine Learning finns en mängd olika alternativ i Azure för att skapa, distribuera och hantera maskininlärningsmodeller. [Läs mer om dem här.](overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Nästa steg
* [Installera och skapa Azure Machine Learning](quickstart-installation.md)
