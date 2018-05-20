---
title: Spark strukturerad strömning i Azure HDInsight | Microsoft Docs
description: Hur du använder Spark strukturerade strömning program på HDInsight Spark-kluster.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: f3eccfb63b127f3cbb8f9d95ec2fa802861d8c9d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="overview-of-spark-structured-streaming"></a>Översikt över Spark strukturerad strömning

Spark strukturerade direktuppspelning kan du implementera skalbara, hög genomströmning feltolerant program för bearbetning av dataströmmar. Strukturerade Streaming bygger på Spark SQL-databasmotor och förbättras med konstruktioner från Spark SQL ramar och datauppsättningar så att du kan skriva strömning frågor på samma sätt som du vill skriva batch frågor.  

Strukturerade Streaming-program körs på HDInsight Spark-kluster och ansluta till strömmande data från Kafka, en TCP-socket (för felsökning), Azure Storage eller Azure Data Lake Store. Dessa två alternativen, som förlitar sig på externa lagringstjänster, kan du titta på för nya filer som lagts till lagring och bearbeta innehållet som om de har strömmats. 

Strukturerade Streaming skapar en tidskrävande fråga under vilka åtgärder som gäller för inkommande data, till exempel markeringen, projektion, aggregering, fönsterhantering och koppla den strömmande DataFrame med referensen DataFrames. Därefter skriver du resultatet för lagring (Azure Storage-Blobbar eller Data Lake Store) eller till någon datalagret med hjälp av anpassad kod (till exempel SQL-databas eller Power BI). Strukturerade Streaming ger också utdata till konsolen för felsökning lokalt och i en tabell i minnet så att du kan se de data som genereras för felsökning i HDInsight. 

