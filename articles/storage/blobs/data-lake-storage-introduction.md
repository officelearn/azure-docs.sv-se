---
title: Azure Data Lake Storage Gen2 förhandsversion introduktion
description: Översikt över Azure Data Lake Storage Gen2 förhandsversion
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0b3c4a4c21bc9b124a98b1f204f85e6e78ff233e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099570"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Introduktion till Azure Data Lake Storage Gen2 förhandsversion

Azure Data Lake Storage Gen2 förhandsversion är en uppsättning funktioner för analys av stordata, bygger på [Azure Blob storage](storage-blobs-introduction.md). Data Lake Storage Gen2 är resultatet av konvergerar funktionerna i våra två befintliga lagringstjänster, Azure Blob storage och Azure Data Lake Storage Gen1. Funktioner från [Azure Data Lake Storage Gen1](https://docs.microsoft.com/en-us/azure/data-lake-store/index), t.ex. i filsystemen, katalog, och filen säkerhet och skalning kombineras med låg kostnad, nivåindelad lagring, hög tillgänglighet/haveriberedskap från [Azure Blob storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för enterprise stordataanalyser

Data Lake Storage Gen2 gör Azure Storage grunden för att skapa enterprise datasjöar i Azure. Data Lake Storage Gen2 kan utformats från början för att hantera flera petabyte information när tjänstemål hundratals Gigabit dataflöde, du enkelt kan hantera stora mängder data.

En grundläggande del av Data Lake Storage Gen2 är att lägga till en [hierarkiskt namnområde](data-lake-storage-namespace.md) till Blob storage. Hierarkiskt namnområde organiserar objekt/filer i en hierarki med kataloger för effektiv dataåtkomst. En gemensam namngivningskonvention för objektet store använder snedstreck i namnet för att efterlikna en hierarkisk katalogstruktur. Den här strukturen blir verkliga med Data Lake Storage Gen2. Åtgärder som att byta namn på eller ta bort en katalog blir enkel atomiska metadataåtgärder i katalogen i stället för uppräkning av och bearbetning av alla objekt som delar namnprefixet för katalogen.

Tidigare hade molnbaserad analys att angripa i delar av prestanda, hantering och säkerhet. Data Lake Storage Gen2 adresser var och en av dessa aspekter på följande sätt:

-   **Prestanda** optimeras eftersom du inte behöver kopiera eller Transformera data som ett krav för analys. Hierarkiskt namnområde förbättrar avsevärt prestandan vid directory hanteringsåtgärder, vilket förbättrar prestandan för jobbet.

-   **Hantering av** är enklare eftersom du kan organisera och manipulera filerna med kataloger och underkataloger.

-   **Security** är verkställbar eftersom du kan definiera POSIX-behörigheter i kataloger eller enskilda filer.

-   **Kostnadseffektivitet** är möjligt eftersom Data Lake Storage Gen2 bygger på låg kostnad [Azure Blob storage](storage-blobs-introduction.md). De extra funktionerna som ytterligare sänka den totala ägandekostnaden för att köra analyser av stordata på Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake Storage Gen2

-   **Hadoop-kompatibel åtkomst**: Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS drivrutinen](data-lake-storage-abfs-driver.md) är tillgängliga i alla Apache Hadoop-miljöer, inklusive [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index), och [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) att komma åt data som lagras i Data Lake Storage Gen2.

-   **En överordnad uppsättning av behörigheter av POSIX**: säkerhetsmodellen för Data Lake Gen2 stöder ACL och POSIX behörigheter tillsammans med vissa extra kornighet som är specifika för Data Lake Storage Gen2. Inställningarna kan konfigureras via Storage Explorer eller ramverk som Hive och Spark.

-   **Kostnadseffektiv**: Data Lake Storage Gen2 erbjuder låg kostnad lagringskapacitet och transaktioner. Som data övergångar genom livscykeln klar, faktureringstaxor ändra kostnader för att se till att latensbidrag via de inbyggda funktionerna för till exempel [Azure Blob storage livscykel](storage-lifecycle-management-concepts.md).

