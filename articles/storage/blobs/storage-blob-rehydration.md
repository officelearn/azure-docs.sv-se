---
title: Dehydratisera BLOB-data från Arkiv lag rings nivå
description: Gör om dina blobbar från Arkiv lag ring så att du kan komma åt data.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958218"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Dehydratisera BLOB-data från Arkiv lag rings nivå

När en BLOB finns i Arkiv åtkomst nivån anses den vara offline och kan inte läsas eller ändras. BLOB-metadata är online och tillgängliga, så att du kan lista bloben och dess egenskaper. Läsning och ändring av BLOB-data är bara tillgängligt med online-nivåer som frekvent eller låg frekvent. Det finns två alternativ för att hämta och komma åt data som lagras i Arkiv åtkomst nivån.

1. [Dehydratiserar en arkiverad blob till en online-nivå](#rehydrate-an-archived-blob-to-an-online-tier) – reserverar en arkiverad blob till frekvent eller låg frekvent genom att ändra dess nivå med hjälp av åtgärden [Ange BLOB-nivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Kopiera en arkiverad blob till en online-nivå](#copy-an-archived-blob-to-an-online-tier) – skapa en ny kopia av en arkiverad BLOB med hjälp av åtgärden [Kopiera BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Ange ett annat BLOB-namn och en mål nivå för frekvent eller låg frekvent.

 Mer information om nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Retorkade en arkiverad blob till en onlinenivå

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopiera en arkiverad blob till en onlinenivå

Om du inte vill omtorka en BLOB kan du välja en [Kopiera BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) -åtgärd. Den ursprungliga blobben förblir oförändrad i arkivet medan du arbetar med den nya blobben på frekvent eller låg frekvent nivå. Du kan ange den valfria egenskapen *x-MS-rehydratiserat-Priority* till standard eller hög (för hands version) när du använder kopierings processen.

Arkiv-blobbar kan bara kopieras till mål nivåerna online. Det finns inte stöd för att kopiera en Arkiv-blob till en annan Arkiv-blob.

Det tar tid att kopiera en BLOB från arkivet. Med den **kopierade BLOB** -åtgärden rehydratiseras tillfälligt bloben för Arkiv källan för att skapa en ny online-BLOB på mål nivån. Den här nya blobben är inte tillgänglig förrän den temporära ÅTERUPPVÄCKNING från arkivet är klar och data skrivs till den nya blobben.

## <a name="pricing-and-billing"></a>Priser och fakturering

Återuppväcks blobs från arkivet till frekventa eller låg frekventa nivåer debiteras som Läs åtgärder och data hämtning. Användning av hög prioritet (för hands version) har högre kostnader för drift och data hämtning jämfört med standard prioritet. ÅTERUPPVÄCKNING med hög prioritet visas som ett separat rad objekt på fakturan. Om en hög prioritets förfrågan om att returnera en Arkiv-blob av några få gigabyte tar över 5 timmar debiteras du inte den höga prioriteten för hämtning. Standard avgifterna för hämtningen gäller dock fortfarande.

Att kopiera blobbar från arkivet till frekventa eller låg frekventa nivåer debiteras som Läs åtgärder och data hämtning. En Skriv åtgärd debiteras för att skapa den nya kopian. Avgifter för tidig borttagning gäller inte när du kopierar till en online-BLOB eftersom käll-bloben förblir oförändrad på Arkiv nivån. Avgifter med hög prioritet gäller.

Blobbar i Arkiv lag rings nivån lagras i minst 180 dagar. Om du tar bort eller återuppväcks arkiverade blobbar innan 180 dagar påförs avgifter för tidig borttagning.

> [!NOTE]
> Mer information om priser för block-blobar och data ÅTERUPPVÄCKNING finns [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/). Mer information om avgifter för utgående data överföringar finns i [pris uppgifter för data överföring](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om Blob Storage nivåer](storage-blob-storage-tiers.md)
* [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
* [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
* [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