![Bearbetning med HDInsight och Spark strukturerad strömning ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> Spark Streaming strukturerade ersätter Spark Streaming (DStreams). Framöver, får strukturerade strömning förbättringar och underhåll, vid DStreams är i underhållsläge endast. Strukturerade Streaming är för närvarande inte som funktionen slutföra som DStreams för källorna och egenskaperna stöder direkt, så utvärdera dina krav och välja lämplig Spark dataströmmen Bearbetningsalternativ. 

## <a name="streams-as-tables"></a>Dataströmmar som tabeller

Spark strukturerade strömning representerar en dataström som en tabell som är unbounded på djupet, det vill säga tabellen fortsätter att växa när nya data tas emot. Detta *indatatabellen* kontinuerligt bearbetas av en fråga som körs länge och resultatet skickas till en *utdatatabell*:

![Strukturerad strömning begrepp](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Med strukturerade strömmande data når systemet och omedelbart inhämtas i en indatatabell. Kan du skriva frågor (med DataFrame och Dataset APIs) som utför åtgärder mot den här tabellen. Frågeresultatet ger en annan tabell i *resultattabellen*. Resultattabellen innehåller resultatet av din fråga, varifrån du ritar data för en extern datalagret, en relationsdatabas. Val av tidpunkt för när data bearbetas från indatatabellen styrs av den *utlösaren intervall*. Som standard är vid utlösare noll, så strukturerade strömning försöker bearbeta data när den tas emot. I praktiken innebär detta innebär att som strukturerade strömning görs en annan bearbetningen körs mot alla nyligen mottagna data startar bearbetning av den föregående frågan. Du kan konfigurera utlösaren som körs vid ett intervall så att strömmande data bearbetas i tidsbaserade grupper. 

Data i resultaten tabeller får bara innehålla data som är nytt sedan senast gången frågan bearbetades (*lägga till*), eller tabellen inte helt uppdateras varje gång det finns nya data i tabellen innehåller alla utdata sedan strömmande frågan påbörjades (*fullständig läge*).

### <a name="append-mode"></a>Lägga till

Lägg till läget endast rader läggs till i resultattabellen eftersom den senaste körningen för frågan finns i resultattabellen och skrivs till extern lagring i. Till exempel kopierar den enklaste frågan just alla data från indatatabellen till resultattabellen utan ändringar. Varje gång som en utlösare intervall långa nya data bearbetas och rader som representerar den nya data visas i resultattabellen. 

Överväg ett scenario där du bearbetar telemetri från temperatursensorer, till exempel en termostat. Anta att den första utlösaren bearbetas en händelse vid tiden 00:01 för enhet 1 med en temperatur läsningen av 95 grader. I den första utlösaren av frågan visas bara raden med tiden 00:01 i resultattabellen. Endast nya raden är raden med tiden 00:02 vid tiden 00:02 när en annan händelse tas emot, och så resultattabellen skulle innehålla endast som en rad.

![Strukturerade strömning lägga till](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

När med hjälp av Lägg till läget frågan skulle vara använder projektioner (genom att markera de kolumner som den mån om), filtrering (plocka endast rader som matchar vissa villkor) eller koppla (höja data med data från en statisk uppslagstabell). Lägg till läget gör det enkelt att skicka enbart de relevanta nya data pekar till extern lagring.

### <a name="complete-mode"></a>Fullständig läge

Tänk dig scenariot som samma nu klar-läge. I fullständig läge uppdateras hela utdatatabellen på varje utlösare så att tabellen innehåller data som inte bara från senaste utlösaren körs, men från alla körs. Du kan använda fullständig läge för att kopiera data utan ändringar från indatatabellen till resultattabellen. På varje utlösta kör visas ny resultatet rader tillsammans med alla tidigare rader. Resultaten utdatatabellen hamna lagring av alla data som samlas in eftersom frågan påbörjades, och du skulle slutligen slut på minne. Fullständig läge är avsedd att användas med sammanställd frågor som summerar inkommande data på något sätt och så vidare varje utlösare resultattabellen uppdateras med en ny sammanfattning. 

Förutsätter så länge det finns fem sekunder som redan bearbetade data och är det dags att bearbeta data i sjätte sekund. Indatatabellen har händelser för tid 00:01 och tid 00:03. Syftet med den här exempelfråga är att ge medeltemperaturen för enheten var femte sekund. Implementeringen av den här frågan använder en mängd som gör att alla värden som ligger inom varje 5 sekunder fönster, medelvärden temperaturen och producerar en rad för medeltemperaturen under den perioden. Det finns två tupplar i slutet av det första fönstret 5 sekunder: (00:01, 1, 95) och (00:03, 1, 98). Så för fönstret 00:00-00:05 sammanställning skapar en tuppel med medeltemperaturen av 96.5 grader. I fönstret nästa 5 sekunder finns det endast en punkt i tiden 00:06 resulterande genomsnittstemperaturen är 98 grader. Med tiden 00:10 fullständig läge resultattabellen har raderna för både windows 00:00-00:05 och 00:05 00:10 eftersom frågan matar ut alla sammanställda rader, inte bara nya. Därför fortsätter resultattabellen att växa när nya fönster har lagts till.    

![Strukturerad direktuppspelningsläge klar](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Inte alla frågor med fullständig läge kommer tabellen att växa utan gränser.  Överväg att i föregående exempel som snarare än medelvärdet temperatur av tidsfönstret den var i genomsnitt i stället av enhets-ID. I resultattabellen innehåller ett fast antal rader (en per enhet) med medeltemperaturen för enheten över alla datapunkter som tagits emot från den enheten. När nya temperaturer tas emot uppdateras resultattabellen så att medelvärden i tabellen alltid är aktuella. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Komponenter i ett program med Spark strukturerade strömning

Ett enkelt exempelfråga kan sammanfatta temperaturavläsningar av timslång windows. I så fall lagras data i JSON-filer i Azure Storage (ansluten som standardlagring för HDInsight-kluster):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

De här JSON-filerna lagras i den `temps` undermapp under behållaren för HDInsight-klustret. 

### <a name="define-the-input-source"></a>Definiera Indatakällan

Konfigurera först en DataFrame som beskriver källan för data och alla inställningar som krävs för den här källan. Det här exemplet ritar från JSON-filer i Azure Storage och gäller ett schema för dem skrivskyddade för närvarande.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Tillämpa frågan

Därefter gäller en fråga som innehåller de önskade åtgärderna mot DataFrame strömning. I det här fallet en aggregering grupperas alla rader i windows 1 timme och beräknar minsta genomsnittliga och högsta temperaturer i fönstret 1 timme.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definiera utdatamottagaren

Därefter definiera mål för de rader som har lagts till i resultattabellen inom varje utlösare intervall. Det här exemplet bara matar ut alla rader i en tabell i minnet `temps` att du senare kan fråga med SparkSQL. Fullständig output-läget säkerställer att alla rader för alla windows är utdata varje gång.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Starta frågan

Starta strömning frågan och kör tills en Avslutningssignal tas emot. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Visa resultaten

När frågan körs i samma SparkSession kan du köra en SparkSQL fråga mot den `temps` tabellen var resultatet av frågan lagras. 

    select * from temps

Den här frågan ger resultat som liknar följande:


| fönstret |  min(Temp) | AVG(Temp) | Max(Temp) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Mer information om Spark-API: et för strukturerad dataström tillsammans med indata källor, åtgärder och utdata egenskaperna den stöder, se [Spark strukturerade strömning Programmeringsguide](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Kontrollpunkter och Skriv-ahead-loggar

För att leverera återhämtning och feltolerans strukturerade strömning förlitar sig på *kontrollpunkter* för att säkerställa att dataströmmen bearbetning kan fortsätta utan avbrott, även om nodfel. I HDInsight skapar Spark kontrollpunkter till beständig lagring, antingen Azure Storage eller Azure Data Lake Store. Kontrollpunkterna lagra statusinformation om strömning frågan. Dessutom strukturerade strömning använder en *write-ahead loggen* (förnyelse). Förnyelse avbildar infogade data som har tagits emot men ännu inte behandlats av en fråga. Om ett fel inträffar och bearbetning startas från förnyelse, är eventuella händelser som tagits emot från källan inte förlorade.

## <a name="deploying-spark-streaming-applications"></a>Distribuera Spark Streaming program

Du skapar vanligtvis ett Spark Streaming program lokalt i en JAR-fil och sedan distribuera den till Spark i HDInsight genom att kopiera JAR-filen till standardlagring kopplad till ditt HDInsight-kluster. Du kan starta tillämpningsprogrammet med LIVIUS REST API: erna tillgänglig från ditt kluster med hjälp av en POST-åtgärd. Innehållet i posten innehåller en JSON-dokument som innehåller sökvägen till din JAR, namnet på den klass vars main-metoden definierar och kör programmet strömmande och eventuellt resurskraven för jobbet (till exempel antalet executors-, minnes- och kärnor) , och kräver att alla konfigurationsinställningar för programkoden.

![Distribuera ett Spark Streaming program](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Status för alla program kan också kontrolleras med en GET-begäran mot en LIVIUS slutpunkt. Slutligen kan du avsluta ett program som körs genom att utfärda en DELETE-begäran mot slutpunkten LIVIUS. Mer information om LIVIUS API: et finns [Remote jobb med LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark strukturerad strömning Programmeringsguide](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Starta Spark jobb via fjärranslutning med LIVY](apache-spark-livy-rest-interface.md)
