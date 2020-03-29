---
title: Migrera Azure HDInsight 3.6 Apache Storm till HDInsight 4.0 Apache Spark
description: Skillnaderna och migreringsflödet för att migrera Apache Storm-arbetsbelastningar till Spark Streaming eller Spark Structured Streaming.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157797"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrera Azure HDInsight 3.6 Apache Storm till HDInsight 4.0 Apache Spark

I det här dokumentet beskrivs hur du migrerar Apache Storm-arbetsbelastningar på HDInsight 3.6 till HDInsight 4.0. HDInsight 4.0 stöder inte apachestormsklustertypen och du måste migrera till en annan streamingdataplattform. Två lämpliga alternativ är Apache Spark Streaming och Spark Structured Streaming. Det här dokumentet beskriver skillnaderna mellan dessa plattformar och rekommenderar också ett arbetsflöde för migrerande Apache Storm-arbetsbelastningar.

## <a name="storm-migration-paths-in-hdinsight"></a>Stormmigreringsvägar i HDInsight

Om du vill migrera från Apache Storm på HDInsight 3.6 har du flera alternativ:

* Spark Streaming på HDInsight 4.0
* Spark strukturerad streaming på HDInsight 4.0
* Azure Stream Analytics

Det här dokumentet innehåller en guide för att migrera från Apache Storm till Spark Streaming och Spark Structured Streaming.

