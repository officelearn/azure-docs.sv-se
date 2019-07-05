---
title: Använda Apache Hadoop Hive med Curl i HDInsight - Azure
description: Lär dig mer om att skicka via en fjärranslutning Apache Pig-jobb till HDInsight med Curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: 334d7b886aa4e2130a12f0c8a7919986fdac55d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508120"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Kör Apache Hive-frågor med Apache Hadoop i HDInsight med hjälp av REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du använder REST-API WebHCat du kör Apache Hive-frågor med Apache Hadoop i Azure HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En REST-klient. Det här dokumentet används [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) på Windows PowerShell och [Curl](https://curl.haxx.se/) på [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Om du använder Bash, måste du också jq, en kommandorad JSON-processor.  Se [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Bas-URI för Rest API

Den grundläggande identifierare URI (Uniform Resource) för REST-API på HDInsight är `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, där `CLUSTERNAME` är namnet på klustret.  Klustrets namn i URI: er är **skiftlägeskänsliga**.  När klustrets namn i den fullständigt kvalificerade namn (FQDN) delen av URI: N (`CLUSTERNAME.azurehdinsight.net`) är skiftlägeskänslig andra förekomster i URI: N är skiftlägeskänsliga.

## <a name="authentication"></a>Autentisering

När du använder cURL eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). För att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern kan du alltid göra begäranden genom att använda säker HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Installationsprogrammet (Preserve autentiseringsuppgifter)
Bevara dina autentiseringsuppgifter för att undvika att behöva skriva in dem för varje exempel.  Klustrets namn kommer att bevaras i ett separat steg.

**EN. Bash**  
Redigera skriptet nedan genom att ersätta `PASSWORD` med det faktiska lösenordet.  Ange sedan kommandot.

```bash
export password='PASSWORD'
```  

**B. PowerShell** köra koden nedan och ange dina autentiseringsuppgifter i popup-fönster:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifiera korrekt alltid i klustrets namn
Faktiska versaler och gemener i klustrets namn kan skilja sig än förväntat, beroende på hur klustret har skapats.  De här stegen visar faktiska gemener och versaler och sedan lagra den i en variabel för alla efterföljande exemplen.

Redigera skripten nedan och Ersätt `CLUSTERNAME` med klusternamnet på ditt. Ange sedan kommandot. (Klusternamnet FQDN-namn är inte skiftlägeskänsligt.)

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

## <a id="curl"></a>Kör en Hive-fråga

1. Kontrollera att du kan ansluta till ditt HDInsight-kluster genom att använda något av följande kommandon:

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

    * `-u` -Det användarnamn och lösenord som används för att autentisera begäran.
    * `-G` -Anger att den här begäran är en åtgärd för hämtning.

1. I början av URL: en, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, är samma för alla förfrågningar. Sökvägen, `/status`, anger att begäran ska returnera statusen WebHCat (även kallat Templeton) för servern. Du kan också begära versionen av Hive med hjälp av följande kommando:

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

1. Använd följande för att skapa en tabell med namnet **log4jLogs**:

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
   * `SELECT` -Väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]** . Det här uttrycket returnerar värdet **3** som det finns tre rader som innehåller det här värdet.

     > [!NOTE]  
     > Observera att blanksteg mellan HiveQL-instruktioner har ersatts av den `+` tecken när det används med Curl. Citerade värden som innehåller ett blanksteg, till exempel avgränsare, bör inte ersättas av `+`.

      Det här kommandot returnerar ett jobb-ID som kan användas för att kontrollera status för jobbet.

1. Om du vill kontrollera status för jobbet, använder du följande kommando:

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

    Om jobbet har slutförts tillståndet är **lyckades**.

1. När tillståndet för jobbet har ändrats till **lyckades**, kan du hämta resultatet av jobbet från Azure Blob storage. Den `statusdir` -parameter som överförs med frågan innehåller platsen för utdatafilen; i det här fallet `/example/rest`. Den här adressen lagrar utdata i den `example/curl` katalogen kluster standardlagring.

    Du kan visa och hämta dessa filer med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI med Azure Storage finns i den [används Azure CLI med Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentet.

## <a id="nextsteps"></a>Nästa steg

Allmän information om Hive med HDInsight:

* [Använda Apache Hive med Apache Hadoop i HDInsight](hdinsight-use-hive.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md)

Mer information om REST API som används i det här dokumentet finns i den [WebHCat referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentet.