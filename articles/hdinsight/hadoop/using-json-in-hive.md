---
title: Analysera och bearbeta JSON-dokument med Apache Hive i Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder JSON-dokument och analysera dem med hjälp av apache Hive i Azure HDInsight
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 19d8e74e6f3ca8e7a0b0a64bdd782ea21b233c17
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202534"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Bearbeta och analysera JSON-dokument med hjälp av Apache Hive i Azure HDInsight

Lär dig mer om att bearbeta och analysera JavaScript Object Notation (JSON) filer med hjälp av Apache Hive i Azure HDInsight. Den här kursen använder följande JSON-dokumentet:

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

Filen finns på **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Mer information om hur du använder Azure Blob storage med HDInsight finns [Använd HDFS-kompatibla Azure Blob storage med Hadoop i HDInsight](../hdinsight-hadoop-use-blob-storage.md). Du kan kopiera filen till standardbehållaren på klustret.

I den här kursen använder du Hive-konsolen. Anvisningar om hur du öppnar konsolen Hive finns [använda Hive med Hadoop i HDInsight med fjärrskrivbord](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Förenkla JSON-dokument
De metoder som anges i nästa avsnitt kräver att JSON-dokumentet består av en enda rad. Därför måste du förenkla JSON-dokument till en sträng. Om JSON-dokumentet redan förenklas du hoppa över det här steget och gå direkt till nästa avsnitt om hur du analyserar JSON-data. Om du vill förenkla JSON-dokumentet, kör du följande skript:

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

Rådata JSON-filen finns på **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Den **StudentsRaw** Hive tabell pekar till rå JSON-dokumentet som inte är förenklad.

Den **StudentsOneLine** Hive-tabell lagrar data i HDInsight filsystemet under den **/json/studenter/** sökväg.

Den **infoga** instruktionen fylls den **StudentOneLine** tabell med Flat JSON-data.

Den **Välj** instruktionen returnerar bara en rad.

Här är resultatet av den **Välj** instruktionen:

![Förenkla JSON-dokumentet][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analysera JSON-dokument i Hive
Hive finns tre olika metoder för att köra frågor på JSON-dokument eller skriva egna:

* Använd get_json_object användardefinierad funktion (UDF).
* Använd json_tuple UDF.
* Använd anpassad serialisering/deserialisering (SerDe).
* Skriva egna UDF med hjälp av Python eller andra språk. Mer information om hur du kör Python koden med Hive finns [Python UDF med Apache Hive och Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Använd get_json_object UDF
Hive ger en inbyggda UDF kallas [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) som kan utföra JSON fråga under körningen. Den här metoden tar två argument--tabellnamnet och metodnamnet som har förenklad JSON-dokumentet och JSON-fält som ska tolkas. Nu ska vi titta på ett exempel för att se hur den här UDF fungerar.

Följande fråga returnerar förnamn och efternamn för varje student:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Här är utdatan när du kör den här frågan i konsolfönstret:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Det finns begränsningar för get_json_object UDF:

* Eftersom varje fält i frågan kräver reparsing av frågan, påverkar prestanda.
* **Hämta\_JSON_OBJECT()** returnerar strängrepresentation av en matris. Om du vill konvertera denna matris till en Hive-matris, du måste använda reguljära uttryck för att ersätta hakparentes ”[” och ”]”, och även måste du anropa delning för att få matrisen.

Det är därför Hive-wiki rekommenderar att du använder json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Använd json_tuple UDF
En annan UDF som tillhandahålls av Hive kallas [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), vilken som presterar bättre än [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Den här metoden tar en uppsättning nycklar och en JSON-sträng, och returnerar en tuppel av värden med hjälp av en funktion. Följande fråga returnerar student-ID och klass från JSON-dokumentet:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Utdata från skriptet i Hive-konsolen:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple UDF använder den [lateral visa](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) syntax i Hive, vilket gör att json\_tuppel att skapa en virtuell tabell genom att använda funktionen UDT för varje rad i den ursprungliga tabellen. Komplexa JSONs blir för ohanterlig på grund av upprepade användningen av **LATERAL visa**. Dessutom **JSON_TUPLE** kan inte hantera kapslade JSONs.

### <a name="use-a-custom-serde"></a>Använda en anpassad SerDe
SerDe är det bästa valet för parsning av kapslade JSON-dokument. Det kan du definiera JSON-schema och sedan kan du använda schemat för att parsa dokumenten. Instruktioner finns i [hur du använder en anpassad JSON-SerDe med Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Sammanfattning
Sammanfattningsvis beror typ av JSON-operator i Hive som du väljer på ditt scenario. Om du har ett enkelt JSON-dokument och du har bara ett fält för att leta upp, kan du använda Hive UDF-get_json_object. Om du har fler än en nyckel för att leta upp kan du använda json_tuple. Om du har en kapslad dokument, bör du använda JSON-SerDe.

## <a name="next-steps"></a>Nästa steg

Relaterade artiklar finns:

* [Använda Hive och HiveQL med Hadoop i HDInsight för att analysera ett exempel Apache log4j-fil](../hdinsight-use-hive.md)
* [Analysera svarta fördröjning data med hjälp av Hive i HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Analysera Twitter-data med hjälp av Hive i HDInsight](../hdinsight-analyze-twitter-data.md)

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

