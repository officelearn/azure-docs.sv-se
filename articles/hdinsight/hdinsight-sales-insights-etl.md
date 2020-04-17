---
title: 'Självstudiekurs: Skapa en heltäckande ETL-pipeline för att härleda försäljningsinsikter i Azure HDInsight'
description: Lär dig hur du använder skapa ETL-pipelines med Azure HDInsight för att härleda insikter från försäljningsdata med hjälp av Spark on-demand-kluster och Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: c213b0089af0af295d44afd38bbc5c17b6db159d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535238"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Självstudiekurs: Skapa en datapipeline från slutna till slutna dagar för att härleda försäljningsinsikter i Azure HDInsight

I den här självstudien ska du skapa en end-to-end-datapipeline som utför extrahera, transformera och läsa in (ETL) åtgärder. Pipelinen använder [Apache Spark-](./spark/apache-spark-overview.md) och Apache Hive-kluster som körs på Azure HDInsight för att fråga och manipulera data. Du använder även tekniker som Azure Data Lake Storage Gen2 för datalagring och Power BI för visualisering.

Den här datapipelinen kombinerar data från olika butiker, tar bort oönskade data, lägger till nya data och läser in allt detta i ditt lagringsutrymme för att visualisera affärsinsikter. Läs mer om ETL-pipelines i [Extrahera, transformera och ladda (ETL) i stor skala](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-arkitektur](./media/hdinsight-sales-insights-etl/architecture.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Azure CLI - minst version 2.2.0. Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* jq, en kommandorads-JSON-processor.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* En medlem [av azure-inbyggd roll - ägare](../role-based-access-control/built-in-roles.md).

* Om du använder PowerShell för att utlösa datafabrikspipelinen behöver du [Az-modulen](https://docs.microsoft.com/powershell/azure/overview).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) för att visualisera affärsinsikter i slutet av den här självstudien.

## <a name="create-resources"></a>Skapa resurser

### <a name="clone-the-repository-with-scripts-and-data"></a>Klona databasen med skript och data

1. Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell väljer du **Prova det** i det övre högra hörnet av kodblocket. Annars anger du kommandot nedan:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Se till att du är [owner](../role-based-access-control/built-in-roles.md)medlem i Azure-rollägaren . Ersätt `user@contoso.com` med ditt konto och ange sedan kommandot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Om ingen post returneras är du inte medlem och kan inte slutföra den här självstudien.

1. Ladda ner data och skript för den här självstudien från [HDInsight försäljningsstatistik ETL-arkivet](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Ange följande kommando:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Se `salesdata scripts templates` till att det har skapats. Verifiera med följande kommando:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuera Azure-resurser som behövs för pipelinen

1. Lägg till körningsbehörigheter för alla skript genom att ange:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Ange variabel för resursgruppen. Ersätt `RESOURCE_GROUP_NAME` med namnet på en befintlig eller ny resursgrupp och ange sedan kommandot:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Kör skriptet. Ersätt `LOCATION` med önskat värde och ange sedan kommandot:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Om du inte är säker på vilken region du ska ange kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [az-kontolisteplatser.](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations)

    Kommandot distribuerar följande resurser:

    * Ett Azure Blob-lagringskonto. Det här kontot kommer att innehålla företagets försäljningsdata.
    * Ett Azure Data Lake Storage Gen2-konto. Det här kontot fungerar som lagringskonto för båda HDInsight-kluster. Läs mer om HDInsight och Data Lake Storage Gen2 i [Azure HDInsight-integrering med Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * En användartilldelad hanterad identitet. Det här kontot ger HDInsight-klustren åtkomst till Data Lake Storage Gen2-kontot.
    * Ett Apache Spark-kluster. Det här klustret används för att rensa och omvandla rådata.
    * Ett Apache Hive [Interactive Query-kluster.](./interactive-query/apache-interactive-query-get-started.md) Det här klustret gör det möjligt att fråga försäljningsdata och visualisera dem med Power BI.
    * Ett virtuellt Azure-nätverk som stöds av NSG-regler (Network Security Group). Det här virtuella nätverket gör det möjligt för kluster att kommunicera och säkrar sin kommunikation.

Det kan ta cirka 20 minuter att skapa kluster.

Standardlösenordet för SSH-åtkomst `Thisisapassword1`till klustren är . Om du vill ändra lösenordet går `./templates/resourcesparameters_remainder.json` du till filen `sparksshPassword`och `sparkClusterLoginPassword` `llapClusterLoginPassword`ändrar `llapsshPassword` lösenordet för parametrarna , , och.

### <a name="verify-deployment-and-collect-resource-information"></a>Verifiera distribution och samla in resursinformation

1. Om du vill kontrollera status för distributionen går du till resursgruppen på Azure-portalen. Under **Inställningar**väljer du **Distributioner**och sedan distributionen. Här kan du se de resurser som har distribuerats och de resurser som fortfarande pågår.

1. Om du vill visa namnen på klustren anger du följande kommando:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Om du vill visa Azure-lagringskontot och åtkomstnyckeln anger du följande kommando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Om du vill visa kontot och åtkomstnyckeln för DataSjölagringsgen2 anger du följande kommando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Azure Data Factory är ett verktyg som hjälper till att automatisera Azure Pipelines. Det är inte det enda sättet att utföra dessa uppgifter, men det är ett bra sätt att automatisera processerna. Mer information om Azure Data Factory finns i [Dokumentationen till Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Denna datafabrik kommer att ha en pipeline med två aktiviteter:

* Den första aktiviteten kopierar data från Azure Blob-lagring till lagringsgend 2-lagringskontot för datasjölagring för att efterlikna datainmatning.
* Den andra aktiviteten omvandlar data i Spark-klustret. Skriptet omvandlar data genom att ta bort oönskade kolumner. Det lägger också till en ny kolumn som beräknar intäkten som en enskild transaktion genererar.

Om du vill konfigurera din Azure Data Factory-pipeline kör du kommandot nedan.  Du bör fortfarande `hdinsight-sales-insights-etl` vara på katalogen.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Det här skriptet gör följande:

1. Skapar ett tjänsthuvudnamn med `Storage Blob Data Contributor` behörigheter för lagringsgend2-lagringskontot Data Lake Storage.
1. Hämtar en autentiseringstoken för att auktorisera POST-begäranden till [datasjölagringsgeny2-filsystemet REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Fyller i det faktiska namnet på ditt Lagringsgend2-lagringskonto för datasjölagring i filerna `sparktransform.py` och. `query.hql`
1. Hämtar lagringsnycklar för datasjölagringsgend2- och Blob-lagringskonton.
1. Skapar en annan resursdistribution för att skapa en Azure Data Factory-pipeline med tillhörande länkade tjänster och aktiviteter. Lagringsnycklarna skickas som parametrar till mallfilen så att de länkade tjänsterna kan komma åt lagringskontona korrekt.

## <a name="run-the-data-pipeline"></a>Kör datapipelinen

### <a name="trigger-the-data-factory-activities"></a>Utlösa datafabrikens aktiviteter

Den första aktiviteten i datafabrikspipelinen som du har skapat flyttar data från Blob-lagring till DataSjölagring gen2. Den andra aktiviteten tillämpar Spark-omvandlingarna på data och sparar de transformerade CSV-filerna på en ny plats. Hela pipelinen kan ta några minuter att slutföra.

Om du vill hämta namnet på Data Factory anger du följande kommando:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Om du vill utlösa pipelinen kan du antingen:

* Utlösa datafabrikspipelinen i PowerShell. Ersätt `RESOURCEGROUP`och `DataFactoryName` med lämpliga värden och kör sedan följande kommandon:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Kör om `Get-AzDataFactoryV2PipelineRun` efter behov för att övervaka förloppet.

    Eller

* Öppna datafabriken och välj **Författare & Monitor**. Utlösa `IngestAndTransform` pipelinen från portalen. Information om hur du utlöser pipelines via portalen finns i [Skapa Apache Hadoop-kluster på begäran i HDInsight med Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Om du vill kontrollera att pipelinen har körts kan du vidta något av följande:

* Gå till avsnittet **Monitor** i datafabriken via portalen.
* Gå till ditt lagringsgend 2-lagringskonto i Azure Storage Explorer. Gå till `files` filsystemet och gå `transformed` sedan till mappen och kontrollera dess innehåll för att se om pipelinen lyckades.

Andra sätt att omvandla data med hjälp av HDInsight finns i [den här artikeln om hur du använder Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Skapa en tabell i det interaktiva frågeklustret för att visa data på Power BI

1. Kopiera `query.hql` filen till LLAP-klustret med hjälp av SCP. Ange kommandot:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Påminnelse: Standardlösenordet är `Thisisapassword1`.

1. Använd SSH för att komma åt LLAP-klustret. Ange kommandot:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Använd följande kommando för att köra skriptet:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Det här skriptet skapar en hanterad tabell i det interaktiva frågekluster som du kan komma åt från Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Skapa en Power BI-instrumentpanel från försäljningsdata

1. Öppna Power BI Desktop.

1. Från menyn navigerar du till **Hämta data** > **mer...**  >  **Azure**Azure > **HDInsight interaktiv fråga**.

1. Välj **Anslut**.

1. I dialogrutan **HDInsight Interactive Query:**
    1. I textrutan **Server** anger du namnet på LLAP-klustret i formatet `https://LLAPCLUSTERNAME.azurehdinsight.net`.
    1. Skriv i `default` **textrutan för databasen** .
    1. Välj **OK**.

1. Från **AzureHive-dialogrutan:**
    1. Skriv `admin`i textrutan **Användarnamn** .
    1. Skriv `Thisisapassword1`i textrutan **Lösenord** .
    1. Välj **Anslut**.

1. Välj och/eller `sales_raw` förhandsgranska data i **Navigator.** `sales` När data har lästs in kan du experimentera med instrumentpanelen som du vill skapa. Se följande länkar för att komma igång med Power BI-instrumentpaneler:

* [Introduktion till instrumentpaneler för Power BI-designers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Självstudiekurs: Kom igång med Power BI-tjänsten](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort alla resurser med hjälp av följande kommando så att du inte debiteras för dem.

1. Om du vill ta bort resursgruppen anger du kommandot:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Om du vill ta bort tjänstens huvudnamn anger du kommandona:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och ladda (ETL) i stor skala](./hadoop/apache-hadoop-etl-at-scale.md)