-   **Optimerad drivrutinen**: abfs-drivrutinen är [optimerats specifikt](data-lake-storage-abfs-driver.md) för analys av stordata. De motsvarande REST-API: erna exponeras via dfs-slutpunkt, dfs.core.windows.net.

### <a name="scalability"></a>Skalbarhet

Azure Storage är skalbar avsiktligt om du har åtkomst till via Data Lake Storage Gen2 eller Blob storage-gränssnitten. Det går att lagra och tillhandahålla *många exabyte med data*. Den här mängden lagringsutrymme som är tillgängliga med dataflöde mätt i Gigabit per sekund (Gbps) vid hög i/o-åtgärder per sekund (IOPS). Utöver bara persistence utförs bearbetning på nära konstant per begäran fördröjningar mäts på tjänsten, konto och filnivåer.

### <a name="cost-effectiveness"></a>Kostnadseffektivitet

En av de många fördelarna med att skapa Data Lake Storage Gen2 ovanpå Azure Blob storage är den låga kostnaden i lagringskapacitet och transaktioner. Till skillnad från andra molnlagringstjänster måste data som lagras i Data Lake Storage Gen2 inte flyttas eller omvandlas innan du utför analys. Mer information om priser finns i [Azure Storage-priser]((https://azure.microsoft.com/pricing/details/storage)).

Dessutom funktioner som den [hierarkiskt namnområde](data-lake-storage-namespace.md) förbättrar prestanda i många analytics-jobb. Denna förbättring av prestanda innebär att du behöver mindre datorkraft för att bearbeta samma mängd data, vilket resulterar i en lägre total ägandekostnad (TCO) för slutpunkt till slutpunkt analytics-jobbet.

### <a name="one-service-multiple-concepts"></a>En tjänst, flera koncept

Data Lake Storage Gen2 är en ytterligare funktion för analys av stordata, bygger på Azure Blob storage. Det finns många fördelar i att utnyttja befintliga Plattformskomponenter av BLOB-och skapa och driva datasjöar för analys, det leda till flera begrepp som beskriver de samma, delade sakerna.

Följande är de motsvarande entiteterna som beskrivs av olika begrepp. Om inget annat anges dessa entiteter är direkt synonyma:

| Begrepp                                | Översta nivån organisation | Lägre nivå organisation                                            | Databehållare |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobbar – lagring för generell användning objekt | Container              | Virtuell katalog (SDK endast – inte ger atomiska manipulering) | Blob           |
| ADLS Gen2 – Storage Analytics          | Filsystem             | Katalog                                                           | Fil           |

## <a name="supported-open-source-platforms"></a>Öppen källkod-plattformar som stöds

Data Lake Storage Gen2 har stöd för flera plattformar för öppen källkod. Dessa plattformar visas i följande tabell.

> [!NOTE]
> Endast de versioner som visas i den här tabellen stöds.

| Plattform |  Versioner som stöds | Mer information |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Vad är Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 2.7 + | [Apache Hadoop släpper Arkiv](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Cloudera Enterprise 6.x viktig information](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 4.2 + | [Databricks Runtime-versioner](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 2.6 + | [Hortonworks-dokumentation](https://docs.hortonworks.com/) |

## <a name="next-steps"></a>Nästa steg

I följande artiklar beskriver några av de huvudsakliga begrepp för Data Lake Storage Gen2 och information om hur du lagrar, få åtkomst till, hantera och få insikter från dina data:

-   [Hierarkiskt namnområde](data-lake-storage-namespace.md)
-   [Skapa ett lagringskonto](data-lake-storage-quickstart-create-account.md)
-   [Skapa ett HDInsight-kluster med Data Lake Storage Gen2](data-lake-storage-quickstart-create-connect-hdi-cluster.md)
-   [Använd ett Data Lake Storage Gen2-konto i Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md)
