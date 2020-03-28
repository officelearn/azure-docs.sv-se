---
title: 'Självstudiekurs: Skapa ett Apache Kafka REST-proxyaktiverat kluster i HDInsight med Azure CLI'
description: Lär dig hur du utför Apache Kafka-åtgärder med hjälp av en Kafka REST-proxy på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201887"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Självstudiekurs: Skapa ett Apache Kafka REST-proxyaktiverat kluster i HDInsight med Azure CLI

I den här självstudien får du lära dig hur du skapar ett Apache Kafka [REST-proxyaktiverat](./rest-proxy.md) kluster i Azure HDInsight med hjälp av Azure command-line interface (CLI). Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Kafka är en distribuerad direktuppspelningsplattform med öppen källkod. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö. Kafka REST Proxy kan du interagera med din Kafka kluster via en [REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) över HTTP. Azure CLI är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

Apache Kafka-API:et kan endast användas av resurser i samma virtuella nätverk. Du kan komma åt klustret direkt med SSH. Om du vill ansluta andra tjänster, nätverk eller virtuella datorer till Apache Kafka måste du först skapa ett virtuellt nätverk och sedan skapa resurser i nätverket. Mer information finns i [Ansluta till Apache Kafka med hjälp av ett virtuellt nätverk](./apache-kafka-connect-vpn-gateway.md).

I den här självstudien lär du dig:

> [!div class="checklist"]
> * Förutsättningar för Kafka REST proxy
> * Skapa ett Apache Kafka-kluster med Azure CLI

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Ett program som registrerats med Azure AD. Klientprogrammen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure. Mer information finns i [Registrera ett program hos Microsofts identitetsplattform](../../active-directory/develop/quickstart-register-app.md).

* En Azure AD-säkerhetsgrupp med ditt registrerade program som medlem. Den här säkerhetsgruppen används för att styra vilka program som tillåts interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [Skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI. Se till att du har minst version 2.0.79. Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Skapa ett Apache Kafka-kluster

1. Logga in på din Azure-prenumeration.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ange miljövariabler. Användningen av variabler i den här självstudien är baserad på Bash. Små variationer kommer att behövas för andra miljöer.

    |Variabel | Beskrivning |
    |---|---|
    |resourceGroupName|Ersätt RESOURCEGROUPNAME med namnet på den nya resursgruppen.|
    |location|Ersätt LOCATION med en region där klustret ska skapas. Om du vill visa en `az account list-locations` lista över giltiga platser använder du kommandot|
    |clusterName|Ersätt CLUSTERNAME med ett globalt unikt namn för det nya klustret.|
    |storageAccount|Ersätt STORAGEACCOUNTNAME med ett namn för ditt nya lagringskonto.|
    |httpPassword (på))httpPassword|Ersätt LÖSENORD med ett lösenord för klusterinloggningen, **admin**.|
    |sshPassword (sshPassword)|Ersätt LÖSENORD med ett lösenord för det säkra skalanvändarnamnet **sshuser**.|
    |securityGroupName|Ersätt SECURITYGROUPNAME med klientens AAD-säkerhetsgruppnamn för Kafka Rest Proxy. Variabeln skickas till `--kafka-client-group-name` parametern `az-hdinsight-create`för .|
    |säkerhet GruppID|Ersätt SECURITYGROUPID med klientenS AAD-säkerhetsgrupp-ID för Kafka Rest Proxy. Variabeln skickas till `--kafka-client-group-id` parametern `az-hdinsight-create`för .|
    |lagringContainer|Lagringsbehållare som klustret kommer att använda, lämna som det är för den här självstudien. Den här variabeln ställs in med namnet på klustret.|
    |workernodeCount|Antal arbetsnoder i klustret, lämna som det är för den här självstudien. För att garantera hög tillgänglighet kräver Kafka minst 3 arbetsnoder|
    |clusterType (klustertyp)|Typ av HDInsight-kluster, lämna som-är för den här guiden.|
    |klusterVersion|HDInsight kluster version, lämna som är för den här guiden. Kafka Rest Proxy kräver en minsta klusterversion av 4.0.|
    |komponentVersion|Kafka version, lämna som-är för den här guiden. Kafka Rest Proxy kräver en minsta komponentversion av 2.1.|

    Uppdatera variablerna med önskade värden. Ange sedan CLI-kommandona för att ställa in miljövariabler.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Skapa resursgruppen](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) genom att ange kommandot nedan:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Skapa ett Azure Storage-konto](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) genom att ange kommandot nedan:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Extrahera primärnyckeln](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) från Azure Storage-kontot och lagra den i en variabel genom att ange kommandot nedan:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Skapa en Azure Storage-behållare](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) genom att ange kommandot nedan:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Skapa HDInsight-klustret](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create). Innan du anger kommandot bör du tänka på följande parametrar:

    1. Obligatoriska parametrar för Kafka-kluster:

        |Parameter | Beskrivning|
        |---|---|
        |--typ|Värdet måste vara **Kafka**.|
        |--workernode-data-disks-per-nod|Antalet datadiskar som ska användas per arbetsnod. HDInsight Kafka stöds endast med datadiskar. Den här självstudien använder värdet **2**.|

    1. Obligatoriska parametrar för Kafka REST proxy:

        |Parameter | Beskrivning|
        |---|---|
        |--kafka-management-nod-storlek|Nodens storlek. Den här självstudien använder värdet **Standard_D4_v2**.|
        |--kafka-client-group-id|Klienten AAD säkerhetsgrupp-ID för Kafka Rest Proxy. Värdet skickas från variabeln **$securityGroupID**.|
        |--kafka-client-group-name --kafka-client-group-name --kafka-client-group-name --|KlientenS AAD-säkerhetsgruppsnamn för Kafka Rest Proxy. Värdet skickas från variabeln **$securityGroupName**.|
        |--version|HDInsight-klusterversionen måste vara minst 4.0. Värdet skickas från variabeln **$clusterVersion**.|
        |--komponent-version|Kafka-versionen måste vara minst 2.1. Värdet skickas från variabeln **$componentVersion**.|
    
        Om du vill skapa klustret utan `--kafka-management-node-size` `--kafka-client-group-id`REST-proxy, eliminera , och `--kafka-client-group-name` från `az hdinsight create` kommandot.

    1. Om du har ett befintligt `--vnet-name` virtuellt nätverk lägger du till parametrarna och `--subnet`och deras värden.

    Ange följande kommando för att skapa klustret:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Det kan ta flera minuter innan processen för att skapa kluster har slutförts. Vanligtvis runt 15.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Ange alla eller några av följande kommandon för att ta bort resurser:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett Apache Kafka REST-proxyaktiverat kluster i Azure HDInsight med Azure CLI använder du Python-kod för att interagera med REST-proxyn:

> [!div class="nextstepaction"]
> [Skapa exempelprogram](./rest-proxy.md#client-application-sample)