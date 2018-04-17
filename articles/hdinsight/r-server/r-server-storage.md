---
title: Azure-lagringslösningar för R Server på HDInsight - Azure | Microsoft Docs
description: Lär dig mer om olika lagringsplatser alternativen med R Server på HDInsight
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: 23e32a913fb73d2207f7cf37ce6230e428fbe95c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-storage-solutions-for-r-server-on-azure-hdinsight"></a>Azure-lagringslösningar för R Server på Azure HDInsight

R Server på HDInsight har olika lagringslösningar för att bevara data, kod eller objekt som innehåller resultatet från analys. Dessa omfattar följande alternativ:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)
- [Azure File storage](https://azure.microsoft.com/services/storage/files/)

Du har också möjlighet att komma åt flera Azure storage-konton eller behållare med ditt HDInsight-kluster. Azure File storage är en lämplig data lagringsalternativ för användning på kantnod som gör att du kan montera en filresurs i Azure Storage, till exempel Linux-filsystem. Men Azure-filresurser kan monteras och används av alla system som har ett operativsystem som stöds, till exempel Windows eller Linux. 

När du skapar ett Hadoop-kluster i HDInsight kan du ange antingen ett **Azure storage** konto eller ett **Data Lake store**. En specifik storage-behållare från detta konto innehåller filsystemet för det kluster som du skapar (till exempel Hadoop Distributed File System). Mer information och vägledning finns:

- [Använda Azure storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Använd Data Lake Store med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-r-server-cluster"></a>Använda Azure Blob storage-konton med R Server-kluster

Om du har angett mer än en storage-konto när du skapar R Server-kluster förklaras i följande anvisningar hur du använder ett sekundärt konto för dataåtkomst och -åtgärder på R Server-kluster. Anta att följande storage-konton och behållare: **storage1** och en standardbehållare kallas **container1**, och **storage2** med **container2**.

> [!WARNING]
> För prestanda skapas HDInsight-klustret i samma datacenter som primär storage-konto som du anger. Med hjälp av ett lagringskonto i en annan plats än HDInsight-kluster stöds inte.

### <a name="use-the-default-storage-with-r-server-on-hdinsight"></a>Använd standardlagring med R Server på HDInsight

1. Använder en SSH-klient kan ansluta till edge-nod i klustret. Information om hur du använder SSH med HDInsight-kluster finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Kopiera en exempelfil, mysamplefile.csv, till katalogen /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Växla till R Studio eller någon annan konsol för R och Skriv R-koden för att ange noden namn för **standard** och platsen för den fil som du vill komma åt.  

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

Alla filer och referenser som pekar på lagringskontot `wasb://container1@storage1.blob.core.windows.net`. Det här är den **standard lagringskonto** som associeras med HDInsight-klustret.

### <a name="use-the-additional-storage-with-r-server-on-hdinsight"></a>Använd det extra lagringsutrymmet med R Server på HDInsight

Anta nu att du vill bearbeta en fil med namnet mysamplefile1.csv som finns i /private för **container2** i **storage2**.

Punkt i koden R namn nod referensen till den **storage2** storage-konto.

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

Alla referenser katalog- och peka på lagringskontot `wasb://container2@storage2.blob.core.windows.net`. Det här är den **namn nod** som du har angett.

Du måste konfigurera/User/RevoShare/<SSH username> på **storage2** på följande sätt:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-r-server-cluster"></a>Använda Azure Data Lake Store med R Server-kluster 

Du måste ge ditt Klusteråtkomst till varje Azure Data Lake Store som du vill använda för att använda Data Lake Store med ditt HDInsight-kluster. Anvisningar för hur du använder Azure-portalen för att skapa ett HDInsight-kluster med ett Azure Data Lake Store-konto som standardlagring eller som en ytterligare butik finns [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Du sedan använda store i din R-skriptet mycket som du gjorde en sekundär Azure storage-konto som beskrivs i föregående procedur.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Lägg till klustret åtkomst till Azure Data Lake-Arkiv
Du har åtkomst till ett Data Lake store med hjälp av ett huvudnamn för tjänsten Azure Active Directory (AD Azure) som är kopplad till ditt HDInsight-kluster.

1. När du skapar ditt HDInsight-kluster, Välj **kluster-AAD-identitet** från den **datakällan** fliken.

2. I den **kluster-AAD-identitet** dialogrutan under **Välj AD tjänstens huvudnamn**väljer **Skapa nytt**.

När du namnge tjänstens huvudnamn och skapa ett lösenord för det, klickar du på **hanterar ADLS-åtkomst** associera tjänstens huvudnamn med Data Lake Store.

Det är också möjligt att lägga till klustret åtkomst till en eller flera datasjölagerkonton efter klustret skapas. Öppna Azure portal posten för ett Data Lake Store och gå till **Data Explorer > åtkomst > Lägg till**. 

### <a name="how-to-access-the-data-lake-store-from-r-server-on-hdinsight"></a>Hur du kommer åt Data Lake store från R Server på HDInsight

När du har gett åtkomst till ett Data Lake Store, kan du använda arkivet i R Server-kluster i HDInsight på sätt som en sekundär Azure storage-konto. Den enda skillnaden är att prefixet **wasb: / /** ändras till **adl: / /** på följande sätt:


    # Point to the ADL store (e.g. ADLtest)
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

Följande kommandon används för att konfigurera Data Lake Store-konto med RevoShare katalogen och Lägg till exempel-CSV-filen från föregående exempel:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server-on-hdinsight"></a>Använda Azure File storage med R Server på HDInsight

Det finns också ett praktiskt data lagringsalternativ för användning på kantnod kallas [Azure filer] ((https://azure.microsoft.com/services/storage/files/). På så sätt kan du montera en filresurs i Azure Storage till Linux-filsystem. Det här alternativet kan vara praktiskt för att lagra filer, R-skript och resultatobjekt som kan behövas senare, särskilt när det praktiskt att använda intern filsystemet på kantnod i stället för HDFS. 

En större fördelarna med Azure-filer är att filresurserna kan montera och används av alla system som har ett operativsystem som stöds, till exempel Windows eller Linux. Den kan till exempel användas av en annan HDInsight-kluster som du eller någon annan i din grupp har, av en Azure VM eller även genom ett lokalt system. Mer information finns i:

- [Använd Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Använda Azure File storage i Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Nästa steg

* [Översikt över R Server-kluster i HDInsight](r-server-overview.md)
* [Kom igång med R Server-kluster på Hadoop](r-server-get-started.md)
* [Alternativ för beräkningskontexter för R Server-kluster i HDInsight](r-server-compute-contexts.md)

