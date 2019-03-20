---
title: Analysera och bearbeta JSON-dokument med Apache Hive - Azure HDInsight
description: Lär dig hur du använder JSON-dokument och analysera dem med hjälp av Apache Hive i Azure HDInsight
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: hrasheed
ms.openlocfilehash: 31909d007727ca5b440343e3c5a035984399b77a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201746"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Bearbeta och analysera JSON-dokument med hjälp av Apache Hive i Azure HDInsight

Lär dig mer om att bearbeta och analysera JavaScript Object Notation (JSON) filer med hjälp av Apache Hive i Azure HDInsight. Den här självstudien använder följande JSON-dokument:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Filen finns på **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. Mer information om hur du använder Azure Blob storage med HDInsight finns i [Använd HDFS-kompatibla Azure Blob storage med Apache Hadoop i HDInsight](../hdinsight-hadoop-use-blob-storage.md). Du kan kopiera filen till standardbehållare på klustret.

I den här självstudien använder du Apache Hive-konsolen. Anvisningar för hur du öppnar konsolen Hive finns i [Använd Apache Ambari Hive-vy med Apache Hadoop i HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Förenkla JSON-dokument
De metoder som anges i nästa avsnitt kräver att JSON-dokumentet består av en enskild rad. Därför måste du förenkla JSON-dokumentet till en sträng. Om JSON-dokumentet redan förenklas du hoppa över det här steget och gå direkt till nästa avsnitt om hur du analyserar JSON-data. Kör följande skript för att platta ut JSON-dokumentet:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Rå JSON-filen finns på **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. Den **StudentsRaw** Hive-tabellen pekar till den råa JSON-dokument som inte är utplattad.

Den **StudentsOneLine** Hive-tabell som lagrar data i standardfilsystemet för HDInsight under den **/json/studenter/** sökväg.

Den **infoga** instruktionen fyller den **StudentOneLine** tabell med Flat JSON-data.

Den **Välj** instruktion returnerar endast en rad.

Här är utdata från den **Välj** instruktionen:

![Förenkla JSON-dokumentet][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analysera JSON-dokument i Hive
Hive tillhandahåller tre olika sätt för att köra frågor i JSON-dokument, eller du kan också skriva egna:

* Använd get_json_object användardefinierad funktion (UDF).
* Använd json_tuple UDF.
* Använd anpassade serialiserare/Deserialiserare (SerDe).
* Skriv din egen UDF med hjälp av Python eller andra språk. Läs mer om hur du kör din egen kod för Python med Hive [Python-UDF med Apache Hive och Apache Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Använd get_json_object UDF
Hive innehåller en inbyggd UDF kallas [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) som kan utföra JSON frågor under körning. Den här metoden tar två argument--tabellnamnet och metodnamnet som har Flat JSON-dokumentet och som krävs för att parsa JSON-fältet. Nu ska vi titta på ett exempel och se hur den här UDF fungerar.

Följande fråga returnerar det förnamn och efternamn för varje elev:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Här är utdata när du kör den här frågan i konsolfönstret:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Det finns begränsningar av get_json_object UDF:

* Eftersom varje fält i frågan kräver reparsing frågans, påverkar prestanda.
* **Hämta\_JSON_OBJECT()** returnerar strängrepresentation av en matris. Om du vill konvertera den här matrisen till en Hive, du måste använda reguljära uttryck för att ersätta hakparenteserna ”[” och ”]”, och som också att anropa delning för att få matrisen.

Det är därför Hive-wiki rekommenderar att du använder json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Använd json_tuple UDF
En annan UDF från Hive kallas [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), vilken som presterar bättre än [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Den här metoden tar en uppsättning nycklar och en JSON-sträng och returnerar en tuppel av värden med hjälp av en funktion. Följande fråga returnerar student-ID och den i företagsklass från JSON-dokument:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Utdata från det här skriptet i Hive-konsolen:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple UDF använder den [lateral visa](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) syntax i Hive, vilket gör det möjligt json\_tuppel att skapa en virtuell tabell genom att använda funktionen UDT för varje rad i den ursprungliga tabellen. Komplexa för bli för svårhanterlig på grund av upprepad användning av **LATERAL visa**. Dessutom **JSON_TUPLE** kan inte hantera kapslad för.

### <a name="use-a-custom-serde"></a>Använda en anpassad SerDe
SerDe är det bästa valet för parsning av kapslad JSON-dokument. Det kan du definiera JSON-schemat och du kan sedan använda schemat för att parsa dokumenten. Anvisningar finns i [hur du använder en anpassad JSON-SerDe med Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Sammanfattning
Sammanfattningsvis beror typ av JSON-operator i Hive som du väljer på ditt scenario. Om du har ett enkelt JSON-dokument och du har bara ett fält att söka efter, kan du använda Hive UDF-get_json_object. Om du har fler än en nyckel ska sökas, kan du använda json_tuple. Om du har en kapslad dokument bör du använda JSON-SerDe.

## <a name="next-steps"></a>Nästa steg

Relaterade artiklar finns här:

* [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel Apache log4j-fil](../hdinsight-use-hive.md)
* [Analysera flygförseningsdata med hjälp av Apache Hive i HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)
* [Analysera Twitter-data med hjälp av Apache Hive i HDInsight](../hdinsight-analyze-twitter-data-linux.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

