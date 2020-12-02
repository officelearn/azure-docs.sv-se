---
title: Cognitive Services för stordata
description: Lär dig hur du utnyttjar Azure Cognitive Services på stora data uppsättningar med python, Java och Scala. Med Cognitive Services för Big data kan du bädda in kontinuerligt och intelligenta modeller direkt i Apache Spark- &trade; och SQL-beräkningar.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: aaade03edbbb109656fb7371a063cdc2512c5a20
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461622"
---
# <a name="azure-cognitive-services-for-big-data"></a>Azure Cognitive Services för Big data

![Azure Cognitive Services för Big data](media/cognitive-services-big-data-overview.svg)

Med hjälp av Azure-Cognitive Services för Big data kan användarna channela terabyte data genom att Cognitive Services använda [Apache Spark &trade; ](/dotnet/spark/what-is-spark). Med Cognitive Services för Big data är det enkelt att skapa storskaliga intelligenta program med alla data lager.

Med Cognitive Services för Big data kan du bädda in kontinuerligt och intelligenta modeller direkt i Apache Spark- &trade; och SQL-beräkningar. Dessa verktyg utvecklar utvecklare från lågnivå information på låg nivå, så att de kan fokusera på att skapa smarta, distribuerade program.

## <a name="features-and-benefits"></a>Funktioner och fördelar

Cognitive Services för Big data kan använda tjänster från valfri region i världen, samt [Cognitive Services för behållare](../cognitive-services-container-support.md). Behållare stöder låga eller inga anslutnings distributioner med svar med ultralåg svars tid. Container Cognitive Services kan köras lokalt, direkt på arbetsnoderna i Spark-klustret eller på en extern Orchestrator som Kubernetes.

## <a name="supported-services"></a>Tjänster som stöds

[Cognitive Services](../index.yml), som nås via API: er och SDK: er, hjälper utvecklare att bygga smarta program utan att ha några AI-eller data vetenskaps kunskaper. Med Cognitive Services kan du göra dina program se, höra, tala, förstå och varför. Om du vill använda Cognitive Services måste programmet skicka data till tjänsten över nätverket. Tjänsten skickar ett intelligent svar i retur när den har tagits emot. Följande tjänster är tillgängliga för stora data arbets belastningar:

### <a name="vision"></a>Visuellt innehåll

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Visuellt innehåll](../computer-vision/index.yml "Visuellt innehåll")| Tjänsten Visuellt innehåll ger dig till gång till avancerade algoritmer för bearbetning av avbildningar och att returnera information. |
|[Ansiktsigenkänning](../face/index.yml "Ansikte")| Ansikts tjänsten ger till gång till avancerade ansikts algoritmer, vilket möjliggör identifiering och igenkänning av ansikts attribut. |

### <a name="speech"></a>Speech

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Tjänst för taligenkänning](../speech-service/index.yml "Tjänst för taligenkänning")|Tal tjänsten ger till gång till funktioner som tal igenkänning, tal syntes, tal översättning och högtalar verifiering och identifiering.|

### <a name="decision"></a>Beslut

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Avvikelseidentifiering](../anomaly-detector/index.yml "Avvikelseidentifiering") | Med tjänsten avvikelse detektor (förhands granskning) kan du övervaka och identifiera avvikelser i dina tids serie data.|

### <a name="language"></a>Språk

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Textanalys](../text-analytics/index.yml "Textanalys")| Tjänsten Textanalys tillhandahåller naturlig språk bearbetning över rå text för sentiment analys, extrahering av nyckel fraser och språk identifiering.|

### <a name="search"></a>Search

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Bildsökning i Bing](/azure/cognitive-services/bing-image-search "Bildsökning i Bing")|Tjänsten Bildsökning i Bing returnerar en visning av bilder som har fastställts vara relevanta för användarens fråga.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Programmeringsspråk som stöds för Cognitive Services för Big data

Cognitive Services för Big data bygger på Apache Spark. Apache Spark är ett distribuerat data bearbetnings bibliotek som stöder Java, Scala, python, R och många andra språk. Dessa språk stöds för närvarande.

### <a name="python"></a>Python

