---
title: Andra maskininlärning produkter från Microsoft - Azure Machine Learning | Microsoft Docs
description: Förutom Azure Machine Learning finns en mängd olika alternativ till Microsoft för att skapa, distribuera och hantera dina maskininlärning modeller.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: bbb8deeab8368d9c0e6d29c8d7e1e2e0a8805d60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Andra machine learning-produkter och tjänster från Microsoft

Utöver [Azure Machine Learning](overview-what-is-azure-ml.md), finns det en mängd olika alternativ hos Microsoft för att skapa, distribuera och hantera dina maskininlärningsmodeller. 
* SQL Server Machine Learning Services
* Microsoft Machine Learning Server
* Azure datavetenskap virtuell dator
* Spark MLLib i HDInsight
* Batch AI Training Service
* Microsoft kognitiva Toolkit (CNTK)
* Kognitiva Azure-tjänster


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services
[SQL Server Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) gör att du kan köra, träna och distribuera machine learning-modeller med R eller Python. Du kan använda data som finns lokalt och i SQL Server-databaser. 

Använd Microsoft Machine Learning-tjänster när du behöver träna eller distribuera modeller lokalt eller i Microsoft SQL Server. Modeller som skapats med Machine Learning-tjänster kan distribueras med Azure Machine Learning-modellhantering. 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) är en företagsserver som gör det möjligt att vara värd för och hantera parallella och distribuerade arbetsbelastningar för R- och Python-processer. Microsoft Machine Learning Server körs på Linux, Windows, Hadoop och Apache Spark. Och är även tillgängligt i [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Det ger en motorn för körning av lösningar som skapats med [Microsoft Machine Learning paket](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), och utökar öppen källkod R och Python med stöd för följande scenarier:

- Analyser med höga prestanda
- Statistiska analyser
- Maskininlärning
- Massiva datauppsättningar

Ytterligare funktioner tillhandahålls via egna paket som installeras med servern. Du kan använda IDE:er för utveckling, till exempel [R Tools för Visual Studio](https://www.visualstudio.com/vs/rtvs/) och [Python Tools för Visual Studio](https://www.visualstudio.com/vs/python/).

Använd Microsoft Machine Learning Server när du behöver:

- Skapa och distribuera modeller som skapats med R och Python på en server
- Distribuera R- och Python-träning i stor skala i ett Hadoop- eller Spark-kluster

## <a name="azure-data-science-virtual-machine"></a>Azure datavetenskap virtuell dator
Den [datavetenskap virtuella](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) är en miljö med anpassade virtuella datorer i Microsoft Azure-moln som skapats specifikt för att göra datavetenskap. Många populära verktyg för datavetenskap och annat finns förinstallerade och förkonfigurerade så att du kan börja direkt med att skapa intelligenta program för avancerade analyser. Den virtuella datorn vetenskap Data är tillgängliga i Windows Server-versionerna 2016 och 2012 och i en Linux-VM på Ubuntu 16.04 LTS och OpenLogic CentOS 7.4-baserade distributioner. 

Använd den virtuella datorn vetenskap Data när du behöver köra eller värd för dina jobb på en enda nod. Eller om du behöver skala upp din bearbetning via fjärranslutning på en enskild dator. Den virtuella datorn för datavetenskap kan vara mål för både Azure Machine Learning- experimentering och Azure Machine Learning-modellhantering. 

## <a name="spark-mllib-in-hdinsight"></a>Spark MLLib i HDInsight
Med [Spark MLLib i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) kan du skapa modeller som en del i Spark-jobb som körs på stordata. Med Spark kan du enkelt omvandla och förbereda data och sedan skala ut modellskapandet i ett enskilt jobb. Modeller som skapas via Spark MLLib kan distribueras, hanteras och övervakas via Azure Machine Learning-modellhantering. Träningskörningar kan skickas och hanteras med Azure Machine Learning-experimentering. Spark kan också användas för att skala ut dataförberedelsejobb som skapats i Machine Learning Workbench. 

Använd Spark när du behöver skala ut din databearbetning och skapa modeller som en del av en datapipeline. Du kan skapa Spark-jobb i Scala, Java, Python eller R. 

## <a name="batch-ai-training"></a>Batch AI Training 
[Azure Batch AI Training](https://aka.ms/batchaitraining) hjälper dig att experimentera parallellt med dina AI-modeller med valfritt ramverk och sedan träna dem i stor skala över klustrade GPU:er. Beskriv jobbkraven och konfigurationen som ska köras så hanterar vi resten. 

Med Batch AI Training kan du skala ut djupinlärningsjobb över klustrade GPU:er med ramverk som:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning-modellhantering kan användas för att hämta modeller från Batch AI Training för att distribuera, hantera och övervaka dem.  Batch AI Training kommer att integreras med Azure Machine Learning-experimentering i framtiden. 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft kognitiva Toolkit (CNTK)
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) är ett enhetligt djupinlärningsverktyg som beskriver neurala nätverk som beräkningssteg i en riktad graf. I den riktade grafen representerar lövnoder indatavärden eller nätverksparametrar, medan andra noder representerar matrisåtgärder vid inmatning. Med Cognitive Toolkit kan du enkelt realisera och kombinera populära modelltyper, till exempel feed-forward DNN:er, konvolutionella nät (CNN:er) och återkommande nätverk (RNN:er/LSTM:er). Cognitive Toolkit implementerar SGD-inlärning (Stochastic Gradient Descent, error backpropagation) med automatisk differentiering och pallallerisering över flera GPU:er och servrar.

Använd Cognitive Toolkit när du vill skapa en modell med djupinlärning.  Cognitive Toolkit kan användas i alla tjänster som nämns ovan.

## <a name="azure-cognitive-services"></a>Kognitiva Azure-tjänster
[Azure-kognitiva tjänster](https://docs.microsoft.com/azure/#pivot=products&panel=ai) är en uppsättning ca 30 API: er som du kan skapa appar som använder fysiska metoder för kommunikation. Dessa API: er att dina appar finns, höra, tala, förstå och tolka användarnas behov med bara några få rader med kod. Lägg enkelt till smarta funktioner i dina appar, till exempel: 

- Identifiering av känslor och attityder
- Syn- och taligenkänning
- Förstå språk (THOMAS)
- Kunskap och sökning

Kognitiva tjänster kan användas för att utveckla appar olika enheter och plattformar. API:erna förbättras ständigt och är enkla att konfigurera. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning](overview-what-is-azure-ml.md).
