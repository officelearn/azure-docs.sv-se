---
title: Använd Azure Data Lake-lagring (ADLS) Gen2 med Apache Hadoop i Azure HDInsight
description: Översikt över Azure Data Lake Storage Gen2 och hur de fungerar med Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: b7cde3a8990a51e95a8ce9ad85bca524d5669e0c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721129"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Använd Azure Data Lake Storage Gen2 med Apache Hadoop i Azure HDInsight

Azure Data Lake Storage (ADLS) Gen2 tar viktiga funktioner från Azure Data Lake Storage Gen1, till exempel ett Hadoop-kompatibel filsystem, Azure Active Directory, och POSIX baserat ACL: er och integrerar dem i Azure Blob Storage. Den här kombinationen kan du dra nytta av Azure Data Lake Storage Gen1 prestanda när du även använder lagringsnivåer Blob-lagring och hantering av livscykeln.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Huvudfunktionerna i Azure Data Lake Storage Gen2

- Hadoop-kompatibel åtkomst: Azure Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en Hadoop Distributed File System (HDFS). ABFS-drivrutinen är tillgängliga i alla Apache Hadoop-miljöer, inklusive Azure HDInsight och Azure Databricks för att komma åt data som lagras i Data Lake Storage Gen2.

- En supermängd POSIX-behörigheter: Säkerhetsmodellen för Data Lake Gen2 stöder ACL och POSIX behörigheter tillsammans med vissa extra kornighet som är specifika för Data Lake Storage Gen2. Inställningarna kan konfigureras via Administrationsverktyg eller ramverk som Apache Hive och Apache Spark.

- Kostnadseffektivt: Data Lake Storage Gen2 erbjuder låg kostnad lagringskapacitet och transaktioner. Funktioner, till exempel Azure Blob storage livscykel att minska kostnaderna genom att justera faktureringstaxor när data flyttas via dess livscykel.

- Fungerar med Blob storage-verktyg, ramverk och appar: Data Lake Storage Gen2 fortsätter att fungera med en mängd olika verktyg, ramverk och program som finns i dag för Blob storage.

- Optimerad drivrutinen: ABFS-drivrutinen har optimerats specifikt för analys av stordata. De motsvarande REST-API: erna exponeras via dfs-slutpunkt, dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Nyheter om Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Hanterade identiteter för säker åtkomst

Azure HDInsight använder hanterade identiteter för att skydda klustret åtkomst till filer i Azure Data Lake Storage Gen2. Hanterade identiteter är en funktion i Azure Active Directory som tillhandahåller Azure-tjänster med en automatiskt hanterade autentiseringsuppgifter. De här autentiseringsuppgifterna kan användas för att autentisera till en tjänst som stöder AD-autentisering. Med hjälp av hanterade identiteter måste inte du lagra autentiseringsuppgifter i kod eller konfigurationsfiler.

Mer information finns i [vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Azure Blob-filsystem (ABFS)-drivrutin

Apache Hadoop-program förväntar sig internt att läsa och skriva data från lokalt diskutrymme. En drivrutin för Hadoop-filsystem som ABFS gör det möjligt för Hadoop-program för molnlagring genom emulerar regelbundna Hadoop filsystemsåtgärder för programmet. Drivrutinen konverterar sedan dessa åtgärder till åtgärder som förstår den faktiska molnplattformen för lagring.

Drivrutinen för Hadoop-filsystem skulle tidigare konvertera alla filsystemsåtgärder till Azure Storage REST API-anrop på klientsidan och anropa REST-API. Den här klienten konverteringen, men resulterade i flera REST-API-anrop för en enda filsystemsåtgärd som att byta namn på en fil. ABFS har flyttats några av logiken för Hadoop-filsystem från klientsidan till serversidan och ADLS Gen2 API nu körs parallellt med Blob-API. Den här migreringen förbättrar prestanda eftersom nu vanliga åtgärder för Hadoop-filsystem kan köras med en REST API-anrop.

Mer information finns i [The Azure Blob-filsystem-drivrutinen (ABFS): En dedikerad Azure Storage-drivrutin för Apache Hadoop](../storage/data-lake-storage/abfs-driver.md).

### <a name="adls-gen-2-uri-scheme"></a>ADLS Gen 2 URI-schema

ADLS Gen2 använder en ny URI-schema för att komma åt filer i Azure storage från HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI-schemat ger SSL-krypterad åtkomst (`abfss://` prefix) och okrypterad åtkomst (`abfs://` prefix). Vi rekommenderar att du använder `abfss` möjligt, även om åtkomst till data som finns i samma region i Azure.

`<FILE_SYSTEM_NAME>` Anger sökvägen till filsystemet Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

Den `<PATH>` är det fil eller katalog HDFS-sökvägsnamnet.

Om värden för `<FILE_SYSTEM_NAME>` och `<ACCOUNT_NAME>` inte anges används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel den `hadoop-mapreduce-examples.jar` -fil som medföljer HDInsight-kluster kan du referera till något av följande sökvägar:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Filnamnet är `hadoop-examples.jar` i HDInsight version 2.1 och 1.6 kluster. När du arbetar med filer utanför HDInsight kan de flesta verktyg inte kan identifiera ABFS formatera och förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

Mer information finns i [Använd Azure Data Lake Storage Gen2 URI](../storage/data-lake-storage/introduction-abfs-uri.md).

## <a name="next-steps"></a>Nästa steg
- [Introduktion till Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction.md)
- [Använda Azure Data Lake Storage Gen2 förhandsversion med Azure HDInsight-kluster](../storage/data-lake-storage/use-hdi-cluster.md)