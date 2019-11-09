---
title: Fråga efter data i HDFS-kompatibelt Azure-lagringsutrymme – Azure HDInsight
description: Lär dig hur du frågar efter data från Azure Storage och Azure Data Lake Storage att lagra resultatet av din analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 1e115c59cab4c340f927da516b5f937abf42e985
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839662"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Använda Azure-lagring med Azure HDInsight-kluster

För att analysera data i HDInsight-klustret kan du lagra data antingen i [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage gen 2](../storage/blobs/data-lake-storage-introduction.md)eller en kombination. Med dessa lagrings alternativ kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning utan att förlora användar data.

Apache Hadoop stöder en begreppet standard fil system. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under skapande processen av HDInsight-kluster kan du ange en BLOB-behållare i Azure Storage som standard fil system, eller med HDInsight 3,6 kan du välja antingen Azure Storage eller Azure Data Lake Storage gen 1/Azure Data Lake Storage gen 2 som standardfiler system med några få undantag. För att kunna använda Data Lake Storage gen 1 som både standard-och länkad lagring, se [tillgänglighet för HDInsight-kluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

I den här artikeln får du lära dig hur Azure Storage fungerar med HDInsight-kluster. Information om hur Data Lake Storage gen 1 fungerar med HDInsight-kluster finns i [använda Azure Data Lake Storage med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-store.md). Mer information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Lagrings konto typen **BlobStorage** kan bara användas som sekundär lagring för HDInsight-kluster.

| Typ av lagrings konto | Tjänster som stöds | Prestanda nivåer som stöds | Åtkomst nivåer som stöds |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (generell användning v2)  | Blob     | Standard                    | Frekvent, låg frekvent, Arkiv\*   |
| Lagring (generell användning v1)   | Blob     | Standard                    | Saknas                    |
| BlobStorage                    | Blob     | Standard                    | Frekvent, låg frekvent, Arkiv\*   |

Vi rekommenderar inte att du använder standard-BLOB-behållaren för lagring av affärs data. Ta bort standardcontainern efter varje användning för att minska lagringskostnaden. Standard behållaren innehåller program-och system loggar. Se till att hämta loggarna innan du tar bort containern.

Delning av en BLOB-behållare som standard fil system för flera kluster stöds inte.

> [!NOTE]  
> Arkiv åtkomst nivån är en offline-nivå som har en svars tid på flera timmar och rekommenderas inte för användning med HDInsight. Mer information finns i [Arkiv åtkomst nivå](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt som du kan använda för att komma åt filerna i Data Lake Storage från ett HDInsight-kluster. URI-schemat ger okrypterad åtkomst (med prefixet *wasb:* ) och SSL-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till kluster roten med:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på data åtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till klustrets huvud nod. I exemplen används alla tre URI-scheman. Ersätt `CONTAINERNAME` och `STORAGEACCOUNT` med relevanta värden

#### <a name="a-few-hdfs-commands"></a>Några HDFS-kommandon

1. Skapa en enkel fil på lokal lagrings plats.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger i kluster lagringen.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till kluster lagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista katalog innehåll i kluster lagringen.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre fil platser visas i syfte att illustrera. För faktisk körning använder du endast en av `LOCATION` posterna.

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

## <a name="identify-storage-path-from-abmari"></a>Identifiera lagrings Sök väg från ABMari

* Om du vill identifiera den fullständiga sökvägen till det konfigurerade standard arkivet går du till:

    **HDFS** > **konfiguration** och anger `fs.defaultFS` i rutan Filter indatamängd.

* Om du vill kontrol lera om wasb Store har kon figurer ATS som sekundär lagring navigerar du till:

    **HDFS** > **konfiguration** och anger `blob.core.windows.net` i rutan Filter indatamängd.

## <a name="blob-containers"></a>Blob-containrar

Om du vill använda blobbar skapar du först ett [Azure Storage-konto](../storage/common/storage-create-storage-account.md). Som en del av detta anger du en Azure-region där lagringskontot ska skapas. Klustret och lagringskontot måste finnas i samma region. Hive-metaarkiv SQL Server Database och Apache Oozie metaarkiv SQL Server Database måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en container på ditt Azure Storage-konto. Den här containern kan vara en befintlig blob som skapats utanför HDInsight eller en container som skapats för ett HDInsight-kluster.

Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-container. Detta kan skada jobbets historik. Vi rekommenderar att du använder en annan behållare för varje kluster och lagrar delade data på ett länkat lagrings konto som anges vid distribution av alla relevanta kluster i stället för standard lagrings kontot. Mer information om hur du konfigurerar länkade lagrings konton finns i [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Du kan emellertid återanvända en standardcontainer för lagring när det ursprungliga HDInsight-klustret har tagits bort. För HBase-kluster kan du faktiskt behålla HBase-tabellens schema och data genom att skapa ett nytt HBase-kluster med hjälp av standard-BLOB-behållaren som används av ett HBase-kluster som har tagits bort.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagera med Azure Storage

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla det till. Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen eller efter att ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Du kan skapa skalbara, långsiktiga lösningar för arkivering av insamlade data samt HDInsight för att få tillgång till informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Kom igång med Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Ladda upp data till HDInsight](hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använd Azure Storage signaturer för delad åtkomst för att begränsa åtkomsten till data med HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Självstudie: extrahera, transformera och läsa in data med hjälp av interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
