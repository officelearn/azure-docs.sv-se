---
title: Snabbstart för Apache Kafka – Azure HDInsight
description: I den här snabbstarten lär du dig hur du skapar ett Apache Kafka-kluster på Azure HDInsight med hjälp av Azure PowerShell. Du lär dig också om Kafka-ämnen, -prenumeranter och -konsumenter.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: mvc,hdinsightactive
ms.topic: quickstart
ms.date: 04/16/2018
ms.openlocfilehash: 756733ec98912685af438861d54db0d18e15a947
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046458"
---
# <a name="quickstart-create-a-kafka-on-hdinsight-cluster"></a>Snabbstart: Skapa ett Kafka-kluster på HDInsight

Kafka är en distribuerad direktuppspelningsplattform med öppen källkod. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö. 

I den här snabbstarten lär du dig hur du skapar ett [Apache Kafka](https://kafka.apache.org)-kluster med hjälp av Azure PowerShell. Du kommer också lära dig hur du kan använda de inkluderade verktygen för att skicka och ta emot meddelanden med Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]
> Kafka-API:et kan endast användas av resurser i samma virtuella nätverk. I den här snabbstarten har du direkt åtkomst till klustret med SSH. Om du vill ansluta andra tjänster, nätverk eller virtuella datorer till Kafka måste du först skapa ett virtuellt nätverk och sedan skapa resurser i nätverket.
>
> Mer information finns i dokumentet [Anslut till Kafka via ett virtuellt nätverk](apache-kafka-connect-vpn-gateway.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Azure PowerShell. Mer information finns i dokumentet [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

* En SSH-klient. Stegen i det här dokumentet använder SSH för att ansluta till klustret.

    Kommandot `ssh` tillhandahålls som standard i Linux-, Unix- och macOS-system. I Windows 10 använder du någon av följande metoder för att installera kommandot `ssh`:

    * Använd [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart). Cloud Shell tillhandahåller `ssh`-kommandot och kan konfigureras för att använda antingen Bash eller PowerShell som gränssnittsmiljö.

    * [Installera Windows-undersystemet för Linux](https://docs.microsoft.com/windows/wsl/install-win10). De Linux-distributioner som är tillgängliga via Microsoft Store tillhandahåller `ssh`-kommandot.

    > [!IMPORTANT]
    > Stegen i det här dokumentet förutsätter att du använder någon av de SSH-klienter som nämns ovan. Om du använder en annan SSH-klient och stöter på problem så sök efter en lösning i SSH-klientens dokumentation.
    >
    > Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med cmdlet:en `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel uppmanas du att ange namn och plats, och därefter skapas en ny resursgrupp:

```powershell
$resourceGroup = Read-Input -Prompt "Enter the resource group name"
$location = Read-Input -Prompt "Enter the Azure region to use"

New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Medan Kafka på HDInsight använder Azure-hanterade diskar för att lagra Kafka-data så använder klustret även Azure Storage för att lagra information, t.ex. loggar. Skapa ett nytt lagringskonto med [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount).

```powershell
$storageName = Read-Host -Prompt "Enter the storage account name"

New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroup `
        -Name $storageName `
        -Type Standard_LRS `
        -Location $location
```

HDInsight lagrar data i lagringskontot i en blobcontainer. Skapa en ny behållare med [New-AzureStorageContainer](/powershell/module/Azure.Storage/New-AzureStorageContainer).

```powershell
$containerName = Read-Host -Prompt "Enter the container name"

$storageKey = (Get-AzureRmStorageAccountKey `
                -ResourceGroupName $resourceGroup `
                -Name $storageName)[0].Value
$storageContext = New-AzureStorageContext `
                    -StorageAccountName $storageName `
                    -StorageAccountKey $storageKey
New-AzureStorageContainer -Name $containerName -Context $storageContext 
```

## <a name="create-a-kafka-cluster"></a>Skapa ett Kafka-kluster

Skapa en Kafka på HDInsight-klustret med [New-AzureRmHDInsightCluster](/powershell/module/AzureRM.HDInsight/New-AzureRmHDInsightCluster).

```powershell
# Create a Kafka 1.0 cluster
$clusterName = Read-Host -Prompt "Enter the name of the Kafka cluster"
$httpCredential = Get-Credential -Message "Enter the cluster login credentials" `
    -UserName "admin"
$sshCredentials = Get-Credential -Message "Enter the SSH user credentials"

$numberOfWorkerNodes = "4"
$clusterVersion = "3.6"
$clusterType="Kafka"
$clusterOS="Linux"
$disksPerNode=2

$kafkaConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$kafkaConfig.Add("kafka", "1.0")

New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroup `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $numberOfWorkerNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -ComponentVersion $kafkaConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials `
        -DisksPerWorkerNode $disksPerNode
```

> [!WARNING]
> Det kan ta upp till 20 minuter att skapa HDInsight-klustret.

> [!TIP]
> Parametern `-DisksPerWorkerNode` konfigurerar Kafkas skalbarhet på HDInsight. Kafka på HDInsight använder de virtuella datorernas lokala diskar i klustret för att lagra data. Kafka är I/O-tungt, och därför används [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) för att tillhandahålla hög genomströmning och mer lagringsutrymme per nod. 
>
> Typen av hanterade diskar kan vara antingen __Standard__ (HDD) eller __Premium__ (SSD). Vilken typ av disk som används beror på vilken VM-storlek arbetarnoderna (Kafka-mäklarna) använder. Premiumdiskar används automatiskt med virtuella datorer i DS- och GS-serien. Alla andra typer av virtuella dator använder standard. Du kan ange typ av virtuell dator med `-WorkerNodeSize`-parametern. Mer information om parametrar finns i [New-AzureRmHDInsightCluster](/powershell/module/AzureRM.HDInsight/New-AzureRmHDInsightCluster)-dokumentationen.


> [!IMPORTANT]
> Om du planerar att använda mer än 32 arbetarnoder (antingen när klustret skapas eller genom att skala klustret när det har skapats) måste du använda `-HeadNodeSize`-parametern för att ange en VM-storlek med minst 8 kärnor och 14 GB RAM-minne.
>
> Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="connect-to-the-cluster"></a>Anslut till klustret

1. Anslut till Kafka-klustrets primära huvudnod med följande kommando. Ersätt `sshuser` med SSH-användarnamnet. Ersätt `mykafka` med namnet på Kafka-klustret

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. När du ansluter till HDInsight första gången kan SSH-klienten visa en varning om att värdens äkthet inte kan fastställas. Skriv __ja__ när du uppmanas till detta och tryck sedan på __Retur__ så läggs värden till i SSH-klientens lista över betrodda servrar.

3. Ange SSH-användarens lösenord när du uppmanas till detta.

När du är ansluten visas ett meddelande av följande slag:

```text
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Hämta information om värden i Zookeeper och Broker

När du arbetar med Kafka måste du känna till *Zookeeper*- och *Broker*-värdarna. Dessa värdar används med Kafka-API och många av de verktyg som levereras med Kafka.

I det här avsnittet hämtas information om värden från klustrets Ambari REST API.

1. Installera verktyget `jq` genom att använda följande kommando från SSH-anslutningen till klustret. Det här verktyget används för att parsa JSON-dokument och är användbart när du ska hämta värdinformation:
   
    ```bash
    sudo apt -y install jq
    ```

2. Använd följande kommando om du vill ange en miljövariabel i klustrets namn:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Ange Kafka-klustrets namn när du uppmanas till detta.

3. Använd följande kommando om du vill ange en miljövariabel med Zookeeper-värdens information:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ange klusterinloggningskontots lösenord när du uppmanas till detta (inte SSH-kontot).

    > [!NOTE]
    > Detta kommando hämtar alla Zookeeper-värdar och returnerar bara de första två posterna. Det beror på att det är bra att ha viss redundans ifall en värd inte kan nås.

4. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Det här kommandot returnerar information liknande följande text:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Använd följande kommando om du vill ange en miljövariabel med Kafka-värdens information om asynkron meddelandekö:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Ange klusterinloggningskontots lösenord när du uppmanas till detta (inte SSH-kontot).

6. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Det här kommandot returnerar information liknande följande text:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-kafka-topics"></a>Hantera Kafka-ämnen

Kafka lagrar dataströmmar i kategorier som kallas *ämnen*. Du kan hantera ämnena med verktyget `kafka-topics.sh`.

* **Du skapar ett ämne** med följande kommando i SSH-anslutningen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot ansluter till Zookeeper med hjälp av värdinformationen som lagras i `$KAFKAZKHOSTS`. Det skapar sedan ett Kafka-ämne med namnet **test**. 

    * Data som lagras i det här ämnet partitioneras över åtta partitioner.

    * Varje partition replikeras mellan tre arbetarnoder i klustret.

        > [!IMPORTANT]
        > Om du har skapat klustret i en Azure-region som tillhandahåller tre feldomäner så använd replikeringsfaktorn 3. I annat fall använder du replikeringsfaktorn 4.
        
        I områden med tre feldomäner kan replikeringsfaktorn 3 tillåta att repliker sprids till feldomänerna. I områden med två feldomäner kan replikeringsfaktorn 4 tillåta att repliker sprids jämnt över domänerna.
        
        Om du vill ha information om antalet feldomäner i en region läser du dokumentet [Availability of Linux virtual machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) (Tillgänglighet för virtuella Linux-datorer).

        > [!IMPORTANT] 
        > Kafka har ingen information om Azure-feldomäner. När du skapar partitionsrepliker för ämnen kanske det inte distribueras repliker korrekt för hög tillgänglighet.

        Garantera hög tillgänglighet med [verktyget för ombalansering av Kafka-partitioner](https://github.com/hdinsight/hdinsight-kafka-tools). Du måste köra det här verktyget från en SSH-anslutning till ditt Kafka-klusters huvudnod.

        Om du vill ha bästa möjliga tillgänglighet för dina Kafka-data måste du balansera om ämnets partitionsrepliker när:

        * Du skapar ett nytt ämne eller en ny partition

        * Du skalar upp ett kluster

* **Om du vill lista ämnena** använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot listar de ämnen som är tillgängliga på Kafka-klustret.

* **Om du vill ta bort ett ämne** använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot tar bort ämnet med namnet `topicname`.

    > [!WARNING]
    > Om du tar bort ämnet `test`, som du skapade tidigare, måste du återskapa det. Det används under steg senare i det här dokumentet.

Om du vill ha mer information om vilka kommandon som är tillgängliga med verktyget `kafka-topics.sh` använder du följande kommando:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Skapa och använda poster

Kafka lagrar *poster* i ämnen. Poster produceras av *producenter*, och används av *konsumenter*. Producenter och konsumenter kommunicerar med *Kafka-koordinator*tjänsten. Varje arbetsnod i HDInsight-klustret är en Kafka-koordinatorvärd.

Använd följande steg för att lagra poster i det testämne som du skapade tidigare och sedan läsa dem med en konsument:

1. Om du vill skriva poster i ämnet använder du verktyget `kafka-console-producer.sh` från SSH-anslutningen:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Efter det här kommandot kommer du till en tom rad.

2. Skriv in ett textmeddelande på den tomma raden och tryck på Retur. Ange några meddelanden på det här sättet och använd sedan **Ctrl + C** för att komma tillbaka till den vanliga kommandotolken. Varje rad skickas som en separat post till Kafka-ämnet.

3. Om du vill läsa poster i ämnet använder du verktyget `kafka-console-consumer.sh` från SSH-anslutningen:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Med det här kommandot hämtar du posterna från ämnet och visar dem. Med hjälp av `--from-beginning` anges att konsumenten ska starta från början av direktuppspelningen så att alla poster hämtas.

    > [!NOTE]
    > Om du använder en äldre version av Kafka ersätter du `--bootstrap-server $KAFKABROKERS` med `--zookeeper $KAFKAZKHOSTS`.

4. Använd __Ctrl + C__ om du vill stoppa konsumenten.

Du kan också programmässigt skapa producenter och konsumenter. Om du vill se ett exempel på att använda denna API kan du läsa dokumentet [Kafka-producent och konsument-API med HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver resursgruppen längre kan du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) till att ta bort resursgruppen, HDInsight och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

> [!WARNING]
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används.
> 
> Om du tar bort en Kafka i ett HDInsight-kluster tas alla data som lagrats i Kafka bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda Apache Spark med Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
