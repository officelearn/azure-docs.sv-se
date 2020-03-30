---
title: Lägga till ytterligare Azure Storage-konton i HDInsight
description: Lär dig hur du lägger till ytterligare Azure Storage-konton i ett befintligt HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206715"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Lägga till ytterligare lagringskonton i HDInsight

Lär dig hur du använder skriptåtgärder för att lägga till ytterligare Azure *Storage-konton* i HDInsight. Stegen i det här dokumentet lägger till ett *lagringskonto* i ett befintligt HDInsight-kluster. Den här artikeln gäller *lagringskonton* (inte standardkontot för klusterlagring) och inte ytterligare lagring, till exempel [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) och Azure Data Lake Storage [Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informationen i det här dokumentet handlar om att lägga till ytterligare lagringskonto i ett kluster när det har skapats. Information om hur du lägger till [lagringskonton när klustret skapas finns i Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Krav

* Ett Hadoop-kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Namn och nyckel för lagringskonto. Se [Hantera åtkomstnycklar för lagringskonto.](../storage/common/storage-account-keys-manage.md)
* Om du använder PowerShell behöver du AZ-modulen.  Se [Översikt över Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Hur det fungerar

Under bearbetningen utför skriptet följande åtgärder:

* Om lagringskontot redan finns i core-site.xml-konfigurationen för klustret avslutas skriptet och inga ytterligare åtgärder utförs.

* Verifierar att lagringskontot finns och kan nås med hjälp av nyckeln.

* Krypterar nyckeln med hjälp av klusterautentiseringsuppgifterna.

* Lägger till lagringskontot i filen core-site.xml.

* Stoppar och startar om [Apache Oozie,](https://oozie.apache.org/) [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), Apache [Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)och [Apache Hadoop HDFS-tjänster.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) Om du stoppar och startar dessa tjänster kan de använda det nya lagringskontot.

> [!WARNING]  
> Det går inte att använda ett lagringskonto på en annan plats än HDInsight-klustret.

## <a name="add-storage-account"></a>Lägg till lagringskonto

Använd [Skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) för att tillämpa ändringarna med följande överväganden:

|Egenskap | Värde |
|---|---|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Nodtyper|Head|
|Parametrar|`ACCOUNTNAME``ACCOUNTKEY` `-p` (valfritt)|

* `ACCOUNTNAME`är namnet på lagringskontot som ska läggas till i HDInsight-klustret.
* `ACCOUNTKEY`är åtkomstnyckeln `ACCOUNTNAME`för .
* `-p` är valfritt. Om det anges krypteras inte nyckeln och lagras i filen core-site.xml som oformaterad text.

## <a name="verification"></a>Verifiering

När du visar HDInsight-klustret i Azure-portalen visas inte lagringskonton som läggs till via den här skriptåtgärden om du väljer posten __Lagringskonton__ under __Egenskaper.__ Azure PowerShell och Azure CLI visar inte heller det ytterligare lagringskontot. Lagringsinformationen visas inte eftersom skriptet bara ändrar konfigurationen `core-site.xml` för klustret. Den här informationen används inte när klusterinformationen hämtas med hjälp av Azure Management API:er.

För att verifiera den ytterligare lagringen använder du någon av de metoder som visas nedan:

### <a name="powershell"></a>PowerShell

Skriptet returnerar de lagringskontonamn som är associerade med det angivna klustret. Ersätt `CLUSTERNAME` med det faktiska klusternamnet och kör sedan skriptet.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Navigera till **HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Observera nycklarna som `fs.azure.account.key`börjar med . Kontonamnet kommer att vara en del av nyckeln som visas i den här exempelbilden:

   ![kontroll genom Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Ta bort lagringskonto

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Navigera till **HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Ta bort följande tangenter:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

När du har tagit bort dessa nycklar och sparat konfigurationen måste du starta om Oozie, Yarn, MapReduce2, HDFS och Hive en efter en.

## <a name="known-issues"></a>Kända problem

### <a name="storage-firewall"></a>Brandvägg för lagring

Om du väljer att skydda ditt **lagringskonto med begränsningarna brandväggar och virtuella nätverk** i valda **nätverk**måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...** så att HDInsight kan komma åt ditt lagringskonto.

### <a name="unable-to-access-storage-after-changing-key"></a>Det gick inte att komma åt lagring efter att du har ändrat nyckeln

Om du ändrar nyckeln för ett lagringskonto kan HDInsight inte längre komma åt lagringskontot. HDInsight använder en cachelagrad kopia av nyckeln i core-site.xml för klustret. Den här cachelagrade kopian måste uppdateras för att matcha den nya nyckeln.

Att köra skriptåtgärden igen uppdaterar __inte__ nyckeln, eftersom skriptet kontrollerar om det redan finns en post för lagringskontot. Om det redan finns en post gör den inga ändringar.

Så här kan du lösa problemet:  
1. Ta bort lagringskontot.
1. Lägg till lagringskontot.

> [!IMPORTANT]  
> Det går inte att rotera lagringsnyckeln för det primära lagringskontot som är kopplat till ett kluster.

### <a name="poor-performance"></a>Dåliga prestanda

Om lagringskontot finns i en annan region än HDInsight-klustret kan det uppstå dåliga prestanda. Åtkomst till data i en annan region skickar nätverkstrafik utanför det regionala Azure-datacentret och över det offentliga internet, vilket kan införa svarstid.

### <a name="additional-charges"></a>Ytterligare avgifter

Om lagringskontot finns i en annan region än HDInsight-klustret kan du märka ytterligare utgående avgifter för din Azure-fakturering. En utgående avgift används när data lämnar ett regionalt datacenter. Den här avgiften tillämpas även om trafiken är avsedd för ett annat Azure-datacenter i en annan region.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du lägger till ytterligare lagringskonton i ett befintligt HDInsight-kluster. Mer information om skriptåtgärder finns i [Anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md)
