---
title: Apache Spark Structured Streaming i Azure HDInsight
description: Hur du använder Spark Structured Streaming program på HDInsight Spark-kluster.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
orgin.date: 02/05/2018
ms.date: 04/01/2019
ms.author: v-yiso
ms.openlocfilehash: 0e9d87e5b344b7091a2a0cf41d6f7fa3484dfcf3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098583"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Översikt över Apache Spark Structured Streaming

[Apache Spark](https://spark.apache.org/) Structured Streaming gör det möjligt att implementera skalbara, högt dataflöde, feltoleranta program för bearbetning av dataströmmar. Strukturerad direktuppspelning bygger på Spark SQL-motor och förbättrat konstruktioner från Spark SQL-dataramar och datauppsättningar så du kan skriva streaming frågar på samma sätt som du skulle skriva frågor för batch.  

Strukturerad direktuppspelning program körs i HDInsight Spark-kluster och Anslut till strömmande data från [Apache Kafka](https://kafka.apache.org/), TCP-socket (för felsökning), Azure Storage eller Azure Data Lake Storage. Dessa två alternativ, som förlitar sig på externa lagringstjänster, kan du bevaka nya filer som läggs till i lagring och bearbeta deras innehåll som om de har strömmas. 

Strukturerad direktuppspelning skapar en tidskrävande fråga som du kan använda åtgärder för inkommande data, till exempel val, projektion, aggregering, fönsterhantering och koppla strömmande DataFrame med referensen dataramar. Nu ska du matar ut resultaten till file storage (Azure Storage-Blobbar eller Data Lake Storage) eller till alla datalager genom att använda anpassad kod (till exempel SQL-databas eller Power BI). Strukturerad direktuppspelning ger också utdata till konsolen för att felsöka lokalt och i en InMemory-tabell så att du kan se de data som genereras för felsökning i HDInsight. 

![Stream bearbetning med HDInsight och Apache Spark Structured Streaming ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> Spark Structured Streaming ersätter Spark Streaming (DStreams). Framöver kommer får Structured Streaming förbättringar och underhåll, medan DStreams ska vara i underhållsläge endast. Strukturerad direktuppspelning är för närvarande inte som funktionen komplettering som DStreams för källorna och egenskaperna att den stöder direkt, så utvärdera dina krav och välja lämplig Spark stream Bearbetningsalternativ. 

## <a name="streams-as-tables"></a>Strömmar som tabeller

Spark Structured Streaming representerar en dataström som en tabell som är obegränsade i detalj, det vill säga tabellen fortsätter att växa när nya data tas emot. Detta *indatatabellen* kontinuerligt bearbetas av en tidskrävande fråga och resultatet skickas till en *utdatatabellen*:

![Strukturerad direktuppspelning begrepp](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

I Structured Streaming data anländer till systemet och omedelbart matas in i en indata-tabellen. Du kan skriva frågor (med den dataram och Dataset APIs) som utför åtgärder mot den här indatatabellen. Frågeresultatet visas ger en annan tabell i *resultattabellen*. Resultattabellen innehåller resultatet av din fråga, från vilken du rita data för en extern datalager, en relationsdatabas. Tidsinställningen för när data har bearbetats från indatatabellen styrs av den *utlösaren intervall*. Som standard är vid utlösaren noll, så Structured Streaming försöker bearbeta data när de anländer. I praktiken är det innebär att när Structured Streaming görs en annan bearbetningen kör mot alla nyligen mottagna data startar bearbetning av körning av den föregående frågan. Du kan konfigurera att utlösaren ska köras vid ett intervall, så att strömmande data bearbetas batchvis tidsbaserade. 

Data i resultatet tabeller får bara innehålla de data som är nytt sedan senast gången frågan bearbetades (*lägga till*), eller tabellen kan inte helt uppdateras varje gång det finns nya data, så tabellen innehåller alla utdata eftersom strömmande frågan började (*fullständig läge*).

### <a name="append-mode"></a>Tilläggsläge

Lägg till läget endast de rader som läggs till i resultattabellen eftersom den senaste körningen av frågan finns i resultattabellen och skrivs till extern lagring i. Till exempel kopierar den enklaste frågan bara alla data från indatatabellen till resultattabellen utan ändringar. Varje gång som ett intervall för utlösare går ut den nya data har bearbetats och rader som representerar den nya data visas i resultattabellen. 

Tänk dig ett scenario där du bearbetar telemetri från temperatursensorer, till exempel en termostat. Anta att den första utlösaren bearbetas en händelse på tid 00:01 för enhet 1 med ett temperatur läsning av 95 grader. I den första utlösaren frågans visas endast raden med tiden 00:01 i resultattabellen. Vid tid 00:02 när en annan händelse tas emot, endast nya raden är raden med tiden 00:02 och därför resultattabellen innehåller endast som en rad.

![Structured Streaming lägga till](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

När med hjälp av Lägg till läge, din fråga skulle vara tillämpar projektioner (och markera de kolumner som den värnar om), filtrering (välja endast de rader som matchar vissa villkor) eller ansluta till (utöka data med data från en statisk uppslagstabell). Lägg till läget gör det enkelt att skicka endast relevanta nya data pekar till extern lagring.

### <a name="complete-mode"></a>Fullständig läge

Överväg att för samma scenario med fullständig läge. I läget för fullständig uppdateras hela utdatatabellen på varje utlösare så att tabellen innehåller data som inte bara från den senaste körningen av utlösaren, men från alla körningar. Du kan använda fullständiga läge för att kopiera data oförändrad från indatatabellen till resultattabellen. I varje utlösta visas de nya resultatraderna tillsammans med alla föregående rader. Utdatatabellen resultaten hamnar lagrar alla data som samlas in eftersom frågan började och kör du så småningom slut på minne. Fullständig läge är avsedd att användas med mängdfrågor som summerar inkommande data på något sätt och så vidare varje utlösare resultattabellen uppdateras med en ny sammanfattning. 

Förutsätter än så länge det finns fem sekunder tillhandahåller redan bearbetas och är det dags att bearbeta data för den sjätte andra. Indatatabellen har händelser för tid 00:01 och tiden 00:03. Målet med den här exempelfråga är att ge medeltemperaturen för enheten var femte sekund. Implementeringen av den här frågan gäller en mängd som gör att alla värden som faller inom varje 5-sekundersfönster anger temperaturgenomsnittet och producerar en rad för medeltemperaturen under den perioden. Det finns två tupplar i slutet av det första 5-sekundersintervall-fönstret: (00:01, 1, 95) och (00:03, 1, 98). Så för fönstret 00:00-00:05 aggregeringen producerar en tuppel med medeltemperatur på 96.5 grader. I fönstret nästa 5-sekundersintervall finns endast en datapunkt på tid 00:06, så den resulterande medeltemperaturen håller 98 grader. Vid tiden 00:10, med fullständig läge resultattabellen innehåller raderna för både windows-00:00-00:05 och 00:05-00:10 eftersom frågan returnerar alla rader aggregerade, inte bara nya. Därför fortsätter resultattabellen att växa när nya windows har lagts till.    

![Strukturerad direktuppspelning fullständig läge](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Inte alla frågor med fullständig läge innebär att tabellen utökas utan gränser.  Överväg att i föregående exempel som snarare än medelvärdet är temperatur av tidsfönster, den i stället i genomsnitt av enhets-ID. Resultattabellen innehåller ett fast antal rader (en per enhet) med medeltemperaturen för enheten för alla datapunkter som tagits emot från den enheten. När nya temperaturer tas emot uppdateras resultattabellen så att medelvärden i tabellen alltid är uppdaterade. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Komponenter i ett Spark Structured Streaming-program

Ett enkelt exempelfråga kan sammanfatta temperaturavläsningar av timslång windows. I det här fallet lagras data i JSON-filer i Azure Storage (ansluten som standardlagringen för HDInsight-kluster):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

De här JSON-filerna lagras i den `temps` undermapp under behållaren för HDInsight-klustret. 

### <a name="define-the-input-source"></a>Definiera Indatakällan

Konfigurera först en dataram som beskriver orsaken data och alla inställningar som krävs för den här källan. Det här exemplet ritar från JSON-filer i Azure Storage och tillämpliga ett schema vid läsning tidpunkt.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Tillämpa frågan

Därefter tillämpas en fråga som innehåller de önskade åtgärderna mot den strömmande dataramen. I det här fallet en aggregering grupperar raderna i windows för 1 timme och beräknar de minsta, genomsnittliga och högsta temperaturerna i fönstret 1 timme.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definiera utdatamottagaren

Definiera mål för de rader som har lagts till i resultattabellen inom varje intervall för utlösare. Det här exemplet visar bara alla rader i en InMemory-tabell `temps` att du senare kan fråga med SparkSQL. Fullständig utdataläge garanterar att alla rader för alla fönster är utdata varje gång.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Starta frågan

Starta direktuppspelning frågan och kör tills en uppsägning signal tas emot. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Visa resultatet

När frågan körs i samma SparkSession, du kan köra en SparkSQL fråga mot den `temps` tabellen där resultatet av frågan lagras. 

    select * from temps

Den här frågan ger resultat som liknar följande:


| fönstret |  min(Temp) | AVG(Temp) | Max(Temp) |
| --- | --- | --- | --- |
|{u'start': u 2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u 2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u 2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u 2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u 2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u 2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u 2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Mer information om Spark-strukturerad Stream-API, tillsammans med indata källor, åtgärder och utdata egenskaperna det stöder, finns i [Apache Spark Structured Streaming Programming Guide](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Kontrollpunkter och Skriv-ahead-loggar

För att leverera elasticitet och feltolerans Structured Streaming förlitar sig på *kontrollpunkter* att säkerställa att dataströmmen bearbetningen kan fortsätta utan avbrott, även med nodfel. I HDInsight skapar Spark kontrollpunkter till beständig lagring, antingen Azure Storage eller Data Lake Storage. Dessa kontrollpunkter lagra statusinformation om strömmande frågan. Dessutom Structured Streaming använder en *write-ahead log* (WAL). WAL fångar insamlade data som har tagits emot men har ännu inte bearbetas av en fråga. Om ett fel inträffar och bearbetning av startas från WAL, försvinner inte alla händelser som tagits emot från källan.

## <a name="deploying-spark-streaming-applications"></a>Distribuera Spark Streaming program

Du vanligtvis skapa ett Spark Streaming-program lokalt i en JAR-fil och sedan distribuera den till Apache Spark på HDInsight genom att kopiera JAR-filen till standardlagring kopplat till ditt HDInsight-kluster. Du kan börja med den [Apache Livy](https://livy.incubator.apache.org/) REST API: er som tillgängliga från ditt kluster med hjälp av en POST-åtgärd. Brödtexten i INLÄGGET innehåller ett JSON-dokument som innehåller sökvägen till din JAR, namnet på klassen vars main-metoden definierar och kör programmet strömmande och eventuellt resurskraven för jobbet (till exempel antalet executors, minne och kärnor) , och kräver att alla konfigurationsinställningar programkoden.

![Distribuera ett Spark Streaming-program](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Status för alla program kan också kontrolleras med en GET-begäran mot en LIVY-slutpunkt. Slutligen kan avsluta du ett program som körs genom att utfärda en DELETE-begäran mot slutpunkten LIVY. Mer information om LIVY-API: et finns [fjärrstyrda jobb med Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark-strukturerad strömning Programmeringsguide](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Starta Apache Spark-jobb via fjärranslutning med Apache LIVY](apache-spark-livy-rest-interface.md)
