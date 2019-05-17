---
title: Skapa Apache Hadoop-kluster med hjälp av Azure CLI - Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med hjälp av plattformsoberoende Azure CLI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597668"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Skapa HDInsight-kluster med Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Stegen i den här genomgången för dokumentet som skapar ett HDInsight 3.6-klustret med hjälp av Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Azure CLI. Om du inte har installerat Azure CLI, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) anvisningar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Skapa ett kluster

1. Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell och sedan markerar du **prova** i det övre högra hörnet i kodblocket. Annars anger du kommandot nedan:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ange miljövariabler. Användning av variabler i den här artikeln är baserad på Bash. Små variationer krävs för andra miljöer. Se [az – hdinsight-skapa](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) för en fullständig lista över möjliga parametrar för att skapa kluster.

    |Parameter | Beskrivning |
    |---|---|
    |`--size`| Antalet arbetarnoder i klustret. Den här artikeln använder variabeln `clusterSizeInNodes` som värdet som skickas till `--size`. |
    |`--version`| HDInsight-klusterversionen. Den här artikeln använder variabeln `clusterVersion` som värdet som skickas till `--version`. Se även: [HDInsight-versioner som stöds](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Typ av HDInsight-kluster, t.ex: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Den här artikeln använder variabeln `clusterType` som värdet som skickas till `--type`. Se även: [Typer och konfiguration av kluster](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|Versioner av olika Hadoop-komponenter i blankstegsavgränsad versioner i ”komponenten = version' format. Den här artikeln använder variabeln `componentVersion` som värdet som skickas till `--component-version`. Se även: [Hadoop-komponenter](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Ersätt `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`, och `PASSWORD` med önskade värden. Ändra värdena för variablerna som du vill. Ange sedan CLI-kommandon.

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

    En lista över giltiga platser, använder den `az account list-locations` kommandot och Använd sedan någon av platserna från den `name` värde.

4. [Skapa ett Azure storage-konto](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) genom att ange kommandot nedan:

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

5. [Extrahera den primära nyckeln från Azure storage-kontot](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) och lagra den i en variabel genom att ange kommandot nedan:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Skapa en Azure storage-behållare](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) genom att ange kommandot nedan:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Skapa HDInsight-kluster](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) genom att ange följande kommando:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight kluster finns olika typer, vilket motsvarar arbetsbelastning eller teknik som klustret är anpassad till. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster.

    Det kan ta flera minuter innan klustret skapas ska slutföras. Normalt cirka 15.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

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

Nu när du har skapat ett HDInsight-kluster med Azure CLI, Använd följande för att lära dig hur du arbetar med ditt kluster:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-kluster

* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
