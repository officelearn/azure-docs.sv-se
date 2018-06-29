---
title: Azure Data Lake lagring Gen2 Preview introduktion
description: En översikt över Azure Data Lake lagring Gen2 Preview
services: storage
documentationcenter: ''
author: jamesbak
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0631b1d0c8da925858f0b7fb1d778cb1161eb737
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061532"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Introduktion till Azure Data Lake lagring Gen2 Preview

Azure Data Lake lagring Gen2 Preview är en uppsättning funktioner som är dedikerad för stordata, byggt ovanpå [Azure Blob storage](../blobs/storage-blobs-introduction.md). På så sätt kan du samverka med dina data med hjälp av böjningsmönster för lagring av system- och båda filen. På så sätt blir Data Lake lagring Gen2 endast molnbaserad flera spärrad lagringstjänsten, så att du kan extrahera analytics värdet från alla dina data.

Data Lake lagring Gen2 funktioner alla egenskaper som krävs för hela livscykeln för analytics-data. Detta beror på att Konvergera funktionerna i vår två befintliga lagringstjänster. Funktioner från [Azure Data Lake lagring Gen1](../../data-lake-store/index.md), till exempel filsystemen, filnivå säkerhet och skala kombineras med låg kostnad, skiktad lagring, funktioner för hög tillgänglighet/katastrofåterställning och en stor SDK/tooling ekosystemet från [Azure Blob storage](../blobs/storage-blobs-introduction.md). I Data Lake lagring Gen2 kvar alla egenskaper för objektlagring medan lägger till fördelarna med en fil systemgränssnittet optimerade för analytics arbetsbelastningar.

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för enterprise stordata

Data Lake lagring Gen2 är grundläggande storage-tjänst för att skapa enterprise data sjöar (EDL) på Azure. Utformat från början för att underhålla flera petabytes information vid stödja hundratals Gigabit genomströmning, ger Data Lake lagring Gen2 dig ett enkelt sätt att hantera stora mängder data.

En grundläggande funktion i Data Lake lagring Gen2 är att lägga till en [hierarkiskt namnområde](./namespace.md) till Blob storage-tjänst som organiserar objekt/filer i en hierarki med kataloger för performant dataåtkomst. Hierarkiskt namnområde kan också Data Lake lagring Gen2 att stödja båda objektet store och filen system böjningsmönster på samma gång. En gemensam namngivningskonvention för objektet store använder exempelvis snedstreck i namnet för att efterlikna en hierarkisk mappstruktur. Den här strukturen blir verkliga med Data Lake lagring Gen2. Åtgärder som till exempel byta namn på eller ta bort en katalog bli enda atomiska metadataåtgärder för katalogen i stället för uppräkning och bearbetning av alla objekt som delar namnprefix för katalogen.

Tidigare hade molnbaserade analytics att angripa i delarna av prestanda, hantering och säkerhet. Data Lake lagring Gen2 löser var och en av dessa aspekter på följande sätt:

- **Prestanda** optimeras eftersom du inte behöver kopiera eller Transformera data som en förutsättning för analys. Hierarkiskt namnområde förbättrar avsevärt prestandan för directory-hanteringsåtgärder som förbättrar prestandan för jobbet.

- **Hantering av** är enklare att eftersom du kan organisera och manipulera filerna med kataloger och underkataloger.

- **Kostnadseffektivitet** är möjligt eftersom Data Lake lagring Gen2 bygger på låg kostnad [Azure Blob storage](../blobs/storage-blobs-introduction.md). Ytterligare funktioner ytterligare minska den totala ägandekostnaden för att köra stordata i Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake lagring Gen2