Vi tillhandahåller ett PySpark-API i `mmlspark.cognitive` namn området för [Microsoft ML för Apache Spark](https://aka.ms/spark). Mer information finns i [python Developer API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Exempel på användnings exempel finns i [python-exemplen](samples-python.md).

### <a name="scala-and-java"></a>Scala och Java

Vi tillhandahåller ett Scala-och Java-baserat Spark-API i `com.microsoft.ml.spark.cognitive` namn området för [Microsoft ML för Apache Spark](https://aka.ms/spark). Mer information finns i [Scala Developer API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Exempel på användnings exempel finns i [Scala-exemplen](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Plattformar och anslutningar som stöds

Cognitive Services för Big data kräver Apache Spark. Det finns flera Apache Spark plattformar som stöder Cognitive Services för Big data.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azures plattform för molntjänster. Den innehåller en enkel klicknings installation, effektiviserade arbets flöden och en interaktiv arbets yta som har stöd för samarbete mellan data experter, data tekniker och affärsanalytiker.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) är ett informations lager för företag som använder massiv parallell bearbetning. Med Synapse Analytics kan du snabbt köra komplexa frågor över petabyte data. Azure Synapse Analytics tillhandahåller hanterade Spark-pooler för att köra Spark-jobb med ett intuitivt Jupyter Notebook gränssnitt.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Azure Kubernetes service (AKS)](../../aks/index.yml) dirigerar Docker-behållare och distribuerade program i massiv skala. AKS är ett hanterat Kubernetes-erbjudande som fören klar användningen av Kubernetes i Azure. Kubernetes kan ge detaljerad kontroll över skalning, svars tid och nätverk för kognitiva tjänster. Vi rekommenderar dock att du använder Azure Databricks eller Azure Synapse Analytics om du inte är bekant med Apache Spark.

### <a name="data-connectors"></a>Data kopplingar

När du har ett Spark-kluster ansluter nästa steg till dina data. Apache Spark har en bred samling databas anslutningar. Dessa anslutningar gör det möjligt för program att arbeta med stora data uppsättningar oavsett var de lagras. Mer information om databaser och anslutningar som stöds finns i [listan över data källor som stöds för Azure Databricks](/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Begrepp

### <a name="spark"></a>Spark

[Apache Spark &trade; ](http://spark.apache.org/) är en enhetlig analys motor för storskalig data bearbetning. Ramverket för parallell bearbetning ökar prestandan för Big data och analys program. Spark kan användas både för batch-och data bearbetnings system, utan att ändra kärn program koden.

Grunden för Spark är DataFrame: en tabell samling med data som distribueras över Apache Spark arbetsnoder. En spark-DataFrame liknar en tabell i en Relations databas eller data ram i R/python, men med obegränsad skala. DataFrames kan skapas från många källor, till exempel: strukturerade datafiler, tabeller i Hive eller externa databaser. När dina data finns i en spark-DataFrame kan du:

   - Gör SQL-format-beräkningar som kopplings-och filter tabeller.
   - Använd Functions för stora data uppsättningar med MapReduce stil parallellitet.
   - Använd distribuerade Machine Learning med Microsoft Machine Learning för Apache Spark.
   - Använd Cognitive Services för Big data för att utöka dina data med färdiga intelligenta tjänster.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning för Apache Spark (MMLSpark)

[Microsoft Machine Learning for Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) är ett distribuerat, integrerat Machine Learning Library (ml) med öppen källkod som bygger på Apache Spark. Cognitive Services för Big data ingår i det här paketet. Dessutom innehåller MMLSpark flera andra ML-verktyg för Apache Spark, till exempel LightGBM, Vowpal Wabbit, OpenCV, kalk och mer. Med MMLSpark kan du bygga kraftfulla förutsägande och analytiska modeller från valfri Spark-datakälla.

### <a name="http-on-spark"></a>HTTP på Spark

Cognitive Services för Big data är ett exempel på hur vi kan integrera intelligenta webb tjänster med Big data. Webb tjänster kraften många program i hela världen och de flesta tjänster kommunicerar via Hypertext Transfer Protocol (HTTP). För att kunna arbeta med *godtyckliga* webb tjänster i stor skala ger vi http på Spark. Med HTTP på Spark kan du skicka flera terabyte data via alla webb tjänster. Under huven använder vi den här tekniken för att driva Cognitive Services för Big data.

## <a name="developer-samples"></a>Utvecklarexempel

- [Recept: förutsägande underhåll](recipes/anomaly-detection.md)
- [Recept: intelligent konst utforskning](recipes/art-explorer.md)

## <a name="blog-posts"></a>Blogginlägg

- [Lär dig mer om hur Cognitive Services fungerar på Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Spara snö leopard med djup inlärning och Visuellt innehåll i Spark](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research poddsändning: MMLSpark, som ger AI för att vara lämpligt med mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [Akademiska fakta blad: storskaliga intelligenta mikrotjänster](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Webb seminarier och videor

- [Azure Cognitive Services på Spark: kluster med inbäddade intelligenta tjänster](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark Summit-ledning: skalbart AI för utmärkt](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cognitive Services för Big data i Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Blixt samtal om storskaliga intelligenta mikrotjänster](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Nästa steg

- [Komma igång med Cognitive Services för Big data](getting-started.md)
- [Enkla python-exempel](samples-python.md)
- [Enkla Scala-exempel](samples-scala.md)