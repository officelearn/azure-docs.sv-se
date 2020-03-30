---
title: Azure Data Lake Storage Gen1 – prestandajustering
description: Beskriver hur du ställer in Azure Data Lake Storage Gen1 för prestanda.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904631"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Justera Azure Data Lake Storage Gen1 för prestanda

Data Lake Storage Gen1 stöder högdataflöde för I/O-intensiva analyser och dataförflyttningar. I Data Lake Storage Gen1 är det viktigt att använda all tillgänglig dataflöde – mängden data som kan läsas eller skrivas per sekund – för att få bästa prestanda. Detta uppnås genom att utföra så många läsningar och skrivningar parallellt som möjligt.

![Prestanda för DataSjölagring Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 kan skalas för att tillhandahålla det nödvändiga dataflödet för alla analysscenario. Som standard ger ett Data Lake Storage Gen1-konto automatiskt tillräckligt med dataflöde för att uppfylla behoven hos en bred kategori av användningsfall. I de fall där kunder hamnar i standardgränsen kan datasjölagringsgenm1-kontot konfigureras för att ge mer dataflöde genom att kontakta Microsoft-supporten.

## <a name="data-ingestion"></a>Datainhämtning

När du inmatar data från ett källsystem till Data Lake Storage Gen1 är det viktigt att tänka på att källmaskinvaran, källnätverksmaskinvaran och nätverksanslutningen till Data Lake Storage Gen1 kan vara flaskhalsen.

![Prestanda för DataSjölagring Gen1](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Det är viktigt att se till att datarörelsen inte påverkas av dessa faktorer.

### <a name="source-hardware"></a>Källmaskinvara

Oavsett om du använder lokala datorer eller virtuella datorer i Azure bör du noggrant välja lämplig maskinvara. För Källdiskhårdvara föredrar du SSD-enheter framför hårddiskar och väljer diskmaskinvara med snabbare spindlar. Använd de snabbaste nätverkskorten som möjligt för källnätverksmaskinvara. På Azure rekommenderar vi virtuella Azure D14-datorer som har den lämpliga kraftfulla disk- och nätverksmaskinvaran.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Nätverksanslutning till Data Lake Storage Gen1

Nätverksanslutningen mellan källdata och Data Lake Storage Gen1 kan ibland vara flaskhalsen. När källdata är lokala bör du överväga att använda en dedikerad länk med [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Om källdata finns i Azure är prestanda bäst när data finns i samma Azure-region som Data Lake Storage Gen1-kontot.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurera datainmatningsverktyg för maximal parallellisering

När du har åtgärdat källmaskinvaran och flaskhalsarna för nätverksanslutningen är du redo att konfigurera inmatningsverktygen. I följande tabell sammanfattas de viktigaste inställningarna för flera populära inmatningsverktyg och artiklar med djupgående prestandajusteringar för dem. Mer information om vilket verktyg du ska använda för ditt scenario finns i den här [artikeln](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Verktyg          | Inställningar | Mer information                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, SamtidigfileCount | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy (AdlCopy)    | Azure Data Lake Analytics-enheter | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp (olika)            | -m (mapper) | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies (parallelCopies) | [Länk](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper) | [Länk](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Strukturera datauppsättningen

När data lagras i Data Lake Storage Gen1 påverkar filstorleken, antalet filer och mappstrukturen prestanda. I följande avsnitt beskrivs bästa praxis inom dessa områden.

### <a name="file-size"></a>Filstorlek

Analysmotorer som HDInsight och Azure Data Lake Analytics har vanligtvis en omkostnader per fil. Om du lagrar dina data som många små filer kan detta påverka prestanda negativt.

I allmänhet ordnar du dina data i filer av större storlek för bättre prestanda. Som en tumregel, organisera datauppsättningar i filer på 256 MB eller större. I vissa fall, till exempel bilder och binära data, är det inte möjligt att bearbeta dem parallellt. I dessa fall rekommenderas att behålla enskilda filer under 2 GB.

Ibland har datapipellines begränsad kontroll över rådata som innehåller många små filer. Det rekommenderas att ha en "matlagning" process som genererar större filer att använda för nedströms program.

### <a name="organize-time-series-data-in-folders"></a>Ordna tidsseriedata i mappar

För Hive- och ADLA-arbetsbelastningar kan partitionsbeskärning av tidsseriedata hjälpa vissa frågor att bara läsa en delmängd av data, vilket förbättrar prestanda.

De pipelines som intar tidsseriedata placerar ofta sina filer med ett strukturerat namn för filer och mappar. Följande är ett vanligt exempel som vi ser för data som är strukturerade efter datum:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Observera att datuminformationen visas både som mappar och i filnamnet.

För datum och tid är följande ett vanligt mönster

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Återigen bör valet du gör med mappen och filorganisationen optimera för de större filstorlekarna och ett rimligt antal filer i varje mapp.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimera I/O-intensiva jobb på Hadoop- och Spark-arbetsbelastningar på HDInsight

Jobb kan delas in i någon av följande tre kategorier:

* **CPU-intensiva.** Dessa jobb har långa beräkningstider med minimala I/O-tider. Exempel på detta är maskininlärning och jobb för bearbetning av naturligt språk.
* **Minnesintensivt.** Dessa jobb använder massor av minne. Exempel är PageRank och realtidsanalysjobb.
* **I/O-intensiv.** Dessa jobb tillbringar större delen av sin tid att göra I / O. Ett vanligt exempel är ett kopieringsjobb som bara läser och skriver åtgärder. Andra exempel är jobb för dataförberedelse som läser många data, utför vissa dataomvandlingar och sedan skriver tillbaka data till arkivet.

Följande vägledning gäller endast för I/O-intensiva jobb.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Allmänna överväganden för ett HDInsight-kluster

* **HDInsight versioner.** För bästa prestanda, använd den senaste versionen av HDInsight.
* **Regioner.** Placera datasjölagringsgenm1-kontot i samma region som HDInsight-klustret.

Ett HDInsight-kluster består av två huvudnoder och vissa arbetsnoder. Varje arbetsnod tillhandahåller ett visst antal kärnor och minne, som bestäms av vm-typen. När du kör ett jobb är YARN resursförhandlaren som allokerar tillgängligt minne och kärnor för att skapa behållare. Varje behållare kör de uppgifter som behövs för att slutföra jobbet. Behållare körs parallellt med processaktiviteter snabbt. Därför förbättras prestanda genom att köra så många parallella behållare som möjligt.

Det finns tre lager i ett HDInsight-kluster som kan justeras för att öka antalet behållare och använda allt tillgängligt dataflöde.

* **Fysiskt lager**
* **GARNskikt**
* **Arbetsbelastningsskikt**

### <a name="physical-layer"></a>Fysiskt lager

**Kör kluster med fler noder och/eller virtuella datorer av större storlek.** Ett större kluster gör att du kan köra fler YARN-behållare som visas på bilden nedan.

![Prestanda för DataSjölagring Gen1](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Använd virtuella datorer med mer nätverksbandbredd.** Mängden nätverksbandbredd kan vara en flaskhals om det finns mindre nätverksbandbredd än datasjölagring gen1-dataflöde. Olika virtuella datorer kommer att ha varierande storlekar nätverksbandbredd. Välj en VM-typ som har största möjliga nätverksbandbredd.

### <a name="yarn-layer"></a>GARNskikt

**Använd mindre YARN-behållare.** Minska storleken på varje YARN-behållare för att skapa fler behållare med samma mängd resurser.

![Prestanda för DataSjölagring Gen1](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Beroende på din arbetsbelastning kommer det alltid att finnas en minsta YARN-behållarestorlek som behövs. Om du väljer en för liten behållare kommer dina jobb att stöta på problem med på minne. Vanligtvis YARN behållare bör inte vara mindre än 1 GB. Det är vanligt att se 3 GB GARNbehållare. För vissa arbetsbelastningar kan du behöva större YARN-behållare.

**Öka kärnor per YARN-behållare.** Öka antalet kärnor som allokerats till varje behållare för att öka antalet parallella aktiviteter som körs i varje behållare. Detta fungerar för program som Spark, som kör flera uppgifter per behållare. För program som Hive som kör en enda tråd i varje behållare är det bättre att ha fler behållare i stället för fler kärnor per behållare.

### <a name="workload-layer"></a>Arbetsbelastningsskikt

**Använd alla tillgängliga behållare.** Ange att antalet aktiviteter ska vara lika med eller större än antalet tillgängliga behållare så att alla resurser används.

![Prestanda för DataSjölagring Gen1](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Misslyckade aktiviteter är kostsamma.** Om varje aktivitet har en stor mängd data att bearbeta, resulterar fel på en aktivitet i ett dyrt nytt försök. Därför är det bättre att skapa fler uppgifter, som var och en bearbetar en liten mängd data.

Utöver de allmänna riktlinjerna ovan har varje program olika parametrar tillgängliga för att ställa in för det specifika programmet. I tabellen nedan visas några av parametrarna och länkarna för att komma igång med prestandajustering för varje program.

| Arbetsbelastning               | Parameter för att ange aktiviteter                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark på HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Num-utförare</li><li>Executor-minne</li><li>Executor-kärnor</li></ul> |
| [Hive på HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size hive.tez.container.size hive.tez.container.size hive</li></ul>         |
| [MapReduce på HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.kartor</li><li>Mapreduce.reduce.memory Mapreduce.reduce.memory Mapreduce.reduce.memory Mapr</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm på HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Antal arbetsprocesser</li><li>Antal piput executor-instanser</li><li>Antal bultutn0-instanser </li><li>Antal pipaktiviteter</li><li>Antal bultuppgifter</li></ul>|

## <a name="see-also"></a>Se även

* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
