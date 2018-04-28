---
title: Övervaka och hantera Hadoop med Ambari REST API - Azure HDInsight | Microsoft Docs
description: Lär dig använda Ambari och övervaka och hantera Hadoop-kluster i Azure HDInsight. I det här dokumentet får du lära dig hur du använder Ambari REST API som ingår i HDInsight-kluster.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 55017b139ce89fa74a8105da05792024ecee86b2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Hantera HDInsight-kluster med Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Lär dig använda Ambari REST API för att hantera och övervaka Hadoop-kluster i Azure HDInsight.

Apache Ambari förenklar hantering och övervakning av ett Hadoop-kluster genom att tillhandahålla ett enkelt att använda webbgränssnittet och REST-API. Ambari ingår i HDInsight-kluster som använder Linux-operativsystem. Du kan använda Ambari för att övervaka klustret och gör ändringar i konfigurationen.

## <a id="whatis"></a>Vad är Ambari

[Apache Ambari](http://ambari.apache.org) ger webbgränssnitt som kan användas för att hantera och övervaka Hadoop-kluster. Utvecklare kan integrera dessa funktioner i sina program med hjälp av den [Ambari REST API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari är som standard med Linux-baserade HDInsight-kluster.

## <a name="how-to-use-the-ambari-rest-api"></a>Använda Ambari REST API

> [!IMPORTANT]
> Information och exempel i det här dokumentet kräver ett HDInsight-kluster som använder Linux-operativsystem. Mer information finns i [komma igång med HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Exemplen i det här dokumentet tillhandahålls för både Bourne shell (bash) och PowerShell. Exemplen bash har testats med GNU bash version 4.3.11, men bör arbeta tillsammans med andra Unix-gränssnitt. PowerShell-exemplen har testats med PowerShell 5.0, men ska fungera med PowerShell 3.0 eller senare.

Om du använder den __Bourne shell__ (Bash), måste du ha följande installerat:

* [cURL](http://curl.haxx.se/): cURL är ett verktyg som kan användas för att arbeta med REST API: er från kommandoraden. I det här dokumentet används den för att kommunicera med Ambari REST API.

Om du använder Bash eller PowerShell, du måste ha [jq](https://stedolan.github.io/jq/) installerad. Jq är ett verktyg för att arbeta med JSON-dokument. Den används i **alla** Bash-exempel och **en** av PowerShell-exempel.

### <a name="base-uri-for-ambari-rest-api"></a>Bas-URI för Ambari Rest API

Bas-URI för Ambari REST API på HDInsight är https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, där **KLUSTERNAMN** är namnet på klustret.

> [!IMPORTANT]
> Klusternamnet i fullständigt kvalificerade domännamnet (FQDN) Namndelen för URI: N (CLUSTERNAME.azurehdinsight.net) är skiftlägeskänslig, är andra förekomster i URI: N skiftlägeskänsliga. Om klustret har namnet till exempel `MyCluster`, följande är giltiga URI: er:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Ett fel returneras följande URI eftersom den andra förekomsten av namnet inte är rätt skiftläge.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Autentisering

Ansluter till Ambari på HDInsight kräver HTTPS. Använda admin-kontonamn (standardvärdet är **admin**) och lösenord som du angav när klustret skapas.

## <a name="examples-authentication-and-parsing-json"></a>Exempel: Autentisering och parsa JSON

Följande exempel visar hur du gör en GET-begäran mot grundläggande Ambari REST API:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Bash exemplen i det här dokumentet gör följande antaganden:
>
> * Inloggningsnamn för klustret som är standardvärdet för `admin`.
> * `$CLUSTERNAME` innehåller namnet på klustret. Du kan ange ett värde med hjälp av `set CLUSTERNAME='clustername'`
> * När du uppmanas, anger du lösenordet för klusterinloggning (admin).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> PowerShell-exemplen i det här dokumentet gör följande antaganden:
>
> * `$creds` är ett autentiseringsuppgiftobjekt som innehåller admin inloggningsnamn och lösenord för klustret. Du kan ange ett värde med hjälp av `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` och anger autentiseringsuppgifter när du tillfrågas.
> * `$clusterName` är en sträng som innehåller namnet på klustret. Du kan ange ett värde med hjälp av `$clusterName="clustername"`.

Båda exempel returnera ett JSON-dokument som börjar med information som liknar följande exempel:

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>Parsning av JSON-data

I följande exempel används `jq` tolka svaret JSON-dokumentet och visa endast de `health_report` information från resultaten.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 och senare innehåller den `ConvertFrom-Json` cmdlet, som konverterar JSON-dokumentet till ett objekt som är enklare att arbeta med från PowerShell. I följande exempel används `ConvertFrom-Json` att visa endast de `health_report` information från resultaten.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> När de flesta exemplen i det här dokumentet används `ConvertFrom-Json` att visa element från svarsdokument i [uppdatering Ambari configuration](#example-update-ambari-configuration) exempel används jq. Jq används i det här exemplet för att skapa en ny mall från svar JSON-dokumentet.

En fullständig referens för REST-API finns [Ambari API-referens V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Exempel: Hämta FQDN för klusternoder

När du arbetar med HDInsight, kanske du behöver veta det fullständigt kvalificerade domännamnet (FQDN) på en klusternod. Du kan lätt kunna hämta det fullständiga Domännamnet för olika noder i klustret med följande exempel:

* **Alla noder**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **HEAD-noder**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Arbetsnoder**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Zookeeper-noder**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Exempel: Hämta klusternoder interna IP-adress

> [!IMPORTANT]
> IP-adresser som returneras av exemplen i det här avsnittet är inte direkt åtkomliga via internet. De är bara tillgängliga i det virtuella Azure-nätverket som innehåller HDInsight-klustret.
>
> Mer information om att arbeta med HDInsight och virtuella nätverk finns [utöka HDInsight funktioner med hjälp av en anpassad Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

Om du vill hitta IP-adress, måste du känna fullständigt kvalificerade domännamnet interna namnet (FQDN) på klusternoderna. När du har FQDN kan du hämta IP-adressen för värden. I följande exempel först fråga Ambari för FQDN för alla värdnoder och fråga Ambari för IP-adressen för varje värd.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> Föregående exempel efterfrågas lösenordet. Det här exemplet körs den `curl` kommandot flera gånger, så att lösenordet har angetts som `$PASSWORD` att undvika flera prompter.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Exempel: Hämta standardlagring

När du skapar ett HDInsight-kluster måste du använda ett Azure Storage-konto eller ett Data Lake Store som standardlagring för klustret. Du kan använda Ambari för att hämta den här informationen när klustret har skapats. Till exempel om du vill läsa/skriva data till behållaren utanför HDInsight.

Följande exempel hämtar lagringskonfigurationen som standard från klustret:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> De här exemplen returnera den första konfigurationen tillämpas på servern (`service_config_version=1`) som innehåller den här informationen. Om du hämtar ett värde som har ändrats efter att klustret har skapats kan du behöva listan konfigurationsversionerna och hämtar den senaste.

Returvärdet liknar följande exempel:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` -Det här värdet anger att klustret använder ett Azure Storage-konto för standardlagring. Den `ACCOUNTNAME` värde är namnet på lagringskontot. Den `CONTAINER` delen är namnet på blob-behållaren i storage-konto. Behållaren är roten till HDFS kompatibel lagringsutrymme för klustret.

* `adl://home` -Det här värdet anger att klustret använder en Azure Data Lake Store för standardlagring.

    Använd följande exempel för att hitta namnet på Data Lake Store-kontot:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Returvärdet liknar `ACCOUNTNAME.azuredatalakestore.net`, där `ACCOUNTNAME` är namnet på Data Lake Store-konto.

    Använd följande exempel för att hitta katalogen i Data Lake Store med lagringsutrymme för klustret:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Returvärdet liknar `/clusters/CLUSTERNAME/`. Det här värdet är en sökväg i Data Lake Store-konto. Den här sökvägen är roten för den HDFS-kompatibel fil för klustret. 

> [!NOTE]
> Den `Get-AzureRmHDInsightCluster` cmdlet som tillhandahålls av [Azure PowerShell](/powershell/azure/overview) returnerar även i informationen för klustret.


## <a name="example-get-configuration"></a>Exempel: Get-konfiguration

1. Hämta de konfigurationer som är tillgängliga för klustret.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Det här exemplet returnerar ett JSON-dokument som innehåller den aktuella konfigurationen (identifieras av den *taggen* värde) för de komponenter som installeras i klustret. I följande exempel är ett utdrag ur data som returneras från en typ av Spark-kluster.
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. Hämta konfigurationen för den komponent som du är intresserad av. I följande exempel ersätter `INITIAL` med tagg-värde returnerades från föregående begäran.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Det här exemplet returnerar ett JSON-dokument som innehåller den aktuella konfigurationen för den `core-site` komponent.

## <a name="example-update-configuration"></a>Exempel: Uppdateringskonfiguration

1. Hämta den aktuella konfigurationen Ambari lagrar som ”desired configuration”:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Det här exemplet returnerar ett JSON-dokument som innehåller den aktuella konfigurationen (identifieras av den *taggen* värde) för de komponenter som installeras i klustret. I följande exempel är ett utdrag ur data som returneras från en typ av Spark-kluster.
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    Den här listan, måste du kopiera namnet på komponenten (till exempel **spark\_thrift\_sparkconf** och **taggen** värde.

2. Hämta konfigurationen för komponenten och tagg med hjälp av följande kommandon:
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Ersätt **spark-thrift-sparkconf** och **inledande** med komponenten och tagg som du vill hämta konfigurationen för.
   
    Jq används för att aktivera data som hämtats från HDInsight till en ny konfigurationsmall för. Mer specifikt utför exemplen följande åtgärder:
   
    * Skapar ett unikt värde som innehåller strängen ”version” och det datum som lagras i `newtag`.

    * Skapar ett rot-dokument för nya önskad konfiguration.

    * Hämtar innehållet i den `.items[]` matrisen och lägger till den under den **desired_config** element.

    * Tar bort den `href`, `version`, och `Config` element, som de här elementen inte behövs för att skicka en ny konfiguration.

    * Lägger till en `tag` element med värdet `version#################`. Den numeriska delen är baserad på det aktuella datumet. Varje konfiguration måste ha en unik tagg.
     
    Slutligen data sparas på den `newconfig.json` dokumentet. Dokumentstrukturen ska se ut ungefär så här:
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Öppna den `newconfig.json` dokument och ändra/Lägg till värden i den `properties` objekt. I följande exempel ändras värdet för `"spark.yarn.am.memory"` från `"1g"` till `"3g"`. Det ger också `"spark.kryoserializer.buffer.max"` med värdet `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Spara filen när du är klar med ändringarna.

4. Använd följande kommandon för att skicka den uppdaterade konfigurationen till Ambari.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Dessa kommandon skicka innehållet i den **newconfig.json** filen till klustret som ny önskad konfiguration. Begäran returnerar ett JSON-dokument. Den **versionTag** element i det här dokumentet ska matcha den version som du skickade och **konfigurationerna** objektet innehåller ändringar i konfigurationen som du begärde.

### <a name="example-restart-a-service-component"></a>Exempel: Starta om en tjänstkomponent

Du tittar på Ambari-webbgränssnittet anger Spark-tjänsten nu måste startas om innan den nya konfigurationen ska börja gälla. Använd följande steg för att starta om tjänsten.

1. Använd följande för att aktivera underhållsläget för Spark-tjänst:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Dessa kommandon skicka ett JSON-dokument till servern som aktiverar underhållsläge. Du kan kontrollera att tjänsten är nu i underhållsläge med följande begäran:
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    Det returnera värdet är `ON`.

2. Använd följande för att stänga av tjänsten:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    Svaret liknar följande exempel:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > Den `href` värdet som returneras av den här URI: N använder interna IP-adressen för noden i klustret. Om du vill använda den från utanför klustret måste du ersätta den '10.0.0.18:8080'-delen med FQDN för klustret. 
    
    Följande kommandon för att hämta status för begäran:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Ett svar av `COMPLETED` anger att begäran har slutförts.

3. När den tidigare begäranden har slutförts kan du använda följande för att starta tjänsten.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    Tjänsten använder den nya konfigurationen.

4. Slutligen, Använd följande för att inaktivera underhållsläge.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Nästa steg

En fullständig referens för REST-API finns [Ambari API-referens V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

