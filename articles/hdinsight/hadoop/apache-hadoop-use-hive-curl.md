---
title: Använda Apache Hadoop Hive med Curl i HDInsight - Azure
description: Lär dig mer om att skicka via en fjärranslutning Apache Pig-jobb till HDInsight med Curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: e020cbc9c18db2142ee7f52cdac22a3518683fce
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095520"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Kör Apache Hive-frågor med Apache Hadoop i HDInsight med hjälp av REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du använder REST-API WebHCat du kör Apache Hive-frågor med Apache Hadoop i Azure HDInsight-kluster.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Linux-baserat Hadoop i HDInsight-kluster av version 3.4 och senare.

  > [!IMPORTANT]  
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En REST-klient. Det här dokumentet används Windows PowerShell och [Curl](https://curl.haxx.se/) exempel.

    > [!NOTE]  
    > Azure PowerShell tillhandahåller dedikerade cmdletar för att arbeta med Hive på HDInsight. Mer information finns i den [använda Apache Hive med Azure PowerShell](apache-hadoop-use-hive-powershell.md) dokumentet.

Det här dokumentet använder också Windows PowerShell och [Jq](https://stedolan.github.io/jq/) att bearbeta JSON-data som returneras från REST-begäranden.

## <a id="curl"></a>Kör en Hive-fråga

> [!NOTE]  
> När du använder cURL eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör.
>
> REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). För att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern kan du alltid göra begäranden genom att använda säker HTTP (HTTPS).

1. Använd någon av följande kommandon för att ställa in klusterinloggning som används av skripten i det här dokumentet:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Ange klusternamnet genom att använda något av följande kommandon:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Kontrollera att du kan ansluta till ditt HDInsight-kluster genom att använda något av följande kommandon:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Du får ett svar som liknar följande text:

    ```json
    {"status":"ok","version":"v1"}
    ```

    De parametrar som används i det här kommandot är följande:

    * `-u` -Det användarnamn och lösenord som används för att autentisera begäran.
    * `-G` -Anger att den här begäran är en åtgärd för hämtning.

   I början av URL: en, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, är samma för alla förfrågningar. Sökvägen, `/status`, anger att begäran ska returnera statusen WebHCat (även kallat Templeton) för servern. Du kan också begära versionen av Hive med hjälp av följande kommando:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Den här begäran returnerar ett svar som liknar följande text:

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

    Denna begäran använder metoden POST som skickar data som en del av begäran till REST API. Följande datavärden skickas med förfrågan:

     * `user.name` -Användaren som kör kommandot.
     * `execute` -HiveQL-instruktioner för att köra.
     * `statusdir` -Katalogen som status för det här jobbet skrivs till.

   Dessa instruktioner utför följande åtgärder:
   
   * `DROP TABLE` -Om tabellen redan finns tas den bort.
   * `CREATE EXTERNAL TABLE` -Skapar en ny ”externa” tabell i Hive. Externa tabeller lagra endast tabelldefinitionen i Hive. Data finns kvar i den ursprungliga platsen.

     > [!NOTE]  
     > Externa tabeller som ska användas när du förväntar dig att underliggande data uppdateras av en extern källa. Till exempel en automatiserade uppladdningen eller en annan MapReduce-åtgärd.
     >
     > Tar bort en extern tabell har **inte** ta bort data, endast tabelldefinitionen.

   * `ROW FORMAT` – Hur informationen har formaterats. Fälten i varje logg avgränsas med ett blanksteg.
   * `STORED AS TEXTFILE LOCATION` -Där data lagras (katalogen/exempeldata) och att den lagras som text.
   * `SELECT` -Väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Det här uttrycket returnerar värdet **3** som det finns tre rader som innehåller det här värdet.

     > [!NOTE]  
     > Observera att blanksteg mellan HiveQL-instruktioner har ersatts av den `+` tecken när det används med Curl. Citerade värden som innehåller ett blanksteg, till exempel avgränsare, bör inte ersättas av `+`.

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

    Om jobbet har slutförts tillståndet är **lyckades**.

6. När tillståndet för jobbet har ändrats till **lyckades**, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet `/example/rest`. Den här adressen lagrar utdata i den `example/curl` katalogen kluster standardlagring.

    Du kan visa och hämta dessa filer med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI med Azure Storage finns i den [används Azure CLI med Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentet.

## <a id="nextsteps"></a>Nästa steg

Allmän information om Hive med HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Mer information om REST API som används i det här dokumentet finns i den [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentet.

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


