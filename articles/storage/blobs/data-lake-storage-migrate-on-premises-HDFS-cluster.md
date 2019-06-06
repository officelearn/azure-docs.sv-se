---
title: Använd Azure Data Box för att migrera data från lokala HDFS lagra till Azure Storage
description: Migrera data från en lokal HDFS-databas till Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/05/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9a42135df38cde91cc6626a3f7d0328334af0a5d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729038"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Använda Azure Data Box för att migrera data från en lokal HDFS-databas till Azure Storage

Du kan migrera data från ett lokalt HDFS Arkiv av ditt Hadoop-kluster till Azure Storage (blob-lagring eller Data Lake Storage Gen2) med hjälp av en Data Box-enhet. Du kan välja från en 80 TB Data box-enhet eller en 770 TB Data Box-aktiverat.

Den här artikeln hjälper dig att utföra dessa uppgifter:

:heavy_check_mark: Kopiera dina data till en Data Box eller en Data Box tung enhet.

:heavy_check_mark: Skicka tillbaka enheten till Microsoft.

:heavy_check_mark: Flytta data till ditt Data Lake Storage Gen2 storage-konto.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver dessa saker att slutföra migreringen.

* Azure Storage-konto som **inte** har aktiverade på den hierarkiska namnområden.

* Om du vill använda Azure Data Lake Storage Gen2 kontot (ett lagringskonto som **har** har aktiverade på den hierarkiska namnområden), och du kanske vill skapa den nu.

* Ett lokalt Hadoop-kluster som innehåller källdata.

* En [Azure Data Box-enhet](https://azure.microsoft.com/services/storage/databox/).

    - [Beställa Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) eller [Data Box aktiverat](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). När ordning din enhet, Kom ihåg att välja ett lagringskonto som **inte** har aktiverade på den hierarkiska namnområden. Det beror på att Data Box-enheter inte stöder ännu direkt inmatning i Azure Data Lake Storage Gen2. Du måste kopiera till ett lagringskonto och gör sedan en kopia till Gen2 ADLS-konto. Instruktioner för detta anges i stegen nedan.
    - Ansluta och ansluta din [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) eller [Data Box tung](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) till ett lokalt nätverk.

Om du är redo kan vi ska börja.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopiera dina data till en Data Box-enhet

Om du vill kopiera data från ditt lokala HDFS store till en Data Box-enhet, ska du konfigurera några saker och sedan använder den [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) verktyget.

Om mängden data som du kopierar är mer än kapaciteten för en enda Data Box eller som en nod på Data Box tung kan du dela upp datauppsättningen till storlekar som ryms i dina enheter.

Följ dessa steg för att kopiera data via REST API: er för Blob/Object-lagring till din Data Box-enhet. REST API-gränssnittet gör enheten visas som ett HDFS-Arkiv i klustret. 


1. Identifiera primitiver för säkerhet och anslutningen att ansluta till REST-gränssnittet på Data Box eller Data Box tung innan du kopierar data via REST. Logga in på det lokala webbtjänst Konfigurationssgränssnittet i Data Box och gå till **Anslut och kopiera** sidan. Mot Azure storage-konto för din enhet under **åtkomstinställningar**, leta upp och välj **REST**.

    ![”Anslut och Kopiera”-sida](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. I storage-konto för åtkomst och dialogrutan för filöverföring av data, kopiera den **Blobbtjänstens slutpunkt** och **lagringskontonyckel**. Från blob-tjänstens slutpunkt utelämna den `https://` och avslutande snedstreck.

    I det här fallet slutpunkten är: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Värddelen av den URI som du använder är: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Ett exempel finns i så här [Anslut till REST över http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogrutan ”åtkomst till lagringskontot och ladda upp data”](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Lägga till slutpunkten och Data Box eller Data Box tung noden IP-adressen `/etc/hosts` på varje nod.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Om du använder någon annan mekanism för DNS, bör du kontrollera att Data Box-slutpunkt kan matchas.
    
4. Ange en gränssnittsvariabel `azjars` så att den pekar till den `hadoop-azure` och `microsoft-windowsazure-storage-sdk` jar-filer. De här filerna är under installationskatalogen Hadoop (du kan kontrollera om de här filerna som finns i det här kommandot `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` där `<hadoop_install_dir>` är den katalog där du har installerat Hadoop) använder de kompletta sökvägarna. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. Skapa behållaren som du vill använda för kopiering av data. Som en del av det här kommandot bör du också ange en målmapp. Detta kan vara en dummy målmapp nu.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. Kör en lista-kommando för att se till att din behållare och en mapp har skapats.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. Kopiera data från Hadoop HDFS till Data Box Blob storage till den behållare som du skapade tidigare. Om den mapp som du kopierar till inte hittas skapas kommandot den automatiskt.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   Den `-libjars` alternativet används för att göra den `hadoop-azure*.jar` och beroende `azure-storage*.jar` filer tillgängliga för `distcp`. Detta kan redan ha vissa kluster.
   
   I följande exempel visas hur `distcp` används för att kopiera data.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Att kopiera snabbare:
- Försök att ändra antalet Mappningskomponenter. (I ovanstående exempel används `m` = 4 Mappningskomponenter.)
- Försök att köra flera `distcp` parallellt.
- Kom ihåg att stora filer bättre prestanda än små filer.
    
## <a name="ship-the-data-box-to-microsoft"></a>Leverera Data Box till Microsoft

Följ dessa steg för att förbereda och skicka Data Box-enhet till Microsoft.

1. När Datakopieringen är klar kör du:
    
    - [Förbered för att skicka på din Data Box eller Data Box tung](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).
    - När enheten förberedelser har slutförts kan du ladda ner BOM-filer. Du ska använda dessa BOM eller manifest filer senare för att kontrollera data har överförts till Azure. 
    - Stäng enheten och ta bort kablarna.
2.  Schemalägga en upphämtning med Avbrottsfria. Följ anvisningarna för att:

    - [Leverera din Data box-enhet](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 
    - [Leverera din Data Box-aktiverat](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).
3.  När Microsoft tar emot din enhet, det är anslutet till datacenternätverket och data har överförts till storage-kontot som du har angett (med hierarkisk namnområden inaktiverad) när du placerade ordningen som enheten. Kontrollera att alla dina data har överförts till Azure mot BOM-filer. Du kan nu flytta dessa data till ett lagringskonto för Data Lake Storage Gen2.


## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Flytta data till ditt Data Lake Storage Gen2 storage-konto

Det här steget krävs om du använder Azure Data Lake Storage Gen2 som datalager. Om du använder bara ett blob storage-konto utan hierarkiskt namnområde som datalager, behöver du inte utför det här steget.

Du kan göra detta på två sätt.

- Använd [Azure Data Factory för att flytta data till ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Du måste ange **Azure Blob Storage** som källa.

- Använd ditt Azure-baserade Hadoop-kluster. Du kan köra det här kommandot DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Det här kommandot kopierar både data och metadata från ditt lagringskonto till ditt Data Lake Storage Gen2 storage-konto.

## <a name="next-steps"></a>Nästa steg

Lär dig hur Data Lake Storage Gen2 fungerar med HDInsight-kluster. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
