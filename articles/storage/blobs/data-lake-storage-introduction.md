---
title: Azure Data Lake Storage Gen2 introduktion
description: Ger en översikt över Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 75bd27f0945c66b9757055c0777b43a050ba67d7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921002"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introduktion till Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys och bygger på [Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 är resultatet av konvergerar funktionerna i våra två befintliga lagringstjänster, Azure Blob storage och Azure Data Lake Storage Gen1. Funktioner från [Azure Data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/index), till exempel semantiska fil system, katalog-och fil nivå säkerhet och skalning kombineras med låg kostnad, nivå lagring, hög tillgänglighet/haveri beredskap från [Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för enterprise stordataanalyser

Data Lake Storage Gen2 gör Azure Storage grunden för att skapa enterprise datasjöar i Azure. Data Lake Storage Gen2 kan utformats från början för att hantera flera petabyte information när tjänstemål hundratals Gigabit dataflöde, du enkelt kan hantera stora mängder data.

En grundläggande del av Data Lake Storage Gen2 är att lägga till ett [hierarkiskt namn område](data-lake-storage-namespace.md) till Blob Storage. Hierarkiskt namnområde organiserar objekt/filer i en hierarki med kataloger för effektiv dataåtkomst. En gemensam namngivningskonvention för objektet store använder snedstreck i namnet för att efterlikna en hierarkisk katalogstruktur. Den här strukturen blir verkliga med Data Lake Storage Gen2. Åtgärder som att byta namn på eller ta bort en katalog blir enkel atomiska metadataåtgärder i katalogen i stället för uppräkning av och bearbetning av alla objekt som delar namnprefixet för katalogen.

Tidigare hade molnbaserad analys att angripa i delar av prestanda, hantering och säkerhet. Data Lake Storage Gen2 adresser var och en av dessa aspekter på följande sätt:

-   **Prestanda** optimeras eftersom du inte behöver kopiera eller transformera data som ett krav för analys. Hierarkiskt namnområde förbättrar avsevärt prestandan vid directory hanteringsåtgärder, vilket förbättrar prestandan för jobbet.

-   Det är enklare att **Hantera hantering** eftersom du kan organisera och manipulera filer via kataloger och under kataloger.

-   **Säkerhet** är tvingande eftersom du kan definiera POSIX-behörigheter för kataloger eller enskilda filer.

-   **Kostnads effektivitet** är möjligt eftersom data Lake Storage Gen2 bygger vidare på [Azure Blob Storage](storage-blobs-introduction.md)med låg kostnad. De extra funktionerna som ytterligare sänka den totala ägandekostnaden för att köra analyser av stordata på Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake Storage Gen2

-   **Hadoop-kompatibel åtkomst**: data Lake Storage Gen2 gör att du kan hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS-drivrutinen](data-lake-storage-abfs-driver.md) är tillgänglig i alla Apache Hadoop miljöer, inklusive [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)och [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) för att komma åt data som lagras i data Lake Storage Gen2.

-   **En supermängd av POSIX-behörigheter**: säkerhets modellen för data Lake Gen2 stöder ACL-och POSIX-behörigheter tillsammans med viss extra detaljerad information som är speciell för data Lake Storage Gen2. Inställningarna kan konfigureras via Storage Explorer eller ramverk som Hive och Spark.

-   **Kostnads effektiv**: data Lake Storage Gen2 erbjuder lagrings kapacitet och transaktioner med låg kostnad. Som data över gångar via hela livs cykeln, ändrar fakturerings taxan kostnaderna till ett minimum via inbyggda funktioner, till exempel [Azure Blob Storage livs cykel](storage-lifecycle-management-concepts.md).

-   **Optimerad driv rutin**: ABFS-drivrutinen har [optimerats specifikt](data-lake-storage-abfs-driver.md) för stor data analys. Motsvarande REST-API: er finns i slut punkts `dfs.core.windows.net`.

### <a name="scalability"></a>Skalbarhet

Azure Storage är skalbar avsiktligt om du har åtkomst till via Data Lake Storage Gen2 eller Blob storage-gränssnitten. Den kan lagra och betjäna *många exabyte data*. Den här mängden lagringsutrymme som är tillgängliga med dataflöde mätt i Gigabit per sekund (Gbps) vid hög i/o-åtgärder per sekund (IOPS). Utöver bara persistence utförs bearbetning på nära konstant per begäran fördröjningar mäts på tjänsten, konto och filnivåer.

### <a name="cost-effectiveness"></a>Kostnadseffektivitet

En av de många fördelarna med att skapa Data Lake Storage Gen2 ovanpå Azure Blob storage är den låga kostnaden i lagringskapacitet och transaktioner. Till skillnad från andra molnlagringstjänster måste data som lagras i Data Lake Storage Gen2 inte flyttas eller omvandlas innan du utför analys. Mer information om priser finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage).

Dessutom förbättrar funktioner som det [hierarkiska namn området](data-lake-storage-namespace.md) avsevärt den övergripande prestandan för många analys jobb. Denna förbättring av prestanda innebär att du behöver mindre datorkraft för att bearbeta samma mängd data, vilket resulterar i en lägre total ägandekostnad (TCO) för slutpunkt till slutpunkt analytics-jobbet.

### <a name="one-service-multiple-concepts"></a>En tjänst, flera koncept

Data Lake Storage Gen2 är en ytterligare funktion för analys av stordata, bygger på Azure Blob storage. Det finns många fördelar i att utnyttja befintliga Plattformskomponenter av BLOB-och skapa och driva datasjöar för analys, det leda till flera begrepp som beskriver de samma, delade sakerna.

Följande är de motsvarande entiteterna som beskrivs av olika begrepp. Om inget annat anges dessa entiteter är direkt synonyma:

| Begrepp                                | Översta nivån organisation | Lägre nivå organisation                                            | Databehållare |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobbar – lagring för generell användning objekt | Container              | Virtuell katalog (SDK endast – inte ger atomiska manipulering) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Container            | Katalog                                                           | Fil           |

## <a name="supported-blob-storage-features"></a>Funktioner för blob-lagring som stöds

Blob Storage-funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och [Blob Storage principer för livs cykel hantering](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namn område. Därför kan du aktivera hierarkiska namn rymder på Blob Storage-kontona utan att förlora åtkomsten till dessa funktioner. 

En lista över stödda Blob Storage-funktioner finns i [Blob Storage funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Azure Service-integration som stöds

Data Lake Storage Gen2 stöder flera Azure-tjänster som du kan använda för att mata in data, utföra analyser och skapa visuella representationer. En lista över Azure-tjänster som stöds finns i [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Öppen källkod-plattformar som stöds

Data Lake Storage Gen2 har stöd för flera plattformar för öppen källkod. En fullständig lista finns i [plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


