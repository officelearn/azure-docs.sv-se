---
title: Lägga till ytterligare Azure storage-konton i HDInsight
description: Lär dig hur du lägger till ytterligare Azure storage-konton i ett befintligt HDInsight-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 6b9577bcf8b527abb0cb7b8720ed83ec8321655b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098739"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Lägga till ytterligare lagringskonton till HDInsight

Lär dig hur du använder skriptåtgärder för att lägga till ytterligare Azure storage *konton* till HDInsight. Stegen i det här dokumentet lägga till ett lagringskonto *konto* i ett befintligt Linux-baserade HDInsight-kluster. Den här artikeln gäller lagring *konton* (inte klustret standardkontot för lagring), och inte ytterligare lagringsutrymme som [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) och [Azure Data Lake Storage Gen2 ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informationen i det här dokumentet handlar om att lägga till ytterligare lagringsutrymme i ett kluster när den har skapats. Information om att lägga till lagringskonton när klustret skapas finns i [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Lagringskontonamn och nyckel. Se [hantera inställningarna för lagringskontot i Azure-portalen](../storage/common/storage-account-manage.md).
* [Korrekt bokstäver klusternamnet](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Om du använder PowerShell måste AZ-modulen.  Se [översikt över Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Om du inte har installerat Azure CLI, se [Azure kommandoradsgränssnitt (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Om du använder bash- eller en windows-kommandotolk, du måste också **jq**, en kommandorad JSON-processor.  Se [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/). Bash i Ubuntu för Windows 10 finns [Windows-undersystem for Linux Installation Guide för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Hur det fungerar

Det här skriptet använder följande parametrar:

* __Azure storage-kontonamn__: Namnet på lagringskontot för att lägga till i HDInsight-klustret. När skriptet har körts, HDInsight läser och skriver data lagras i det här lagringskontot.

* __Azure storage-kontonyckel__: En nyckel som ger åtkomst till lagringskontot.

* __-p__ (valfritt): Om nyckeln inte är krypterad och lagras i filen core-site.xml som oformaterad text.

Under bearbetning utför skriptet följande åtgärder:

* Om lagringskontot finns redan i core-site.xml-konfigurationen för klustret, skriptet avslutas och inga ytterligare åtgärder utförs.

* Verifierar att lagringskontot finns och kan nås med hjälp av nyckeln.

* Krypterar nyckeln med hjälp av autentiseringsuppgifter för klustret.

* Lägger till lagringskontot i filen core-site.xml.

* Stoppar och startar om den [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), och [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) tjänster. Stoppa och starta dessa tjänster gör att de kan använda det nya lagringskontot.

> [!WARNING]  
> Med ett storage-konto i en annan plats än HDInsight-kluster stöds inte.

## <a name="the-script"></a>Skriptet

__Skriptplats__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Krav för__:  Skriptet måste tillämpas på den __huvudnoder__. Du behöver inte att markera det här skriptet som __bevarade__, som uppdateras direkt Ambari-konfigurationen för klustret.

## <a name="to-use-the-script"></a>Du använder skriptet

Det här skriptet kan användas från Azure PowerShell, Azure CLI eller Azure-portalen.

### <a name="powershell"></a>PowerShell

Med hjälp av [skicka AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Ersätt `CLUSTERNAME`, `ACCOUNTNAME`, och `ACCOUNTKEY` med lämpliga värden.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

Med hjälp av [az hdinsight-skriptåtgärder köra](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Ersätt `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, och `ACCOUNTKEY` med lämpliga värden.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure Portal

Se [gäller en skriptåtgärd för ett aktivt kluster](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Kända problem

### <a name="storage-firewall"></a>Storage-brandväggen

Om du väljer att skydda ditt lagringskonto med den **brandväggar och virtuella nätverk** begränsningar för **valda nätverk**, måste du aktivera undantaget **Tillåt att betrodda Microsoft tjänster...**  så att HDInsight kan komma åt ditt storage-konto.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Storage-konton som inte visas i Azure-portalen eller verktyg

När du visar HDInsight-kluster i Azure portal, välja den __Lagringskonton__ posten under __egenskaper__ visas inte storage-konton som har lagts till via den här skriptåtgärden. Azure PowerShell och Azure CLI visas inte ytterligare storage-konto.

Information om visas inte eftersom skriptet ändras endast core-site.xml-konfigurationen för klustret. Den här informationen används inte vid hämtning av klusterinformationen med hjälp av API: er för Azure hantering.

Använda Ambari REST API för att visa information på lagringskontot läggs till klustret med det här skriptet. Använd följande kommandon för att hämta den här informationen för klustret:

### <a name="powershell"></a>PowerShell

Ersätt `CLUSTERNAME` med korrekt alltid i klustrets namn. Först identifiera service config-version som används genom att ange kommandot nedan:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Ersätt `ACCOUNTNAME` med de faktiska namnen. Ersätt sedan `4` med den faktiska tjänsten config-versionen och ange kommando. När du uppmanas, anger du lösenordet för klusterinloggning.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash
Ersätt `myCluster` med korrekt alltid i klustrets namn.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Ersätt `myAccount` med faktiska lagringskontonamn. Ersätt sedan `4` med den faktiska tjänsten config-versionen och ange kommando:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Ersätt `CLUSTERNAME` med korrekt bokstäver klusternamnet i båda skripten. Först identifiera service config-version som används genom att ange kommandot nedan:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Ersätt `ACCOUNTNAME` med faktiska lagringskontonamn. Ersätt sedan `4` med den faktiska tjänsten config-versionen och ange kommando:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 Information som returneras från det här kommandot ser ut som följande text:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Den här texten är ett exempel på en krypterad nyckel som används för att komma åt lagringskontot.

### <a name="unable-to-access-storage-after-changing-key"></a>Det går inte att komma åt lagring när du har ändrat nyckel

Om du ändrar nyckeln för ett lagringskonto, HDInsight inte längre komma åt lagringskontot. HDInsight använder en cachelagrad kopia av nyckeln i core-site.xml för klustret. Den här cachelagrade kopian måste uppdateras så att den matchar den nya nyckeln.

Kör skriptet igen har __inte__ uppdatera nyckeln, eftersom skriptet kontrollerar om det redan finns en post för lagringskontot. Om det finns redan en post, den inte göra några ändringar.

Om du vill undvika det här problemet måste du ta bort den befintliga posten för lagringskontot. Använd följande steg för att ta bort den befintliga posten:

1. Öppna Ambari-Webbgränssnittet för ditt HDInsight-kluster i en webbläsare. URI: N är `https://CLUSTERNAME.azurehdinsight.net`. Ersätt `CLUSTERNAME` med namnet på klustret.

    När du uppmanas, anger du http-logga in användare och lösenord för klustret.

2. I listan över tjänster till vänster på sidan Välj __HDFS__. Välj sedan den __Peeringkonfigurationer__ fliken i mitten av sidan.

3. I den __Filter...__  fältet, anger du värdet __fs.azure.account__. Det här returnerar poster för alla ytterligare lagringskonton som har lagts till i klustret. Det finns två typer av uppgifter. __keyprovider__ och __nyckeln__. Båda innehåller namnet på lagringskontot som en del av nyckelnamnet.

    Följande är Exempelposter för ett lagringskonto med namnet __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. När du har identifierat nycklarna för lagringskontot som du vill ta bort kan du använda röda '-' ikonen till höger i posten för att ta bort den. Använd sedan den __spara__ för att spara dina ändringar.

5. När ändringar har sparats, kan du använda skriptåtgärden för att lägga till lagringskontot och det nya nyckelvärdet i klustret.

### <a name="poor-performance"></a>Sämre prestanda

Om lagringskontot är i en annan region än HDInsight-kluster kan uppstå det sämre prestanda. Åtkomst till data i en annan region skickar nätverkstrafik utanför regionala Azure-datacentret och över offentliga internet, vilket kan medföra svarstid.

### <a name="additional-charges"></a>Ytterligare avgifter

Om lagringskontot är i en annan region än HDInsight-kluster kan märka du kostnader för ytterligare utgående trafik på din Azure-fakturering. En utgående avgift tillämpas när data lämnar ett regionala datacenter. Den här avgiften tillämpas även om trafiken som är avsedd för en annan Azure-datacenter i en annan region.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du lägger till ytterligare lagringskonton i ett befintligt HDInsight-kluster. Mer information om skriptåtgärder finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md)
