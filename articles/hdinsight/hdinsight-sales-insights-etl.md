---
title: 'Självstudie: skapa en ETL-pipeline från slut punkt till slut punkt för att härleda Sälj insikter'
description: Lär dig hur du använder skapa ETL-pipelines med Azure HDInsight för att härleda insikter från försäljnings data med Spark-kluster på begäran och Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: d662ad59722658ed888aa732c1f45afdf48f850c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889211"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Självstudie: skapa en pipeline från slut punkt till slut punkt för att härleda Sälj insikter

I den här självstudien skapar du en pipeline från slut punkt till slut punkt som utför åtgärder för att extrahera, transformera och läsa in (ETL). Pipelinen använder Apache Spark-och Apache Hive-kluster som körs på Azure HDInsight för att skicka frågor till och ändra data. Du använder också tekniker som Azure Data Lake Storage Gen2 för data lagring och Power BI för visualisering.

Den här datapipelinen kombinerar data från olika butiker, tar bort alla oönskade data, lägger till nya data och läser in allt detta på lagrings platsen för att visualisera affärs insikter. Läs mer om ETL-pipelines i [Extract, Transform och load (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)i stor skala.

![ETL-arkitektur](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

Hämta [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) för att visualisera affärs insikter i slutet av den här självstudien.

## <a name="create-resources"></a>Skapa resurser

### <a name="clone-the-repository-with-scripts-and-data"></a>Klona lagrings platsen med skript och data

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Öppna Azure Cloud Shell från den översta meny raden. Välj din prenumeration för att skapa en fil resurs om Cloud Shell uppmanas till det.

   ![Öppna Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. I list rutan **Välj miljö** väljer du **bash**.
1. Logga in på ditt Azure-konto och ange prenumerationen. 
1. Konfigurera resurs gruppen för projektet.
   1. Välj ett unikt namn för resursgruppen.
   1. Kör följande kodfragment i Cloud Shell för att ange variabler som ska användas i senare steg:

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Hämta data och skript för den här självstudien från [ETL-databasen för HDInsight Sales Insights](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) genom att ange följande kommandon i Cloud Shell:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Ange `ls` i Shell-prompten för att se att följande filer och kataloger har skapats:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuera Azure-resurser som krävs för pipelinen 

1. Lägg till körnings behörigheter för `chmod +x scripts/*.sh`-skriptet.
1. Använd kommandot `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` för att köra skriptet för att distribuera följande resurser i Azure:

   1. Ett Azure Blob Storage-konto. Det här kontot kommer att innehålla företagets försäljnings data.
   2. Ett Azure Data Lake Storage Gen2 konto. Det här kontot kommer att fungera som lagrings konto för båda HDInsight-klustren. Läs mer om HDInsight och Data Lake Storage Gen2 i [Azure HDInsight-integrering med data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
   3. En användare som tilldelats en hanterad identitet. Det här kontot ger HDInsight-kluster åtkomst till Data Lake Storage Gen2 kontot.
   4. Ett Apache Spark kluster. Klustret kommer att användas för att rensa och transformera rå data.
   5. Ett Apache Hive Interactive Query-kluster. Det här klustret kommer att tillåta att fråga försäljnings data och visualisera dem med Power BI.
   6. Ett virtuellt Azure-nätverk som stöds av regler för nätverks säkerhets grupper (NSG). Det här virtuella nätverket gör att klustren kan kommunicera och säkra sin kommunikation. 

Det kan ta ungefär 20 minuter att skapa kluster.

Skriptet `resources.sh` innehåller följande kommando. Det här kommandot använder en Azure Resource Manager-mall (`resourcestemplate.json`) för att skapa de angivna resurserna med önskad konfiguration.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

`resources.sh` skriptet överför också försäljnings data. csv-filer till det nya Blob Storage-kontot med hjälp av det här kommandot:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Standard lösen ordet för SSH-åtkomst till klustren är `Thisisapassword1`. Om du vill ändra lösen ordet går du till `resourcesparameters.json`-filen och ändrar lösen ordet för parametrarna `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` och `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verifiera distribution och samla in resursinformation

1. Om du vill kontrol lera statusen för din distribution går du till resurs gruppen på Azure Portal. Välj **distributioner** under **Inställningar**. Välj namnet på distributionen `ResourcesDeployment`. Här kan du se vilka resurser som har distribuerats och vilka resurser som fortfarande pågår.
1. När distributionen är färdig går du till Azure Portal > **resurs grupper** > < RESOURCE_GROUP_NAME >.
1. Leta upp det nya Azure Storage-kontot som skapades för att lagra Sales-filerna. Namnet på lagrings kontot börjar med `blob` och innehåller sedan en slumpmässig sträng. Gör följande:
   1. Anteckna namnet på lagrings kontot för senare användning.
   1. Välj namnet på Blob Storage-kontot.
   1. På vänster sida av portalen under **Inställningar**väljer du **åtkomst nycklar**.
   1. Kopiera strängen i rutan **KEY1** och spara den för senare användning.
1. Leta upp det Data Lake Storage Gen2 konto som skapades som lagring för HDInsight-klustren. Det här kontot finns i samma resurs grupp som Blob Storage-kontot, men börjar med `adlsgen2`. Gör följande:
   1. Anteckna namnet på Data Lake Storage Gen2 kontot.
   1. Välj namnet på Data Lake Storage Gen2 kontot.
   1. Välj **åtkomst nycklar**under **Inställningar**på vänster sida av portalen.
   1. Kopiera strängen i rutan **KEY1** och spara den för senare användning.

> [!Note]
> När du känner till namnen på lagrings kontona kan du hämta konto nycklar genom att använda följande kommando i Azure Cloud Shell prompten:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Azure Data Factory är ett verktyg som hjälper dig att automatisera Azure-pipeliner. Det är inte det enda sättet att utföra dessa uppgifter, men det är ett bra sätt att automatisera processerna. Mer information om Azure Data Factory finns i Azure Data Factory- [dokumentationen](https://azure.microsoft.com/services/data-factory/). 

Den här data fabriken kommer att ha en pipeline med två aktiviteter: 

- Den första aktiviteten kopierar data från Azure Blob Storage till Data Lake Storage gen 2-lagrings konto för att efterlikna data inmatningen.
- Den andra aktiviteten kommer att transformera data i Spark-klustret. Skriptet transformerar data genom att ta bort oönskade kolumner. Den lägger också till en ny kolumn som beräknar intäkterna som en enskild transaktion genererar.

Konfigurera Azure Data Factory pipelinen genom att köra `adf.sh`-skriptet:

1. Använd `chmod +x adf.sh` om du vill lägga till körnings behörigheter för filen.
1. Använd `./adf.sh` för att köra skriptet. 

Det här skriptet gör följande:

1. Skapar ett huvud namn för tjänsten med `Storage Blob Data Contributor`-behörigheter på Data Lake Storage Gen2 lagrings kontot.
1. Hämtar en autentiseringstoken för att auktorisera POST begär anden till [data Lake Storage Gen2 fil system REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Fyller i det faktiska namnet på ditt Data Lake Storage Gen2 lagrings konto i `sparktransform.py`-och `query.hql`-filer.
1. Hämtar lagrings nycklar för Data Lake Storage Gen2-och Blob Storage-konton.
1. Skapar en annan resurs distribution för att skapa en Azure Data Factory pipeline med tillhör ande länkade tjänster och aktiviteter. Den överför lagrings nycklarna som parametrar till mallfilen så att de länkade tjänsterna kan komma åt lagrings kontona på rätt sätt.

Data Factory pipelinen distribueras via följande kommando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Kör data pipelinen

### <a name="trigger-the-data-factory-activities"></a>Utlös Data Factory aktiviteter

Den första aktiviteten i Data Factory pipelinen som du har skapat flyttar data från Blob Storage till Data Lake Storage Gen2. Den andra aktiviteten använder Spark-omvandlingarna för data och sparar transformerade CSV-filer på en ny plats. Det kan ta några minuter att slutföra hela pipelinen.

Om du vill utlösa pipelinen kan du antingen:

- Kör följande kommandon för att utlösa Data Factory pipelines i PowerShell: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Öppna data fabriken och välj **redigera & övervakare**. Utlös kopierings pipelinen och sedan Spark-pipelinen från portalen. Information om hur du aktiverar pipelines via portalen finns i [skapa Apache Hadoop kluster på begäran i HDInsight med Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Du kan kontrol lera att pipelinerna har körts genom att utföra något av följande steg:

- Gå till **Monitor** -avsnittet i din data fabrik via portalen.
- I Azure Storage Explorer går du till ditt Data Lake Storage gen 2-lagrings konto. Gå till fil systemet `files` och gå sedan till mappen `transformed` och kontrol lera dess innehåll för att se om pipelinen lyckades.

Andra sätt att transformera data med hjälp av HDInsight finns i [den här artikeln om hur du använder Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Skapa en tabell i det interaktiva fråga klustret om du vill visa data på Power BI

1. Kopiera `query.hql`-filen till LLAP-klustret med hjälp av SCP: n:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Använd SSH för att få åtkomst till LLAP-klustret med hjälp av följande kommando och ange sedan lösen ordet. Om du inte har ändrat `resourcesparameters.json`-filen är lösen ordet `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Använd följande kommando för att köra skriptet:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Det här skriptet skapar en hanterad tabell på det interaktiva Query-kluster som du kan komma åt från Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Skapa en Power BI instrument panel från försäljnings data

1. Öppna Power BI Desktop.
1. Välj **Hämta data**.
1. Sök efter **kluster för interaktiv HDInsight-fråga**.
1. Klistra in URI: n för klustret där. Versionsformatet ska vara `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

   Ange `default` för databasen.
1. Ange det användar namn och lösen ord som du använder för att komma åt klustret.

När data har lästs in kan du experimentera med den instrument panel som du vill skapa. Se följande länkar för att komma igång med Power BI-instrumentpaneler:

* [Introduktion till instrument paneler för Power BI designers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Självstudie: kom igång med Power BI-tjänst](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort alla resurser genom att använda följande kommando så att du inte debiteras för dem.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in (ETL) i skala](./hadoop/apache-hadoop-etl-at-scale.md)
