---
title: Översikt över Azure Data Lake Storage Gen2 i HDInsight
description: Översikt över Data Lake Storage Gen2 i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873347"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Översikt över Azure Data Lake Storage Gen2 i HDInsight

Azure Data Lake Storage Gen2 tar kärnfunktioner från Azure Data Lake Storage Gen1 och integrerar dem i Azure Blob-lagring. Dessa funktioner inkluderar ett filsystem som är kompatibelt med Hadoop, Azure Active Directory (Azure AD) och POSIX-baserade åtkomstkontrollistor (ACL). Med den här kombinationen kan du dra nytta av prestandan hos Azure Data Lake Storage Gen1. Samtidigt som du använder nivåindelning och datalivscykelhantering av Blob-lagring.

Mer information om Azure Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Grundläggande funktioner för Azure Data Lake Storage Gen2

* **Åtkomst som är kompatibel med Hadoop:** I Azure Data Lake Storage Gen2 kan du hantera och komma åt data på samma sätt som med ett HADoop Distributed File System (HDFS). Azure Blob File System (ABFS) är tillgänglig i alla Apache Hadoop-miljöer, inklusive Azure HDInsight och Azure Databricks. Använd ABFS för att komma åt data som lagras i Data Lake Storage Gen2.

* **En superuppsättning av POSIX-behörigheter:** Säkerhetsmodellen för Data Lake Gen2 stöder behörigheter för ACL och POSIX tillsammans med några extra granularitet som är specifika för Data Lake Storage Gen2. Inställningar kan konfigureras via administrationsverktyg eller ramverk som Apache Hive och Apache Spark.

* **Kostnadseffektivitet:** Data Lake Storage Gen2 erbjuder billig lagringskapacitet och transaktioner. Azure Blob-lagringslivscykler hjälper till att sänka kostnaderna genom att justera faktureringsfrekvensen när data går igenom dess livscykel.

* **Kompatibilitet med Blob-lagringsverktyg, ramverk och appar:** Data Lake Storage Gen2 fortsätter att arbeta med ett brett utbud av verktyg, ramverk och program för Blob-lagring.

* **Optimerad drivrutin:** ABFS-drivrutinen är optimerad speciellt för stordataanalys. Motsvarande REST-API:er visas via dfs-slutpunkten (Distributed File System) dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Nyheter för Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Hanterade identiteter för säker filåtkomst

Azure HDInsight använder hanterade identiteter för att skydda klusteråtkomst till filer i Azure Data Lake Storage Gen2. Hanterade identiteter är en funktion i Azure Active Directory som tillhandahåller Azure-tjänster med en uppsättning automatiskt hanterade autentiseringsuppgifter. Dessa autentiseringsuppgifter kan användas för att autentisera till alla tjänster som stöder Active Directory-autentisering. Om du använder hanterade identiteter krävs inte att du lagrar autentiseringsuppgifter i kod- eller konfigurationsfiler.

Mer information finns i [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Azure Blob-filsystemdrivrutin

Apache Hadoop-program förväntar sig inbyggt att läsa och skriva data från lokal disklagring. En Hadoop filsystem drivrutin som ABFS gör att Hadoop program för att arbeta med molnlagring. Fungerar genom att efterlikna regelbundna Hadoop filsystem operationer. Drivrutinen konverterar de kommandon som tas emot från programmet till åtgärder som den faktiska molnlagringsplattformen förstår.

Tidigare konverterade Hadoop-filsystemdrivrutinen alla filsystemåtgärder till AZURE Storage REST API-anrop på klientsidan. Och sedan åberopas REST API. Den här konverteringen på klientsidan resulterade dock i flera REST API-anrop för en enda filsystemåtgärd som att byta namn på en fil. ABFS har flyttat Hadoop filsystem logik från klientsidan till serversidan. Azure Data Lake Storage Gen2 API körs nu parallellt med Blob API. Den här migreringen förbättrar prestanda eftersom vanliga Hadoop-filsystemåtgärder nu kan utföras med ett REST API-anrop.

Mer information finns i [Azure Blob Filesystem driver (ABFS): En dedikerad Azure Storage-drivrutin för Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema för Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen2 använder ett nytt URI-schema för att komma åt filer i Azure Storage från HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI-schemat ger SSL-krypterad åtkomst.

`<FILE_SYSTEM_NAME>`identifierar sökvägen till filsystemet Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identifierar Azure Storage-kontonamnet. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

`<PATH>`är fil- eller katalogens HDFS-sökvägsnamn.

Om värden `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` för och inte anges används standardfilsystemet. För filerna i standardfilsystemet använder du en relativ sökväg eller en absolut sökväg. `hadoop-mapreduce-examples.jar` Filen som medföljer HDInsight-kluster kan till exempel refereras till med hjälp av någon av följande sökvägar:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Filnamnet finns `hadoop-examples.jar` i HDInsight version 2.1- och 1.6-kluster. När du arbetar med filer utanför HDInsight, kommer du att upptäcka att de flesta verktyg inte känner igen `example/jars/hadoop-mapreduce-examples.jar`ABFS-format utan i stället förväntar sig en grundläggande väg format, till exempel .

Mer information finns [i Använda Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
* [Översikt över Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)