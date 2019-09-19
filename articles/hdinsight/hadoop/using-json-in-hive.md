---
title: Analysera och bearbeta JSON-dokument med Apache Hive i Azure HDInsight
description: Lär dig hur du använder JSON-dokument och analyserar dem med hjälp av Apache Hive i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 629a7c98a7b46b470470445cc56a6f53d9e4f4b4
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077222"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Bearbeta och analysera JSON-dokument med hjälp av Apache Hive i Azure HDInsight

Lär dig att bearbeta och analysera JavaScript Object Notation-filer (JSON) med Apache Hive i Azure HDInsight. I den här artikeln används följande JSON-dokument:

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

Filen finns på `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Mer information om hur du använder Azure Blob Storage med HDInsight finns i [använda HDFS-kompatibla Azure Blob Storage med Apache Hadoop i HDInsight](../hdinsight-hadoop-use-blob-storage.md). Du kan kopiera filen till standard behållaren för klustret.

I den här artikeln använder du Apache Hive-konsolen. Instruktioner för hur du öppnar Hive-konsolen finns i [använda Apache Ambari Hive-vy med Apache Hadoop i HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Förenkla JSON-dokument
Metoderna som anges i nästa avsnitt kräver att JSON-dokumentet består av en enda rad. Därför måste du förenkla JSON-dokumentet till en sträng. Om JSON-dokumentet redan är utplattat kan du hoppa över det här steget och gå direkt till nästa avsnitt om hur du analyserar JSON-data. Om du vill förenkla JSON-dokumentet kör du följande skript:

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

Rå JSON-filen finns på `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Hive-tabellen i **StudentsRaw** pekar på det obehandlade JSON-dokument som inte är utplattat.

Hive-tabellen för **StudentsOneLine** lagrar data i HDInsight-standardfilsystemet under **/JSON/Students/** -sökvägen.

Instruktionen **insert** fyller i **StudentOneLine** -tabellen med de sammanslagna JSON-data.

**Select** -instruktionen returnerar bara en rad.

Här är resultatet av **Select** -instruktionen:

![HDInsight förenkla JSON-dokumentet](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analysera JSON-dokument i Hive
Hive innehåller tre olika mekanismer för att köra frågor på JSON-dokument, eller så kan du skriva egna:

* Använd get_json_object-användardefinierad funktion (UDF).
* Använd json_tuple UDF.
* Använd anpassad serialisering/deserialiserare (SerDe).
* Skriv din egen UDF genom att använda python eller andra språk. Mer information om hur du kör din egen python-kod med Hive finns i [python UDF med Apache Hive och Apache gris] [HDInsight-python].

### <a name="use-the-get_json_object-udf"></a>Använd get_json_object UDF
Hive innehåller en inbyggd UDF-fil med namnet [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) som kan utföra JSON-frågor under körning. Den här metoden har två argument: tabell namn och metod namn, som har det sammanslagna JSON-dokumentet och JSON-fältet som behöver parsas. Nu ska vi titta på ett exempel för att se hur UDF fungerar.

Följande fråga returnerar förnamn och efter namn för varje elev:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Här är utdata när du kör den här frågan i konsol fönstret:

![Apache Hive Hämta JSON-objekt UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Det finns begränsningar för get_json_object UDF:

* Eftersom varje fält i frågan kräver omparsning av frågan, påverkas prestandan.
* **Get\_JSON_OBJECT ()** returnerar sträng representationen för en matris. Om du vill konvertera matrisen till en Hive-matris måste du använda reguljära uttryck för att ersätta hakparenteserna "[" och "]" och sedan måste du också anropa Split för att hämta matrisen.

Det är därför som Hive-wikin rekommenderar att du använder json_tuple.  

### <a name="use-the-json_tuple-udf"></a>Använd json_tuple UDF
En annan UDF som tillhandahålls av Hive kallas [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), som fungerar bättre än [get_ JSON-_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Den här metoden tar en uppsättning nycklar och en JSON-sträng och returnerar en tupel med värden med hjälp av en funktion. Följande fråga returnerar Student-ID och klass från JSON-dokumentet:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Utdata från det här skriptet i Hive-konsolen:

![Apache Hive JSON-frågeresultat](./media/using-json-in-hive/hdinsight-json-tuple.png)

UDF-json_tuple [använder i Hive-syntaxen](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) i Hive, som gör\_att JSON-tupel kan skapa en virtuell tabell genom att använda UDT-funktionen på varje rad i den ursprungliga tabellen. Komplexa JSON blir för svårhanterligt på grund av den upprepade användningen av **sido visning**. Dessutom kan **JSON_TUPLE** inte hantera KAPSLAde JSON-artiklar.

### <a name="use-a-custom-serde"></a>Använd en anpassad SerDe
SerDe är det bästa valet för att parsa kapslade JSON-dokument. Du kan definiera JSON-schemat och sedan använda schemat för att parsa dokumenten. Instruktioner finns i [så här använder du en anpassad JSON-SerDe med Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Sammanfattning
Den typ av JSON-operator i Hive som du väljer beror på ditt scenario. Om du har ett enkelt JSON-dokument och det bara finns ett fält att söka efter, kan du välja att använda registrerings data filen UDF get_json_object. Om du har mer än en nyckel att söka på kan du använda json_tuple. Om du har ett kapslat dokument bör du använda JSON-SerDe.

## <a name="next-steps"></a>Nästa steg

Relaterade artiklar finns i:

* [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel på Apache log4j-filen](../hdinsight-use-hive.md)
* [Analysera flyg fördröjnings data med hjälp av interaktiv fråga i HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analysera Twitter-data med Apache Hive i HDInsight](../hdinsight-analyze-twitter-data-linux.md)
