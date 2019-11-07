---
title: Åtkomst till flera protokoll på Azure Data Lake Storage | Microsoft Docs
description: 'Använd BLOB-API: er och program som använder BLOB-API: er med Azure Data Lake Storage Gen2.'
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f3e852d178a6720f8d567f4ecb042f0b83bcd56c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583154"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Åtkomst till flera protokoll på Azure Data Lake Storage

BLOB-API: er fungerar nu med konton som har ett hierarkiskt namn område. Detta låser upp eko systemet för verktyg, program och tjänster, samt flera Blob Storage-funktioner till konton som har ett hierarkiskt namn område.

Tills nyligen har du kanske haft för att ha haft separata lagrings lösningar för objekt lagring och analys lagring. Det beror på att Azure Data Lake Storage Gen2 har begränsat stöd för eko systemet. Det hade också begränsad åtkomst till Blob Service funktioner som diagnostisk loggning. En fragmenterad lagrings lösning är svår att underhålla eftersom du måste flytta data mellan konton för att utföra olika scenarier. Du behöver inte längre göra det.

Med åtkomst med flera protokoll på Data Lake Storage kan du arbeta med dina data med hjälp av eko systemets verktyg, program och tjänster. Detta inkluderar även verktyg och program från tredje part. Du kan peka dem på konton som har ett hierarkiskt namn område utan att behöva ändra dem. Dessa program fungerar *som de är* även om de anropar BLOB-API: er, eftersom BLOB-API: er nu kan köras på data i konton som har ett hierarkiskt namn område.

Blob Storage-funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och principer för [hantering av BLOB-lagrings livs cykeln](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namn område. Därför kan du aktivera hierarkiska namn rymder på Blob Storage-kontona utan att förlora åtkomsten till dessa viktiga funktioner. 

> [!NOTE]
> Åtkomst till flera protokoll på Data Lake Storage är allmänt tillgänglig och är tillgänglig i alla regioner. Vissa Azure-tjänster eller Blob Storage-funktioner som aktive ras av åtkomst till flera protokoll är fortfarande i för hands version. Mer information finns i tabellerna i varje avsnitt i den här artikeln. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Så här fungerar åtkomst till flera protokoll på data Lake Storage

BLOB-API: er och Data Lake Storage Gen2-API: er kan köras på samma data i lagrings konton som har ett hierarkiskt namn område. Data Lake Storage Gen2 dirigerar BLOB-API: er via det hierarkiska namn området så att du kan få fördelarna med de första klasserna i klass katalog och POSIX-kompatibla åtkomst kontrol listor (ACL: er). 

![Åtkomst till flera protokoll på Data Lake Storage konceptuell](./media/data-lake-storage-interop/interop-concept.png) 

Befintliga verktyg och program som använder BLOB API får dessa fördelar automatiskt. Utvecklare behöver inte ändra dem. Data Lake Storage Gen2 konsekvent tillämpar katalog-och fil nivå åtkomst kontrol listor oavsett vilket protokoll som verktyg och program använder för att komma åt data. 

## <a name="blob-storage-feature-support"></a>Stöd för Blob Storage-funktioner

Med åtkomst med flera protokoll på Data Lake Storage kan du använda fler Blob Storage-funktioner med din Data Lake Storage. I den här tabellen listas de funktioner som aktive ras av åtkomst till flera protokoll på Data Lake Storage. 

De objekt som visas i den här tabellen ändras med tiden att stöd för Blob Storage-funktioner fortsätter att expandera. 

> [!NOTE]
> Även om åtkomsten till flera protokoll på Data Lake Storage är allmänt tillgänglig, är stöd för vissa av dessa funktioner fortfarande i för hands version. 

|Blob Storage-funktion | Support nivå |
|---|---|
|[Låg frekvent åtkomst nivå](storage-blob-storage-tiers.md)|Allmänt tillgänglig|
|BLOB-SDK: er |Allmänt tillgänglig|
|[PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Allmänt tillgänglig|
|[CLI](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Allmänt tillgänglig|
|[Diagnostikloggar](../common/storage-analytics-logging.md)| Förhandsversion|
|[Principer för livs cykel hantering](storage-lifecycle-management-concepts.md)| Förhandsversion|
|[Meddelanden via Azure Event Grid](data-lake-storage-events.md)|Förhandsversion|
|[Arkivåtkomstnivå](storage-blob-storage-tiers.md)| Förhandsversion|
|[blobfuse](storage-how-to-mount-container-linux.md)|Stöds inte ännu|
|[Oåterkalleligt lagrings utrymme](storage-blob-immutable-storage.md)|Stöds inte ännu|
|[Ögonblicks bilder](storage-blob-snapshots.md)|Stöds inte ännu|
|[Mjuk borttagning](storage-blob-soft-delete.md)|Stöds inte ännu|
|[Statiska webbplatser](storage-blob-static-website.md)|Stöds inte ännu|

Mer information om allmänna kända problem och begränsningar med Azure Data Lake Storage Gen2 finns i [kända problem](data-lake-storage-known-issues.md).

## <a name="azure-ecosystem-support"></a>Stöd för Azure eko system

Med åtkomst till flera protokoll på Data Lake Storage kan du också ansluta fler Azure-tjänster med din Data Lake Storage. I den här tabellen listas de tjänster som aktive ras av åtkomst av flera protokoll på Data Lake Storage. 

Precis som listan över stödda Blob Storage-funktioner kommer objekten som visas i den här tabellen att ändras med tiden som stöd för Azure-tjänster fortsätter att expandera. 

> [!NOTE]
> Även om åtkomsten till flera protokoll på Data Lake Storage är allmänt tillgänglig, är stöd för vissa av dessa tjänster fortfarande i för hands version. 

|Azure-tjänst | Support nivå |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Allmänt tillgänglig|
|[Samling av Azure-Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Allmänt tillgänglig|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Allmänt tillgänglig|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Allmänt tillgänglig|
|[Logic Apps](https://azure.microsoft.com/services/logic-apps/)|Allmänt tillgänglig|
|[Azure Search](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|Förhandsversion|

En fullständig lista över stöd för Azure eko system för Data Lake Storage Gen2 finns i [integrera Azure Data Lake Storage med Azure-tjänster](data-lake-storage-integrate-with-azure-services.md).

Mer information om allmänna kända problem och begränsningar med Azure Data Lake Storage Gen2 finns i [kända problem](data-lake-storage-known-issues.md).

## <a name="next-steps"></a>Nästa steg

Se [kända problem](data-lake-storage-known-issues.md)




