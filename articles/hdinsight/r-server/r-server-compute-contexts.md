---
title: Compute-kontexten alternativ för ML-tjänster på HDInsight - Azure | Microsoft Docs
description: Lär dig mer om olika beräkning kontexten alternativen för användare med ML-tjänster på HDInsight
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 57480cef48182a56b315d7d6932883c485f5a7c8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050116"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Compute-kontexten alternativ för ML-tjänster på HDInsight

Ml – tjänster i Azure HDInsight styr hur anropen genom att ange beräknings-kontexten. Den här artikeln beskrivs de alternativ som är tillgängliga för att ange om och hur körningen paralleliserad över kärnor kantnod eller HDInsight-kluster.

Edge-nod i ett kluster ger en lämplig plats att ansluta till klustret och köra R-skript. Med en kantnod har möjlighet att köra parallelized distribuerade funktioner RevoScaleR över kärnor på noden gränsservern. Du kan också köra dem mellan noder i klustret med hjälp av Revoscaler's Hadoop kartan minska eller compute kontexter för Spark.

## <a name="ml-services-on-azure-hdinsight"></a>Ml – tjänster i Azure HDInsight
[Ml – tjänster i Azure HDInsight](r-server-overview.md) innehåller de senaste funktionerna för analys av R-baserade. Det kan använda data som lagras i en HDFS-behållare i din [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob storage") storage-konto, ett Data Lake store eller lokala Linux-filsystem. Eftersom ML Services bygger på öppen källkod R gäller R-baserade program som du skapar öppen källkod R-paket 8000 +. De kan också använda rutiner i [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), Microsofts big analytics datapaketet som ingår i ML-tjänster.  

## <a name="compute-contexts-for-an-edge-node"></a>Beräkna kontexter för en kantnod
I allmänhet körs ett R-skript som körs i ML Services klustret på noden edge inom R-tolken på noden. Undantagen är de steg som anropar en RevoScaleR funktion. RevoScaleR anropen köras i en miljö för beräkning som bestäms av hur du ställer in RevoScaleR beräknings-kontexten.  När du kör din R-skriptet från en kantnod är beräknings-kontexten möjliga värden:

- lokal sekventiella (*lokala*)
- lokala parallell (*localpar*)
- Minska karta
- Spark

Den *lokala* och *localpar* alternativ skiljer sig i hur **rxExec** anropen. De båda köra andra rx funktionsanrop på ett sätt som parallellt över alla tillgängliga kärnor om inget annat anges med hjälp av RevoScaleR **numCoresToUse** alternativ, till exempel `rxOptions(numCoresToUse=6)`. Parallell körning alternativ ger optimala prestanda.

I följande tabell sammanfattas de olika beräkning kontext alternativen för att ange hur anropen:

| Beräkningskontext  | Hur du ställer in                      | Körningskontext                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokal sekventiella | rxSetComputeContext('local')    | Paralleliserad körning över kärnor av noden gränsservern förutom rxExec anrop, som utförs i följd |
| Lokala parallellt   | rxSetComputeContext('localpar') | Paralleliserad körning av noden gränsservern över kärnor |
| Spark            | RxSpark()                       | Paralleliserad distribuerade körning via Spark mellan noderna av HDI-klustret |
| Minska karta       | RxHadoopMR()                    | Paralleliserad distribuerade utförs via minska karta över noderna i HDI-klustret |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Riktlinjer för att besluta om en beräknings-kontext

Som av de tre alternativen du väljer som tillhandahåller paralleliserad körning beror på vilka slags analytics arbetet, storlek och plats för dina data. Det finns ingen enkel formeln som anger vilket compute kontext för att använda. Det finns dock vissa övergripande riktlinjer som hjälper dig att fatta rätt beslut eller åtminstone hjälpa dig att begränsa dina val innan du kör en prestandamått. Dessa riktlinjer omfattar:

- Lokal Linux-filsystemet är snabbare än HDFS.
- Upprepade analys är snabbare om data är lokala och om den är i XDF.
- Är det bättre att strömma små mängder data från en datakälla för text. Om mängden data som är större, konvertera du det till XDF innan analys.
- Arbetet med att kopiera eller strömmande data till kantnoden för analys blir svårhanterligt för mycket stora mängder data.
- Spark är snabbare än kartan minska för analys i Hadoop.

I följande avsnitt erbjuder ges dessa principer vissa allmänna råden för att välja en beräknings-kontext.

### <a name="local"></a>Lokal
* Om mängden data att analysera är liten och kräver inte upprepade analys, sedan strömmas direkt i den analysis rutinunderhåll med hjälp av *lokala* eller *localpar*.
* Om mängden data att analysera är liten eller medelstor och kräver upprepade analys, sedan kopiera den till det lokala filsystemet, importera den till XDF och analysera den via *lokala* eller *localpar*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Om mängden data att analysera är stor kan sedan importera det till ett Spark-DataFrame med **RxHiveData** eller **RxParquetData**, eller XDF i HDFS (om inte lagring är ett problem), och analysera den med hjälp av Spark-beräkning kontexten.

### <a name="hadoop-map-reduce"></a>Minska Hadoop-karta
* Använda context kartan minska beräkning endast om det uppstår ett oöverstigliga problem med Spark beräkning kontexten eftersom det är vanligtvis långsammare.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Infogade hjälp om rxSetComputeContext
Mer information och exempel på RevoScaleR beräkning kontexter finns infogat hjälp i R om metoden rxSetComputeContext, till exempel:

    > ?rxSetComputeContext

Du kan också gå till den [distribuerad databehandling översikt](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) i [Machine Learning Server-dokumentationen](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om de alternativ som är tillgängliga för att ange om och hur körningen paralleliserad över kärnor kantnod eller HDInsight-kluster. Mer information om hur du använder ML-tjänster med HDInsight-kluster finns i följande avsnitt:

* [Översikt över ML Services för Hadoop](r-server-overview.md)
* [Kom igång med Hadoop ML-tjänster](r-server-get-started.md)
* [Azure lagringsalternativ för ML-tjänster på HDInsight](r-server-storage.md)

