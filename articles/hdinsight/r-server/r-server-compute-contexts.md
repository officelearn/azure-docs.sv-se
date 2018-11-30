---
title: Alternativ för beräkningskontexter för ML-tjänster på HDInsight - Azure
description: Lär dig mer om de olika alternativ för beräkningskontexter finns tillgängliga för användare med ML-tjänster på HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 1e01a3db2c0ca1f9024afb3faecf677ac4e3131b
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494475"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Alternativ för beräkningskontexter för ML-tjänster på HDInsight

ML-tjänster på Azure HDInsight styr hur anropen genom att ställa in beräkningskontexten. Den här artikeln beskrivs de alternativ som är tillgängliga för att ange om och hur körning parallelliseras över kärnor för kantnoden eller HDInsight-kluster.

Edge-nod i ett kluster är ett bra ställe att ansluta till klustret och köra R-skript. Med en kantnod har du möjlighet att köra parallelliserad distribuerade functions av RevoScaleR över kärnor i noden gränsservern. Du kan också köra dem mellan noderna i klustret med hjälp av Revoscaler's Hadoop Map Reduce eller Apache Spark compute-sammanhang.

## <a name="ml-services-on-azure-hdinsight"></a>ML-tjänster på Azure HDInsight
[ML-tjänster på Azure HDInsight](r-server-overview.md) innehåller de senaste funktionerna för R-baserad analys. Den kan använda data som lagras i en Apache Hadoop HDFS-behållare i din [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob storage") storage-konto, ett Data Lake store eller lokala Linux-filsystem. Eftersom ML Services bygger på R med öppen källkod, kan R-baserade program som du skapar installera 8000 + R med öppen källkod paket. De kan också använda rutiner i [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), Microsofts big data analytics paket som ingår i ML-tjänster.  

## <a name="compute-contexts-for-an-edge-node"></a>Compute-sammanhang för en kantnod
I allmänhet körs ett R-skript som körs i klustret för ML-tjänster på gränsnoden inom interpret R på noden. Undantagen är de steg som anropar en funktion för RevoScaleR. RevoScaleR-anrop körs i en beräkningsmiljö som bestäms av hur du ställer in beräkningskontexten RevoScaleR.  När du kör ditt R-skript från en kantnod, är de möjliga värdena för beräkningskontexten:

- lokal sekventiella (*lokala*)
- lokala parallella (*localpar*)
- Map Reduce
- Spark

Den *lokala* och *localpar* alternativen skiljer sig åt i hur **rxExec** anropen. De båda köra andra rx-funktionsanrop i en parallell sätt över alla tillgängliga kärnor om inget annat anges med hjälp av RevoScaleR **numCoresToUse** alternativ, till exempel `rxOptions(numCoresToUse=6)`. Parallell körning alternativ ger optimala prestanda.

I följande tabell sammanfattas de olika alternativ för beräkningskontexter att ställa in hur anropen:

| Beräkningskontext  | Hur du ställer in                      | Körningskontext                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokal sekventiella | rxSetComputeContext('local')    | Parallelliserad körning över kärnorna av noden gränsservern förutom rxExec-anrop, vilket utförs säkerhetskopieringarna i följd |
| Lokala parallellt   | rxSetComputeContext('localpar') | Parallelliserad körning över kärnorna av edge-nod-servern |
| Spark            | RxSpark()                       | Parallelliserad distribuerade körning via Spark noder av HDI-kluster |
| Map Reduce       | RxHadoopMR()                    | Parallelliserad distribuerade körning via Map Reduce noder av HDI-kluster |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Riktlinjer för att besluta om en beräkningskontext

Vilket av de tre alternativen du väljer som tillhandahåller parallelliserad körning beror på typen av ditt arbete med analytics, storlek och platsen för dina data. Det finns ingen enkel formel som talar om vilket beräkningskontext att använda. Det finns dock vissa riktlinjerna som kan hjälpa dig fatta rätt beslut eller minst, hjälpa dig att förfina dina alternativ innan du kör ett prestandamått. Dessa riktlinjer inkluderar:

- Det lokala Linux-filsystemet är snabbare än HDFS.
- Upprepad analyser är snabbare om data är lokala och om den tillhör XDF.
- Det är bättre att strömma små mängder data från en datakälla för text. Om mängden data som är större, konvertera du den till XDF före analysen.
- Arbetet med att kopiera eller strömmande data till gränsnoden för analys blir svårhanterligt för mycket stora mängder data.
- ApacheSpark är snabbare än Map Reduce för analys i Hadoop.

I följande avsnitt erbjuda med dessa principer kan vissa allmänna råden för att välja en beräkningskontext.

### <a name="local"></a>Lokal
* Om mängden data som ska analyseras är liten och inte kräver upprepad analys, sedan strömma den direkt i analysis rutinmässig med *lokala* eller *localpar*.
* Om mängden data som ska analyseras är små eller medelstora och analys av upprepade krävs, sedan kopiera den till det lokala filsystemet, importera den till XDF och analysera dem via *lokala* eller *localpar*.

### <a name="apache-spark"></a>Apache Spark
* Om mängden data som ska analyseras är stor kan sedan importera den till en Spark DataFrame med **RxHiveData** eller **RxParquetData**, eller till XDF i HDFS (såvida inte lagring är ett problem), och analysera den med hjälp av Spark-beräkning kontext.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Map Reduce
* Använda Map Reduce-beräkningskontexten endast om du får ett oöverstigliga problem med Spark-beräkningskontexten eftersom det är vanligtvis långsammare.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Infogad hjälp på rxSetComputeContext
Mer information och exempel på RevoScaleR-beräkningskontext finns i infogat hjälp i R på metoden rxSetComputeContext, till exempel:

    > ?rxSetComputeContext

Du kan även gå till den [distribuerade översikt över](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) i [dokumentation om Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om de alternativ som är tillgängliga för att ange om och hur körning parallelliseras över kärnor för kantnoden eller HDInsight-kluster. Mer information om hur du använder ML-tjänster med HDInsight-kluster finns i följande avsnitt:

* [Översikt över ML-tjänster för Apache Hadoop](r-server-overview.md)
* [Kom igång med ML-tjänster för Apache Hadoop](r-server-get-started.md)
* [Alternativ för Azure Storage för ML-tjänster på HDInsight](r-server-storage.md)

