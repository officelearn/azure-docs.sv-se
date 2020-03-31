---
title: 'Snabbstart: Apache Spark-kluster med Azure CLI - Azure HDInsight'
description: Den här snabbstarten visar hur du använder Azure CLI för att skapa ett Apache Spark-kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: e4679d5a04be7b8c0145fd93818e4187170b4194
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049686"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Snabbstart: Skapa Apache Spark-kluster i Azure HDInsight med Azure CLI

I den här snabbstarten får du lära dig hur du skapar ett Apache Spark-kluster i Azure HDInsight med hjälp av Azure command-line interface (CLI). Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Spark-ramverket för HDInsight möjliggör snabb dataanalys och klusterdatorer med hjälp av minnesbearbetning. Azure CLI är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), om du inte vill använda Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Skapa ett Apache Spark-kluster

1. Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell väljer du **Prova det** i det övre högra hörnet av följande kodblock. Annars anger du följande kommando:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ange miljövariabler. Användningen av variabler i den här snabbstarten baseras på Bash. Små variationer kommer att behövas för andra miljöer. Ersätt RESOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME och PASSWORD i kodavsnittet nedan med önskade värden. Ange sedan CLI-kommandona för att ställa in miljövariabler.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Skapa resursgruppen genom att ange kommandot nedan:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Skapa ett Azure-lagringskonto genom att ange kommandot nedan:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrahera den primära nyckeln från Azure-lagringskontot och lagra den i en variabel genom att ange kommandot nedan:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Skapa en Azure-lagringsbehållare genom att ange kommandot nedan:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Skapa Apache Spark-klustret genom att ange följande kommando:

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

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

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

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Apache Spark-kluster i Azure HDInsight med Azure CLI.  Gå vidare till nästa självstudiekurs om du vill lära dig hur du använder ett HDInsight-kluster för att köra interaktiva frågor om exempeldata.

> [!div class="nextstepaction"]
> [Köra interaktiva frågor i Apache Spark](./apache-spark-load-data-run-query.md)
