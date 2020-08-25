---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: ba3c7304b0ce52cb720a9e2ac7ae6cf1b5ffeeea
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751469"
---
# <a name="azure-hdinsight-release-notes"></a>Viktig information om Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

## <a name="release-date-08092020"></a>Utgivnings datum: 08/09/2020

Den här versionen gäller endast för HDInsight 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="support-for-sparkcruise"></a>Stöd för SparkCruise
SparkCruise är ett system för automatisk beräknings åter användning för Spark. Det väljer vanliga under uttryck för att materialiseraa baserat på tidigare frågans arbets belastning. SparkCruise materialiserar dessa under uttryck som en del av bearbetningen av frågor och åter användning av data används automatiskt i bakgrunden. Du kan dra nytta av SparkCruise utan att ändra Spark-koden.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Hive-vy för HDInsight 4,0
Apache Ambari Hive-vyn är utformad för att hjälpa dig att skapa, optimera och köra Hive-frågor från webbläsaren. Hive-vyn stöds internt för HDInsight 4,0-kluster från den här versionen. Den gäller inte för befintliga kluster. Du måste släppa och återskapa klustret för att få den inbyggda Hive-vyn.
 
### <a name="support-tez-view-for-hdinsight-40"></a>Stöd för Tez-vy för HDInsight 4,0
Apache Tez-vyn används för att spåra och felsöka körning av Hive Tez-jobb. Tez-vyn stöds internt för HDInsight 4,0 med början från den här versionen. Den gäller inte för befintliga kluster. Du måste släppa och återskapa klustret för att få den inbyggda Tez-vyn.

## <a name="deprecation"></a>Utfasning
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Utfasning av Spark 2.1 och 2.2 i HDInsight 3.6 Spark-kluster
Från och med den 1 2020 juli kan kunder inte skapa nya Spark-kluster med Spark 2,1 och 2,2 på HDInsight 3,6. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att gå vidare till Spark 2,3 i HDInsight 3,6 och 30 2020 för att undvika potentiell system/support-avbrott.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Utfasning av Spark 2.3 i HDInsight 4.0 Spark-kluster
Från och med den 1 2020 juli kan kunder inte skapa nya Spark-kluster med Spark 2,3 på HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till Spark 2.4 på HDInsight 4.0 den 30 juni 2020 för att undvika potentiella system-/supportavbrott.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Utfasning av Kafka 1.1 i HDInsight 4.0 Kafka-kluster
Från och med juli 1 2020 kommer kunderna inte att kunna skapa nya Kafka-kluster med Kafka 1,1 på HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till Kafka 2.1 på HDInsight 4.0 den 30 juni 2020 för att undvika potentiella system-/supportavbrott.

## <a name="behavior-changes"></a>Beteende ändringar
### <a name="ambari-stack-version-change"></a>Ambari-stack versions ändring
I den här versionen ändras Ambari-versionen från 2. x. x. x till 4,1. Du kan hämta Ambari-versionen från Ambari UI > om.

## <a name="upcoming-changes"></a>Kommande ändringar
Inga kommande överändrade ändringar som du behöver betala.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

Nedan JIRAs-portarna för Hive:
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

Nedan JIRAs-portarna för HBase:
* [HBASE – 21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE – 24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE – 24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="known-issues"></a>Kända problem

När du försöker skapa ett Azure HDInsight-kluster från Azure Portal och använder en SSH-autentiseringstyp av en offentlig nyckel, uppstår ett fel när användaren klickar på **Granska + skapa**. Felet i portalen är "får inte innehålla tre tecken i följd från SSH-användarnamn." Det här problemet behandlas. men om du upplever det här felet, skapar lösningen ett kluster med en ARM-mall. 
