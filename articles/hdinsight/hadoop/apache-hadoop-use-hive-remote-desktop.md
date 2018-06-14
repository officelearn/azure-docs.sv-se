---
title: Använda Hadoop Hive och fjärrskrivbord i HDInsight - Azure | Microsoft Docs
description: Lär dig hur du ansluter till Hadoop-kluster i HDInsight med hjälp av fjärrskrivbord och köra Hive-frågor med hjälp av kommandoradsgränssnittet Hive.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c228e35-d58a-4f22-917a-1d20c9da89b4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 70eab088ce87d8a62d3f258b70aaec5e2e147d0e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400647"
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Använda Hive med Hadoop i HDInsight med fjärrskrivbord
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

I den här artikeln kommer du lär dig hur du ansluter till ett HDInsight-kluster med hjälp av fjärrskrivbord och köra Hive-frågor med hjälp av Hive kommandoradsgränssnittet (CLI).

> [!IMPORTANT]
> Fjärrskrivbord är bara tillgängligt på HDInsight-kluster som använder Windows som operativsystem. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> För HDInsight 3.4 eller större finns [använda Hive med HDInsight och Beeline](apache-hadoop-use-hive-beeline.md) information om hur du kör Hive-frågor direkt på klustret från en kommandorad.

## <a id="prereq"></a>Förhandskrav
Du behöver följande för att slutföra stegen i den här artikeln:

* Ett kluster med Windows-baserade HDInsight (Hadoop på HDInsight)
* En klientdator som kör Windows 10, Windows 8 eller Windows 7

## <a id="connect"></a>Ansluta med fjärrskrivbord
Aktivera Fjärrskrivbord för HDInsight-klustret och sedan ansluta till den genom att följa anvisningarna i [Anslut till HDInsight-kluster med RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Använd kommandot Hive
När du har anslutit till skrivbordet för HDInsight-kluster, kan du använda följande steg för att arbeta med Hive:

1. HDInsight-skrivbordet och starta den **Hadoop kommandoraden**.
2. Ange följande kommando för att starta Hive-CLI:

        %hive_home%\bin\hive

    När CLI har startats visas CLI Hive-fråga: `hive>`.
3. Med hjälp av CLI, ange följande instruktioner för att skapa en ny tabell med namnet **log4jLogs** med exempeldata:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Dessa instruktioner utför följande åtgärder:

   * **DROP TABLE**: tar bort tabellen och datafilen om tabellen redan finns.
   * **Skapa extern tabell**: skapar en ny ”externa” tabell i Hive. Externa tabeller lagra endast tabelldefinitionen i Hive (data finns kvar i den ursprungliga platsen).

     > [!NOTE]
     > Externa tabeller ska användas när du förväntar dig underliggande data uppdateras av en extern källa (till exempel en automatisk överföring av data) eller av en annan MapReduce-åtgärd, men du vill använda Hive-frågor för att använda den senaste informationen.
     >
     > Släppa en extern tabell har **inte** ta bort data, endast tabelldefinitionen.
     >
     >
   * **RADEN FORMAT**: talar om Hive hur data ska formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.
   * **LAGRAS AS TEXTFILE plats**: talar om Hive där data är lagras (exempel/datakatalog) och som den lagras som text.
   * **Välj**: väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Detta bör returnera ett värde av **3** eftersom det finns tre rader som innehåller det här värdet.
   * **INPUT__FILE__NAME som '%.log'** -talar om Hive som vi ska bara returnera data från filer som slutar på. log. Detta begränsar sökningen till sample.log-filen som innehåller data och håller den från att returnera data från andra exempel filer som inte matchar det schema som vi har definierat.
4. Använd följande instruktioner för att skapa en ny ”interna” tabell med namnet **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Dessa instruktioner utför följande åtgärder:

   * **Skapa tabell om inte finns**: skapar en tabell om den inte redan finns. Eftersom den **externa** nyckelordet används inte, det här är en intern tabell som lagras i datalagret Hive och hanteras helt av Hive.

     > [!NOTE]
     > Till skillnad från **externa** tabeller, släppa en intern tabell även tar bort den underliggande data.
     >
     >
   * **LAGRAS AS ORC**: lagrar data i optimerad raden (ORC) kolumnformat. Detta är ett mycket optimerad och effektiv format för att lagra data med Hive.
   * **INFOGA ÖVER... Välj**: väljer rader från den **log4jLogs** tabellen som innehåller **[fel]**, infogar data till den **errorLogs** tabell.

     Kontrollera att endast rader som innehåller **[fel]** i kolumnen t4 har lagrats till den **errorLogs** tabell, använder du följande instruktion returnera alla rader från **errorLogs**:

       Välj * från errorLogs;

     Tre raderna med data ska returneras, som innehåller alla **[fel]** i kolumnen t4.

## <a id="summary"></a>Sammanfattning
Som du ser i Hive-kommandot ger ett enkelt sätt att köra Hive-frågor på ett HDInsight-kluster, övervaka jobbstatus och hämta utdata interaktivt.

## <a id="nextsteps"></a>Nästa steg
Allmän information om Hive i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Om du använder Tez med Hive finns i följande dokument för felsökningsinformation:

* [Använda Tez-Användargränssnittet på Windows-baserade HDInsight](../hdinsight-debug-tez-ui.md)
* [Använd vyn Ambari Tez på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
