---
title: "Azure-lagringslösningar för R Server på HDInsight - Azure | Microsoft Docs"
description: "Lär dig mer om de olika lagringsalternativ som är tillgängliga för användare med R Server på HDInsight"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: aafcc818af4c6e5d141d3633b31b913802a21752
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>Azure-lagringslösningar för R Server på HDInsight

Microsoft R Server på HDInsight har olika lagringslösningar för att bevara data, kod eller objekt som innehåller resultatet från analys. Dessa omfattar följande alternativ:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Lagring av Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)
- [Azure File storage](https://azure.microsoft.com/services/storage/files/)

Dessutom har du möjlighet att komma åt flera Azure storage-konton eller behållare med HDI-klustret. Azure File storage är en lämplig data lagringsalternativ för användning på kantnod som gör att du kan montera en filresurs i Azure Storage, till exempel Linux-filsystem. Men Azure-filresurser kan monteras och används av alla system som har ett operativsystem som stöds, till exempel Windows eller Linux. 

När du skapar ett Hadoop-kluster i HDInsight kan du ange antingen ett **Azure storage** konto eller ett **Data Lake store**. En specifik storage-behållare från detta konto innehåller filsystemet för det kluster som du skapar (till exempel Hadoop Distributed File System). Mer information och vägledning finns:

- [Använda Azure storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Använd Data Lake Store med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md). 

Mer information om Azure-lagringslösningar finns [introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md). 

Anvisningar om hur du väljer den lämpligaste lagringsalternativ för ditt scenario finns [avgöra när du behöver använda Azure BLOB, Azure-filer eller Azure Datadiskar](../../storage/common/storage-decide-blobs-files-disks.md) 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>Använda Azure Blob storage-konton med R Server

Om det behövs kan du komma åt flera Azure storage-konton eller behållare med HDI-klustret. Att göra det, måste du ange ytterligare storage-konton i Användargränssnittet när du skapar klustret och följ stegen nedan för att använda dem med R Server.

> [!WARNING]
> För prestanda skapas HDInsight-klustret i samma datacenter som primär storage-konto som du anger. Med hjälp av ett lagringskonto i en annan plats än HDInsight-kluster stöds inte.

1. Skapa ett HDInsight-kluster med namnet på ett lagringskonto för **storage1** och en standardbehållare kallas **container1**.
2. Ange ett konto för ytterligare lagringsutrymme som heter **storage2**.  
3. Kopiera filen mycsv.csv till katalogen /share och utföra analyser på filen.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. Ange noden namn i R-koden **standard** och ange din katalog och en fil som ska behandlas.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Alla filer och referenser som pekar på lagringskontot wasb://container1@storage1.blob.core.windows.net. Det här är den **standard lagringskonto** som associeras med HDInsight-klustret.

Anta nu att du vill bearbeta en fil med namnet mySpecial.csv som finns i /private för **container2** i **storage2**.

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
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Alla referenser katalog- och peka på lagringskontot wasb://container2@storage2.blob.core.windows.net. Det här är den **namn nod** som du har angett.

Du måste konfigurera/User/RevoShare/<SSH username> på **storage2** på följande sätt:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>Använda en Azure Data Lake store med R Server

Om du vill använda Data Lake lagrar med ditt HDInsight-konto som du behöver ge ditt Klusteråtkomst till varje Azure Data Lake store som du vill använda. Anvisningar för hur du använder Azure-portalen för att skapa ett HDInsight-kluster med ett Azure Data Lake Store-konto som standardlagring eller som en ytterligare butik finns [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Du sedan använda store i din R-skriptet mycket som du gjorde en sekundär Azure storage-konto som beskrivs i föregående procedur.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Lägg till klustret åtkomst till Azure Data Lake-Arkiv
Du har åtkomst till ett Data Lake store med hjälp av ett huvudnamn för tjänsten Azure Active Directory (AD Azure) som är kopplad till ditt HDInsight-kluster.

Lägg till en Azure AD tjänstens huvudnamn:

1. När du skapar ditt HDInsight-kluster, Välj **kluster-AAD-identitet** från den **datakällan** fliken.

2. I den **kluster-AAD-identitet** dialogrutan under **Välj AD tjänstens huvudnamn**väljer **Skapa nytt**.

När du namnge tjänstens huvudnamn och skapa ett lösenord för det, klickar du på **hanterar ADLS-åtkomst** associera tjänstens huvudnamn med Data Lake-Arkiv.

Det är också möjligt att lägga till klustret åtkomst till en eller flera Data Lake lagrar följande klustret skapas. Öppna Azure portal posten för ett Data Lake store och gå till **Data Explorer > åtkomst > Lägg till**. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>Hur du kommer åt Data Lake store från R Server

När du har gett åtkomst till ett Data Lake store, kan du använda arkivet i R Server på HDInsight på sätt som en sekundär Azure storage-konto. Den enda skillnaden är att prefixet **wasb: / /** ändras till **adl: / /** på följande sätt:


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
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


Följande kommandon används för att konfigurera Data Lake storage-konto med RevoShare katalogen och Lägg till exempel-CSV-filen från föregående exempel:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>Använda Azure File storage med R Server

Det finns också ett praktiskt data lagringsalternativ för användning på kantnod kallas [Azure filer] ((https://azure.microsoft.com/services/storage/files/). På så sätt kan du montera en filresurs i Azure Storage till Linux-filsystem. Det här alternativet kan vara praktiskt för att lagra filer, R-skript och resultatobjekt som kan behövas senare, särskilt när det praktiskt att använda intern filsystemet på kantnod i stället för HDFS. 

En större fördelarna med Azure-filer är att filresurserna kan montera och används av alla system som har ett operativsystem som stöds, till exempel Windows eller Linux. Den kan till exempel användas av en annan HDInsight-kluster som du eller någon annan i din grupp har, av en Azure VM eller även genom ett lokalt system. Mer information finns i:

- [Använd Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Använda Azure File storage i Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Nästa steg

Nu när du förstår Azure lagringsalternativ, Använd följande länkar om för att identifiera sätt för att få datavetenskap uppgifter göras med R Server på HDInsight.

* [Översikt över R Server på HDInsight](r-server-overview.md)
* [Komma igång med R server på Hadoop](r-server-get-started.md)
* [Alternativ för beräkningskontexter för R Server på HDInsight](r-server-compute-contexts.md)

