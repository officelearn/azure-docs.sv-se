---
title: Övervaka och hantera Hadoop med Ambari REST-API – Azure HDInsight
description: Lär dig mer om att använda Ambari och övervaka och hantera Hadoop-kluster i Azure HDInsight. I detta dokument kan lära du dig att använda Ambari REST API som ingår i HDInsight-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: d15b61c70f1587cdd1c0d76d2e3eab81294674fc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761142"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Hantera HDInsight-kluster med hjälp av Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Lär dig hur du använder Apache Ambari REST API för att hantera och övervaka Apache Hadoop-kluster i Azure HDInsight.

## <a id="whatis"></a>Vad är Apache Ambari
[Apache Ambari](https://ambari.apache.org) förenklar hantering och övervakning av Hadoop-kluster genom att tillhandahålla en lättanvänd webbgränssnittet backas upp av dess [REST API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari är som standard med Linux-baserade HDInsight-kluster.

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Ett Hadoop-kluster på HDInsight**. Se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash på Ubuntu på Windows 10**.  Exemplen i den här artikeln använder Bash-gränssnittet i Windows 10. Se [Windows-undersystem for Linux Installation Guide för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) för installationssteg.  Andra [Unix gränssnitt](https://www.gnu.org/software/bash/) fungerar också.  Exempel, med några små ändringar kan arbeta i en kommandotolk i Windows.  Du kan också använda Windows PowerShell.

* **jq**, en kommandorad JSON-processor.  Se [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Du kan också använda [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Bas-URI för Ambari Rest API

 Bas-URI för Ambari REST API på HDInsight är `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, där `CLUSTERNAME` är namnet på klustret.  Klustrets namn i URI: er är **skiftlägeskänsliga**.  Klustrets namn i den fullständigt kvalificerade namn (FQDN) delen av URI: N (CLUSTERNAME.azurehdinsight.net) är skiftlägeskänsliga, är andra förekomster i URI: N skiftlägeskänsliga.

## <a name="authentication"></a>Autentisering

Ansluta till Ambari på HDInsight kräver HTTPS. Använda admin-kontonamn (standardvärdet är **admin**) och lösenord som du angav när klustret skapas.

## <a name="examples"></a>Exempel

### <a name="setup-preserve-credentials"></a>Installationsprogrammet (Preserve autentiseringsuppgifter)
Bevara dina autentiseringsuppgifter för att undvika att behöva skriva in dem för varje exempel.  Klustrets namn kommer att bevaras i ett separat steg.

**EN. Bash**  
Redigera skriptet nedan genom att ersätta `PASSWORD` med det faktiska lösenordet.  Ange sedan kommandot.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifiera korrekt alltid i klustrets namn
Faktiska versaler och gemener i klustrets namn kan skilja sig än förväntat beroende på hur klustret har skapats.  De här stegen visar faktiska gemener och versaler och sedan lagra den i en variabel för alla efterföljande exemplen.

Redigera skripten nedan och Ersätt `CLUSTERNAME` med klusternamnet på ditt. Ange sedan kommandot. (Klusternamnet FQDN-namn är inte skiftlägeskänsligt.)

**EN. Bash**  

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

### <a name="parsing-json-data"></a>Parsa JSON-data

I följande exempel används [jq](https://stedolan.github.io/jq/) eller [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) att parsa JSON-svar dokumentet och visa endast de `health_report` information från resultaten.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Hämta det fullständiga Domännamnet för klusternoder

När du arbetar med HDInsight kan behöva du veta det fullständigt kvalificerade domännamnet (FQDN) på en klusternod. Du kan enkelt hämta det fullständiga Domännamnet för de olika noderna i klustret med följande exempel:

**Alla noder**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Huvudnoder**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Arbetsnoder**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper-noder**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Hämta interna IP-adressen för klusternoder

IP-adresser som returneras av exemplen i det här avsnittet är inte direkt åtkomliga via internet. De är bara tillgängliga i Azure virtuella nätverk som innehåller HDInsight-kluster.

Mer information om hur du arbetar med HDInsight och virtuella nätverk finns i [utöka HDInsight-funktioner med hjälp av en anpassad Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

För att hitta IP-adress, måste du veta det interna fullständigt kvalificerade domännamnet (FQDN) av klusternoderna. När du har det fullständiga Domännamnet kan kan du hämta IP-adressen för värden. I följande exempel kan du först fråga Ambari efter det fullständiga Domännamnet för alla värdnoder och sedan fråga Ambari efter IP-adressen för varje värd.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Hämta standardlagring

När du skapar ett HDInsight-kluster måste du använda ett Azure Storage-konto eller Data Lake Storage som standardlagring för klustret. Du kan använda Ambari för att hämta den här informationen när klustret har skapats. Exempel: Om du vill läsa/skriva data till behållaren utanför HDInsight.

I följande exempel hämtar standardkonfigurationen för lagring från klustret:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> De här exemplen returnera den första konfigurationen tillämpas på servern (`service_config_version=1`) som innehåller den här informationen. Om du hämtar ett värde som har ändrats när klustret har skapats, kan du behöva listan konfigurationsversionerna och hämtar den senaste.

Det returnera värdet är av följande exempel:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Det här värdet anger att klustret använder ett Azure Storage-konto för standardlagring. Den `ACCOUNTNAME` värdet är namnet på lagringskontot. Den `CONTAINER` delen är namnet på blob-behållare i lagringskontot. Behållaren är roten för HDFS kompatibla lagringsutrymmet för klustret.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` – Det här värdet anger att klustret använder Azure Data Lake Storage Gen2 för standardlagring. Den `ACCOUNTNAME` och `CONTAINER` värden ha innebörden som är samma som för Azure Storage som tidigare nämnts.

* `adl://home` – Det här värdet anger att klustret använder Azure Data Lake Storage Gen1 för standardlagring.

    Använd följande exempel för att hitta namnet på Data Lake Storage-kontot:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Det returnera värdet liknar `ACCOUNTNAME.azuredatalakestore.net`, där `ACCOUNTNAME` är namnet på Data Lake Storage-konto.

    Använd följande exempel för att hitta katalogen i Data Lake Storage som innehåller lagringsutrymme för klustret:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Det returnera värdet liknar `/clusters/CLUSTERNAME/`. Det här värdet är en sökväg i Data Lake Storage-konto. Den här sökvägen är roten för HDFS-kompatibla filsystemets för klustret.  

> [!NOTE]  
> Den [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet som tillhandahålls av [Azure PowerShell](/powershell/azure/overview) också returnerar i informationen för klustret.


### <a name="get-all-configurations"></a> Hämta alla konfigurationer

Få de konfigurationer som är tillgängliga för ditt kluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Det här exemplet returnerar ett JSON-dokument som innehåller den aktuella konfigurationen (identifieras av den *taggen* värde) för de komponenter som installeras i klustret. I följande exempel är ett utdrag från de data som returneras från en typ av Spark-kluster.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Hämta konfiguration för en viss komponent

Hämta konfigurationen för den komponent som du är intresserad av. I följande exempel ersätter `INITIAL` med Taggvärdet returnerades från föregående begäran.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Det här exemplet returnerar ett JSON-dokument som innehåller den aktuella konfigurationen för den `livy2-conf` komponent.

### <a name="update-configuration"></a>Uppdatera konfiguration

1. Skapa `newconfig.json`.  
   Ändra och sedan ange nedanstående kommandon:

   * Ersätt `livy2-conf` med önskad-komponenten.
   * Ersätt `INITIAL` med faktiskt värde hämtades för `tag` från [hämta alla konfigurationer](#get-all-configurations).

     **EN. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **B. powershell**  
     PowerShell-skript använder [jq](https://stedolan.github.io/jq/).  Redigera `C:\HD\jq\jq-win64` nedan för att återspegla din faktiska sökvägen och versionen av [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq används för att aktivera data som hämtats från HDInsight till en ny konfigurationsmall för. De här exemplen utföra mer specifikt kan följande åtgärder:

   * Skapar ett unikt värde som innehåller strängen ”version” och datum, som lagras i `newtag`.

   * Skapar ett rot-dokument för den nya önskade konfigurationen.

   * Hämtar innehållet i den `.items[]` matris och lägger till den under den **desired_config** element.

   * Tar bort den `href`, `version`, och `Config` element, som de här elementen krävs inte för att skicka en ny konfiguration.

   * Lägger till en `tag` element med ett värde av `version#################`. Den numeriska delen är baserad på det aktuella datumet. Varje konfiguration måste ha en unik tagg.

     Slutligen data sparas på den `newconfig.json` dokumentet. Dokumentstrukturen bör se ut ungefär så här:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Redigera `newconfig.json`.  
   Öppna den `newconfig.json` dokumentet och ändra/lägga till värden i den `properties` objekt. I följande exempel ändras värdet för `"livy.server.csrf_protection.enabled"` från `"true"` till `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Spara filen när du är klar ändringar.

3. Skicka `newconfig.json`.  
   Använd följande kommandon för att skicka den uppdaterade konfigurationen till Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    De här kommandona skicka innehållet i den **newconfig.json** till klustret som ny önskad konfiguration. Begäran returnerar ett JSON-dokument. Den **versionTag** element i det här dokumentet måste matcha den version som du skickat och **peeringkonfigurationer** objektet innehåller de konfigurationsändringar du begärt.

### <a name="restart-a-service-component"></a>Starta om en tjänstkomponent

Du tittar på Ambari-webbgränssnittet, visar Spark-tjänst nu att den behöver startas om innan den nya konfigurationen börjar gälla. Använd följande steg för att starta om tjänsten.

1. Använd följande för att aktivera underhållsläget för tjänsten Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Kontrollera underhållsläge  

    De här kommandona Skicka ett JSON-dokument till den server som aktiverar underhållsläge. Du kan verifiera att tjänsten är nu i underhållsläge med följande begäran:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Det returnera värdet är `ON`.

3. Använd följande för att stänga av tjänsten Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
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
    > Den `href` värdet som returneras av den här URI: N använder den interna IP-adressen för klusternoden. Om du vill använda den från utanför klustret, ersätter du ”10.0.0.18:8080”-delen med det fullständiga Domännamnet för klustret.  

4. Kontrollera begäran.  
    Redigera kommandot nedan genom att ersätta `29` med det faktiska värdet för `id` returnerades från föregående steg.  Följande kommandon för att hämta status för begäran:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Svaret `COMPLETED` betyder det att begäran har slutförts.

5. När den tidigare begäran har slutförts kan du använda följande för att starta tjänsten Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    Tjänsten använder den nya konfigurationen.

6. Slutligen kan du använda följande för att inaktivera underhållsläge.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Nästa steg

En fullständig referens för REST API finns i [Apache Ambari API-referens V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

