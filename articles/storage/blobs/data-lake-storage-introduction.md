---
title: Azure Data Lake Storage Gen2 introduktion
description: Ger en översikt över Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 99863f68c20a2f95dfc744e13a977bf3ccbbf639
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580433"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introduktion till Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys och bygger på [Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 är resultatet av att konvergera funktionerna i våra två befintliga lagrings tjänster, Azure Blob Storage och Azure Data Lake Storage Gen1. Funktioner från [Azure Data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/index), till exempel semantiska fil system, katalog-och fil nivå säkerhet och skalning kombineras med låg kostnad, nivå lagring, hög tillgänglighet/haveri beredskap från [Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för företags Big data Analytics

Data Lake Storage Gen2 gör Azure Storage grunden för att skapa företags data sjöar på Azure. Data Lake Storage Gen2 är utformad från start-till-tjänst-flera petabyte med information medan hundratals Gigabit av data flödet används, så att du enkelt kan hantera enorma mängder data.

En grundläggande del av Data Lake Storage Gen2 är att lägga till ett [hierarkiskt namn område](data-lake-storage-namespace.md) till Blob Storage. Det hierarkiska namn området ordnar objekt/filer i en hierarki med kataloger för effektiv data åtkomst. En vanlig namngivnings konvention för objekt lagring använder snedstreck i namnet för att imitera en hierarkisk katalog struktur. Den här strukturen blir riktig med Data Lake Storage Gen2. Åtgärder som att byta namn på eller ta bort en katalog blir enkla atomiska metadata-åtgärder i katalogen i stället för att räkna upp och bearbeta alla objekt som delar namn prefixet för katalogen.

Tidigare var molnbaserad analys en kompromiss i områden med prestanda, hantering och säkerhet. Data Lake Storage Gen2 hanterar var och en av dessa aspekter på följande sätt:

-   **Prestanda** optimeras eftersom du inte behöver kopiera eller transformera data som ett krav för analys. Det hierarkiska namn området ger avsevärt bättre prestanda för katalog hanterings åtgärder, vilket förbättrar jobbets övergripande prestanda.

-   Det är enklare att **Hantera hantering** eftersom du kan organisera och manipulera filer via kataloger och under kataloger.

-   **Säkerhet** är tvingande eftersom du kan definiera POSIX-behörigheter för kataloger eller enskilda filer.

-   **Kostnads effektivitet** är möjligt eftersom data Lake Storage Gen2 bygger vidare på [Azure Blob Storage](storage-blobs-introduction.md)med låg kostnad. De ytterligare funktionerna sänker längre den totala ägande kostnaden för att köra stor data analys på Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake Storage Gen2

-   **Hadoop-kompatibel åtkomst**: data Lake Storage Gen2 gör att du kan hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS-drivrutinen](data-lake-storage-abfs-driver.md) är tillgänglig i alla Apache Hadoop miljöer, inklusive [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)och [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) för att komma åt data som lagras i data Lake Storage Gen2.

-   **En supermängd av POSIX-behörigheter**: säkerhets modellen för data Lake Gen2 stöder ACL-och POSIX-behörigheter tillsammans med viss extra detaljerad information som är speciell för data Lake Storage Gen2. Inställningarna kan konfigureras via Storage Explorer eller genom ramverk som Hive och Spark.

-   **Kostnads effektiv**: data Lake Storage Gen2 erbjuder lagrings kapacitet och transaktioner med låg kostnad. Som data över gångar via hela livs cykeln, ändrar fakturerings taxan kostnaderna till ett minimum via inbyggda funktioner, till exempel [Azure Blob Storage livs cykel](storage-lifecycle-management-concepts.md).

-   **Optimerad driv rutin**: ABFS-drivrutinen har [optimerats specifikt](data-lake-storage-abfs-driver.md) för stor data analys. Motsvarande REST-API: er finns i slut punkts `dfs.core.windows.net`.

### <a name="scalability"></a>Skalbarhet

Azure Storage är skalbart genom att designa om du kommer åt via Data Lake Storage Gen2-eller Blob Storage-gränssnitt. Den kan lagra och betjäna *många exabyte data*. Den här mängden lagring är tillgänglig med data flöde mätt i gigabit per sekund (Gbit/s) vid höga nivåer av in-/utdata-åtgärder per sekund (IOPS). Efter beständighet utförs bearbetningen med nära konstant svars tid per begäran som mäts på tjänst-, konto-och fil nivåerna.

### <a name="cost-effectiveness"></a>Kostnads effektivitet

En av de många fördelarna med att skapa Data Lake Storage Gen2 ovanpå Azure Blob Storage är den låga kostnaden för lagrings kapacitet och transaktioner. Till skillnad från andra moln lagrings tjänster behöver inte data som lagrats i Data Lake Storage Gen2 flyttas eller omvandlas innan analysen kan utföras. Mer information om priser finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage).

Dessutom förbättrar funktioner som det [hierarkiska namn området](data-lake-storage-namespace.md) avsevärt den övergripande prestandan för många analys jobb. Den här förbättringen av prestanda innebär att du behöver mindre beräknings kraft för att bearbeta samma data mängd, vilket resulterar i en lägre totalkostnad (TCO) för det slutliga analys jobbet.

### <a name="one-service-multiple-concepts"></a>En tjänst, flera koncept

Data Lake Storage Gen2 är en ytterligare funktion för stor data analys som bygger på Azure Blob Storage. Även om det finns många fördelar med att dra nytta av befintliga plattforms komponenter i blobbar för att skapa och använda data sjöar för analys, leder det till flera koncept som beskriver samma delade saker.

Följande är motsvarande entiteter, enligt beskrivningen i olika koncept. Om inget annat anges är dessa entiteter direkt synonyma:

| Begrepp                                | Organisation på högsta nivå | Organisation på lägre nivå                                            | Data behållare |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobbar – lagring av generell användnings objekt | Container              | Virtuell katalog (endast SDK – ger inte atomiska modifieringar) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Container            | Katalog                                                           | Fil           |

## <a name="supported-open-source-platforms"></a>Plattformar som stöds med öppen källkod

Flera plattformar med öppen källkod har stöd för Data Lake Storage Gen2. Dessa plattformar visas i följande tabell.

> [!NOTE]
> Endast de versioner som visas i den här tabellen stöds.

| Plattform |  Version (er) som stöds | Mer information |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Vilka Apache Hadoops komponenter och versioner är tillgängliga med HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3,2 + | [Arkiv för Apache Hadoop-versioner](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Viktig information om Cloudera Enterprise 6. x](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Databricks Runtime versioner](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [Konfigurera åtkomst till moln data](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="supported-azure-services"></a>Azure-tjänster som stöds

Data Lake Storage Gen2 stöder flera Azure-tjänster som du kan använda för att mata in data, utföra analyser och skapa visuella representationer. En lista över Azure-tjänster som stöds finns i [integrera Azure Data Lake Storage med Azure-tjänster](data-lake-storage-integrate-with-azure-services.md).

## <a name="next-steps"></a>Nästa steg

I följande artiklar beskrivs några av de viktigaste begreppen i Data Lake Storage Gen2 och information om hur du lagrar, använder, hanterar och får insikter från dina data:

- [Hierarkiskt namn område](data-lake-storage-namespace.md)
- [Skapa ett lagringskonto](data-lake-storage-quickstart-create-account.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
- [Integrera Azure Data Lake Storage med Azure-tjänster](data-lake-storage-integrate-with-azure-services.md);
