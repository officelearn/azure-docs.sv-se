---
title: Prestanda nivåer för Azure Block Blob Storage – Azure Storage
description: Prestanda nivåer för Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ac483a338b7d71142b89b13e41fc048346ac037f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802653"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Prestanda nivåer för Azure Block Blob Storage

När företag distribuerar prestanda känsliga moln program är det viktigt att du har alternativ för kostnads effektiv data lagring på olika prestanda nivåer.

Azure Block Blob Storage erbjuder två olika prestanda nivåer:

- Premium: optimerat för höga transaktions priser och ensiffriga konsekvent lagrings fördröjning
- Standard: optimerad för hög kapacitet och högt data flöde

Följande överväganden gäller för de olika prestanda nivåerna:

- Standard prestanda är tillgängligt i alla [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=storage). Premium-prestanda finns i [utvalda regioner](https://azure.microsoft.com/global-infrastructure/services/?products=storage).
- Premium-prestanda ger optimerade priser för program med höga transaktions priser för att [minska den totala lagrings kostnaden](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) för dessa arbets belastningar.
- Premium-prestanda kräver att du använder Block Blob Storage-konton, som stöder Block-Blob och tillägg av BLOB.
- Standard prestanda är tillgängligt med Generell användning v1-, Generell användning v2-och Blob Storage-konton.
- Premium-och standard-prestanda har både stöd för [block blobbar med högt genomflöde](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). Block blobbar med höga data flöden är tillgängliga för förstklassiga prestanda med mer än 256 KiB. Block blobbar med höga data flöden är tillgängliga för standard prestanda på över 4 MiB-block eller i stället för att ange BLOB-storlekar.
- Premium prestanda är för närvarande endast tillgängligt med lokalt redundant lagring (LRS).

## <a name="premium-performance"></a>Förstklassig prestanda

Med Premium Performance Block Blob Storage blir data tillgängliga via maskin vara med hög prestanda. Data lagras i solid state-hårddiskar (SSD) som är optimerade för låg latens. SSD ger högre data flöde jämfört med traditionella hård diskar.

Förstklassiga prestanda Block Blob Storage är perfekt för arbets belastningar som kräver snabba och konsekventa svars tider. Det är bäst för arbets belastningar som utför många små transaktioner.

## <a name="standard-performance"></a>Standard prestanda

Standard prestanda stöder olika [åtkomst nivåer](storage-blob-storage-tiers.md) för att lagra data på det mest kostnads effektiva sättet. Den är optimerad för hög kapacitet och högt data flöde på stora data mängder.

## <a name="blob-lifecycle-management"></a>Hantering av BLOB-livscykel

Blob Storage livs cykel hantering ger en omfattande, regelbaserade princip:

- Premium-data förfaller i slutet av livs cykeln
- Standard-över gångs data till bästa åtkomst nivå och förfaller data i slutet av livs cykeln

Mer information finns i [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md).

Data som lagras i ett Premium Block Blob Storage-konto kan inte flyttas mellan frekventa, låg frekventa och Arkiv lag rings nivåer. Du kan dock kopiera blobbar från ett Block Blob Storage-konto till frekvent åtkomst nivå i ett *annat* konto. Använd [blockera-block från URL](/rest/api/storageservices/put-block-from-url) -API eller [AzCopy-v10](../common/storage-use-azcopy-v10.md) för att kopiera data till ett annat konto. Funktionen för att **blockera från URL** : er kopierar synkront data på servern. Anropet slutförs först efter att alla data har flyttats från den ursprungliga Server platsen till mål platsen.

## <a name="next-steps"></a>Nästa steg

Utvärdera frekvent, låg frekvent och Arkiv lag ring i GPv2-och Blob Storage-konton

- [Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)
- [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
- [Lär dig mer om återuppväcks BLOB-data från Arkiv lag rings nivån](storage-blob-rehydration.md)
- [Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)
- [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
- [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
