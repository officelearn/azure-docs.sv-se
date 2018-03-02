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
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: dfe9efdb57a0ce2506abd251267f39020568d081
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Köra Hive-frågor med Hadoop i HDInsight med hjälp av REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du använder WebHCat REST API för att köra Hive-frågor med Hadoop på Azure HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* En Linux-baserade Hadoop på HDInsight-kluster av version 3.4 eller större.

  > [!IMPORTANT]
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En REST-klient. Det här dokumentet använder Windows PowerShell och [Curl](http://curl.haxx.se/) exempel.

    > [!NOTE]
    > Azure PowerShell tillhandahåller dedikerad cmdlets för att arbeta med Hive i HDInsight. Mer information finns i [använda Hive med Azure PowerShell](apache-hadoop-use-hive-powershell.md) dokumentet.

Det här dokumentet använder också Windows PowerShell och [Jq](http://stedolan.github.io/jq/) till processen JSON-data som returneras från REST-begäranden.

## <a id="curl"></a>Köra en Hive-fråga

> [!NOTE]
> När du använder cURL eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör.
>
> REST API skyddas via [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). För att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern, alltid göra begäranden genom att använda säker HTTP (HTTPS).

1. Om du vill ange klusterinloggning som används av skript i det här dokumentet använder du någon av följande kommandon:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Om du vill ange klusternamnet, använder du någon av följande kommandon:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Använd något av följande kommandon för att kontrollera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Du får ett svar som liknar följande:

    ```json
    {"status":"ok","version":"v1"}
    ```

    De parametrar som används i det här kommandot är följande:

    * `-u` -Användarnamn och lösenord som används för att autentisera begäran.
    * `-G` -Anger att begäran är en GET-åtgärd.

   I början av URL-Adressen `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, är samma för alla begäranden. Sökvägen `/status`, anger att begäran ska returnera statusen WebHCat (även kallat Templeton) för servern. Du kan också begära versionen av Hive med hjälp av följande kommando:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Denna begäran returnerar ett svar som liknar följande:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Använd följande för att skapa en tabell med namnet **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Denna begäran använder metoden POST som skickar data som en del av begäran till REST API. Följande datavärden skickas till begäran:

     * `user.name` -Användaren som kör kommandot.
     * `execute` -HiveQL-instruktioner för att köra.
     * `statusdir` -Katalogen som skrivs till status för jobbet.

   Dessa instruktioner utför följande åtgärder:
   
   * `DROP TABLE` -Om tabellen redan finns tas bort.
   * `CREATE EXTERNAL TABLE` – Skapar en ny ”externa” tabell i Hive. Externa tabeller lagra endast tabelldefinitionen i Hive. Data finns kvar i den ursprungliga platsen.

     > [!NOTE]
     > Externa tabeller ska användas när du förväntar dig underliggande data uppdateras av en extern källa. Till exempel en automatisk överföring av data eller en annan MapReduce-åtgärd.
     >
     > Släppa en extern tabell har **inte** ta bort data, endast tabelldefinitionen.

   * `ROW FORMAT` -Hur data ska formateras. Fälten i varje logg avgränsas med ett blanksteg.
   * `STORED AS TEXTFILE LOCATION` -Var data lagras (exempel/datakatalog) och att den lagras som text.
   * `SELECT` -Väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Den här instruktionen returnerar ett värde för **3** som det finns tre rader som innehåller det här värdet.

     > [!NOTE]
     > Observera att blanksteg mellan HiveQL-instruktioner har ersatts av den `+` tecken när det används med Curl. Citattecken värden som innehåller blanksteg, till exempel avgränsaren, bör inte ersättas av `+`.

      Det här kommandot returnerar ett jobb-ID som kan användas för att kontrollera status för jobbet.

5. Om du vill kontrollera status för jobbet, använder du följande kommando:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Om jobbet har slutförts i tillståndet är **lyckades**.

6. När status för jobbet har ändrats till **lyckades**, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet `/example/rest`. Den här adressen lagrar utdata i den `example/curl` katalogen i standardlagring kluster.

    Du kan visa och ladda ned dessa filer med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI med Azure Storage finns i [Använd Azure CLI 2.0 med Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentet.

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


