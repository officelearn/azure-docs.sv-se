---
title: Azure Storage-lösningar för ML-tjänster på HDInsight - Azure
description: Lär dig mer om de olika lagringsalternativ som är tillgängliga med ML-tjänster på HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: ffde6f3e94cab94fca5a815db28478790a2a4221
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523400"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure Storage-lösningar för ML-tjänster på Azure HDInsight

ML-tjänster på HDInsight kan använda en mängd olika lagringslösningar för att bevara data, kod eller objekt som innehåller resultaten från analys. Dessa omfattar följande alternativ:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure-fillagring](https://azure.microsoft.com/services/storage/files/)

Du har också möjlighet att få åtkomst till flera Azure storage-konton eller behållare med ditt HDInsight-kluster. Azure-fillagring är ett praktiskt data för användning på gränsnoden som gör att du kan montera en filresurs i Azure Storage, till exempel Linux-filsystem. Men Azure-filresurser kan monteras och används av alla system som har ett operativsystem, till exempel Windows eller Linux. 

När du skapar ett Apache Hadoop-kluster i HDInsight kan du ange antingen en **Azure storage** konto eller **Data Lake Storage**. En specifik storage-behållare från detta konto innehåller filsystemet för klustret som du skapar (t.ex, Hadoop Distributed File System). Mer information och riktlinjer finns:

- [Använda Azure storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Använda Data Lake Storage med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Använd Azure Blob storage-konton med ML-Services-kluster

Om du har angett mer än en storage-konto när du skapar klustret ML-tjänster förklaras i följande anvisningar hur du använder ett sekundärt konto för åtkomst till data och åtgärder på ett kluster för ML-tjänster. Anta att följande storage-konton och behållaren: **storage1** och en standardbehållare som kallas **container1**, och **storage2** med **container2**.

> [!WARNING]  
> För prestanda skapas HDInsight-klustret i samma datacenter som det primära lagringskontot som du anger. Med ett storage-konto i en annan plats än HDInsight-kluster stöds inte.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Använd standardlagring med ML-tjänster på HDInsight

1. Med en SSH-klient kan ansluta till gränsnoden för klustret. Information om hur du använder SSH med HDInsight-kluster finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Kopiera en exempelfil, mysamplefile.csv, till katalogen /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Växla till R Studio eller en annan R-konsolen och skriva R-kod för att ange namn på noden för **standard** och platsen för den fil som du vill komma åt.  

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

Alla filer och referenser som pekar på lagringskontot `wasb://container1@storage1.blob.core.windows.net`. Det här är den **standard storage-konto** som associeras med HDInsight-klustret.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Använda det extra lagringsutrymmet med ML-tjänster på HDInsight

Anta nu att du vill bearbeta en fil med namnet mysamplefile1.csv som finns i /private för **container2** i **storage2**.

I din R-kod, peka namnet noden referensen till den **storage2** storage-konto.

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
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

Alla referenser katalog- och peka på lagringskontot `wasb://container2@storage2.blob.core.windows.net`. Det här är den **namn noden** som du har angett.

Du måste konfigurera den `/user/RevoShare/<SSH username>` på **storage2** på följande sätt:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Använd Azure Data Lake Storage med ML-Services-kluster 

Om du vill använda Data Lake Storage med HDInsight-kluster, måste du ge ditt Klusteråtkomst till varje Azure Data Lake Storage som du vill använda. Anvisningar för hur du använder Azure-portalen för att skapa ett HDInsight-kluster med ett Azure Data Lake Storage-konto som standardlagring eller som ytterligare lagringsutrymme finns i [skapa ett HDInsight-kluster med Data Lake Storage med hjälp av Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Du sedan använda lagring i ditt R-skript mycket som du gjorde en sekundär Azure storage-konto som beskrivs i föregående procedur.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Lägga till klustret åtkomst till din Azure Data Lake Storage
Du har åtkomst till Data Lake Storage med hjälp av ett tjänstobjekt för Azure Active Directory (Azure AD) som är associerat med ditt HDInsight-kluster.

1. När du skapar ditt HDInsight-kluster, Välj **kluster AAD-identitet** från den **datakälla** fliken.

2. I den **kluster AAD-identitet** dialogrutan **väljer AD-tjänstens huvudnamn**väljer **Skapa ny**.

När du ge tjänstens huvudnamn ett namn och skapa ett lösenord för den, klickar du på **hantera ADLS åtkomst** associera tjänstens huvudnamn med Data Lake Storage.

Det är också möjligt att lägga till klustret åtkomst till en eller flera Data Lake Storage-konton när klustret har skapats. Öppna Azure portal posten för en Data Lake Storage och gå till **Datautforskaren > åtkomst > Lägg till**. 

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Hur du kommer åt Data Lake Storage Gen1 från ML-tjänster på HDInsight

När du har beviljat åtkomst till Data Lake Storage Gen1, kan du använda lagringen i ML-Services-kluster i HDInsight på sätt som en sekundär Azure-lagringskonto. Den enda skillnaden är att prefixet **wasb: / /** ändras till **adl: / /** på följande sätt:


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

Följande kommandon används för att konfigurera Gen1 för Data Lake Storage-konto med RevoShare katalogen och Lägg till exempel-CSV-filen från föregående exempel:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Använd Azure File storage med ML-tjänster på HDInsight

Det finns också ett praktiskt data lagringsalternativ för användning på kantnoden heter [Azure Files](https://azure.microsoft.com/services/storage/files/). Det gör att du kan montera en Azure Storage-filresurs till Linux-filsystem. Det här alternativet kan vara praktiskt för att lagra datafiler, R-skript och resultatobjekt som kan behövas senare, särskilt när det klokt att använda interna filsystemet på gränsnoden i stället för HDFS. 

En stor fördel med Azure Files är att filresurserna kan monteras och används av alla system som har ett operativsystem som stöds, till exempel Windows eller Linux. Det kan till exempel användas av en annan HDInsight-kluster som du eller någon i teamet har, av en Azure-dator eller genom ett lokalt system. Mer information finns i:

- [Använd Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Hur du använder Azure File storage i Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Nästa steg

* [Översikt över ML-Services-kluster i HDInsight](r-server-overview.md)
* [Kom igång med ML-tjänster på Apache Hadoop-kluster](r-server-get-started.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