> [!div class="mx-imgBorder"]
> ![Migreringsväg för HDInsight Storm](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Jämförelse mellan Apache Storm och Spark Streaming, Spark Structured Streaming

Apache Storm kan ge olika nivåer av garanterad meddelandebehandling. Till exempel kan en grundläggande Storm-applikation garantera minst en gång bearbetning, och [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) kan garantera exakt en gång bearbetning. Spark Streaming och Spark Structured Streaming garanterar att alla indatahändelser bearbetas exakt en gång, även om ett nodfel inträffar. Storm har en modell som bearbetar varje enskild händelse, och du kan också använda Micro Batch-modellen med Trident. Spark Streaming och Spark Structured Streaming ger Micro-Batch-bearbetningsmodell.

|  |Storm |Spark-strömning | Gnista strukturerad strömning|
|---|---|---|---|
|**Garanti för händelsebehandling**|Minst en gång <br> Exakt en gång (Trident) |[Exakt en gång](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exakt en gång](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Bearbetningsmodell**|Realtid <br> Mikrosats (Trident) |Mikro parti |Mikro parti |
|**Stöd för händelsetid**|[Ja](https://storm.apache.org/releases/2.0.0/Windowing.html)|Inga|[Ja](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Språk**|Java, etc.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark streaming vs Spark strukturerad streaming

Spark Structured Streaming ersätter Spark Streaming (DStreams). Strukturerad strömning fortsätter att ta emot förbättringar och underhåll, medan DStreams endast är i underhållsläge. **Obs: behöver länkar för att betona denna punkt**. Strukturerad direktuppspelning har inte lika många funktioner som DStreams för de källor och sänkor som den stöder direkt ur lådan, så utvärdera dina krav för att välja lämpligt Spark-flödesbearbetningsalternativ.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Streamingbearbetning (single event) jämfört med micro-batch-bearbetning

Storm tillhandahåller en modell som bearbetar varje enskild händelse. Detta innebär att alla inkommande poster kommer att behandlas så snart de anländer. Spark Streaming-program måste vänta en bråkdel av en sekund för att samla in varje mikrobatch av händelser innan du skickar batchen på för bearbetning. Däremot bearbetar ett händelsedrivet program varje händelse omedelbart. Svarstiden för Spark Streaming är vanligtvis under några sekunder. Fördelarna med mikrobatsatsmetoden är effektivare databehandling och enklare aggregerade beräkningar.

> [!div class="mx-imgBorder"]
> ![streaming och bearbetning av mikro-batch](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Stormarkitektur och komponenter

Storm-topologier består av flera komponenter som är ordnade i en riktad acyklisk graf (DAG). Data flödar mellan komponenter i diagrammet. Varje komponent använder en eller flera dataströmmar och kan du generera en eller flera strömmar.

|Komponent |Beskrivning |
|---|---|
|Pip|För in data i en topologi. De sänder en eller flera strömmar till topologin.|
|Bult|Förbrukar strömmar som avges från pipar eller andra bultar. Bultar kan eventuellt sända strömmar till topologin. Bultar ansvarar för att skriva data till extern lagring, till exempel HDFS, Kafka eller HBase.|

> [!div class="mx-imgBorder"]
> ![interaktion mellan stormkomponenter](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm består av följande tre demoner, som håller Storm klustret fungerar.

|Daemon |Beskrivning |
|---|---|
|Nimbus|I likhet med Hadoop JobTracker är det ansvarigt för att distribuera kod runt klustret och tilldela uppgifter till datorer och övervaka fel.|
|Zookeeper|Används för klusterkoordination.|
|Chef|Lyssnar efter arbete som tilldelats dess maskin och startar och stoppar arbetsprocesser baserat på direktiv från Nimbus. Varje arbetsprocess kör en delmängd av en topologi. Användarens programlogik (Spouts och Bolt) körs här.|

> [!div class="mx-imgBorder"]
> ![nimbus, zookeeper och handledare demoner](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark Streaming arkitektur och komponenter

I följande steg sammanfattas hur komponenter fungerar tillsammans i Spark Streaming (DStreams) och Spark Structured Streaming:

* När Spark Streaming startas startar drivrutinen uppgiften i utföraren.
* Utföraren tar emot en ström från en strömmande datakälla.
* När utföraren tar emot dataströmmar delar den upp strömmen i block och behåller dem i minnet.
* Datablock replikeras till andra utförare.
* Bearbetade data lagras sedan i måldatalagret.

> [!div class="mx-imgBorder"]
> ![tända strömningsväg till utdata](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Arbetsflöde för Spark Streaming (DStream)

När varje batchintervall förflutit produceras en ny RDD som innehåller alla data från det intervallet. De kontinuerliga uppsättningarna rdds samlas in i en DStream. Om batchintervallet till exempel är en sekund långt avger din DStream en batch varje sekund som innehåller en RDD som innehåller alla data som intas under den sekunden. Vid bearbetning av DStream visas temperaturhändelsen i en av dessa batchar. Ett Spark Streaming-program bearbetar de batchar som innehåller händelserna och slutligen fungerar på data som lagras i varje RDD.

> [!div class="mx-imgBorder"]
> ![spark streaming bearbetning batchar](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Mer information om de olika omvandlingar som är tillgängliga med Spark Streaming finns i [Omvandlingar på DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Gnista strukturerad streaming

Spark Structured Streaming representerar en dataström som en tabell som är obunden på djupet. Tabellen fortsätter att växa när nya data anländer. Den här indatatabellen bearbetas kontinuerligt av en tidskrävande fråga och resultaten skickas till en utdatatabell.

I Strukturerad direktuppspelning kommer data till systemet och matas omedelbart in i en indatatabell. Du skriver frågor (med hjälp av DataFrame och Dataset API:er) som utför åtgärder mot den här indatatabellen.

Frågeutdata ger en *resultattabell*som innehåller resultatet av frågan. Du kan hämta data från resultattabellen för ett externt datalager, till exempel en relationsdatabas.

Tidpunkten för när data bearbetas från indatatabellen styrs av utlösarintervallet. Som standard är utlösarintervallet noll, så strukturerad strömning försöker bearbeta data så fort den anländer. I praktiken innebär detta att så snart strukturerad direktuppspelning är klar med bearbetningen av körningen av den föregående frågan startar en annan bearbetning köras mot nyare mottagna data. Du kan konfigurera utlösaren så att den körs med ett intervall, så att strömmande data bearbetas i tidsbaserade batchar.

> [!div class="mx-imgBorder"]
> ![behandling av data i strukturerad strömning](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![programmeringsmodell för strukturerad strömning](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Allmänt migrationsflöde

Det rekommenderade migreringsflödet från Storm till Spark förutsätter följande inledande arkitektur:

* Kafka används som strömmande datakälla
* Kafka och Storm distribueras i samma virtuella nätverk
* Data som bearbetas av Storm skrivs till en datamottagare, till exempel Azure Storage eller Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagram över förmodad aktuell miljö](./media/migrate-storm-to-spark/presumed-current-environment.png)

Så här migrerar du programmet från Storm till något av Spark-direktuppspelnings-API:erna:

1. **Distribuera ett nytt kluster.** Distribuera ett nytt HDInsight 4.0 Spark-kluster i samma virtuella nätverk och distribuera ditt Spark Streaming- eller Spark Structured Streaming-program på det och testa det noggrant.

    > [!div class="mx-imgBorder"]
    > ![ny gnistdistribution i HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Sluta konsumera på det gamla Storm-klustret.** I den befintliga Stormen slutar du att använda data från datakällan för direktuppspelning och väntar på att data ska slutföra skrivandet till målmottagaren.

    > [!div class="mx-imgBorder"]
    > ![sluta konsumera i det aktuella klustret](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Börja konsumera på det nya Spark-klustret.** Börja strömma data från ett nyligen distribuerat HDInsight 4.0 Spark-kluster. Vid denna tid, processen tas över genom att konsumera från den senaste Kafka offset.

    > [!div class="mx-imgBorder"]
    > ![börja konsumera på nytt kluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Ta bort det gamla klustret efter behov.** När växeln är klar och fungerar korrekt tar du bort det gamla HDInsight 3.6 Storm-klustret efter behov.

    > [!div class="mx-imgBorder"]
    > ![ta bort gamla HDInsight-kluster efter behov](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Storm, Spark Streaming och Spark Structured Streaming finns i följande dokument:

* [Översikt över Apache Spark Streaming](../spark/apache-spark-streaming-overview.md)
* [Översikt över Apache Spark Structured Streaming](../spark/apache-spark-structured-streaming-overview.md)