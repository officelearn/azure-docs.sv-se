---
title: Introduktion till Azure Data Lake Storage Gen2
description: Ger en översikt över Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942916"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introduktion till Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stordataanalys, byggd på [Azure Blob-lagring](storage-blobs-introduction.md). Data Lake Storage Gen2 är resultatet av konvergerande funktionerna i våra två befintliga lagringstjänster, Azure Blob storage och Azure Data Lake Storage Gen1. Funktioner från [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), till exempel filsystemssenmantik, katalog- och filnivåsäkerhet och skala kombineras med låg kostnad, nivåinställd lagring, hög tillgänglighet/katastrofåterställningsfunktioner från Azure [Blob-lagring](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för stordataanalys för företag

Data Lake Storage Gen2 gör Azure Storage till grunden för att bygga företagsdatasjöar på Azure. Designad från början för att betjäna flera petabyte information samtidigt upprätthålla hundratals gigabits av dataflöde, datasjölagring Gen2 kan du enkelt hantera stora mängder data.

En grundläggande del av Data Lake Storage Gen2 är tillägget av ett [hierarkiskt namnområde](data-lake-storage-namespace.md) till Blob-lagring. Det hierarkiska namnområdet ordnar objekt/filer i en hierarki av kataloger för effektiv dataåtkomst. En gemensam namngivningskonvention för objektarkiv använder snedstreck i namnet för att efterlikna en hierarkisk katalogstruktur. Denna struktur blir verklig med Data Lake Storage Gen2. Åtgärder som att byta namn på eller ta bort en katalog blir enstaka atomadataåtgärder i katalogen i stället för att räkna upp och bearbeta alla objekt som delar katalogens namnprefix.

DataSjölagring Gen2 bygger på Blob-lagring och förbättrar prestanda, hantering och säkerhet på följande sätt:

-   **Prestanda** optimeras eftersom du inte behöver kopiera eller omvandla data som en förutsättning för analys. Jämfört med det platta namnområdet för Blob-lagring förbättrar det hierarkiska namnområdet avsevärt prestanda för kataloghanteringsåtgärder, vilket förbättrar den övergripande jobbprestandan.

-   **Hantering** är enklare eftersom du kan ordna och manipulera filer via kataloger och underkataloger.

-   **Säkerhet** är verkställbart eftersom du kan definiera POSIX-behörigheter för kataloger eller enskilda filer.

Dessutom är Data Lake Storage Gen2 mycket kostnadseffektivt eftersom det är byggt ovanpå den billiga [Azure Blob-lagring](storage-blobs-introduction.md). De ytterligare funktionerna sänker ytterligare den totala ägandekostnaden för att köra stordataanalys på Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake Storage Gen2

-   **Hadoop kompatibel åtkomst:** Data Lake Storage Gen2 kan du hantera och komma åt data precis som du skulle med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS-drivrutinen](data-lake-storage-abfs-driver.md) är tillgänglig i alla Apache Hadoop-miljöer, inklusive [Azure HDInsight,](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)och SQL Data [Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) för att komma åt data som lagras i Data Lake Storage Gen2.

-   **En superuppsättning av POSIX-behörigheter**: Säkerhetsmodellen för Data Lake Gen2 stöder ACL- och POSIX-behörigheter tillsammans med några extra granularitet som är specifika för Data Lake Storage Gen2. Inställningar kan konfigureras via Storage Explorer eller via ramverk som Hive och Spark.

-   **Kostnadseffektivt:** Data Lake Storage Gen2 erbjuder billig lagringskapacitet och transaktioner. När data övergår genom hela livscykeln ändras faktureringskostnaderna så att kostnaderna hålls till ett minimum via inbyggda funktioner som [Azure Blob-lagringslivscykel](storage-lifecycle-management-concepts.md).

-   **Optimerad drivrutin:** ABFS-drivrutinen är [optimerad speciellt](data-lake-storage-abfs-driver.md) för stordataanalys. Motsvarande REST-API:er visas genom `dfs.core.windows.net`ändpunkten .

### <a name="scalability"></a>Skalbarhet

Azure Storage är skalbart avsiktligt oavsett om du kommer åt via Data Lake Storage Gen2 eller Blob storage interfaces. Det kan lagra och tjäna *många exabyte data*. Den här mängden lagringsutrymme är tillgänglig med dataflöde mätt i gigabit per sekund (Gbps) vid höga nivåer av in-/utdataoperationer per sekund (IOPS). Utöver bara persistens körs bearbetningen på nästan konstanta svarstider per begäran som mäts på tjänst-, konto- och filnivåer.

### <a name="cost-effectiveness"></a>Kostnadseffektivitet

En av de många fördelarna med att bygga Data Lake Storage Gen2 ovanpå Azure Blob-lagring är den låga kostnaden för lagringskapacitet och transaktioner. Till skillnad från andra molnlagringstjänster behöver data som lagras i Data Lake Storage Gen2 inte flyttas eller omvandlas innan analys utförs. Mer information om priser finns i [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage).

Dessutom förbättrar funktioner som det [hierarkiska namnområdet](data-lake-storage-namespace.md) avsevärt prestanda för många analysjobb. Den här förbättringen av prestanda innebär att du behöver mindre beräkningskraft för att bearbeta samma mängd data, vilket resulterar i en lägre total ägandekostnad (TCO) för det end-to-end-analysjobbet.

### <a name="one-service-multiple-concepts"></a>En tjänst, flera koncept

Data Lake Storage Gen2 är en ytterligare funktion för stordataanalys, byggd ovanpå Azure Blob-lagring. Även om det finns många fördelar med att utnyttja befintliga plattformskomponenter i Blobbar för att skapa och driva datasjöar för analys, leder det till flera begrepp som beskriver samma, delade saker.

Följande är motsvarande entiteter, som beskrivs av olika begrepp. Om inte annat anges är dessa enheter direkt synonyma:

| Begrepp                                | Organisation på högsta nivå | Organisation på lägre nivå                                            | Databehållare |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobbar – Lagring av objekt för allmänt bruk | Container              | Virtuell katalog (endast SDK – ger inte atommanipulering) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Container            | Katalog                                                           | Fil           |

## <a name="supported-blob-storage-features"></a>Blob-lagringsfunktioner som stöds

Blob lagringsfunktioner som [diagnostikloggning,](../common/storage-analytics-logging.md) [åtkomstnivåer](storage-blob-storage-tiers.md)och [Blob Storage livscykelhanteringsprinciper](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namnområde. Därför kan du aktivera hierarkiska namnområden på dina Blob-lagringskonton utan att förlora åtkomsten till dessa funktioner. 

En lista över Blob-lagringsfunktioner som stöds finns [i Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integreringar av Azure-tjänster som stöds

Gen2 för DataSjölagring stöder flera Azure-tjänster som du kan använda för att använda data, utföra analyser och skapa visuella representationer. En lista över Azure-tjänster som stöds finns i [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plattformar med öppen källkod som stöds

Flera plattformar med öppen källkod stöder Data Lake Storage Gen2. En fullständig lista finns i [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


