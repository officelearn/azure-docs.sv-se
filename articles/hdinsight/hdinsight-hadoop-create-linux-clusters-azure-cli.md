---
title: Skapa Apache Hadoop-kluster med Azure CLI - Azure HDInsight
description: Lär dig hur du skapar Azure HDInsight-kluster med Azure CLI som inte är plattformen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77199049"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Skapa HDInsight-kluster med Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Stegen i det här dokumentets genomgång skapar ett HDInsight 3.6-kluster med Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Azure CLI. Om du inte har installerat Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) för steg.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Skapa ett kluster

1. Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell väljer du **Prova det** i det övre högra hörnet av kodblocket. Annars anger du kommandot nedan:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ange miljövariabler. Användningen av variabler i den här artikeln är baserad på Bash. Små variationer kommer att behövas för andra miljöer. Se [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) för en fullständig lista över möjliga parametrar för att skapa kluster.

    |Parameter | Beskrivning |
    |---|---|
    |`--workernode-count`| Antalet arbetsnoder i klustret. I den här `clusterSizeInNodes` artikeln används `--workernode-count`variabeln som det värde som skickades till . |
    |`--version`| HDInsight-klusterversionen. I den här `clusterVersion` artikeln används `--version`variabeln som det värde som skickades till . Se även [HDInsight-versioner som stöds](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Typ av HDInsight kluster, som: hadoop, interactivehive, hbase, kafka, storm, gnista, rserver, mlservices.  I den här `clusterType` artikeln används `--type`variabeln som det värde som skickades till . Se även [klustertyper och konfiguration](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Versionerna av olika Hadoop-komponenter, i utrymmesavgränsade versioner i formatet component=version. I den här `componentVersion` artikeln används `--component-version`variabeln som det värde som skickades till . Se även [Hadoop components](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Ersätt `RESOURCEGROUPNAME` `LOCATION`, `CLUSTERNAME` `STORAGEACCOUNTNAME`, `PASSWORD` , och med önskade värden. Ändra värden för de andra variablerna efter behov. Ange sedan CLI-kommandona.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Skapa resursgruppen](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) genom att ange kommandot nedan:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Om du vill visa en `az account list-locations` lista över giltiga platser använder `name` du kommandot och använder sedan en av platserna från värdet.

4. [Skapa ett Azure Storage-konto](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) genom att ange kommandot nedan:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extrahera primärnyckeln från Azure Storage-kontot](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) och lagra den i en variabel genom att ange kommandot nedan:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Skapa en Azure Storage-behållare](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) genom att ange kommandot nedan:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Skapa HDInsight-klustret](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) genom att ange följande kommando:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight-kluster finns i olika typer, vilket motsvarar den arbetsbelastning eller teknik som klustret är inställt för. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster.

    Det kan ta flera minuter innan processen för att skapa kluster har slutförts. Vanligtvis runt 15.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Ange alla eller några av följande kommandon för att ta bort resurser:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster med Hjälp av Azure CLI kan du använda följande för att lära dig hur du arbetar med klustret:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använd MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Komma igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kluster

* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
