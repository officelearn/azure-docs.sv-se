---
title: 'Självstudier: Skapa en pipeline för extrahering, transformering, load (ETL) för att härleda Sälj insikter'
description: Lär dig hur du använder skapa ETL-pipelines med Azure HDInsight för att härleda insikter från försäljnings data med Spark-kluster på begäran och Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695822"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Självstudier: Skapa en pipeline från slut punkt till slut punkt för att härleda Sälj insikter

I den här självstudien skapar du en pipeline för data slut punkt till slut punkt som utför extraherings-, omvandlings-och inläsnings åtgärder. Pipelinen använder Apache Spark-och Apache Hive-kluster som körs på Azure HDInsight för att skicka frågor till och ändra data. Andra tekniker som används är Data Lake Storage Gen2 för data lagring och Power BI för visualisering.

Den här datapipelinen kombinerar data från alla olika butiker, tar bort alla oönskade data, lägger till nya data och läser in dem till lagrings utrymmet för att visualisera affärs insikter. Läs mer om ETL-pipeline [: extrahera, transformera och Läs in (ETL) i stor skala](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-arkitektur](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

Hämta [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) för att visualisera affärs insikter i slutet av den här självstudien.

## <a name="create-resources"></a>Skapa resurser

### <a name="clone-the-repository-with-scripts-and-data"></a>Klona lagrings platsen med skript och data

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Öppna Cloud Shell från den övre meny raden. Välj din prenumeration för att skapa en fil resurs om du uppmanas att göra det Azure Cloud Shell.

    ![Öppna Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Välj "bash" på den nedrullningsbara menyn "Välj miljö".
1. Logga in på ditt Azure-konto och ange prenumerationen. 
1. Konfigurera resurs gruppen för projektet.
    1. Välj ett unikt resurs grupp namn.
    1. Kör kodfragmentet nedan i Azure Cloud Shell för att ange variabler som ska användas i senare steg

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Hämta data och skript för den här självstudien från [ETL-databasen för HDInsight Sales Insights](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) genom att skriva följande kommandon på Cloud Shell:

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. Skriv `ls` vid kommando tolken för att se följande filer och kataloger har skapats:

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuera Azure-resurser som krävs för pipelinen 

1. Lägg till körnings behörigheter för skriptet `chmod +x scripts/*.sh`
1. Använd kommandot `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` för att köra skriptet för att distribuera följande resurser i Azure:

    1. Ett Azure Blob Storage-konto – det här kontot kommer att innehålla företagets försäljnings data
    2. Ett Azure Data Lake Storage Gen2 konto – det här kontot kommer att fungera som lagrings konto för båda HDInsight-klustren. Läs mer om HDInsight och Data Lake Storage Gen2 i [Azure HDInsight-integrering med data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    3. En användardefinierad hanterad identitet – det här kontot ger HDInsight-kluster åtkomst till Data Lake Storage Gen2 kontot.
    4. En Apache Spark Luster – det här klustret kommer att användas för att rensa och transformera rå data
    5. Ett Apache Hive Interactive Query-kluster – det här klustret kommer att tillåta att frågor mot försäljnings data visualiseras med Power BI
    6. Ett virtuellt Azure-nätverk som stöds av regler för nätverks säkerhets grupper (NSG) – det här virtuella nätverket gör det möjligt för kluster att kommunicera och även säkra kommunikationen. 

Det kan ta ungefär 20 minuter att skapa kluster.

@No__t-0-skriptet innehåller följande kommando, som använder en Resource Manager-mall (`resourcestemplate.json`) för att skapa de angivna resurserna med önskad konfiguration.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

@No__t-0-skriptet överför också CSV-filerna för försäljning till det nya Blob Storage-kontot med hjälp av kommandot:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Standard lösen ordet som används för SSH-åtkomst till klustret är `Thisisapassword1`. Om du vill ändra lösen ordet navigerar du till `resourcesparameters.json`-filen och ändrar lösen ordet för parametrarna `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` och `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verifiera distribution och samla in resursinformation

1. Om du vill kontrol lera statusen för din distribution navigerar du till resurs gruppen på Azure Portal. Klicka på **distributioner** under **Inställningar**. Klicka på namnet på distributionen `ResourcesDeployment`. Här kan du se de resurser som har distribuerats och de som fortfarande pågår.
1. När distributionen har slutförts går du till Azure Portal > **resurs grupper** > < RESOURCE_GROUP_NAME >
1. Leta upp det nya Azure Storage-kontot som skapades för att lagra Sales-filerna. Namnet på lagrings kontot börjar med `blob` och innehåller sedan en slumpmässig sträng. 
    1. Anteckna namnet på lagrings kontot för senare användning.
    1. Klicka på namnet på Blob Storage-kontot.
    1. På vänster sida av portalen under **Inställningar**klickar du på **åtkomst nycklar**.
    1. Kopiera strängen i rutan **KEY1** och spara den för senare användning.
1. Leta upp det Data Lake Storage Gen2 konto som skapades som lagring för HDInsight-klustren. Det här kontot finns i samma resurs grupp som Blob Storage-kontot, men börjar med `adlsgen2`.
    1. Anteckna namnet på Data Lake Storage Gen2 kontot.
    1. Klicka på namnet på Data Lake Storage Gen2 kontot.
    1. Klicka på **åtkomst nycklar** under **Inställningar**på vänster sida av portalen
    1. Kopiera strängen i rutan **KEY1** och spara den för senare användning.

> [!Note]
> När du känner till namnen på lagrings kontona kan du också hämta konto nycklar med hjälp av följande kommando i Azure Cloud Shell prompten:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Skapa en Azure Data Factory

Azure Data Factory är ett verktyg som hjälper dig att automatisera Azure-pipeliner. Det är inte det enda sättet att utföra dessa uppgifter, men det är ett bra sätt att automatisera dessa processer. Mer information om Azure Data Factory finns i Läs mer om IT- [Azure Data Factory-dokumentationen](https://azure.microsoft.com/services/data-factory/). 

Den här Azure Data Factory får en pipeline med två aktiviteter: 

1. Den första aktiviteten kopierar data från Azure-Blob Storage till Data Lake Storage gen 2-lagrings konto för att efterlikna data inmatningen.
2. Den andra aktiviteten kommer att transformera data i Spark-klustret. Skriptet transformerar data genom att ta bort oönskade kolumner och lägga till en ny kolumn som beräknar intäkterna som genereras av en enskild transaktion.

Om du vill konfigurera Azure Data Factory pipelinen kör du skriptet `adf.sh`:

1. Lägg till kör behörighet för filen med `chmod +x adf.sh`
1. Kör skriptet med `./adf.sh` 

Det här skriptet gör följande:

1. Skapar ett huvud namn för tjänsten med `Storage Blob Data Contributor`-behörigheter på Data Lake Storage Gen2 lagrings kontot.
1. Hämtar en autentiseringstoken för att auktorisera POST förfrågningar till [data Lake Storage Gen2 fil systemet REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Fyller i det faktiska namnet på ditt Data Lake Storage Gen2 lagrings konto i `sparktransform.py`-och `query.hql`-filer.
1. Hämtar lagrings nycklar för Data Lake Storage Gen2-och Blob Storage-kontot.
1. Skapar en annan resurs distribution för att skapa en Azure Data Factory pipeline med tillhör ande länkade tjänster och aktiviteter. Den överför lagrings nycklarna som parametrar till mallfilen så att de länkade tjänsterna kan komma åt lagrings kontona på rätt sätt.

ADF-pipeline distribueras med hjälp av följande kommando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Kör data pipelinen

### <a name="trigger-the-adf-activities"></a>Utlös ADF-aktiviteter

Den första aktiviteten i den ADF-pipeline som du har skapat flyttar data från Blob Storage till Data Lake Storage Gen2. Den andra aktiviteten använder Spark-omvandlingarna för data och sparar transformerade CSV-filer på en ny plats. Hela pipelinen kan ta några minuter att slutföra.

Om du vill utlösa pipelinen kan du antingen:

1. Kör följande kommandon för att aktivera ADF-pipelinen i PowerShell. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. Du kan också öppna Data Factory, välja författare & övervaka och utlösa kopierings pipelinen, sedan Spark-pipelinen från portalen. Se [skapa Apache Hadoop kluster på begäran i HDInsight med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline) för information om att utlösa pipelines via portalen.

För att kontrol lera att pipelinen har körts kan du göra något av följande:

1. Navigera till **Monitor** -avsnittet i Azure Data Factory via portalen.
2. Gå till Data Lake Storage gen 2 lagrings konto lagrings Utforskaren, gå till `files`-fil systemet och navigera till mappen `transformed` och kontrol lera dess innehåll för att se om pipelinen lyckades.

Andra sätt att transformera data med hjälp av HDInsight finns i den här artikeln om att använda [Jupyter Notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query)

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Skapa en tabell i det interaktiva fråga klustret om du vill visa data på Power BI

1. Kopiera filen Query. HQL till LLAP-klustret med SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Använd SSH i LLAP-klustret med följande kommando och ange ditt lösen ord. Om du inte har ändrat `resourcesparameters.json`-filen är lösen ordet `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Kör följande kommando för att köra skriptet

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Det här skriptet skapar en hanterad tabell på det interaktiva Query-kluster som du kan komma åt från Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Skapa en Power BI instrument panel från försäljnings data

1. Öppna Power BI Desktop
1. Välj **Hämta data**.
1. Sök efter **kluster för interaktiv HDInsight-fråga**.
1. Klistra in URI: n för klustret där. Den ska vara i formatet `https://<LLAP CLUSTER NAME>.azurehdinsight.net`, typ `default` för databasen.
1. Ange det användar namn och lösen ord som du använder för att komma åt klustret.

När data har lästs in kan du experimentera med instrument panelen som du vill skapa. Se följande länkar för att komma igång med Power BI-instrumentpaneler:

* [Introduktion till instrument paneler för Power BI designers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Självstudier: Kom igång med Power BI-tjänst @ no__t-0

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort alla resurser med följande steg så att du inte debiteras för dem.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in (ETL) i skala](./hadoop/apache-hadoop-etl-at-scale.md)
