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
ms.openlocfilehash: 51f38cf7ade01b58ad5ce7925af5546d1a4f1a0c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525390"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Introduktion till Azure Data Lake Storage Gen2 förhandsversion

Azure Data Lake Storage Gen2 förhandsversion är en uppsättning funktioner för analys av stordata, byggt ovanpå [Azure Blob storage](../blobs/storage-blobs-introduction.md). Det kan du samverka med dina data med paradigm för lagring av system- och båda fil. På så sätt blir Data Lake Storage Gen2 tjänsten endast molnbaserad multimodal lagring, så att du kan extrahera analytics värde från alla dina data.

Data Lake Storage Gen2 har alla egenskaper som krävs för hela livscykeln för analytics-data. Detta beror på att Konvergera funktionerna i våra två befintliga lagringstjänster. Funktioner från [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), till exempel filsystemen, filen säkerhet på radnivå och skala kombineras med nivåindelad lagring med låg kostnad, hög tillgänglighet/haveriberedskap och en stor SDK/verktyg ekosystemet från [Azure Blob storage](../blobs/storage-blobs-introduction.md). I Data Lake Storage Gen2 kvar samma egenskaper som objektlagring medan att lägga till fördelarna med ett gränssnitt filsystem optimerade för analytics arbetsbelastningar.

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för enterprise stordataanalyser

Data Lake Storage Gen2 är den grundläggande storage-tjänsten för att skapa enterprise datasjöar (EDL) på Azure. Utformats från början för att hantera flera petabyte information när tjänstemål hundratals Gigabit dataflöde, ger Data Lake Storage Gen2 dig ett enkelt sätt att hantera stora mängder data.

En grundläggande funktion i Data Lake Storage Gen2 är att lägga till en [hierarkiskt namnområde](./namespace.md) till Blob storage-tjänsten som organiserar objekt/filer i en hierarki med kataloger för högpresterande dataåtkomst. Hierarkiskt namnområde kan också Data Lake Storage Gen2 du stöd för både objektlagring-system paradigm och på samma gång. En gemensam namngivningskonvention för objektet store använder exempelvis snedstreck i namnet för att efterlikna en hierarkisk mappstruktur. Den här strukturen blir verkliga med Data Lake Storage Gen2. Åtgärder som att byta namn på eller ta bort en katalog blir enkel atomiska metadataåtgärder i katalogen i stället för uppräkning av och bearbetning av alla objekt som delar namnprefixet för katalogen.

Tidigare hade molnbaserad analys att angripa i delar av prestanda, hantering och säkerhet. Data Lake Storage Gen2 adresser var och en av dessa aspekter på följande sätt:

- **Prestanda** optimeras eftersom du inte behöver kopiera eller Transformera data som ett krav för analys. Hierarkiskt namnområde förbättrar avsevärt prestandan vid directory hanteringsåtgärder som förbättrar prestandan för jobbet.

- **Hantering av** är enklare eftersom du kan organisera och manipulera filerna med kataloger och underkataloger.

- **Kostnadseffektivitet** är möjligt eftersom Data Lake Storage Gen2 bygger på låg kostnad [Azure Blob storage](../blobs/storage-blobs-introduction.md). De extra funktionerna som ytterligare sänka den totala ägandekostnaden för att köra analyser av stordata på Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake Storage Gen2

> [!NOTE]
> Några av funktionerna nedan kan variera i deras tillgänglighet under den offentliga förhandsversionen av Data Lake Storage Gen2. När nya funktioner och regioner är tillgängliga under förhandsvisningsprogrammet, förmedlas denna information.
> [Registrera dig](https://aka.ms/adlsgen2signup) till den offentliga förhandsversionen av Data Lake Storage Gen2.  

- **Hadoop-kompatibel åtkomst**: Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS drivrutinen](./abfs-driver.md) är tillgängliga i alla Apache Hadoop-miljöer, inklusive [Azure HDInsight](../../hdinsight/index.yml) och [Azure Databricks](../../azure-databricks/index.yml) att komma åt data som lagras i Data Lake Storage Gen2.

- **Flera protokoll och multimodal dataåtkomst**: Data Lake Storage Gen2 anses vara en **flera modal** lagringstjänsten eftersom den innehåller både objektlagring och filen systemgränssnitt för att samma data **samtidigt tid**. Detta uppnås genom att tillhandahålla flera protokoll-slutpunkter som kommer komma åt samma data. 

    Till skillnad från andra Analyslösningar behöver inte data som lagras i Data Lake Storage Gen2 ska flyttas eller omvandlas innan du kan köra en mängd olika analysverktyg. Du kan komma åt data via traditionella [Blob-lagring API: er](../blobs/storage-blobs-introduction.md) (till exempel: mata in data via [Event Hubs Capture](../../event-hubs/event-hubs-capture-enable-through-portal.md)) och bearbeta data med hjälp av HDInsight eller Azure Databricks på samma gång. 

- **Kostnadseffektiv**: Data Lake Storage Gen2 funktioner billigt lagringskapacitet och transaktioner. Som data övergångar genom livscykeln klar, faktureringstaxor ändra kostnader för att se till att latensbidrag via de inbyggda funktionerna för till exempel [Azure Blob storage livscykel](../common/storage-lifecycle-managment-concepts.md).

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