---
title: Analysera & process JSON med Apache Hive - Azure HDInsight
description: Lär dig hur du använder JSON-dokument och analyserar dem med hjälp av Apache Hive i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8e0abf780589207b065b7262afb99de81e625fe8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732215"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Bearbeta och analysera JSON-dokument med hjälp av Apache Hive i Azure HDInsight

Lär dig hur du bearbetar och analyserar JSON-filer (JavaScript Object Notation) med hjälp av Apache Hive i Azure HDInsight. I den här artikeln används följande JSON-dokument:

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

Filen finns på `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Mer information om hur du använder Azure Blob-lagring med HDInsight finns i [Använda HDFS-kompatibel Azure Blob-lagring med Apache Hadoop i HDInsight](../hdinsight-hadoop-use-blob-storage.md). Du kan kopiera filen till standardbehållaren i klustret.

I den här artikeln använder du Apache Hive-konsolen. Instruktioner om hur du öppnar Hive-konsolen finns i [Använd Apache Ambari Hive View med Apache Hadoop i HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> Hive View är inte längre tillgängligt i HDInsight 4.0.

## <a name="flatten-json-documents"></a>Förenkla JSON-dokument

De metoder som anges i nästa avsnitt kräver att JSON-dokumentet består av en enda rad. Därför måste du förenkla JSON-dokumentet till en sträng. Om ditt JSON-dokument redan är förenklat kan du hoppa över det här steget och gå direkt till nästa avsnitt om hur du analyserar JSON-data. Om du vill förenkla JSON-dokumentet kör du följande skript:

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

Den råa JSON-filen finns på `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Tabellen **StudentsRaw** Hive pekar på det råa JSON-dokumentet som inte är tillplattat.

Tabellen **StudentsOneLine** Hive lagrar data i standardfilsystemet HDInsight under sökvägen **/json/students/path.**

**INSERT-satsen** fyller i **tabellen StudentOneLine** med de förenklade JSON-data.

**SELECT-uttrycket** returnerar bara en rad.

Här är utdata från **SELECT-satsen:**

![HDInsight plattar till JSON-dokumentet](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analysera JSON-dokument i Hive

Hive innehåller tre olika mekanismer för att köra frågor om JSON-dokument, eller så kan du skriva egna:

* Använd den get_json_object användardefinierade funktionen (UDF).
* Använd json_tuple UDF.
* Använd den anpassade Serializer/Deserializer (SerDe).
* Skriv din egen UDF med python eller andra språk. Mer information om hur du kör din egen Python-kod med Hive finns i [Python UDF med Apache Hive och Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Använd get_json_object UDF

Hive tillhandahåller en inbyggd UDF som kallas [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) som frågar JSON under körning. Den här metoden innehåller två argument: tabellnamnet och metodnamnet. Metodnamnet har det förenklade JSON-dokumentet och JSON-fältet som måste tolkas. Låt oss titta på ett exempel för att se hur denna UDF fungerar.

Följande fråga returnerar för- och efternamn för varje deltagare:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Här är utdata när du kör den här frågan i konsolfönstret:

![Apache Hive får json objekt UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Det finns begränsningar i get_json_object UDF:

* Eftersom varje fält i frågan kräver en reparsering av frågan påverkar det prestandan.
* **GET\_JSON_OBJECT()** returnerar strängrepresentationen av en matris. Om du vill konvertera den här matrisen till en Hive-matris måste du använda reguljära uttryck för att ersätta hakparenteserna "[" och "]", och sedan måste du också anropa delning för att hämta matrisen.

Den här konverteringen är anledningen till att Hive-wikin rekommenderar att du använder **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Använd json_tuple UDF

En annan UDF från Hive kallas [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), vilket gör bättre än [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Den här metoden tar en uppsättning nycklar och en JSON-sträng. Returnerar sedan en tuppel av värden. Följande fråga returnerar student-ID och resultatet från JSON-dokumentet:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Resultatet av det här skriptet i Hive-konsolen:

![Apache Hive json frågeresultat](./media/using-json-in-hive/hdinsight-json-tuple.png)

UDF använder den [laterala](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) vysyntaxen i Hive, som gör det möjligt för json\_tuple att skapa en virtuell tabell genom att använda UDT-funktionen på varje rad i den ursprungliga tabellen. `json_tuple` Komplexa JSONs blir för tungrodda på grund av upprepad användning av **LATERAL VIEW**. Dessutom kan JSON_TUPLE inte hantera kapslade JSON.Dessutom kan **JSON_TUPLE** inte hantera kapslade JSONs.

### <a name="use-a-custom-serde"></a>Använda en anpassad SerDe

SerDe är det bästa valet för att tolka kapslade JSON-dokument. Det låter dig definiera JSON-schemat och sedan kan du använda schemat för att tolka dokumenten. Instruktioner finns i [Så här använder du en anpassad JSON SerDe med Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Sammanfattning

Vilken typ av JSON-operator i Hive du väljer beror på ditt scenario. Med ett enkelt JSON-dokument och ett fält att slå upp väljer du Hive UDF **get_json_object**. Om du har mer än en nyckel att slå upp på kan du använda **json_tuple**. Använd **JSON SerDe**för kapslade dokument .

## <a name="next-steps"></a>Nästa steg

För relaterade artiklar, se:

* [Använd Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel Apache log4j-fil](../hdinsight-use-hive.md)
* [Analysera flygfördröjningsdata med hjälp av interaktiv fråga i HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
