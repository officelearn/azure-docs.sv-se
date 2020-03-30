---
title: Övervaka och hantera Hadoop med Ambari REST API - Azure HDInsight
description: Lär dig hur du använder Ambari för att övervaka och hantera Hadoop-kluster i Azure HDInsight. I det här dokumentet får du lära dig hur du använder Ambari REST API som ingår i HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: 1d684957939c5cb83aae05962c1694f7a8d8da23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272401"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Hantera HDInsight-kluster med hjälp av Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Lär dig hur du använder Apache Ambari REST API för att hantera och övervaka Apache Hadoop-kluster i Azure HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Vad är Apache Ambari

[Apache Ambari](https://ambari.apache.org) förenklar hantering och övervakning av Hadoop kluster genom att tillhandahålla en lättanvänd webbgränssnitt backas upp av dess [REST API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari tillhandahålls som standard med Linux-baserade HDInsight-kluster.

## <a name="prerequisites"></a>Krav

* **Ett Hadoop-kluster på HDInsight**. Se [Komma igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash på Ubuntu på Windows 10**.  Exemplen i den här artikeln använder bash-skalet på Windows 10. Mer ominstallationssteg finns i [Installationsguiden för Windows Undersystem för Linux för Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Andra [Unix-skal](https://www.gnu.org/software/bash/) fungerar också.  Exemplen, med några smärre ändringar, kan fungera på en Kommandotolken i Windows.  Du kan också använda Windows PowerShell.

* **jq**, en kommandorads-JSON-processor.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Alternativt kan du använda [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Bas URI för Ambari Rest API

 Basen Enhetlig resursidentifierare (URI) för Ambari REST `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`API `CLUSTERNAME` på HDInsight är , där är namnet på ditt kluster.  Klusternamn i URI:er är **skiftlägeskänsliga**.  Klusternamnet i den fullständigt kvalificerade domännamnsdelen (FQDN)`CLUSTERNAME.azurehdinsight.net`i URI () är skiftlägesokänslig, men andra förekomster i URI är skiftlägeskänsliga.

## <a name="authentication"></a>Autentisering

Att ansluta till Ambari på HDInsight kräver HTTPS. Använd administratörskontonamnet (standardvärdet är **administratör)** och lösenord som du angav när klustret skapades.

`admin`Använd ett fullständigt kvalificerat användarnamn som `username@domain.onmicrosoft.com`.

## <a name="examples"></a>Exempel

### <a name="setup-preserve-credentials"></a>Inställningar (Bevara autentiseringsuppgifter)
Bevara dina autentiseringsuppgifter för att undvika att ange dem igen för varje exempel.  Klusternamnet bevaras i ett separat steg.

**A. Bash**  
Redigera skriptet nedan `PASSWORD` genom att ersätta med ditt faktiska lösenord.  Ange sedan kommandot.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifiera korrekt skiftlägesklusternamn
Den faktiska höljet för klusternamnet kan vara annorlunda än du förväntar dig, beroende på hur klustret skapades.  Stegen här visar själva höljet och lagrar det sedan i en variabel för alla efterföljande exempel.

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

### <a name="parsing-json-data"></a>Tolka JSON-data

I följande exempel används [jq](https://stedolan.github.io/jq/) eller [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) för att tolka `health_report` JSON-svarsdokumentet och endast visa informationen från resultaten.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a><a name="example-get-the-fqdn-of-cluster-nodes"></a>Hämta FQDN för klusternoder

När du arbetar med HDInsight kan du behöva känna till det fullständigt kvalificerade domännamnet (FQDN) för en klusternod. Du kan enkelt hämta FQDN för de olika noderna i klustret med hjälp av följande exempel:

**Alla noder**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper noder**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a><a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Hämta den interna IP-adressen för klusternoder

IP-adresserna som returneras av exemplen i det här avsnittet är inte direkt tillgängliga via Internet. De är endast tillgängliga i Azure Virtual Network som innehåller HDInsight-klustret.

Mer information om hur du arbetar med HDInsight och virtuella nätverk finns i [Planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

Om du vill hitta IP-adressen måste du känna till det interna fullständigt kvalificerade domännamnet (FQDN) för klusternoderna. När du har FQDN kan du sedan få IP-adressen för värden. Följande exempel frågar först Ambari för FQDN för alla värdnoder och frågar sedan Ambari efter IP-adressen för varje värd.

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

### <a name="get-the-default-storage"></a>Hämta standardlagringen

När du skapar ett HDInsight-kluster måste du använda ett Azure Storage-konto eller DataSjölagring som standardlagring för klustret. Du kan använda Ambari för att hämta den här informationen när klustret har skapats. Om du till exempel vill läsa/skriva data till behållaren utanför HDInsight.

I följande exempel hämtas standardlagringskonfigurationen från klustret:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> De här exemplen returnerar den`service_config_version=1`första konfigurationen som tillämpas på servern ( ) som innehåller den här informationen. Om du hämtar ett värde som har ändrats när klustret har skapats kan du behöva lista konfigurationsversionerna och hämta det senaste.

Returvärdet liknar ett av följande exempel:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`- Det här värdet anger att klustret använder ett Azure Storage-konto för standardlagring. Värdet `ACCOUNTNAME` är namnet på lagringskontot. Delen `CONTAINER` är namnet på blob-behållaren i lagringskontot. Behållaren är roten till den HDFS-kompatibla lagringen för klustret.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`- Det här värdet anger att klustret använder Azure Data Lake Storage Gen2 för standardlagring. `ACCOUNTNAME` Värdena `CONTAINER` och har samma betydelser som för Azure Storage som nämnts tidigare.

* `adl://home`- Det här värdet anger att klustret använder Azure Data Lake Storage Gen1 för standardlagring.

    Så här hittar du kontonamnet För Data Lake Storage:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Returvärdet liknar `ACCOUNTNAME.azuredatalakestore.net`, var `ACCOUNTNAME` är namnet på datasjölagringskontot.

    Om du vill hitta katalogen i DataSjölagring som innehåller lagringen för klustret använder du följande exempel:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Returvärdet liknar `/clusters/CLUSTERNAME/`. Det här värdet är en sökväg i datasjölagringskontot. Den här sökvägen är roten till det HDFS-kompatibla filsystemet för klustret.  

> [!NOTE]  
> [Cmdleten Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) som tillhandahålls av [Azure PowerShell](/powershell/azure/overview) returnerar också lagringsinformationen för klustret.

### <a name="get-all-configurations"></a><a name="get-all-configurations"></a>Hämta alla konfigurationer

Hämta de konfigurationer som är tillgängliga för klustret.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

I det här exemplet returneras ett JSON-dokument som innehåller den aktuella konfigurationen (identifieras av *taggvärdet)* för de komponenter som är installerade i klustret. Följande exempel är ett utdrag från data som returneras från en Spark-klustertyp.

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

### <a name="get-configuration-for-specific-component"></a>Hämta konfiguration för specifik komponent

Hämta konfigurationen för den komponent som du är intresserad av. I följande exempel `INITIAL` ersätter du med taggvärdet som returnerats från föregående begäran.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

I det här exemplet returneras ett `livy2-conf` JSON-dokument som innehåller den aktuella konfigurationen för komponenten.

### <a name="update-configuration"></a>Uppdatera konfiguration

1. Skapa `newconfig.json`.  
   Ändra och ange sedan kommandona nedan:

   * Ersätt `livy2-conf` med önskad komponent.
   * Ersätt `INITIAL` med det faktiska `tag` värdet som hämtats för från [Hämta alla konfigurationer](#get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     PowerShell-skriptet använder [jq](https://stedolan.github.io/jq/).  Redigera `C:\HD\jq\jq-win64` nedan för att återspegla din faktiska sökväg och version av [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq används för att omvandla data som hämtas från HDInsight till en ny konfigurationsmall. I de här exemplen utförs följande åtgärder:

   * Skapar ett unikt värde som innehåller strängen "version" `newtag`och datumet, som lagras i .

   * Skapar ett rotdokument för den nya önskade konfigurationen.

   * Hämtar innehållet i `.items[]` matrisen och lägger till det under **det desired_config** elementet.

   * Tar bort `href` `version`elementen `Config` , och elementen, eftersom dessa element inte behövs för att skicka en ny konfiguration.

   * Lägger `tag` till ett `version#################`element med värdet . Den numeriska delen baseras på det aktuella datumet. Varje konfiguration måste ha en unik tagg.

     Slutligen sparas data i `newconfig.json` dokumentet. Dokumentstrukturen bör se ut ungefär som i följande exempel:

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
   Öppna `newconfig.json` dokumentet och ändra/lägga `properties` till värden i objektet. I följande exempel ändras `"true"` `"false"`värdet `"livy.server.csrf_protection.enabled"` för från till .

        "livy.server.csrf_protection.enabled": "false",

    Spara filen när du är klar med ändringarna.

3. Skicka `newconfig.json`in .  
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

    Dessa kommandon skickar innehållet i filen **newconfig.json** till klustret som den nya önskade konfigurationen. Begäran returnerar ett JSON-dokument. **Versionstag-elementet** i det här dokumentet ska matcha den version du skickade och **configs-objektet** innehåller de konfigurationsändringar som du begärde.

### <a name="restart-a-service-component"></a>Starta om en tjänstkomponent

Om du tittar på Ambari-webbgränssnittet anger Spark-tjänsten att den måste startas om innan den nya konfigurationen kan börja gälla. Gör så här för att starta om tjänsten.

1. Använd följande för att aktivera underhållsläge för Spark2-tjänsten:

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
    ```

2. Verifiera underhållsläge  

    Dessa kommandon skickar ett JSON-dokument till servern som aktiverar underhållsläge. Du kan kontrollera att tjänsten nu är i underhållsläge med hjälp av följande begäran:

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

    Returvärdet är `ON`.

3. Använd sedan följande för att stänga av Spark2-tjänsten:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > Värdet `href` som returneras av den här URI:n använder den interna IP-adressen för klusternoden. Om du vill använda den `10.0.0.18:8080` utanför klustret ersätter du delen med klustrets FQDN.  

4. Verifiera begäran.  
    Redigera kommandot nedan `29` genom att ersätta `id` det faktiska värdet för returneras från föregående steg.  Följande kommandon hämtar begärans status:

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

    Ett svar `COMPLETED` anger att begäran är klar.

5. När den tidigare begäran är klar använder du följande för att starta Spark2-tjänsten.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    Tjänsten använder nu den nya konfigurationen.

6. Slutligen kan du använda följande för att stänga av underhållsläget.

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

En fullständig referens för REST API finns i [Apache Ambari API Reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Se även [auktorisera användare för Apache Ambari Views](./hdinsight-authorize-users-to-ambari.md)
