---
title: Azure Data Lake Storage Gen2 prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Storage Gen2 prestandajustering riktlinjer
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: f175360586428b57d1ff10e3529ae9e3283399e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60804718"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>Justering av Azure Data Lake Storage Gen2 för prestanda

Azure Data Lake Storage Gen2 har stöd för stora dataflöden för i/o-intensiva analys- och förflyttning.  Data Lake Storage Gen2 är med alla tillgängliga dataflöde – mängden data som kan läsas eller skrivas per sekund – viktigt att uppnå optimala prestanda.  Detta uppnås genom att utföra så många läsningar och skrivningar parallellt som möjligt.

![Data Lake Storage Gen2 prestanda](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 kan skala för att tillhandahålla nödvändiga dataflödet för alla analytics-scenario. Som standard ger ett Data Lake Storage Gen2 konto automatiskt tillräckligt dataflöde för att uppfylla behoven hos väldigt många användningsområden. I de fall där kunderna stöter på Standardgränsen, Gen2 för Data Lake Storage-kontot kan konfigureras för att ge större dataflöde genom att kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Datainhämtning

När du samlar in data från ett källsystem till Data Lake Storage Gen2, är det viktigt att tänka på källan maskinvara, nätverksmaskinvara för källa och nätverksanslutningen till Data Lake Storage Gen2 att flaskhalsen.  

![Data Lake Storage Gen2 prestanda](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Det är viktigt att se till att dataförflyttning inte påverkas av dessa faktorer.

### <a name="source-hardware"></a>Källkod

Om du använder lokala datorer eller virtuella datorer i Azure, bör du noggrant välja rätt maskinvara. För källa Disk maskinvara, föredrar SSD: er till hårddiskar och välj disk maskinvara med snabbare axlar. Använd snabbaste möjliga nätverkskort för nätverksmaskinvara vilket källa.  På Azure rekommenderar vi D14 virtuella datorer i Azure som har korrekt kraftfulla disk- och nätverksmaskinvara.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Nätverksanslutningen till Data Lake Storage Gen2

Nätverksanslutningen mellan dina källdata och Data Lake Storage Gen2 kan ibland vara flaskhalsen. När dina källdata är på plats kan du använda en dedikerad länk med [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Om dina källdata är i Azure och prestanda som är bäst när data är i samma Azure-region som kontot Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurera verktyg för datainhämtning för maximal parallellisering

När du har åtgärdat maskinvaran som källa och network connectivity flaskhalsar ovan, är du redo att konfigurera verktyg för datainhämtning. I följande tabell sammanfattas Nyckelinställningar för flera populära inmatning verktyg och ger djupgående prestandajustering artiklar för dessa.  Mer information om vilket verktyg du använder för ditt scenario finns i den här [artikeln](data-lake-storage-data-scenarios.md).

| Verktyg               | Inställningar     | Mer information                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [Länk](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Länk](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Länk](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Strukturera datauppsättningen

När data lagras i Data Lake Storage Gen2 filstorlek, påverka antal filer och mappstruktur prestandan.  I följande avsnitt beskrivs bästa praxis inom följande områden.  

### <a name="file-size"></a>Filstorlek

Analytics-motorer, till exempel HDInsight och Azure Data Lake Analytics har vanligtvis en kostnader per fil. Om du sparar dina data så många små filer, kan det påverka prestanda negativt. I allmänhet organisera dina data i större storlekar filer för bättre prestanda (256MB till 100GB i storlek). Vissa motorer och program kan ha problem med att effektivt bearbeta filer som är större än 100GB i storlek.

Ibland har datapipelines begränsad kontroll över rådata som har mycket små filer. Vi rekommenderar att ha en ”tillagning” process som genererar större filer ska användas för applikationer nedströms.

### <a name="organizing-time-series-data-in-folders"></a>Ordna time series-data i mappar

Partition rensningen av time series-data kan hjälpa några frågor som läser endast en delmängd av data, vilket förbättrar prestanda för Hive-arbetsbelastningar.    

Dessa pipelines som matar in time series-data, placera ofta sina filer med en mycket strukturerade namngivningen för filer och mappar. Nedan visas ett mycket vanligt exempel som vi kan se för data som är strukturerad efter datum:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Observera att datetime-information visas både som mappar och i filnamnet.

För datum och tid är följande ett vanligt mönster

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Återigen åtgärd som du utför med mappen och filen organisation bör Optimera för större filstorlekar och ett rimligt antal filer i varje mapp.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimera i/o-intensiva jobb på Hadoop och Spark-arbetsbelastningar på HDInsight

Jobb indelas i följande tre kategorier:

* **Processorintensiv.**  Dessa jobb har lång beräkning gånger med minimal i/o-gånger.  Exempel är machine learning och naturlig språkbearbetning jobb.  
* **Minnesintensiva.**  Dessa jobb använder mycket minne.  Exempel: PageRank och analys i realtid jobb.  
* **I/o-intensiva.**  Dessa jobb tillbringa större delen av tiden gör i/o.  Ett vanligt exempel är ett kopieringsjobb som endast Läs- och skrivåtgärder.  Andra exempel är dataförberedelsejobb som läser stora mängder data, utför vissa Dataomvandling och skriver dessa data tillbaka till arkivet.  

Följande riktlinjer gäller endast för i/o-intensiva jobb.

## <a name="general-considerations"></a>Allmänna överväganden

Du kan ha ett jobb som läser eller skriver så mycket som 100MB i en enda åtgärd, men en buffert med den storleken som kan påverka datorns prestanda.
Se till att storleken på en i/o-åtgärd mellan 4 och 16MB för att optimera prestanda.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Allmänna överväganden för ett HDInsight-kluster

* **HDInsight-versioner.** Använd den senaste versionen av HDInsight för bästa prestanda.
* **Regioner.** Placera Gen2 för Data Lake Storage-konto i samma region som HDInsight-klustret.  

Ett HDInsight-kluster består av två huvudnoder och vissa arbetsnoder. Varje arbetsnod innehåller ett visst antal kärnor och minne, vilket bestäms av VM-typen.  När du kör ett jobb är YARN resource-negotiator som allokerar det tillgängliga minnet och kärnor för att skapa behållare.  Varje behållare körs de uppgifter som krävs för att slutföra jobbet.  Behållare körs parallellt att bearbeta uppgifter snabbt. Därför bättre prestanda genom att köra så många parallella behållare som möjligt.

Det finns tre lager i ett HDInsight-kluster som kan anpassas för att öka antalet behållare och använda alla tillgängliga dataflöde.  

* **Fysiska lagret**
* **YARN-lager**
* **Arbetsbelastningen lager**

### <a name="physical-layer"></a>Fysiska lagret

**Kör kluster med flera noder och/eller större storlek virtuella datorer.**  Ett större kluster kan du köra fler YARN-behållare som du ser i bilden nedan.

![Data Lake Storage Gen2 prestanda](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Använda virtuella datorer med mer bandbredd i nätverket.**  Mängden bandbredd i nätverket kan utgöra en flaskhals om det finns mindre bandbredd i nätverket än Data Lake Storage Gen2 dataflöde.  Olika virtuella datorer har varierande nätverksbandbredd storlekar.  Välj en VM-typ som har den största möjliga nätverksbandbredden.

### <a name="yarn-layer"></a>YARN-lager

**Använd mindre YARN-behållare.**  Minska storleken på varje YARN-behållare för att skapa fler behållare med samma mängd resurser.

![Data Lake Storage Gen2 prestanda](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Beroende på arbetsbelastningen, kommer alltid att en minsta storlek av YARN-behållare som krävs. Om du väljer för liten för en behållare kommer körs dina jobb i minnet är slut problem. Vanligtvis får YARN-behållare inte vara större än 1GB. Det är vanligt att 3GB YARN-behållare. För vissa arbetsbelastningar kanske du behöver större YARN-behållare.  

**Öka kärnor per YARN-behållare.**  Öka antalet kärnor som tilldelas varje behållare för att öka antalet parallella aktiviteter som körs i varje behållare.  Detta fungerar för program som Spark som kör flera aktiviteter per behållare.  För program som Hive som kör en tråd i varje behållare, är det bättre att ha fler behållare i stället för fler kärnor per behållare.

### <a name="workload-layer"></a>Arbetsbelastningen lager

**Använd alla tillgängliga behållare.**  Ange antalet aktiviteter som ska vara lika med eller större än antalet tillgängliga behållare så att alla resurser som används.

![Data Lake Storage Gen2 prestanda](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Uppgifter som misslyckats är dyra.** Om varje aktivitet har en stor mängd data att bearbeta, resulterar fel på en aktivitet i en dyr försök igen.  Därför är det bättre att skapa fler aktiviteter, som bearbetar en liten mängd data.

Förutom de allmänna riktlinjerna ovan har olika parametrar som är tillgängliga för justering för det specifika programmet i varje program. I tabellen nedan visas några av de parametrar och länkar till Kom igång med prestandajustering för varje program.

| Arbetsbelastning | Parametern för att ställa in uppgifter |
|----------|------------------------|
| [Spark i HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>NUM executors</li><li>Executor-minne</li><li>Executor-kärnor</li></ul> |
| [Hive på HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce på HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.Map.Memory</li><li>Mapreduce.Job.Maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm på HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Antal arbetsprocesser</li><li>Antal spout executor instanser</li><li>Antal bult executor instanser </li><li>Antal spout uppgifter</li><li>Antal bult uppgifter</li></ul>|

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
