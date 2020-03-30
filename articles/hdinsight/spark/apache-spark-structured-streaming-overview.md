---
title: Spark strukturerad streaming i Azure HDInsight
description: Så här använder du Spark Structured Streaming-program på HDInsight Spark-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 19cfd5d8ed4100048c270fb41e5e54a920c61516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548844"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Översikt över Apache Spark Structured Streaming

[Apache gnista](https://spark.apache.org/) Med strukturerad direktuppspelning kan du implementera skalbara, höggenomströmningsbara, feltoleranta program för bearbetning av dataströmmar. Strukturerad strömning bygger på Spark SQL-motorn och förbättrar konstruerarna från Spark SQL Data Frames och Datasets så att du kan skriva strömmande frågor på samma sätt som du skulle skriva batchfrågor.  

Strukturerade strömmande program körs på HDInsight Spark-kluster och ansluter till strömmande data från [Apache Kafka](https://kafka.apache.org/), en TCP-socket (för felsökningsändamål), Azure Storage eller Azure Data Lake Storage. De två sistnämnda alternativen, som är beroende av externa lagringstjänster, gör att du kan titta efter nya filer som lagts till i lagring och bearbeta deras innehåll som om de strömmades.

Strukturerad direktuppspelning skapar en tidskrävande fråga där du tillämpar åtgärder på indata, till exempel val, projektion, aggregering, fönsterning och anslutning till dataramen för direktuppspelning med referensdataramar. Därefter skriver du ut resultaten till fillagring (Azure Storage Blobbar eller Data Lake Storage) eller till alla datalager med hjälp av anpassad kod (till exempel SQL Database eller Power BI). Strukturerad direktuppspelning ger också utdata till konsolen för felsökning lokalt och till en minnestabell så att du kan se data som genereras för felsökning i HDInsight.

![Stream bearbetning med HDInsight och Spark strukturerad streaming](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> Spark Structured Streaming ersätter Spark Streaming (DStreams). Framöver kommer Structured Streaming att få förbättringar och underhåll, medan DStreams endast kommer att vara i underhållsläge. Strukturerad direktuppspelning är för närvarande inte lika funktionsuppror som DStreams för de källor och sänkor som den stöder direkt ur rutan, så utvärdera dina krav för att välja lämpligt Spark-flödesbearbetningsalternativ.

## <a name="streams-as-tables"></a>Strömmar som tabeller

Spark Structured Streaming representerar en dataström som en tabell som är obegränsad på djupet, det vill än att tabellen fortsätter att växa när nya data anländer. Den här *indatatabellen* bearbetas kontinuerligt av en tidskrävande fråga och resultaten skickas till en *utdatatabell:*

![Strukturerat streamingkoncept](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

I Strukturerad direktuppspelning kommer data till systemet och matas omedelbart in i en indatatabell. Du skriver frågor (med hjälp av DataFrame och Dataset API:er) som utför åtgärder mot den här indatatabellen. Frågeutdata ger en annan tabell, *resultattabellen*. Resultattabellen innehåller resultatet av frågan, från vilken du ritar data för ett externt datalager, till exempel en relationsdatabas. Tidpunkten för när data bearbetas från indatatabellen styrs av *utlösningsintervallet*. Som standard är utlösarintervallet noll, så strukturerad strömning försöker bearbeta data så fort den anländer. I praktiken innebär detta att så snart strukturerad direktuppspelning är klar med bearbetningen av körningen av den föregående frågan startar en annan bearbetning köras mot nyare mottagna data. Du kan konfigurera utlösaren så att den körs med ett intervall, så att strömmande data bearbetas i tidsbaserade batchar.

Data i resultattabellerna kan bara innehålla de data som är nya sedan den senaste gången frågan bearbetades *(tilläggsläge),* eller så kan tabellen uppdateras varje gång det finns nya data så att tabellen innehåller alla utdata sedan strömningsfrågan började (*fullständigt läge*).

### <a name="append-mode"></a>Lägg till läge

I tilläggsläge finns bara de rader som lagts till i resultattabellen sedan den senaste frågekörningen finns i resultattabellen och skrivs till extern lagring. Den enklaste frågan kopierar till exempel bara alla data från indatatabellen till resultattabellen oförändrad. Varje gång ett utlösarintervall förflyter bearbetas de nya data och raderna som representerar de nya data som visas i resultattabellen.

Tänk dig ett scenario där du bearbetar telemetri från temperatursensorer, till exempel en termostat. Anta att den första utlösaren bearbetade en händelse vid 00:01 för enhet 1 med en temperaturavläsning på 95 grader. I den första utlösaren av frågan visas bara raden med tiden 00:01 i resultattabellen. Vid 00:02 när en annan händelse anländer är den enda nya raden raden med tiden 00:02 och resultattabellen innehåller därför bara den raden.

![Läget För tillägg för strukturerad strömning](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

När du använder tilläggsläge använder frågan prognoser (markerar de kolumner som den bryr sig om), filtrering (endast plockningsrader som matchar vissa villkor) eller sammanfogar (utökar data med data från en statisk uppslagstabell). Append-läget gör det enkelt att bara skicka relevanta nya data påpekar till extern lagring.

### <a name="complete-mode"></a>Fullständigt läge

Tänk på samma scenario, den här gången med hjälp av komplett läge. I fullständigt läge uppdateras hela utdatatabellen på varje utlösare så att tabellen innehåller data inte bara från den senaste utlösarkörningen, utan från alla körningar. Du kan använda fullständigt läge för att kopiera data oförändrade från indatatabellen till resultattabellen. På varje utlöst körning visas de nya resultatraderna tillsammans med alla föregående rader. Tabellen utdataresultat kommer att lagra alla data som samlats in sedan frågan började, och du skulle så småningom få på minne. Fullständigt läge är avsett att användas med aggregerade frågor som sammanfattar inkommande data på något sätt, så på varje utlösare uppdateras resultattabellen med en ny sammanfattning.

Anta att det hittills finns fem sekunders data som redan har bearbetats, och det är dags att bearbeta data för den sjätte sekunden. Indatatabellen har händelser för tiden 00:01 och tid 00:03. Målet med den här exempelfrågan är att ge medeltemperaturen för enheten var femte sekund. Implementeringen av den här frågan tillämpar en mängd som tar alla värden som faller inom varje 5-sekundersfönster, genomsnitt temperaturen, och producerar en rad för den genomsnittliga temperaturen över det intervallet. I slutet av det första 5-sekundersfönstret finns två tupplar: (00:01, 1, 95) och (00:03, 1, 98). Så för fönstret 00:00-00:05 ger aggregeringen en tuppel med medeltemperaturen på 96,5 grader. I nästa 5-sekundersfönster finns det bara en datapunkt vid tiden 00:06, så den resulterande medeltemperaturen är 98 grader. Vid tiden 00:10, med fullständigt läge, har resultattabellen raderna för båda fönstren 00:00-00:05 och 00:05-00:10 eftersom frågan matar ut alla aggregerade rader, inte bara de nya. Därför fortsätter resultattabellen att växa i takt med att nya fönster läggs till.

![Komplett läge för strukturerad strömning](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Alla frågor som använder fullständigt läge gör att tabellen växer utan gränser.  Tänk i föregående exempel på att i stället för att beräkna temperaturen efter tidsfönster i genomsnitt beräknas den i stället efter enhets-ID. Resultattabellen innehåller ett fast antal rader (en per enhet) med medeltemperaturen för enheten över alla datapunkter som tas emot från den enheten. När nya temperaturer tas emot uppdateras resultattabellen så att medelvärdena i tabellen alltid är aktuella.

## <a name="components-of-a-spark-structured-streaming-application"></a>Komponenter i ett Spark Structured Streaming-program

En enkel exempelfråga kan sammanfatta temperaturavläsningarna med timslånga fönster. I det här fallet lagras data i JSON-filer i Azure Storage (bifogas som standardlagring för HDInsight-klustret):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Dessa JSON-filer lagras `temps` i undermappen under HDInsight-klustrets behållare.

### <a name="define-the-input-source"></a>Definiera indatakällan

Konfigurera först en DataFrame som beskriver datakällan och eventuella inställningar som krävs av den källan. Det här exemplet hämtar från JSON-filerna i Azure Storage och tillämpar ett schema på dem vid lästillfället.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Använda frågan

Använd sedan en fråga som innehåller de önskade åtgärderna mot Dataframe för direktuppspelning. I det här fallet grupperar en aggregering alla rader i 1-timmarsfönster och beräknar sedan lägsta, genomsnittliga och högsta temperaturer i det 1-timmarsfönstret.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definiera utdatamottagaren

Definiera sedan målet för de rader som läggs till i resultattabellen inom varje utlösarintervall. Det här exemplet matar bara ut alla `temps` rader till en minnestabell som du senare kan fråga med SparkSQL. Komplett utmatningsläge säkerställer att alla rader för alla fönster matas ut varje gång.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Starta frågan

Starta direktuppspelningsfrågan och kör tills en avslutningssignal tas emot.

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Visa resultatet

Medan frågan körs kan du i samma SparkSession köra en SparkSQL-fråga mot `temps` tabellen där frågeresultaten lagras.

    select * from temps

Den här frågan ger resultat som liknar följande:

| fönster |  min(temp) | avg(temp) | max(temp) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Mer information om Spark Structured Stream API, tillsammans med indatakällor, åtgärder och utdatamottagare som stöds finns i [Apache Spark Structured Streaming Programming Guide](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Kontrollpunkter och skriv-framåt loggar

För att leverera återhämtning och feltolerans förlitar sig structured streaming på *kontrollpunkter* för att säkerställa att dataströmbearbetning kan fortsätta oavbrutet, även med nodfel. I HDInsight skapar Spark kontrollpunkter till hållbar lagring, antingen Azure Storage eller Data Lake Storage. Dessa kontrollpunkter lagrar förloppsinformation om streamingfrågan. Dessutom använder Structured Streaming en *write-ahead log* (WAL). WAL fångar in intas data som har tagits emot men ännu inte bearbetats av en fråga. Om ett fel inträffar och bearbetningen startas om från WAL går alla händelser som tas emot från källan inte förlorade.

## <a name="deploying-spark-streaming-applications"></a>Distribuera Spark Streaming-program

Du skapar vanligtvis ett Spark Streaming-program lokalt i en JAR-fil och distribuerar det sedan till Spark på HDInsight genom att kopiera JAR-filen till standardlagringen som är ansluten till ditt HDInsight-kluster. Du kan starta programmet med [Apache Livy](https://livy.incubator.apache.org/) REST API:er som är tillgängliga från klustret med hjälp av en POST-åtgärd. Brödtexten i POST innehåller ett JSON-dokument som ger sökvägen till jar:n, namnet på den klass vars huvudmetod definierar och kör direktuppspelningsprogrammet och eventuellt resurskraven för jobbet (till exempel antalet utförare, minne och kärnor) och alla konfigurationsinställningar som programkoden kräver.

![Distribuera ett Spark Streaming-program](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Status för alla program kan också kontrolleras med en GET-begäran mot en LIVY-slutpunkt. Slutligen kan du avsluta ett program som körs genom att utfärda en DELETE-begäran mot LIVY-slutpunkten. Mer information om LIVY API finns i [Fjärrjobb med Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmeringsguide för Apache Spark-strukturerad strömning](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Starta Apache Spark jobb på distans med Apache LIVY](apache-spark-livy-rest-interface.md)
