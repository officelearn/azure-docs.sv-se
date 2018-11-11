---
title: Azure Data Lake Storage Gen2 förhandsversion introduktion
description: Översikt över Azure Data Lake Storage Gen2 förhandsversion
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c86609ae5b993328beced468b74c7f2a1b65def4
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283622"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Introduktion till Azure Data Lake Storage Gen2 förhandsversion

Azure Data Lake Storage Gen2 förhandsversion är en uppsättning funktioner för analys av stordata, inbyggda i [Azure Blob storage](../blobs/storage-blobs-introduction.md). Det kan du samverka med dina data med paradigm för lagring av system- och båda fil. Att lägga till Data Lake Storage Gen2 gör Azure Storage endast molnbaserad multimodal plattformen, så att du kan extrahera analytics värde från alla dina data.

Data Lake Storage Gen2 ger alla egenskaper som krävs för hela livscykeln för analytics-data till Azure Storage. Det är resultatet av konvergerar för funktioner i våra två befintliga storage services, Azure Blob Storage och Azure Data Lake Storage Gen1. Funktioner från [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), till exempel filsystemen, filen säkerhet på radnivå och skala kombineras med låg kostnad, nivåindelad lagring, hög tillgänglighet/haveriberedskap från [Azure BLOB-lagring](../blobs/storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för enterprise stordataanalyser

Data Lake Storage Gen2 gör Azure storage grunden för att skapa enterprise datasjöar i Azure. Data Lake Storage Gen2 kan utformats från början för att hantera flera petabyte information när tjänstemål hundratals Gigabit dataflöde, du enkelt kan hantera stora mängder data.

En grundläggande del av Data Lake Storage Gen2 är att lägga till en [hierarkiskt namnområde](./namespace.md) till Blob storage-tjänsten som organiserar objekt/filer i en hierarki med kataloger för effektiv dataåtkomst. Hierarkiskt namnområde kan också Data Lake Storage Gen2 du stöd för både objektlagring-system paradigm och på samma gång. En gemensam namngivningskonvention för objektet store använder exempelvis snedstreck i namnet för att efterlikna en hierarkisk mappstruktur. Den här strukturen blir verkliga med Data Lake Storage Gen2. Åtgärder som att byta namn på eller ta bort en katalog blir enkel atomiska metadataåtgärder i katalogen i stället för uppräkning av och bearbetning av alla objekt som delar namnprefixet för katalogen.

Tidigare hade molnbaserad analys att angripa i delar av prestanda, hantering och säkerhet. Data Lake Storage Gen2 adresser var och en av dessa aspekter på följande sätt:

- **Prestanda** optimeras eftersom du inte behöver kopiera eller Transformera data som ett krav för analys. Hierarkiskt namnområde förbättrar avsevärt prestandan vid directory hanteringsåtgärder som förbättrar prestandan för jobbet.

- **Hantering av** är enklare eftersom du kan organisera och manipulera filerna med kataloger och underkataloger.

- **Security** är verkställbar eftersom du kan definiera POSIX-behörigheter för mappar eller enskilda filer.

- **Kostnadseffektivitet** är möjligt eftersom Data Lake Storage Gen2 bygger på låg kostnad [Azure Blob storage](../blobs/storage-blobs-introduction.md). De extra funktionerna som ytterligare sänka den totala ägandekostnaden för att köra analyser av stordata på Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake Storage Gen2

> [!NOTE]
> Några av funktionerna nedan kan variera i deras tillgänglighet under den offentliga förhandsversionen av Data Lake Storage Gen2. När nya funktioner och regioner är tillgängliga under förhandsvisningsprogrammet, förmedlas denna information.
> [Registrera dig](https://aka.ms/adlsgen2signup) till den offentliga förhandsversionen av Data Lake Storage Gen2.  

- **Hadoop-kompatibel åtkomst**: Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS drivrutinen](./abfs-driver.md) är tillgängliga i alla Apache Hadoop-miljöer, inklusive [Azure HDInsight](../../hdinsight/index.yml) och [Azure Databricks](../../azure-databricks/index.yml) att komma åt data som lagras i Data Lake Storage Gen2.

- **En överordnad uppsättning av behörigheter av POSIX**: säkerhetsmodellen för Data Lake Gen2 stöder ACL och POSIX behörigheter tillsammans med vissa extra kornighet som är specifika för Data Lake Storage Gen2. Inställningarna kan konfigureras via Administrationsverktyg eller ramverk som Hive och Spark.

- **Kostnadseffektiv**: Data Lake Storage Gen2 erbjuder låg kostnad lagringskapacitet och transaktioner. Som data övergångar genom livscykeln klar, faktureringstaxor ändra kostnader för att se till att latensbidrag via de inbyggda funktionerna för till exempel [Azure Blob storage livscykel](../common/storage-lifecycle-managment-concepts.md).

- **Fungerar med Blob storage-verktyg, ramverk och appar**: Data Lake Storage Gen2 fortsätter att fungera med en mängd olika verktyg, ramverk och program som finns i dag för Blob storage.

- **Optimerad drivrutinen**: den `abfs` drivrutinen är [optimerats specifikt](./abfs-driver.md) för analys av stordata. De motsvarande REST-API: erna exponeras via den `dfs` slutpunkt, `dfs.core.windows.net`.

## <a name="scalability"></a>Skalbarhet

Azure Storage är skalbar avsiktligt om du har åtkomst till via Data Lake Storage Gen2 eller Blob storage-gränssnitten. Det går att lagra och tillhandahålla *många exabyte med data*. Den här mängden lagringsutrymme som är tillgängliga med dataflöde mätt i Gigabit per sekund (Gbps) vid hög i/o-åtgärder per sekund (IOPS). Utöver bara persistence utförs bearbetning på nära konstant per begäran fördröjningar mäts på tjänsten, konto och filnivåer.

## <a name="cost-effectiveness"></a>Kostnadseffektivitet

En av de många fördelarna med att skapa Data Lake Storage Gen2 ovanpå Azure Blob storage är den [prisvärda](https://azure.microsoft.com/pricing/details/storage) av lagringskapacitet och transaktioner. Till skillnad från andra molnlagringstjänster sänker Data Lake Storage Gen2 kostnaderna eftersom data inte måste flyttas eller omvandlas innan du utför analys.

Dessutom funktioner som den [hierarkiskt namnområde](./namespace.md) förbättrar prestanda i många analytics-jobb. Denna förbättring av prestanda innebär att du behöver mindre datorkraft för att bearbeta samma mängd data, vilket resulterar i en lägre total ägandekostnad (TCO) för slutpunkt till slutpunkt analytics-jobbet.

## <a name="next-steps"></a>Nästa steg

I följande artiklar beskriver några av de huvudsakliga begrepp för Data Lake Storage Gen2 och information om hur du lagrar, få åtkomst till, hantera och få insikter från dina data:

* [Hierarkiskt namnområde](./namespace.md)
* [Skapa ett lagringskonto](./quickstart-create-account.md)
* [Skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Använda ett Azure Data Lake Storage Gen2-konto i Azure Databricks](./quickstart-create-databricks-account.md)