> [!NOTE]
> Några av funktionerna i listan nedan kan variera i deras tillgänglighet under förhandsversion av Data Lake lagring Gen2. När nya funktioner och regioner är tillgängliga under förhandsgranskningsprogrammet, förmedlas denna information.
> [Registrera dig](https://aka.ms/adlsgen2signup) till offentliga förhandsversionen av Data Lake lagring Gen2.  

- **Hadoop-kompatibel åtkomst**: Data Lake lagring Gen2 kan du hantera och komma åt data, precis som med en [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS drivrutinen](./abfs-driver.md) är tillgänglig i alla Apache Hadoop-miljöer, inklusive [Azure HDInsight](../../hdinsight/index.yml) och [Azure Databricks](../../azure-databricks/index.yml) att komma åt data som lagras i Data Lake Storage Gen2.

- **Flera protokoll och flera spärrat dataåtkomst**: Data Lake lagring Gen2 anses vara en **flera modal** storage-tjänst som tillhandahåller både lager objekt och filen systemgränssnitt till samma data **samtidigt tid**. Detta uppnås genom att tillhandahålla flera protokoll-slutpunkter som har tillgång till samma data. 

    Till skillnad från andra Analyslösningar behöver data som lagras i Data Lake lagring Gen2 inte flytta eller omvandlas innan du kan köra en mängd olika verktyg för analys. Du kan komma åt data via traditionella [Blob storage-API: er](../blobs/storage-blobs-introduction.md) (till exempel: mata in data via [Event Hubs avbilda](../../event-hubs/event-hubs-capture-enable-through-portal.md)) och bearbeta dessa data med HDInsight eller Azure Databricks på samma gång. 

- **Kostnadseffektiv**: Data Lake lagring Gen2 funktioner prisvärda lagringskapacitet och transaktioner. Som data övergångar genom livscykeln fullständig, fakturering priser ändra hålls kostnader till ett minimum via inbyggda funktioner som [Azure Blob storage livscykel](../common/storage-lifecycle-managment-concepts.md).

- **Fungerar med Blob storage-verktyg, ramverk och appar**: Data Lake lagring Gen2 fortsätter att fungera med en mängd olika verktyg, ramverk och program som finns idag för Blob storage.

- **Optimerad drivrutinen**: den `abfs` drivrutinen är [optimerats specifikt](./abfs-driver.md) för stordata. Motsvarande REST API: er som är anslutna via den `dfs` slutpunkt, `dfs.core.windows.net`.

## <a name="scalability"></a>Skalbarhet

Azure Storage är skalbara av design om du kommer åt via Data Lake lagring Gen2 eller Blob storage-gränssnitt. Det går att lagra och hantera *många exabyte av data*. Den här mängden lagringsutrymme som är tillgängligt med genomflödet mätt i Gigabit per sekund (Gbps) på hög nivå av i/o-åtgärder per sekund (IOPS). Utöver bara beständiga utförs bearbetning vid nära konstant per begäran fördröjningar som mäts i tjänsten, konto och filnivåer.

## <a name="cost-effectiveness"></a>Kostnadseffektivitet

En av de många fördelarna med att skapa Data Lake lagring Gen2 ovanpå Azure Blob storage är den [prisvärda](https://azure.microsoft.com/pricing/details/storage) av lagringskapacitet och transaktioner. Till skillnad från andra molntjänster lagring minskar Data Lake lagring Gen2 kostnader eftersom data inte behöver flyttas eller omvandlas innan du utför analys.

Dessutom funktioner som den [hierarkiskt namnområde](./namespace.md) förbättrar prestanda i många analytics-jobb. Denna förbättring av prestanda innebär att du behöver mindre datorkraft att bearbeta samma mängd data, vilket resulterar i en lägre ägandekostnader (TCO) för slutpunkt till slutpunkt analytics-jobbet.

## <a name="next-steps"></a>Nästa steg

I följande artiklar beskrivs några av de viktigaste begrepp av Data Lake lagring Gen2 och information om hur du lagrar, komma åt, hantera och få insikter från dina data:

* [Hierarkiskt namnområde](./namespace.md)
* [Skapa ett lagringskonto](./quickstart-create-account.md)
* [Skapa ett HDInsight-kluster med Azure Data Lake lagring Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Använda ett Azure Data Lake lagring Gen2 konto i Azure Databricks](./quickstart-create-databricks-account.md) 