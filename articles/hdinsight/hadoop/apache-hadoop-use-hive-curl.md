---
title: Använd Apache Hadoop Hive med curl i HDInsight - Azure
description: Lär dig hur du skickar Apache Pig-jobb till Azure HDInsight med Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298758"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Kör Apache Hive-frågor med Apache Hadoop i HDInsight med REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du använder WEBHCat REST API för att köra Apache Hive-frågor med Apache Hadoop i Azure HDInsight-klustret.

## <a name="prerequisites"></a>Krav

* En Apache Hadoop kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En REST-klient. I det här dokumentet används [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) på Windows PowerShell och [Curl](https://curl.haxx.se/) on [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Om du använder Bash behöver du också JQ, en kommandorads-JSON-processor.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Api för bas-URI för vila

Basens URI (Uniform Resource Identifier) för REST `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`API `CLUSTERNAME` på HDInsight är , där är namnet på klustret.  Klusternamn i URI:er är **skiftlägeskänsliga**.  Klusternamnet i den fullständigt kvalificerade domännamnsdelen (FQDN)`CLUSTERNAME.azurehdinsight.net`i URI () är skiftlägesokänslig, men andra förekomster i URI är skiftlägeskänsliga.

## <a name="authentication"></a>Autentisering

När du använder cURL eller annan REST-kommunikation med WebHCat måste du autentisera begäranden genom att ange användarnamn och lösenord för HDInsight-klusteradministratören. REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). För att säkerställa att dina autentiseringsuppgifter skickas till servern på ett säkert sätt kan du alltid göra förfrågningar med hjälp av Secure HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Inställningar (Bevara autentiseringsuppgifter)

Bevara dina autentiseringsuppgifter för att undvika att ange dem igen för varje exempel.  Klusternamnet bevaras i ett separat steg.

**A. Bash**  
Redigera skriptet nedan `PASSWORD` genom att ersätta med ditt faktiska lösenord.  Ange sedan kommandot.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Kör koden nedan och ange dina autentiseringsuppgifter i popup-fönstret:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifiera korrekt skiftlägesklusternamn

Den faktiska höljet för klusternamnet kan vara annorlunda än du förväntar dig, beroende på hur klustret skapades.  Stegen här visar själva höljet och lagrar det sedan i en variabel för alla senare exempel.

Redigera skripten nedan `CLUSTERNAME` för att ersätta med ditt klusternamn. Ange sedan kommandot. (Klusternamnet för FQDN är inte skiftlägeskänsligt.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. Om du vill kontrollera att du kan ansluta till HDInsight-klustret använder du något av följande kommandon:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
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

    * `-u`- Användarnamnet och lösenordet som används för att autentisera begäran.
    * `-G`- Anger att den här begäran är en GET-åtgärd.

1. Början av webbadressen, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`är densamma för alla begäranden. Sökvägen `/status`anger att begäran är att returnera status för WebHCat (kallas även Templeton) för servern. Du kan också begära versionen av Hive med hjälp av följande kommando:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Den här begäran returnerar ett svar som liknar följande text:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Använd följande för att skapa en tabell med namnet **log4jLogs:**

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Den här begäran använder POST-metoden, som skickar data som en del av begäran till REST API. Följande datavärden skickas med begäran:

     * `user.name`- Användaren som kör kommandot.
     * `execute`- HiveQL-uttalandena som ska utföras.
     * `statusdir`- Katalogen som statusen för det här jobbet skrivs till.

   Dessa satser utför följande åtgärder:

   * `DROP TABLE`- Om tabellen redan finns, tas den bort.
   * `CREATE EXTERNAL TABLE`- Skapar en ny "extern" tabell i Hive. Externa tabeller lagrar endast tabelldefinitionen i Hive. Data finns kvar på den ursprungliga platsen.

     > [!NOTE]  
     > Externa tabeller bör användas när du förväntar dig att de underliggande data som ska uppdateras av en extern källa. Till exempel en automatisk dataöverföringsprocess eller en annan MapReduce-åtgärd.
     >
     > Om du släpper en extern tabell tas **inte** data bort, bara tabelldefinitionen.

   * `ROW FORMAT`- Hur data formateras. Fälten i varje logg avgränsas med ett blanksteg.
   * `STORED AS TEXTFILE LOCATION`- Var data lagras (exempel/datakatalog) och att de lagras som text.
   * `SELECT`- Markerar ett antal rader där kolumn **t4** innehåller värdet **[FEL]**. Den här utdraget returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.

     > [!NOTE]  
     > Observera att mellanrummen mellan HiveQL-satser ersätts med tecknet när det `+` används med Curl. Citerade värden som innehåller ett blanksteg, till exempel avgränsare, bör inte ersättas med `+`.

      Det här kommandot returnerar ett jobb-ID som kan användas för att kontrollera jobbets status.

1. Om du vill kontrollera jobbets status använder du följande kommando:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

    Om jobbet är klart **lyckades**tillståndet .

1. När jobbets tillstånd har **ändrats**till LYCKADES kan du hämta resultatet av jobbet från Azure Blob-lagring. Parametern `statusdir` som skickas med frågan innehåller platsen för utdatafilen. i detta `/example/rest`fall, . Den här adressen lagrar `example/curl` utdata i katalogen i klustrets standardlagring.

    Du kan lista och hämta dessa filer med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI med Azure Storage finns i [dokumentet Använd Azure CLI med Azure Storage.](https://docs.microsoft.com/azure/storage/storage-azure-cli)

## <a name="next-steps"></a>Nästa steg

För information om andra sätt du kan arbeta med Hadoop på HDInsight:

* [Använd Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
* [Använd MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Mer information om REST API som används i det här dokumentet finns i [WebHCat-referensdokumentet.](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)