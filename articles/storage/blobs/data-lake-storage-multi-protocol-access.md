---
title: Åtkomst till flera protokoll på Azure Data Lake Storage (för hands version) | Microsoft Docs
description: 'Använd BLOB-API: er och program som använder BLOB-API: er med Azure Data Lake Storage Gen2.'
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 471e3008c25ddef83dd9b502dd8f677ae31cc71b
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259379"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Åtkomst till flera protokoll på Azure Data Lake Storage (för hands version)

BLOB-API: er fungerar nu med konton som har ett hierarkiskt namn område. Detta låser upp hela eko systemet med verktyg, program och tjänster, samt alla Blob Storage-funktioner till konton som har ett hierarkiskt namn område.

Tills nyligen har du kanske haft för att ha haft separata lagrings lösningar för objekt lagring och analys lagring. Det beror på att Azure Data Lake Storage Gen2 har begränsat stöd för eko systemet. Det hade också begränsad åtkomst till Blob Service funktioner som diagnostisk loggning. En fragmenterad lagrings lösning är svår att underhålla eftersom du måste flytta data mellan konton för att utföra olika scenarier. Du behöver inte längre göra det.

> [!NOTE]
> Åtkomst till flera protokoll på Data Lake Storage finns i en offentlig för hands version och är tillgänglig i [dessa regioner](#region-availability). Information om hur du granskar begränsningar finns i artikeln om [kända problem](data-lake-storage-known-issues.md) . För att registrera dig i för hands versionen, se [den här sidan](https://aka.ms/blobinteropsignup).

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Använd hela eko systemet för program, verktyg och tjänster

Om du registrerar dig i för hands versionen av åtkomst till flera protokoll på Data Lake Storage kan du arbeta med alla dina data med hjälp av hela eko systemet för verktyg, program och tjänster. Detta inkluderar Azure-tjänster som [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IOT Hub](https://docs.microsoft.com/azure/iot-hub/), [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources)och många andra. 

Detta inkluderar även verktyg och program från tredje part. Du kan peka dem på konton som har ett hierarkiskt namn område utan att behöva ändra dem. Dessa program fungerar *som de är* även om de anropar BLOB-API: er, eftersom BLOB-API: er nu kan köras på data i konton som har ett hierarkiskt namn område.

> [!NOTE]
> Information om hur du granskar begränsningar finns i artikeln om [kända problem](data-lake-storage-known-issues.md) .

## <a name="use-all-blob-storage-features"></a>Använd alla Blob Storage-funktioner

Blob Storage-funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och principer för [hantering av BLOB-lagrings livs cykeln](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namn område. Därför kan du aktivera hierarkiska namn rymder på Blob Storage-kontona utan att återfick åtkomst till dessa viktiga funktioner. 

> [!NOTE]
> Information om hur du granskar begränsningar finns i artikeln om [kända problem](data-lake-storage-known-issues.md) .

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Så här fungerar åtkomst till flera protokoll på data Lake Storage

BLOB-API: er och Data Lake Storage Gen2-API: er kan köras på samma data i lagrings konton som har ett hierarkiskt namn område. Data Lake Storage Gen2 dirigerar BLOB-API: er via det hierarkiska namn området så att du kan få fördelarna med de första klasserna i klass katalog och POSIX-kompatibla åtkomst kontrol listor (ACL: er). 

![Åtkomst till flera protokoll på Data Lake Storage konceptuell](./media/data-lake-storage-interop/interop-concept.png) 

Befintliga verktyg och program som använder BLOB API får dessa fördelar automatiskt. Utvecklare behöver inte ändra dem. Data Lake Storage Gen2 konsekvent tillämpar katalog-och fil nivå åtkomst kontrol listor oavsett vilket protokoll som verktyg och program använder för att komma åt data. 

<a id="region-availability" />

## <a name="region-availability"></a>Regional tillgänglighet

Åtkomst till flera protokoll på Azure Data Lake Storage (för hands version) är tillgänglig i följande regioner:

|||||
|-|-|-|-|
|Centrala USA|Västra centrala USA|Centrala Kanada|
|East US|Östasien|Norra Europa|
|USA, östra 2|Sydostasien|Västra Europa|
|Västra USA|Östra Australien|Östra Japan|
|Västra USA 2|Södra Brasilien||

## <a name="next-steps"></a>Nästa steg

Se [kända problem](data-lake-storage-known-issues.md)




