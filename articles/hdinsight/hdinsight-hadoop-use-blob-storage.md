---
title: Fråga efter data i HDFS-kompatibelt Azure-lagringsutrymme – Azure HDInsight
description: Lär dig hur du frågar data från Azure storage och Azure Data Lake Storage för att lagra resultat av din analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: cd6ba50cf81b93da887134e89d75313acb6bd936
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869864"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Använda Azure-lagring med Azure HDInsight-kluster

Du kan lagra data i [Azure Storage,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)eller [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Eller en kombination av dessa alternativ. Med de här lagringsalternativen kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

Apache Hadoop stöder en uppfattning om standardfilsystemet. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under hdinsight-klusterskapandeprocessen kan du ange en blob-behållare i Azure Storage som standardfilsystem. Eller med HDInsight 3.6 kan du välja antingen Azure Storage eller Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 som standardfilsystem med några få undantag. För support av att använda Data Lake Storage Gen 1 som både standardlagring och länkad lagring finns i [Tillgänglighet för HDInsight-kluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

I den här artikeln får du lära dig hur Azure Storage fungerar med HDInsight-kluster. Mer information om hur Data Lake Storage Gen 1 fungerar med HDInsight-kluster finns i [Använda Azure Data Lake Storage med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-store.md). Mer information om hur du skapar ett HDInsight-kluster finns i [Skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Lagringskonto typ **BlobStorage** kan endast användas som sekundär lagring för HDInsight-kluster.

| Typ av lagringskonto | Tjänster som stöds | Prestandanivåer som stöds |Prestandanivåer som inte stöds| Åtkomstnivåer som stöds |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (generell användning v2)  | Blob     | Standard                    |Premium| Hot, Cool, Arkiv\*   |
| Lagring (generell användning v1)   | Blob     | Standard                    |Premium| Ej tillämpligt                    |
| BlobStorage (BlobStorage)                    | Blob     | Standard                    |Premium| Hot, Cool, Arkiv\*   |

Vi rekommenderar inte att du använder standardbumpbehållaren för lagring av affärsdata. Ta bort standardcontainern efter varje användning för att minska lagringskostnaden. Standardbehållaren innehåller program- och systemloggar. Se till att hämta loggarna innan du tar bort containern.

Det går inte att dela en blob-behållare som standardfilsystem för flera kluster.

> [!NOTE]  
> Arkivåtkomstnivån är en offlinenivå som har en svarstid för flera timmars hämtning och rekommenderas inte för användning med HDInsight. Mer information finns i [Arkivåtkomstnivå](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Komma åt filer från klustret

Det finns flera sätt att komma åt filerna i DataSjölagring från ett HDInsight-kluster. URI-schemat ger okrypterad åtkomst (med *wasb:* prefix) och TLS-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till klusterroten med:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på dataåtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till huvudnoden för klustret. Exemplen använder alla tre URI-scheman. Ersätta `CONTAINERNAME` `STORAGEACCOUNT` och med relevanta värden

#### <a name="a-few-hdfs-commands"></a>Några hdfs-kommandon

1. Skapa en fil på lokal lagring.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger för klusterlagring.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till klusterlagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista kataloginnehåll på klusterlagring.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre filplatser visas för belysande ändamål. För faktisk körning använder du `LOCATION` bara en av posterna.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Komma åt filer från externt kluster

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzKopia](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identifiera lagringssökväg från Ambari

* Om du vill identifiera den fullständiga sökvägen till det konfigurerade standardarkivet navigerar du till:

    **HDFS** > **Configs** `fs.defaultFS` och ange i filterinmatningsrutan.

* Om du vill kontrollera om wasb-arkivet är konfigurerat som sekundär lagring navigerar du till:

    **HDFS** > **Configs** `blob.core.windows.net` och ange i filterinmatningsrutan.

Information om hur du hämtar sökvägen med Ambari REST API finns i [Hämta standardlagring](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Blob-containrar

Om du vill använda blobar måste du först skapa ett [Azure Storage-konto](../storage/common/storage-create-storage-account.md). Som en del av det här steget anger du en Azure-region där lagringskontot skapas. Klustret och lagringskontot måste finnas i samma region. Hive-metabutiken SQL Server-databasen och Apache Oozie-metabutiken SQL Server-databasen måste finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en container på ditt Azure Storage-konto. Den här behållaren kan vara en befintlig blob som skapats utanför HDInsight. Eller så kan det vara en behållare som skapas för ett HDInsight-kluster.

Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-container. Den här åtgärden kan skada jobbhistoriken. Vi rekommenderar att du använder en annan behållare för varje kluster. Placera delade data på ett länkat lagringskonto som har angetts för alla relevanta kluster i stället för standardlagringskontot. Mer information om hur du konfigurerar länkade lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Du kan emellertid återanvända en standardcontainer för lagring när det ursprungliga HDInsight-klustret har tagits bort. För HBase-kluster kan du behålla HBase-tabellschemat och data genom att skapa ett nytt HBase-kluster med hjälp av standardblobbehållaren som används av ett borttaget HBase-kluster

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla det till. Du kan också lägga till ytterligare lagringskonton från samma Azure-prenumeration eller olika Azure-prenumerationer under skapandeprocessen. Eller när ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Med den här lagringen kan du skapa anpassningsbara, långsiktiga, arkiveringslösningar för datainsamling och använda HDInsight för att låsa upp informationen i de lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Komma igång med Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Överföra data till HDInsight](hdinsight-upload-data.md)
* [Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Självstudiekurs: Extrahera, transformera och läsa in data med interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
