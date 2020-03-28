---
title: 'Självstudiekurs: Skapa en heltäckande ETL-pipeline för att härleda försäljningsinsikter i Azure HDInsight'
description: Lär dig hur du använder skapa ETL-pipelines med Azure HDInsight för att härleda insikter från försäljningsdata med hjälp av Spark on-demand-kluster och Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247273"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Självstudiekurs: Skapa en datapipeline från slutna till slutna dagar för att härleda försäljningsinsikter i Azure HDInsight

I den här självstudien ska du skapa en end-to-end-datapipeline som utför extrahera, transformera och läsa in (ETL) åtgärder. Pipelinen använder [Apache Spark-](./spark/apache-spark-overview.md) och Apache Hive-kluster som körs på Azure HDInsight för att fråga och manipulera data. Du använder även tekniker som Azure Data Lake Storage Gen2 för datalagring och Power BI för visualisering.

Den här datapipelinen kombinerar data från olika butiker, tar bort oönskade data, lägger till nya data och läser in allt detta i ditt lagringsutrymme för att visualisera affärsinsikter. Läs mer om ETL-pipelines i [Extrahera, transformera och ladda (ETL) i stor skala](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-arkitektur](./media/hdinsight-sales-insights-etl/architecture.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Azure CLI. Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* En medlem [av azure-inbyggd roll - ägare](../role-based-access-control/built-in-roles.md).

* [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) för att visualisera affärsinsikter i slutet av den här självstudien.

## <a name="create-resources"></a>Skapa resurser

### <a name="clone-the-repository-with-scripts-and-data"></a>Klona databasen med skript och data

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna Azure Cloud Shell i det övre menyraden. Välj din prenumeration för att skapa en filresurs om Cloud Shell frågar dig.

   ![Öppna Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. **Välj**Bash på listrutan **Välj miljö** .

1. Se till att du är [owner](../role-based-access-control/built-in-roles.md)medlem i Azure-rollägaren . Ersätt `user@contoso.com` med ditt konto och ange sedan kommandot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Om ingen post returneras är du inte medlem och kan inte slutföra den här självstudien.

1. Lista dina prenumerationer som anger kommandot:

    ```azurecli
    az account list --output table
    ```

    Observera ID:t för den prenumeration som du ska använda för det här projektet.

1. Ange den prenumeration som du ska använda för det här projektet. Ersätt `SUBSCRIPTIONID` med det faktiska värdet och ange sedan kommandot.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Skapa en ny resursgrupp för projektet. Ersätt `RESOURCEGROUP` med önskat namn och ange sedan kommandot.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Ladda ner data och skript för den här självstudien från [HDInsight försäljningsstatistik ETL-arkivet](https://github.com/Azure-Samples/hdinsight-sales-insights-etl).  Ange följande kommando:

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

1. Kör skriptet. Ersätt `RESOURCE_GROUP_NAME` `LOCATION` och med relevanta värden och ange sedan kommandot:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    Kommandot distribuerar följande resurser:

    * Ett Azure Blob-lagringskonto. Det här kontot kommer att innehålla företagets försäljningsdata.
    * Ett Azure Data Lake Storage Gen2-konto. Det här kontot fungerar som lagringskonto för båda HDInsight-kluster. Läs mer om HDInsight och Data Lake Storage Gen2 i [Azure HDInsight-integrering med Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * En användartilldelad hanterad identitet. Det här kontot ger HDInsight-klustren åtkomst till Data Lake Storage Gen2-kontot.
    * Ett Apache Spark-kluster. Det här klustret används för att rensa och omvandla rådata.
    * Ett Apache Hive [Interactive Query-kluster.](./interactive-query/apache-interactive-query-get-started.md) Det här klustret gör det möjligt att fråga försäljningsdata och visualisera dem med Power BI.
    * Ett virtuellt Azure-nätverk som stöds av NSG-regler (Network Security Group). Det här virtuella nätverket gör det möjligt för kluster att kommunicera och säkrar sin kommunikation.

Det kan ta cirka 20 minuter att skapa kluster.

Skriptet `resources.sh` innehåller följande kommandon. Det krävs inte för dig att köra dessa kommandon om du redan kört skriptet i föregående steg.

* `az group deployment create`- Det här kommandot använder`resourcestemplate.json`en Azure Resource Manager-mall ( ) för att skapa de angivna resurserna med önskad konfiguration.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch`- Det här kommandot överför försäljningsdata .csv-filer till det nyskapade Blob-lagringskontot med det här kommandot:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

Standardlösenordet för SSH-åtkomst `Thisisapassword1`till klustren är . Om du vill ändra lösenordet går `resourcesparameters.json` du till filen `sparksshPassword`och `sparkClusterLoginPassword` `llapClusterLoginPassword`ändrar `llapsshPassword` lösenordet för parametrarna , , och.

### <a name="verify-deployment-and-collect-resource-information"></a>Verifiera distribution och samla in resursinformation

1. Om du vill kontrollera status för distributionen går du till resursgruppen på Azure-portalen. Välj **Distributioner** under **Inställningar**. Välj namnet på distributionen. `ResourcesDeployment` Här kan du se de resurser som har distribuerats och de resurser som fortfarande pågår.

1. Om du vill visa namnen på klustren anger du följande kommando:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Om du vill visa Azure-lagringskontot och åtkomstnyckeln anger du följande kommando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Om du vill visa kontot och åtkomstnyckeln för DataSjölagringsgen2 anger du följande kommando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

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

Så här konfigurerar du azure data factory-pipelinen:

```bash
./scripts/adf.sh
```

Det här skriptet gör följande:

1. Skapar ett tjänsthuvudnamn med `Storage Blob Data Contributor` behörigheter för lagringsgend2-lagringskontot Data Lake Storage.
1. Hämtar en autentiseringstoken för att auktorisera POST-begäranden till [datasjölagringsgeny2-filsystemet REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Fyller i det faktiska namnet på ditt Lagringsgend2-lagringskonto för datasjölagring i filerna `sparktransform.py` och. `query.hql`
1. Hämtar lagringsnycklar för datasjölagringsgend2- och Blob-lagringskonton.
1. Skapar en annan resursdistribution för att skapa en Azure Data Factory-pipeline med tillhörande länkade tjänster och aktiviteter. Lagringsnycklarna skickas som parametrar till mallfilen så att de länkade tjänsterna kan komma åt lagringskontona korrekt.

Data Factory-pipelinen distribueras via följande kommando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Kör datapipelinen

### <a name="trigger-the-data-factory-activities"></a>Utlösa datafabrikens aktiviteter

Den första aktiviteten i datafabrikspipelinen som du har skapat flyttar data från Blob-lagring till DataSjölagring gen2. Den andra aktiviteten tillämpar Spark-omvandlingarna på data och sparar de transformerade CSV-filerna på en ny plats. Hela pipelinen kan ta några minuter att slutföra.

Om du vill utlösa pipelines kan du antingen:

* Utlösa datafabrikspipelorna i PowerShell. Ersätt `DataFactoryName` med det faktiska datafabriksnamnet och kör sedan följande kommandon:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    Eller

* Öppna datafabriken och välj **Författare & Monitor**. Utlösa kopieringspipelinen och sedan Spark-pipelinen från portalen. Information om hur du utlöser pipelines via portalen finns i [Skapa Apache Hadoop-kluster på begäran i HDInsight med Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Om du vill kontrollera att pipelines har körts kan du vidta något av följande:

* Gå till avsnittet **Monitor** i datafabriken via portalen.
* Gå till ditt lagringsgend 2-lagringskonto i Azure Storage Explorer. Gå till `files` filsystemet och gå `transformed` sedan till mappen och kontrollera dess innehåll för att se om pipelinen lyckades.

Andra sätt att omvandla data med hjälp av HDInsight finns i [den här artikeln om hur du använder Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Skapa en tabell i det interaktiva frågeklustret för att visa data på Power BI

1. Kopiera `query.hql` filen till LLAP-klustret med hjälp av SCP. Ersätt `LLAPCLUSTERNAME` med det faktiska namnet och ange sedan kommandot:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Använd SSH för att komma åt LLAP-klustret. Ersätt `LLAPCLUSTERNAME` med det faktiska namnet och ange sedan kommandot. Om du inte har `resourcesparameters.json` ändrat filen är `Thisisapassword1`lösenordet .

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Använd följande kommando för att köra skriptet:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Det här skriptet skapar en hanterad tabell i det interaktiva frågekluster som du kan komma åt från Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Skapa en Power BI-instrumentpanel från försäljningsdata

1. Öppna Power BI Desktop.
1. Välj **Hämta data**.
1. Sök efter **klustret HDInsight Interactive Query**.
1. Klistra in URI för klustret där. Versionsformatet ska vara `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Ange `default` för databasen.
1. Ange det användarnamn och lösenord som du använder för att komma åt klustret.

När data har lästs in kan du experimentera med instrumentpanelen som du vill skapa. Se följande länkar för att komma igång med Power BI-instrumentpaneler:

* [Introduktion till instrumentpaneler för Power BI-designers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Självstudiekurs: Kom igång med Power BI-tjänsten](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort alla resurser med hjälp av följande kommando så att du inte debiteras för dem.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och ladda (ETL) i stor skala](./hadoop/apache-hadoop-etl-at-scale.md)
