---
title: Beräkna kontextalternativ för ML-tjänster på HDInsight - Azure
description: Lär dig mer om de olika beräkningskontextalternativ som är tillgängliga för användare med ML Services på HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660264"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Beräkna kontextalternativ för ML-tjänster på HDInsight

ML Services på Azure HDInsight styr hur anrop körs genom att ange beräkningskontexten. I den här artikeln beskrivs de alternativ som är tillgängliga för att ange om och hur körningen parallelliseras mellan kärnorna i kantnoden eller HDInsight-klustret.

Kantnoden i ett kluster är en praktisk plats för att ansluta till klustret och köra dina R-skript. Med en kantnod har du möjlighet att köra de parallelliserade distribuerade funktionerna i RevoScaleR över kärnorna på kantnodservern. Du kan också köra dem över noderna i klustret med hjälp av RevoScaleR:s Hadoop Map Reduce- eller Apache Spark-beräkningskontexter.

## <a name="ml-services-on-azure-hdinsight"></a>ML-tjänster på Azure HDInsight

[ML Services på Azure HDInsight](r-server-overview.md) ger de senaste funktionerna för R-baserad analys. Den kan använda data som lagras i en Apache Hadoop HDFS-behållare i ditt [Azure Blob-lagringskonto,](../../storage/common/storage-introduction.md "Azure Blob Storage") ett DataSjölag eller det lokala Linux-filsystemet. Eftersom ML Services bygger på R med öppen källkod kan de R-baserade program som du skapar använda något av de 8000+ R-paket med öppen källkod. De kan också använda rutinerna i [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), Microsofts stordataanalyspaket som ingår i ML Services.  

## <a name="compute-contexts-for-an-edge-node"></a>Beräkna kontexter för en kantnod

I allmänhet körs ett R-skript som körs i ML Services-klustret på kantnoden inom R-tolken på den noden. Undantagen är de steg som anropar en RevoScaleR-funktion. RevoScaleR-anrop körs i en beräkningsmiljö som bestäms av hur du anger beräkningskontexten RevoScaleR.  När du kör R-skriptet från en kantnod är de möjliga värdena för beräkningskontexten:

- lokala sekventiella (*lokala*)
- lokal parallell *(localpar)*
- Karta Minska
- Spark

Alternativen *för lokala* och *lokalapar* skiljer sig bara åt i hur **rxExec-anrop** körs. De båda kör andra rx-funktion samtal på ett parallellt sätt över alla tillgängliga kärnor om inte annat anges med `rxOptions(numCoresToUse=6)`hjälp av RevoScaleR **numCoresToUse** alternativet, till exempel . Parallella körningsalternativ ger optimal prestanda.

I följande tabell sammanfattas de olika beräkningskontextalternativen för att ange hur anrop ska utföras:

| Beräkningskontext  | Så här ställer du in                      | Körningskontext                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokalt sekventiellt | rxSetComputeContext('lokal')    | Parallelliserad körning över kärnorna på kantnodservern, med undantag för rxExec-anrop, som körs seriellt |
| Lokal parallell   | rxSetComputeContext('localpar') | Parallelliserad körning över kärnorna på kantnodsservern |
| Spark            | RxSpark()                       | Parallelliserad distribuerad körning via Spark över noderna i HDI-klustret |
| Karta Minska       | RxHadoopMR()                    | Parallelliserad distribuerad körning via karta Minska över noderna i HDI-klustret |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Riktlinjer för att besluta om en beräkningskontext

Vilket av de tre alternativ du väljer som ger parallelliserad körning beror på typ av analysarbete, storlek och plats för dina data. Det finns ingen enkel formel som talar om för dig, vilken beräkningskontext som ska användas. Det finns dock några vägledande principer som kan hjälpa dig att göra rätt val, eller åtminstone hjälpa dig att begränsa dina val innan du kör ett riktmärke. Dessa vägledande principer omfattar:

- Det lokala Linux-filsystemet är snabbare än HDFS.
- Upprepade analyser är snabbare om data är lokala och om de finns i XDF.
- Det är bättre att strömma små mängder data från en textdatakälla. Om mängden data är större konverterar du den till XDF före analys.
- Omkostnaderna för kopiering eller direktuppspelning av data till kantnoden för analys blir ohanterlig för mycket stora mängder data.
- ApacheSpark är snabbare än Karta Minska för analys i Hadoop.

Med tanke på dessa principer erbjuder följande avsnitt några allmänna tumregler för att välja en beräkningskontext.

### <a name="local"></a>Lokal

- Om mängden data som ska analyseras är liten och inte kräver upprepad analys, strömma den direkt till analysrutinen med hjälp av *local* eller *localpar*.
- Om mängden data som ska analyseras är liten eller medelstor och kräver upprepad analys, kopiera den sedan till det lokala filsystemet, importera den till XDF och analysera den via *local* or *localpar*.

### <a name="apache-spark"></a>Apache Spark

- Om mängden data som ska analyseras är stor importerar du den till en Spark DataFrame med **RxHiveData** eller **RxParquetData**eller till XDF i HDFS (såvida inte lagring är ett problem) och analyserar den med beräkningskontexten Spark.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Karta Minska

- Använd endast beräkningskontexten Förminska mappning om du stöter på ett oöverstigligt problem med Spark-beräkningskontexten eftersom det i allmänhet är långsammare.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Infogad hjälp på rxSetComputeContext
Mer information och exempel på RevoScaleR-beräkningskontexter finns i hjälpen för infogade funktioner i R på metoden rxSetComputeContext, till exempel:

    > ?rxSetComputeContext

Du kan också läsa [översikten Distribuerad datoranvändning](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) i [Machine Learning Server-dokumentationen](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de alternativ som är tillgängliga för att ange om och hur körningen parallelliseras mellan kärnorna i kantnoden eller HDInsight-klustret. Mer information om hur du använder ML-tjänster med HDInsight-kluster finns i följande avsnitt:

- [Översikt över ML-tjänster för Apache Hadoop](r-server-overview.md)
- [Azure Storage-alternativ för ML-tjänster på HDInsight](r-server-storage.md)
