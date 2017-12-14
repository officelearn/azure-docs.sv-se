---
title: "Använda Hadoop Hive med Curl i HDInsight - Azure | Microsoft Docs"
description: "Lär dig mer om att skicka Pig-jobb till HDInsight med Curl via fjärranslutning."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: 07386c2fbb5fe4ce4fe1ca6844f6308cf59cbfb4
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Köra Hive-frågor med Hadoop i HDInsight med hjälp av REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du använder WebHCat REST API för att köra Hive-frågor med Hadoop på Azure HDInsight-kluster.

[CURL](http://curl.haxx.se/) används för att demonstrera hur du kan interagera med HDInsight med hjälp av rådata HTTP-begäranden. Den [jq](http://stedolan.github.io/jq/) verktyget för att bearbeta JSON-data som returneras från REST-begäranden.

> [!NOTE]
> Om du redan är bekant med Linux-baserade Hadoop-servrar, men nya HDInsight, se den [vad du behöver veta om Hadoop i Linux-baserat HDInsight](../hdinsight-hadoop-linux-information.md) dokumentet.

## <a id="curl"></a>Köra Hive-frågor

> [!NOTE]
> När du använder cURL eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör.
>
> Kommandona i det här avsnittet, ersätter **admin** med användaren för att autentisera för klustret. Ersätt **CLUSTERNAME** med namnet på klustret. När du uppmanas, anger du lösenordet för användarkontot.
>
> REST API skyddas via [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). För att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern, alltid göra begäranden genom att använda säker HTTP (HTTPS).

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Du får ett svar som liknar följande:

        {"status":"ok","version":"v1"}

    De parametrar som används i det här kommandot är följande:

    * **-u** – Det användarnamn och lösenord som används för att autentisera begäran.
    * **-G** -anger att begäran är en GET-åtgärd.

   I början av URL-Adressen **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, är samma för alla begäranden. Sökvägen **/status**, anger att begäran ska returnera statusen WebHCat (även kallat Templeton) för servern. Du kan också begära versionen av Hive med hjälp av följande kommando:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    Denna begäran returnerar ett svar som liknar följande:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Använd följande för att skapa en tabell med namnet **log4jLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Följande parametrar som används med denna begäran:

   * **-d** - sedan `-G` används inte begäran som standard POST-metoden. `-d`Anger datavärdena som skickas med begäran.

     * **User.name** -användaren som kör kommandot.
     * **köra** -det HiveQL-instruktioner för att köra.
     * **statusdir** -katalogen som skrivs till status för jobbet.

   Dessa instruktioner utför följande åtgärder:
   
   * **DROP TABLE** -om tabellen redan finns tas den bort.
   * **Skapa extern tabell** -skapar en ny ”externa” tabell i Hive. Externa tabeller lagra endast tabelldefinitionen i Hive. Data finns kvar i den ursprungliga platsen.

     > [!NOTE]
     > Externa tabeller ska användas när du förväntar dig underliggande data uppdateras av en extern källa. Till exempel en automatisk överföring av data eller en annan MapReduce-åtgärd.
     >
     > Släppa en extern tabell har **inte** ta bort data, endast tabelldefinitionen.

   * **RADEN FORMAT** – hur data är formaterad. Fälten i varje logg avgränsas med ett blanksteg.
   * **LAGRAS AS TEXTFILE plats** – var data lagras (exempel/datakatalog) och att den lagras som text.
   * **Välj** -väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Den här instruktionen returnerar ett värde för **3** som det finns tre rader som innehåller det här värdet.

     > [!NOTE]
     > Observera att blanksteg mellan HiveQL-instruktioner har ersatts av den `+` tecken när det används med Curl. Citattecken värden som innehåller blanksteg, till exempel avgränsaren, bör inte ersättas av `+`.

   * **INPUT__FILE__NAME som '% 25.log'** -instruktionen begränsar sökningen så att bara använda filer som slutar på. log.

     > [!NOTE]
     > Den `%25` är URL-kodade formatet %, så är det faktiska förhållandet `like '%.log'`. % Måste vara URL-kodade, eftersom den behandlas som ett specialtecken i URL: er.

   Det här kommandot returnerar ett jobb-ID som kan användas för att kontrollera status för jobbet.

       {"id":"job_1415651640909_0026"}

3. Om du vill kontrollera status för jobbet, använder du följande kommando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Ersätt **JOBID** med värdet som returneras i föregående steg. Om exempelvis returvärde `{"id":"job_1415651640909_0026"}`, sedan **JOBID** skulle vara `job_1415651640909_0026`.

    Om jobbet har slutförts i tillståndet är **lyckades**.

   > [!NOTE]
   > Den här Curl-begäran returnerar ett JavaScript Object Notation (JSON) dokument med information om jobbet. Jq används för att hämta tillståndet värdet.

4. När status för jobbet har ändrats till **lyckades**, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet **-exempel curl**. Den här adressen lagrar utdata i den **exempel/curl** katalogen i standardlagring kluster.

    Du kan visa och ladda ned dessa filer med hjälp av den [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI med Azure Storage finns i [Använd Azure CLI 2.0 med Azure Storage](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentet.

5. Använd följande instruktioner för att skapa en ny ”interna” tabell med namnet **errorLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Dessa instruktioner utför följande åtgärder:

   * **Skapa tabell om inte finns** -skapar en tabell om den inte redan finns. Den här instruktionen skapar en intern tabell som är lagrad i Hive-datalagret. Den här tabellen hanteras av Hive.

     > [!NOTE]
     > Till skillnad från externa tabeller kan släppa en intern tabell tar bort de underliggande data.

   * **LAGRAS AS ORC** -lagrar data i optimerad raden kolumner (ORC)-format. ORC är ett mycket optimerad och effektiv format för att lagra data med Hive.
   * **INFOGA ÖVER... Välj** -väljer rader från den **log4jLogs** tabellen som innehåller **[fel]**, infogar data till den **errorLogs** tabell.
   * **Välj** -markeras alla rader från den nya **errorLogs** tabell.

6. Använda jobb-ID som returneras till Kontrollera status för jobbet. När den har slutförts Använd Azure CLI som beskrivs tidigare att hämta och visa resultat. Resultatet bör innehålla tre rader, som alla innehåller **[fel]**.

## <a id="nextsteps"></a>Nästa steg

Allmän information om Hive med HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Om du använder Tez med Hive finns i följande dokument för felsökningsinformation:

* [Använd vyn Ambari Tez på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

Mer information om REST-API som används i det här dokumentet finns på [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentet.

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




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


