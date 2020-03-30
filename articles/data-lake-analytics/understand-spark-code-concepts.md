---
title: Förstå Apache Spark-kodbegrepp för Azure Data Lake Analytics U-SQL-utvecklare.
description: I den här artikeln beskrivs Apache Spark-koncept för att hjälpa U-SQL-utvecklare att förstå Spark-kodbegrepp.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424000"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Förstå Apache Spark-kod för U-SQL-utvecklare

Det här avsnittet innehåller vägledning på hög nivå om hur du omvandlar U-SQL-skript till Apache Spark.

- Det börjar med en [jämförelse av de två språkets bearbetning paradigm](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Ger tips om hur du:
   - [Omvandla skript](#transform-u-sql-scripts) inklusive U-SQL-raduppsättningsuttryck [rowset expressions](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET-kod](#transform-net-code)
   - [Datatyper](#transform-typed-values)
   - [Katalogobjekt](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Förstå U-SQL och Spark språk och bearbetning paradigm

Innan du börjar migrera Azure Data Lake Analytics U-SQL-skript till Spark är det användbart att förstå det allmänna språket och bearbetningsfilosofierna i de två systemen.

U-SQL är ett SQL-liknande deklarativt frågespråk som använder ett paradigm för dataflöde och gör att du enkelt kan bädda in och skala ut användarkod skriven i .NET (till exempel C#), Python och R. Användartilläggen kan implementera enkla uttryck eller användardefinierade funktioner, men kan också ge användaren möjlighet att implementera så kallade användardefinierade operatorer som implementerar anpassade operatorer för att utföra omvandlingar på radnivå, extraheringar och skrivutdata.

Spark är ett utskalningsramverk som erbjuder flera språkbindningar i Scala, Java, Python, .NET etc. där du i första hand skriver din kod på ett av dessa språk, skapar dataabstraherande som kallas fröstiga distribuerade datauppsättningar (RDD), dataramar och datauppsättningar och använd sedan ett LINQ-liknande domänspecifikt språk (DSL) för att omvandla dem. Det ger också SparkSQL som ett deklarativt underspråk på dataramen och datamängdstrahertioner. DSL innehåller två kategorier av åtgärder, omvandlingar och åtgärder. Om du tillämpar omvandlingar på dataabstraheringarna kommer inte omvandlingen utan i stället att bygga upp körningsplanen som ska skickas för utvärdering med en åtgärd (till exempel skriva resultatet i en tillfällig tabell eller fil eller skriva ut resultatet).

Således när översätta ett U-SQL-skript till en Spark-program, måste du bestämma vilket språk du vill använda för att åtminstone generera dataram abstraktion (som för närvarande är den mest använda data abstraktion) och om du vill skriva deklarativa dataflödesomvandlingar med hjälp av DSL eller SparkSQL. I vissa mer komplexa fall kan du behöva dela upp ditt U-SQL-skript i en sekvens av Spark och andra steg som implementeras med Azure Batch eller Azure Functions.

Dessutom erbjuder Azure Data Lake Analytics U-SQL i en serverlös jobbtjänstmiljö, medan både Azure Databricks och Azure HDInsight erbjuder Spark i form av en klustertjänst. När du omvandlar ditt program måste du ta hänsyn till konsekvenserna av att nu skapa, dimensionera, skala och inaktivera kluster.

## <a name="transform-u-sql-scripts"></a>Omvandla U-SQL-skript

U-SQL-skript följer följande bearbetningsmönster:

1. Data läses från antingen ostrukturerade `EXTRACT` filer, med hjälp av utdraget, en plats- eller filuppsättningsspecifikation och den inbyggda eller användardefinierade utsugs- och önskat schemat, eller från U-SQL-tabeller (hanterade eller externa tabeller). Den representeras som en raduppsättning.
2. Raduppsättningarna omvandlas i flera U-SQL-uttryck som tillämpar U-SQL-uttryck på raduppsättningarna och producerar nya raduppsättningar.
3. Slutligen matas de resulterande raduppsättningarna `OUTPUT` ut i antingen filer med hjälp av uttrycket som anger platsen/platserna och en inbyggd eller användardefinierad utdatater, eller till en U-SQL-tabell.

Skriptet utvärderas lättjefullt, vilket innebär att varje extrahering och omvandlingssteg består i ett uttrycksträd och utvärderas globalt (dataflödet).

Spark-program är liknande i att du skulle använda Spark-kopplingar för att läsa data och skapa dataramar, sedan tillämpa omvandlingar på dataramar med antingen LINQ-liknande DSL eller SparkSQL, och sedan skriva resultatet i filer, temporära Spark tabeller, vissa programmeringsspråktyper eller konsolen.

## <a name="transform-net-code"></a>Transformera .NET-kod

U-SQL:s uttrycksspråk är C# och det erbjuder en mängd olika sätt att skala ut anpassad .NET-kod.

Eftersom Spark för närvarande inte stöder körning av .NET-kod måste du antingen skriva om dina uttryck till ett motsvarande Spark-, Scala-, Java- eller Python-uttryck eller hitta ett sätt att anropa till .NET-koden. Om skriptet använder .NET-bibliotek har du följande alternativ:

- Översätt .NET-koden till Scala eller Python.
- Dela ditt U-SQL-skript i flera steg, där du använder Azure Batch-processer för att tillämpa .NET-omvandlingarna (om du kan få acceptabel skala)
- Använd en .NET-språkbindning som är tillgänglig i Open Source som heter Moebius. Det här projektet är inte i ett tillstånd som stöds.

I vilket fall som helst, om du har en stor mängd .NET-logik i dina U-SQL-skript, vänligen kontakta oss via din Microsoft-kontorepresentant för ytterligare vägledning.

Följande information finns i de olika fallen med .NET- och C#-användningar i U-SQL-skript.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Omvandla scalar inline U-SQL C# uttryck

U-SQL:s uttrycksspråk är C#. Många av de scalar inline U-SQL-uttrycken implementeras internt för bättre prestanda, medan mer komplexa uttryck kan köras genom att anropa till .NET-ramverket.

Spark har sitt eget skalenade uttrycksspråk (antingen som en del av DSL eller i SparkSQL) och tillåter inringning i användardefinierade funktioner skrivna på värdspråket.

Om du har skaläruttryck i U-SQL bör du först hitta det mest lämpliga inbyggda gnämpparskaleuttrycket för att få mest prestanda och sedan mappa de andra uttrycken till en användardefinierad funktion för Spark-värdspråket som du väljer.

Tänk på att .NET och C# har olika typer av semantik än Spark-värdspråken och Spark's DSL. Se [nedan](#transform-typed-values) för mer information om typ systemskillnader.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Omvandla användardefinierade skalär .NET-funktioner och användardefinierade aggregatorer

U-SQL innehåller sätt att anropa godtyckliga skalär .NET-funktioner och anropa användardefinierade aggregatorer skrivna i .NET.

Spark erbjuder också stöd för användardefinierade funktioner och användardefinierade aggregatorer skrivna på de flesta av sina värdspråk som kan anropas från Spark's DSL och SparkSQL.

### <a name="transform-user-defined-operators-udos"></a>Omvandla användardefinierade operatorer

U-SQL innehåller flera kategorier av användardefinierade operatorer (UDOs) såsom extractors, outputters, reducers, processorer, appliers och combiners som kan skrivas i .NET (och - i viss mån - i Python och R).

Spark erbjuder inte samma utökningsmodell för operatorer, men har motsvarande funktioner för vissa.

Gnist som motsvarar utsug och uttappare är Spark-kontakter. För många U-SQL-utsug kan du hitta en motsvarande koppling i Spark-communityn. För andra måste du skriva en anpassad koppling. Om U-SQL-utsugaren är komplex och använder flera .NET-bibliotek kan det vara bättre att skapa en koppling i Scala som använder interop för att anropa till .NET-biblioteket som gör den faktiska bearbetningen av data. I så fall måste du distribuera .NET Core-körningen till Spark-klustret och se till att de refererade .NET-biblioteken är .NET Standard 2.0-kompatibla.

De andra typerna av U-SQL UDOs måste skrivas om med hjälp av användardefinierade funktioner och sammanställare och det semantiskt lämpliga Spark DLS- eller SparkSQL-uttrycket. En behandlare kan till exempel mappas till en SELECT av en mängd olika UDF-anrop, paketerade som en funktion som tar en dataram som ett argument och returnerar en dataram.

### <a name="transform-u-sqls-optional-libraries"></a>Transformera U-SQL:s valfria bibliotek

U-SQL innehåller en uppsättning valfria bibliotek och demobibliotek som erbjuder [Python,](data-lake-analytics-u-sql-python-extensions.md) [R,](data-lake-analytics-u-sql-r-extensions.md) [JSON, XML, AVRO-stöd](https://github.com/Azure/usql/tree/master/Examples/DataFormats)och vissa [funktioner för kognitiva tjänster](data-lake-analytics-u-sql-cognitive.md).

Spark erbjuder sin egen Python- och R-integrering, pySpark respektive SparkR, och tillhandahåller kopplingar för att läsa och skriva JSON, XML och AVRO.

Om du behöver omvandla ett skript som refererar till biblioteken för kognitiva tjänster rekommenderar vi att du kontaktar oss via din Microsoft-kontorepresentant.

## <a name="transform-typed-values"></a>Omforma maskinskrivna värden

Eftersom U-SQL:s typsystem är baserat på .NET-typsystemet och Spark har ett eget typsystem, som påverkas av värdspråksbindningen, måste du se till att de typer du arbetar med är nära och för vissa typer kan typintervallen, precisionen och/eller skalan vara något annorlunda. Dessutom behandlar `null` U-SQL och Spark värden på olika sätt.

### <a name="data-types"></a>Datatyper

Följande tabell ger motsvarande typer i Spark, Scala och PySpark för de angivna U-SQL-typerna.

| U-SQL | Spark |  Scala | Pyspark (på en) |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Mer information finns i:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Skapa typer av TÄNDa SQL- och DataFrames](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala-värdetyper](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Behandling av NULL

I Spark tillåter typer per standard NULL-värden medan du i U-SQL uttryckligen markerar skalär, icke-objekt som nullable. Spark kan definiera en kolumn som ogiltig, men den framtvingar inte begränsningen och [kan leda till fel resultat](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

I Spark anger NULL att värdet är okänt. Ett Spark NULL-värde skiljer sig från vilket värde som helst, inklusive sig själv. Jämförelser mellan två Spark NULL-värden, eller mellan ett NULL-värde och något annat värde, returnerar okänt eftersom värdet för varje NULL är okänt.  

Detta är annorlunda än U-SQL, som följer C `null` # semantik där skiljer sig från alla värde men lika med sig själv.  

Således `SELECT` en SparkSQL-sats som använder `WHERE column_name = NULL` returnerar noll `column_name`rader även om det finns NULL-värden `column_name` i `null`, medan det i U-SQL skulle returnera raderna där är inställt på . På samma sätt `SELECT` returnerar `WHERE column_name != NULL` en Spark-sats som använder noll `column_name`rader även om det finns icke-null-värden i , medan det i U-SQL returnerar de rader som inte är null. Om du vill ha U-SQL null-check semantik, bör du använda [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) och [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) respektive (eller deras DSL motsvarande).

## <a name="transform-u-sql-catalog-objects"></a>Omvandla U-SQL-katalogobjekt

En stor skillnad är att U-SQL Scripts kan använda sig av dess katalogobjekt, varav många inte har någon direkt Spark-motsvarighet.

Spark ger stöd för Hive Meta-lagringsbegreppen, främst databaser och tabeller, så att du kan mappa U-SQL-databaser och scheman till Hive-databaser och U-SQL-tabeller till Spark-tabeller (se [Flytta data som lagras i U-SQL-tabeller](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)), men det har inget stöd för vyer, tabellvärderade funktioner (TVFs), lagrade procedurer, U-SQL-sammansättningar, externa datakällor etc.

U-SQL-kodobjekt som vyer, TV-filer, lagrade procedurer och sammansättningar kan modelleras via kodfunktioner och bibliotek i Spark och refereras med hjälp av värdspråkets funktion och processuella abstraktionsmekanismer (till exempel genom att importera Python-moduler eller refererar till Scala-funktioner).

Om U-SQL-katalogen har använts för att dela data och kodobjekt mellan projekt och team måste motsvarande mekanismer för delning användas (till exempel Maven för delning av kodobjekt).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Omvandla U-SQL-raduppsättningsuttryck och SQL-baserade skaläruttryck

U-SQL:s kärnspråk omvandlar raduppsättningar och baseras på SQL. Följande är en icke uttömmande lista över de vanligaste raduppsättningsuttrycken som erbjuds i U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Aggregat+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` uttryck
- `CROSS`/`OUTER``APPLY` uttryck
- `PIVOT`/`UNPIVOT`Uttryck
- `VALUES`rowset konstruktor

- Ange uttryck`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Dessutom ger U-SQL en mängd SQL-baserade skaläruttryck, t.ex.

- `OVER`fönsterputsa uttryck
- en mängd inbyggda aggregatorer och`SUM` `FIRST` rankingfunktioner (, etc.)
- Några av de mest kända SQL `CASE` `LIKE`scalar`NOT` `IN`uttryck: , , ( ) , `AND`etc. `OR`

Spark erbjuder likvärdiga uttryck i både DSL- och SparkSQL-form för de flesta av dessa uttryck. Vissa av de uttryck som inte stöds internt i Spark måste skrivas om med hjälp av en kombination av de ursprungliga Spark-uttrycken och semantiskt likvärdiga mönster. Till exempel `OUTER UNION` måste översättas till motsvarande kombination av prognoser och fackföreningar.

På grund av den olika hanteringen av NULL-värden matchar en U-SQL-koppling alltid en rad om båda kolumnerna som jämförs innehåller ett NULL-värde, medan en koppling i Spark inte matchar sådana kolumner om inte explicita null-kontroller läggs till.

## <a name="transform-other-u-sql-concepts"></a>Omvandla andra U-SQL-koncept

U-SQL erbjuder också en mängd andra funktioner och koncept, till exempel federerade frågor mot SQL Server-databaser, parametrar, `OPTION` skalär och lambda uttrycksvariabler, systemvariabler, tips.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Federerade frågor mot SQL Server-databaser/externa tabeller

U-SQL tillhandahåller datakällor och externa tabeller samt direkta frågor mot Azure SQL Database. Spark erbjuder inte samma objektabstraktioner, men [spark-anslutningsappen för Azure SQL-databas](../sql-database/sql-database-spark-connector.md) som kan användas för att fråga SQL-databaser.

### <a name="u-sql-parameters-and-variables"></a>U-SQL-parametrar och variabler

Parametrar och användarvariabler har likvärdiga begrepp i Spark och deras värdspråk.

I Scala kan du till exempel `var` definiera en variabel med nyckelordet:

```
var x = 2 * 3;
println(x)
```

U-SQL:s systemvariabler (variabler som börjar med) `@@`kan delas in i två kategorier:

- Inställningsbara systemvariabler som kan ställas in på specifika värden för att påverka skriptets beteende
- Informationssystemvariabler som frågar information om system- och arbetsnivå

De flesta av de inställningsbara systemvariablerna har ingen direkt motsvarighet i Spark. Vissa informationssystemvariabler kan modelleras genom att skicka informationen som argument under jobbkörning, andra kan ha en motsvarande funktion i Sparks värdspråk.

### <a name="u-sql-hints"></a>U-SQL tips

U-SQL erbjuder flera syntaktiska sätt att ge tips till frågeoptimeraren och körningsmotorn:  

- Ange en U-SQL-systemvariabel
- en `OPTION` sats som är associerad med raduppsättningsuttrycket för att ge en data- eller planledtråd
- en kopplingstips i syntaxen för kopplingsuttrycket (till exempel `BROADCASTLEFT`)

Sparks kostnadsbaserade frågeoptimerar har sina egna funktioner för att ge tips och justera frågeprestanda. Se motsvarande dokumentation.

## <a name="next-steps"></a>Nästa steg

- [Förstå Spark-dataformat för U-SQL-utvecklare](understand-spark-data-formats.md)
- [.NET för Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Uppgradera dina lösningar för stordataanalys från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Omvandla data med Spark-aktivitet i Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Omvandla data med Hadoop Hive-aktivitet i Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Vad är Apache Spark i Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
