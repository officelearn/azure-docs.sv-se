---
title: Lägg till ytterligare Azure Storage-konton i HDInsight
description: Lär dig hur du lägger till ytterligare Azure Storage-konton i ett befintligt HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 86b9230dbdca82c5599c1839fd64bd3df4725051
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435579"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Lägg till ytterligare lagrings konton i HDInsight

Lär dig hur du använder skript åtgärder för att lägga till ytterligare Azure Storage- *konton* i HDInsight. Stegen i det här dokumentet lägger till ett lagrings *konto* i ett befintligt Linux-baserat HDInsight-kluster. Den här artikeln gäller lagrings *konton* (inte standard klustrets lagrings konto) och inte ytterligare lagrings utrymme, till exempel [Azure Data Lake Storage gen1](hdinsight-hadoop-use-data-lake-store.md) och [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informationen i det här dokumentet är att lägga till ytterligare lagrings konton i ett kluster när det har skapats. Information om hur du lägger till lagrings konton när du skapar kluster finns i [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Krav

* Ett Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Lagrings kontots namn och nyckel. Se [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md).
* [Korrekt bokstäver kluster namn](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Om du använder PowerShell behöver du AZ-modulen.  Se [Översikt över Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Om du inte har installerat Azure CLI kan du läsa mer i [Azure kommando rads gränssnitt (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Om du använder bash eller en kommando tolk i Windows behöver du också **JQ**, en JSON-processor med kommando rad.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). För bash på Ubuntu i Windows 10 Se [Windows-undersystemet för Linux installations guide för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Så här fungerar det

Det här skriptet använder följande parametrar:

* __Namn på Azure Storage-konto__: namnet på det lagrings konto som ska läggas till i HDInsight-klustret. När du har kört skriptet kan HDInsight läsa och skriva data som lagras i det här lagrings kontot.

* __Azure Storage-konto nyckel__: en nyckel som beviljar åtkomst till lagrings kontot.

* __-p__ (valfritt): om det här alternativet har angetts krypteras nyckeln inte och lagras i filen site. xml som oformaterad text.

Under bearbetningen utför skriptet följande åtgärder:

* Om lagrings kontot redan finns i site. XML-konfigurationen för klustret, avslutas skriptet och inga ytterligare åtgärder utförs.

* Kontrollerar att lagrings kontot finns och kan nås med hjälp av nyckeln.

* Krypterar nyckeln med hjälp av kluster autentiseringsuppgiften.

* Lägger till lagrings kontot i site. XML-filen.

* Stoppar och startar om [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)och [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) -tjänster. Genom att stoppa och starta dessa tjänster kan de använda det nya lagrings kontot.

> [!WARNING]  
> Det finns inte stöd för att använda ett lagrings konto på en annan plats än HDInsight-klustret.

## <a name="the-script"></a>Skriptet

__Skript plats__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Krav__: skriptet måste appliceras på __huvudnoderna__. Du behöver inte markera det här skriptet som __beständigt__, eftersom det uppdaterar Ambari-konfigurationen direkt för klustret.

## <a name="to-use-the-script"></a>Använda skriptet

Det här skriptet kan användas från Azure PowerShell, Azure CLI eller Azure Portal.

### <a name="powershell"></a>PowerShell

Använder [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Ersätt `CLUSTERNAME`, `ACCOUNTNAME`och `ACCOUNTKEY` med lämpliga värden.

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

Använda [AZ HDInsight-skript – åtgärd kör](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Ersätt `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`och `ACCOUNTKEY` med lämpliga värden.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure portal

Se [tillämpa en skript åtgärd på ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Kända problem

### <a name="storage-firewall"></a>Lagrings brand vägg

Om du väljer att skydda ditt lagrings konto med **brand väggar och begränsningar för virtuella nätverk** på **valda nätverk**måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...** så att HDInsight kan komma åt ditt lagrings konto.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Lagrings konton som inte visas i Azure Portal eller verktyg

När du visar HDInsight-klustret i Azure Portal, visas inte lagrings konton som lagts till genom den här skript åtgärden om du väljer posten __lagrings konton__ under __Egenskaper__ . Azure PowerShell och Azure CLI visar inte det ytterligare lagrings kontot antingen.

Lagrings informationen visas inte eftersom skriptet bara ändrar site. XML-konfigurationen för klustret. Den här informationen används inte när du hämtar kluster informationen med hjälp av API: er för Azure Management.

Om du vill visa information om lagrings kontot som har lagts till i klustret med det här skriptet använder du Ambari-REST API. Använd följande kommandon för att hämta den här informationen för klustret:

### <a name="powershell"></a>PowerShell

Ersätt `CLUSTERNAME` med rätt bokstäver-kluster namn. Ersätt `ACCOUNTNAME` med faktiska namn. När du uppmanas till det anger du lösen ordet för kluster inloggning.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Ersätt `CLUSTERNAME` med rätt bokstäver-kluster namn. Ersätt `PASSWORD` med lösen ordet för kluster administratören. Ersätt `STORAGEACCOUNT` med det faktiska lagrings konto namnet.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Ersätt `CLUSTERNAME` med det bokstäver kluster namnet korrekt i båda skripten. Identifiera först den tjänst konfigurations version som används genom att ange kommandot nedan:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Ersätt `ACCOUNTNAME` med det faktiska lagrings konto namnet. Ersätt sedan `4` med den faktiska service config-versionen och ange kommandot:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

Information som returneras från det här kommandot ser ut ungefär som i följande text:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Den här texten är ett exempel på en krypterad nyckel som används för att komma åt lagrings kontot.

### <a name="unable-to-access-storage-after-changing-key"></a>Det gick inte att komma åt lagring efter ändring av nyckel

Om du ändrar nyckeln för ett lagrings konto kan HDInsight inte längre komma åt lagrings kontot. HDInsight använder en cachelagrad kopia av nyckeln i site. xml för klustret. Den cachelagrade kopian måste uppdateras för att matcha den nya nyckeln.

Om du kör skript åtgärden igen uppdateras __inte__ nyckeln eftersom skriptet kontrollerar om det redan finns en post för lagrings kontot. Om det redan finns en post görs inga ändringar.

För att undvika det här problemet måste du ta bort den befintliga posten för lagrings kontot. Använd följande steg för att ta bort den befintliga posten:

> [!IMPORTANT]  
> Det går inte att rotera lagrings nyckeln för det primära lagrings kontot som är kopplat till ett kluster.

1. Öppna Ambari-webbgränssnittet för ditt HDInsight-kluster i en webbläsare. URI: n är `https://CLUSTERNAME.azurehdinsight.net`. Ersätt `CLUSTERNAME` med namnet på klustret.

    När du uppmanas till det anger du användar namn och lösen ord för HTTP-inloggning för klustret.

2. I listan över tjänster till vänster på sidan väljer du __HDFS__. Välj sedan fliken __konfigurationer__ i mitten av sidan.

3. I fältet __filter...__ anger du ett värde för __FS. Azure. account__. Detta returnerar poster för eventuella ytterligare lagrings konton som har lagts till i klustret. Det finns två typer av poster: nyckel __utfärdare__ och __nyckel__. Båda innehåller namnet på lagrings kontot som en del av nyckel namnet.

    Följande är exempel poster för ett lagrings konto med namnet __unstorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. När du har identifierat nycklarna för det lagrings konto du behöver ta bort, använder du den röda ikonen till höger om posten för att ta bort den. Använd sedan knappen __Spara__ för att spara ändringarna.

5. När ändringarna har sparats använder du skript åtgärden för att lägga till lagrings kontot och ett nytt nyckel värde i klustret.

### <a name="poor-performance"></a>Dåliga prestanda

Om lagrings kontot finns i en annan region än HDInsight-klustret kan det uppstå dåliga prestanda. Att komma åt data i en annan region skickar nätverks trafik utanför det regionala Azure-datacenteret och över det offentliga Internet, vilket kan leda till svars tid.

### <a name="additional-charges"></a>Ytterligare avgifter

Om lagrings kontot finns i en annan region än HDInsight-klustret kan du lägga märke till ytterligare utgående kostnader på din Azure-fakturering. En utgående avgift används när data lämnar ett regionalt Data Center. Den här avgiften används även om trafiken är avsedd för ett annat Azure-datacenter i en annan region.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du lägger till ytterligare lagrings konton i ett befintligt HDInsight-kluster. Mer information om skript åtgärder finns i [Anpassa Linux-baserade HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md)
