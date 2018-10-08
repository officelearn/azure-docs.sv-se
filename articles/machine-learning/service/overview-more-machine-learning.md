---
title: Jämför maskininlärningsprodukter från Microsoft – Azure | Microsoft Docs
description: Jämför olika produkter från Microsoft för att bygga, distribuera och hantera dina maskininlärningsmodeller. Bestäm vilka produkter du ska välja för din lösning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 182504373795b3cb0f2794acbed5e253ac6bc95c
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419567"
---
# <a name="what-are-the-machine-learning-product-options-from-microsoft"></a>Vilka maskininlärningsprodukter har Microsoft?

Microsoft har olika produktalternativ för att bygga, distribuera och hantera dina maskininlärningsmodeller. Jämför dessa produkter och välj vad du behöver för att utveckla dina maskininlärningslösningar på effektivast möjliga sätt.

| Maskininlärningsprodukt | Vad det är | Det här kan du göra |
|-|-|-|
| I molnet | | |
| [Azure Machine Learning-tjänst](#azure-machine-learning-services) | Hanterad molntjänst för ML  | Träna, distribuera och hantera modeller i Azure med Python och CLI |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | Visuellt dra och släpp-gränssnitt för ML | Skapa, experimentera och distribuera modeller med förkonfigurerade algoritmer |
| [Azure Databricks](#azure-databricks) | Apache Spark-baserad analysplattform | Skapa och distribuera modeller och dataarbetsflöden |
| [Azure Cognitive Services](#azure-cognitive-services) | Azure-tjänster med inbyggda AI- och ML-modeller | Ett enkelt sätt att lägga till smarta funktioner i dina appar |
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | Virtuell dator med förinstallerade verktyg för datavetenskap | Utveckla ML-lösningar i en förkonfigurerad miljö |
| Lokal | | |
| [SQL Server Machine Learning Services](#sql-server-machine-learning-services) | Analysmotor som är inbäddad i SQL | Skapa och distribuera modeller i SQL Server |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | Fristående företagsserver för förutsägbar analys | Skapa och distribuera modeller med R och Python |
| Utvecklarverktyg | | |
| [ML.NET](#mlnet) | Plattformsoberoende ML-SDK med öppen källkod | Utveckla ML-lösningar för .NET-program |
| [Windows ML](#windows-ml) | ML-plattform för Windows 10 | Utvärdera tränade modeller på en Windows 10-enhet |

## <a name="azure-machine-learning-service"></a>Azure Machine Learning-tjänst

[Azure Machine Learning-tjänsten](overview-what-is-azure-ml.md) (förhandsversion) är en fullständigt hanterad molntjänst som används till att träna, distribuera och hantera ML-modeller i skala. Den stöder tekniker för öppen källkod, vilket innebär att du kan använda tiotusentals Python-paket med öppen källkod som exempelvis TensorFlow, PyTorch och scikit-learn. Omfattande verktyg finns också, till exempel [Azure Notebooks](https://notebooks.azure.com/), [Jupyter Notebooks](http://jupyter.org) eller [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/), som gör det enkelt att utforska och omvandla data, och sedan träna och distribuera modeller. Azure Machine Learning-tjänsten innehåller funktioner som automatiserar modellgenerering och finjustering med enkelhet, effektivitet och precision.

Använd Azure Machine Learning-tjänsten till att träna, distribuera och hantera ML-modeller med hjälp av Python och CLI i molnskala.

>[!Note]
> Du kan testa Azure Machine Learning kostnadsfritt. Det behövs inte något kreditkort eller någon Azure-prenumeration. Kom igång nu. https://azure.microsoft.com/free/

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Med [Azure Machine Learning Studio](../studio/what-is-ml-studio.md) får du en interaktiv, visuell arbetsyta som du kan använda för att snabbt och enkelt bygga, testa och distribuera modeller med hjälp av färdiga maskininlärningsalgoritmer. Tjänsten Machine Learning Studio publicerar modeller som webbtjänster som enkelt kan användas av anpassade appar eller BI-verktyg som Excel.
Ingen programmering krävs – du skapar din maskininlärningsmodell genom att koppla ihop datauppsättningar och analysmoduler på en interaktiv arbetsyta och sedan distribuera den med ett par klick.

Använd Machine Learning Studio när du vill utveckla och distribuera modeller utan kodning.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azures plattform för molntjänster. Databricks är integrerat med Azure för att ge konfiguration med ett klick, effektiva arbetsflöden och en interaktiv arbetsyta som möjliggör samarbete mellan dataforskare, dataingenjörer och affärsanalytiker.
Använd Python-, R-, Scala- och SQL-kod i webbaserade anteckningsböcker för att fråga, visualisera och modellera data.

Använd Databricks när du vill samarbeta om att skapa maskininlärningslösningar i Apache Spark.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services

[Azure Cognitive Services](/azure/cognitive-services/welcome) består av en uppsättning API:er som gör att du kan skapa appar som använder naturliga kommunikationsmetoder. Dessa API: er gör att dina appar kan se, höra, tala, förstå och tolka våra behov med bara ett par rader med kod. Lägg enkelt till smarta funktioner i dina appar, till exempel: 

- Identifiering av känslor och attityder
- Syn- och taligenkänning
- Language Understanding (LUIS)
- Kunskap och sökning

Med Cognitive Services kan du utveckla appar för flera enheter och plattformar. API:erna förbättras ständigt och är enkla att konfigurera.

## <a name="azure-data-science-virtual-machine"></a>Virtuell Azure-dator för datavetenskap

[Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md) är en anpassad virtuell datormiljö i Microsoft Azure-molnet som skapats specifikt för datavetenskap. Många populära verktyg för datavetenskap och annat finns förinstallerade och förkonfigurerade så att du kan börja direkt med att skapa intelligenta program för avancerade analyser.
Data Science Virtual Machine är tillgänglig i versioner för både Windows och Linux Ubuntu (Azure Machine Learning-tjänsten stöds inte på Linux CentOS).
Specifik versionsinformation och en lista över vad som ingår finns i [Introduktion till Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md).
Data Science Virtual Machine kan användas som ett mål för Azure Machine Learning-tjänsten.

Använd den virtuella datorn för datavetenskap när du behöver köra eller vara värd för jobb på en enda nod. Eller om du behöver skala upp din bearbetning via fjärranslutning på en enskild dator.

## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services

[SQL Server Microsoft Machine Learning-tjänsten](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) lägger till statistisk analys, datavisualisering och förutsägelseanalys i R och Python för relationsdata i SQL Server-databaser. R- och Python-bibliotek från Microsoft innehåller avancerad modellering och ML-algoritmer, vilka kan köras parallellt och i skala i SQL Server.

Använd SQL Server Machine Learning Services när du behöver inbyggd AI och förutsägelseanalys av relationsdata i SQL Server.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) är en företagsserver som gör det möjligt att vara värd för och hantera parallella och distribuerade arbetsbelastningar för R- och Python-processer. Microsoft Machine Learning Server körs på Linux, Windows, Hadoop och Apache Spark och finns också i [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Den innehåller en körningsmotor för lösningar som skapats med [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) och [MicrosoftML-paket](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), samt utökar R med öppen källkod och Python med stöd för högpresterande analyser, statistiska analyser, maskininlärning och massiva datauppsättningar. Ytterligare funktioner tillhandahålls via egna paket som installeras med servern. Du kan använda IDE:er för utveckling, till exempel [R Tools för Visual Studio](https://www.visualstudio.com/vs/rtvs/) och [Python Tools för Visual Studio](https://www.visualstudio.com/vs/python/).

Använd Microsoft Machine Learning Server när du vill skapa och operationalisera modeller som skapats med R och Python på en server, eller distribuera R- och Python-träning i skala i ett Hadoop- eller Spark-kluster.

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) är ett ramverk för kostnadsfri och plattformsoberoende maskininlärning med öppen källkod som hjälper dig att skapa anpassade maskininlärningslösningar och integrera dem i dina .NET-program.

Använd ML.NET när du vill integrera maskininlärningslösningar i dina .NET-program.

## <a name="windows-ml"></a>Windows ML

Med [Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) kan du använda tränade maskininlärningsmodeller i dina program och utvärdera tränade modeller lokalt på Windows 10-enheter.

Använd Windows ML när du vill använda tränade maskininlärningsmodeller i dina Windows-program.

## <a name="next-steps"></a>Nästa steg

- Mer information om alla AI-utvecklingsprodukter (Articifical Intelligence) från Microsoft finns på [Microsofts AI-plattform](https://www.microsoft.com/ai)
- Utbildning i hur du utvecklar AI-lösningar finns i [Microsoft AI School](https://aischool.microsoft.com/learning-paths)
