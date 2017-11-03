---
title: "Riktlinjer för prestandajustering Azure Data Lake Store | Microsoft Docs"
description: "Riktlinjer för prestandajustering Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 900447ab931f15e4d27aedd525eba7881ba813b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tuning-azure-data-lake-store-for-performance"></a>Justera Azure Data Lake Store för prestanda

Data Lake Store stöder hög genomströmning för i/o-intensiva analyser och dataförflyttning.  Azure Data Lake Store är med hjälp av alla tillgängliga genomflödet – mängden data som kan läsas eller skrivas per sekund – viktigt att få bästa möjliga prestanda.  Detta uppnås genom att utföra så många läsningar och skrivningar parallellt som möjligt.

![Data Lake Store-prestanda](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Azure Data Lake Store kan skalas för att tillhandahålla nödvändiga genomströmning för alla analytics scenario. Ett Azure Data Lake Store-konto ger som standard automatiskt tillräckligt med dataflöde för att uppfylla behoven i väldigt många användningsområden. I de fall där kunder stöter på Standardgränsen, kan ADLS-konto konfigureras för att ge mer genomströmning genom att kontakta Microsoft support.

## <a name="data-ingestion"></a>Datainhämtning

När du vill föra in data från ett källsystem till ADLS, är det viktigt att tänka på att källan maskinvara, nätverksmaskinvara för källa och nätverksanslutning till ADLS kan vara orsaken till detta.  

![Data Lake Store-prestanda](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Det är viktigt att säkerställa att data flyttas inte påverkas av dessa faktorer.

### <a name="source-hardware"></a>Käll-maskinvara

Om du använder lokala datorer eller virtuella datorer i Azure, bör du noggrant välja lämplig maskinvara. Föredrar SSD hårddiskar för källa maskinvara, och Välj maskinvara med snabbare axlar. Använd snabbaste nätverkskort möjliga för källa nätverksmaskinvara.  På Azure rekommenderar vi Azure D14 virtuella datorer som har korrekt kraftfulla disk- och nätverksmaskinvara.

### <a name="network-connectivity-to-azure-data-lake-store"></a>Nätverksanslutningen till Azure Data Lake Store

Nätverksanslutningen mellan datakällan och Azure Data Lake store kan ibland vara flaskhals. När datakällan är lokalt, Överväg att använda en dedikerad länk med [Azure ExpressRoute](https://azure.microsoft.com/en-us/services/expressroute/) . Om datakällan är i Azure, prestanda som är bäst när data är i samma Azure-region som Data Lake Store.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurera Datapåfyllning verktyg för maximal parallellisering

När du har åtgärdat maskinvaran som källa och network connectivity flaskhalsar ovan, är du redo att konfigurera införandet-verktyg. Följande tabell sammanfattar viktiga inställningar för flera populära införandet verktyg och ger detaljerad prestandajustering artiklar för dem.  Mer information om vilka verktyg som ska användas för ditt scenario finns [artikel](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-data-scenarios).

| Verktyget               | Inställningar     | Mer information                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount ConcurrentFileCount |  [Länk](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-powershell#performance-guidance-while-using-powershell)   |
| AdlCopy    | Azure Data Lake Analytics-enheter  |   [Länk](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper)   | [Länk](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Länk](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.block.size -m (mapper)    |   [Länk](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Struktur datauppsättningen

När data lagras i Data Lake Store, filstorlek, antal filer och mappstrukturer kan påverka prestanda.  I följande avsnitt beskrivs bästa praxis inom följande områden.  

### <a name="file-size"></a>Filstorlek

Analytics-motorer, till exempel HDInsight och Azure Data Lake Analytics har vanligtvis en kostnader per fil.  Om du lagrar data så många små filer kan detta påverka prestanda negativt.  

I allmänhet ordna dina data i större storlek filer för bättre prestanda.  Som en tumregel ordna datauppsättningar i filer på 256MB eller större. I vissa fall, till exempel bilder och binära data går inte att behandla dem. parallellt.  I dessa fall rekommenderas att hålla enskilda filer under 2GB.

Ibland begränsad data pipelines kontroll över rådata som har mycket små filer.  Vi rekommenderar att du har en ”tillagning” process som genererar större filer ska användas för underordnade program.  

### <a name="organizing-time-series-data-in-folders"></a>Ordna Time Series-data i mappar

Partitionen rensas time series-data kan hjälpa några frågor läsa en delmängd av de uppgifter som förbättrar prestanda för Hive och ADLA arbetsbelastningar.    

Dessa pipelines som mata in time series-data, placera ofta sina filer med en mycket strukturerat namngivning av filer och mappar. Nedan visas ett vanligt exempel som visas för data som är strukturerad efter datum:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Observera att datetime information visas både som mappar och i filnamnet.

För datum och tid följer vanliga mönstret

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Återigen val som du gör med mappen och filen organisation ska optimeras för större filstorlek och ett rimligt antal filer i varje mapp.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimera beräkningsintensiva i/o-jobb på Hadoop och Spark arbetsbelastningar i HDInsight

Jobb indelas i följande tre kategorier:

* **CPU-intensiv.**  Dessa jobb har lång beräkning gånger med minimal i/o-tider.  Exempel är machine learning och naturligt språk bearbeta jobb.  
* **Minnesintensiva.**  Dessa jobb använder mycket minne.  Exempel: PageRank och analys i realtid jobb.  
* **I/o-intensiva.**  Dessa jobb tillbringar större delen av tiden gör i/o.  Ett vanligt exempel är en kopieringsjobbet som endast Läs- och skrivåtgärder.  Andra exempel är data förberedelse jobb som läsa stora mängder data, utför vissa Dataomvandling och skriver sedan data tillbaka till arkivet.  

Följande riktlinjer gäller endast i/o-intensiva jobb.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Allmänna överväganden för ett HDInsight-kluster

* **HDInsight-versioner.** Använd den senaste versionen av HDInsight för bästa prestanda.
* **Regioner.** Placera Data Lake Store i samma region som HDInsight-klustret.  

Ett HDInsight-kluster består av två huvudnoderna och vissa arbetsnoder. Varje arbetsnod innehåller ett specifikt antal kärnor och minne som bestäms av VM-typen.  När du kör ett jobb är YARN resurs förhandlare som allokerar tillgängligt minne och kärnor för att skapa behållare.  Varje behållare körs de uppgifter som krävs för att slutföra jobbet.  Behållare körs parallellt för att utföra aktiviteter snabbt. Därför bättre prestanda genom att köra så många parallella behållare som möjligt.

Det finns tre lager i ett HDInsight-kluster som kan anpassas för att öka antalet behållare och använda alla tillgängliga genomflöde.  

* **Fysiska lagret**
* **YARN-lager**
* **Arbetsbelastningen lager**

### <a name="physical-layer"></a>Fysiska lagret

**Kör kluster med flera noder och/eller större storlek virtuella datorer.**  Större gör att du kan köra fler YARN-behållare som visas i bilden nedan.

![Data Lake Store-prestanda](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Använda virtuella datorer med mer bandbredd i nätverket.**  Mängden nätverksbandbredd kan utgöra en flaskhals om det finns mindre bandbredd än Data Lake Store dataflöde.  Olika virtuella datorer har olika storlekar för nätverksbandbredd.  Välj en VM-typ som har den största möjliga nätverksbandbredden.

### <a name="yarn-layer"></a>YARN-lager

**Använda mindre YARN-behållare.**  Minska storleken på varje YARN-behållare för att skapa fler behållare med samma grad av resurser.

![Data Lake Store-prestanda](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Beroende på din arbetsbelastning, kommer det alltid att en YARN behållare minimistorleken som krävs. Om du väljer för liten för en behållare körs jobb i minnet är slut problem. Vanligtvis vara YARN behållare mindre än 1GB. Det är vanligt att visa 3GB YARN behållare. Du kanske måste större YARN-behållare för vissa arbetsbelastningar.  

**Öka kärnor per YARN-behållare.**  Öka antalet kärnor tilldelas varje behållare för att öka antalet parallella aktiviteter som körs i varje behållare.  Detta fungerar för program som Spark som kör flera uppgifter per behållare.  För program som Hive som kör en tråd i varje behållare, är det bättre att ha fler behållare i stället för flera kärnor per behållare.   

### <a name="workload-layer"></a>Arbetsbelastningen lager

**Använd alla tillgängliga behållare.**  Ange antalet aktiviteter som ska vara lika med eller större än antalet tillgängliga behållare så att alla resurser används.

![Data Lake Store-prestanda](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Misslyckade aktiviteter är kostsamma.** Om varje aktivitet har en stor mängd data att bearbeta, resulterar fel på en aktivitet i en dyr försök igen.  Därför är det bättre att skapa fler aktiviteter, som bearbetar en liten mängd data.

Förutom de allmänna riktlinjerna ovan har olika parametrar som är tillgängliga för att finjustera för det specifika programmet i varje program. I tabellen nedan visas några av de parametrar och länkar till komma igång med prestandajustering för varje program.

| Arbetsbelastning               | Att ange uppgifter                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark i HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Antal executors</li><li>Utföraren-minne</li><li>Utföraren kärnor</li></ul> |
| [Hive i HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce på HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.Map.Memory</li><li>Mapreduce.Job.Maps</li><li>Mapreduce.Reduce.Memory</li><li>Mapreduce.Job.reduces</li></ul> |
| [Storm på HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Antalet arbetsprocesser</li><li>Antalet instanser för kanal utförare</li><li>Antalet instanser av bulten utförare </li><li>Antalet aktiviteter som kanal</li><li>Antal bult uppgifter</li></ul>|

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Store](data-lake-store-overview.md)
* [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
