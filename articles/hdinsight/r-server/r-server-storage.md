---
title: Azure-lagringslösningar för ML-tjänster på HDInsight - Azure
description: Lär dig mer om de olika lagringsalternativen som finns med ML Services på HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660247"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure-lagringslösningar för ML-tjänster på Azure HDInsight

ML Services på HDInsight kan använda olika lagringslösningar för att bevara data, kod eller objekt som innehåller resultat från analys. Dessa lösningar omfattar följande alternativ:

- [Azure-blobb](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure-fillagring](https://azure.microsoft.com/services/storage/files/)

Du har också möjlighet att komma åt flera Azure-lagringskonton eller behållare med ditt HDInsight-kluster. Azure File storage är ett praktiskt datalagringsalternativ för användning på kantennoden som gör att du kan montera en Azure-lagringsfilresurs till till exempel Linux-filsystemet. Men Azure File-resurser kan monteras och användas av alla system som har ett operativsystem som stöds, till exempel Windows eller Linux.

När du skapar ett Apache Hadoop-kluster i HDInsight anger du antingen ett **Azure Storage-konto** eller **DataSjölagring**. En specifik lagringsbehållare från det kontot innehåller filsystemet för klustret som du skapar (till exempel Hadoop Distributed File System). Mer information och vägledning finns i:

- [Använda Azure Storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Använda Datasjölagring med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Använda Azure Blob-lagringskonton med ML Services-kluster

Om du angav mer än ett lagringskonto när du skapade ML Services-klustret förklarar följande instruktioner hur du använder ett sekundärt konto för dataåtkomst och åtgärder i ett ML Services-kluster. Antag följande lagringskonton och behållare: **storage1** och en standardbehållare som kallas **container1**och **storage2** med **container2**.

> [!WARNING]  
> För prestandaändamål skapas HDInsight-klustret i samma datacenter som det primära lagringskonto som du anger. Det går inte att använda ett lagringskonto på en annan plats än HDInsight-klustret.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Använda standardlagringen med ML Services på HDInsight

1. Anslut till kantnoden i klustret med hjälp av en SSH-klient. Information om hur du använder SSH med HDInsight-kluster finns i [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Kopiera en exempelfil, mysamplefile.csv, till katalogen /share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Växla till R Studio eller en annan R-konsol och skriv R-kod för att ställa in namnnoden till **standard** och plats för den fil som du vill komma åt.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Alla katalog- och filreferenser pekar `wasbs://container1@storage1.blob.core.windows.net`på lagringskontot . Det här är **standardlagringskontot** som är associerat med HDInsight-klustret.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Använda ytterligare lagringsutrymme med ML Services på HDInsight

Anta nu att du vill bearbeta en fil som heter mysamplefile1.csv som finns i katalogen /private of **container2** i **storage2**.

I R-koden pekar du namnnodens referens till **storage2-lagringskontot.**

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Alla katalog- och filreferenser pekar nu `wasbs://container2@storage2.blob.core.windows.net`på lagringskontot . Det här är **den namnnod** som du har angett.

Konfigurera `/user/RevoShare/<SSH username>` katalogen på **storage2** enligt följande:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Använda Azure Data Lake Storage med ML Services-kluster

Om du vill använda DataSjölagring med ditt HDInsight-kluster måste du ge klustret åtkomst till varje Azure Data Lake Storage som du vill använda. Instruktioner om hur du använder Azure-portalen för att skapa ett HDInsight-kluster med ett Azure Data Lake Storage-konto som standardlagring eller som ytterligare lagring finns i [Skapa ett HDInsight-kluster med DataSjölagring med Azure-portalen](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Du använder sedan lagringen i ditt R-skript ungefär som du gjorde ett sekundärt Azure-lagringskonto enligt beskrivningen i föregående procedur.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Lägga till klusteråtkomst till azure datasjölagring

Du kommer åt DataSjölagring med hjälp av ett Azure Active Directory -tjänsthuvudnamn (Azure AD) som är associerat med ditt HDInsight-kluster.

1. När du skapar HDInsight-klustret väljer du **Cluster AAD Identity** på fliken **Datakälla.**

2. Välj **Skapa nytt**under Välj **AD-tjänsthuvudnamn**i dialogrutan **KlusterAAD-identitet.**

När du har gett tjänsthuvudansvarig ett namn och skapat ett lösenord för det klickar du på **Hantera ADLS-åtkomst** för att associera tjänstens huvudnamn med datasjölagringen.

Det är också möjligt att lägga till klusteråtkomst till ett eller flera lagringskonton för datasjö efter att klustret har skapats. Öppna Azure-portalposten för en DataSjölagring och gå till **Data Explorer > Access > Add**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Så här kommer du åt Data Lake Storage Gen1 från ML Services på HDInsight

När du har gett åtkomst till Data Lake Storage Gen1 kan du använda lagringen i ML Services-klustret på HDInsight på det sätt som du skulle ha ett sekundärt Azure-lagringskonto. Den enda skillnaden är att prefixet **wasbs://** ändras till **adl://** enligt följande:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Följande kommandon används för att konfigurera datasjölagringsgenm1-kontot med katalogen RevoShare och lägga till exempelfilen CSV från föregående exempel:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Använda Azure File Storage med ML Services på HDInsight

Det finns också ett praktiskt datalagringsalternativ för användning på kantnoden [Azure Files](https://azure.microsoft.com/services/storage/files/). Det gör att du kan montera en Azure Storage-filresurs till Linux-filsystemet. Det här alternativet kan vara praktiskt för att lagra datafiler, R-skript och resultatobjekt som kan behövas senare, särskilt när det är vettigt att använda det inbyggda filsystemet på kantnoden i stället för HDFS.

En stor fördel med Azure Files är att filresurserna kan monteras och användas av alla system som har ett operativsystem som stöds, till exempel Windows eller Linux. Den kan till exempel användas av ett annat HDInsight-kluster som du eller någon i ditt team har, av en virtuell Azure-dator eller till och med av ett lokalt system. Mer information finns i:

- [Använd Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Så här använder du Azure File storage i Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nästa steg

- [Översikt över ML Services-kluster på HDInsight](r-server-overview.md)
- [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
