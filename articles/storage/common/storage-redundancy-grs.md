---
title: Geo-redundant lagring (GRS) för över-regionala hållbarhet
titleSuffix: Azure Storage
description: Geo-redundant lagring (GRS) replikerar data mellan två regioner som är hundratals mil i avstånd. GRS skyddar mot maskin varu fel i data centret samt regionala katastrofer.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c44c13f268a561e3094ae76757504a86627e1f58
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895224"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Geo-redundant lagring (GRS): replikering mellan regioner för Azure Storage

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Geo-redundant lagring med läsbehörighet (RA-GRS)

Read-Access Geo-redundant lagring (RA-GRS) maximerar tillgängligheten för ditt lagrings konto. RA-GRS tillhandahåller skrivskyddad åtkomst till data på den sekundära platsen, förutom geo-replikering över två regioner.

När du aktiverar skrivskyddad åtkomst till dina data i den sekundära regionen, är dina data tillgängliga på en sekundär slut punkt samt på den primära slut punkten för ditt lagrings konto. Den sekundära slut punkten liknar den primära slut punkten, men lägger till suffixet `–secondary` till konto namnet. Om den primära slut punkten för Blob Service till exempel är `myaccount.blob.core.windows.net`, är den sekundära slut punkten `myaccount-secondary.blob.core.windows.net`. Åtkomst nycklarna för ditt lagrings konto är desamma för både den primära och den sekundära slut punkten.

Tänk på följande när du använder RA-GRS:

- Ditt program måste hantera vilken slut punkt som den interagerar med när du använder RA-GRS.
- Eftersom asynkron replikering innebär en fördröjning kan ändringar som ännu inte har repliker ATS till den sekundära regionen förloras om det inte går att återställa data från den primära regionen.
- Du kan kontrol lera den senaste Sync-tiden för ditt lagrings konto. Tid för senaste synkronisering är ett GMT-datum/tid-värde. Alla primära skrivningar före den senaste synkroniseringstid-tiden har skrivits till den sekundära platsen, vilket innebär att de är tillgängliga för läsning från den sekundära platsen. Primära skrivningar efter den senaste synkroniseringen kan eventuellt vara otillgängligt för läsningar än. Du kan fråga det här värdet med hjälp av [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md)eller från ett av Azure Storage-klient biblioteken.
- Om du initierar en konto redundansväxling (för hands version) av ett GRS-eller RA-GRS-konto till den sekundära regionen återställs skriv åtkomst till kontot när redundansväxlingen har slutförts. Mer information finns i [haveri beredskap och lagrings konto redundans (för hands version)](storage-disaster-recovery-guidance.md).
- RA-GRS är avsedd för användning med hög tillgänglighet. Mer information om skalbarhet finns i [Check lista för prestanda](storage-performance-checklist.md).
- Förslag på hur du utformar för hög tillgänglighet med RA-GRS finns i [utforma hög tillgängliga program med hjälp av RA-GRS-lagring](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Vad är återställnings-och RTO med GRS?

**Återställnings punkt mål (återställnings punkt mål):** I GRS och RA-GRS replikerar lagrings tjänsten asynkront Geo-data från den primära till den sekundära platsen. I händelse av att den primära regionen blir otillgänglig kan du utföra en konto redundansväxling (för hands version) till den sekundära regionen. När du initierar en redundansväxling kan de senaste ändringarna som ännu inte har geo-replikeras gå förlorade. Antalet minuter av potentiella data som förloras kallas för återställningen. Återställnings punkten anger tidpunkten då data kan återställas. Azure Storage har vanligt vis en drift som är mindre än 15 minuter, även om det inte finns något service avtal för hur länge geo-replikeringen tar.

**Mål för återställnings tid (RTO):** RTO är ett mått på hur lång tid det tar att utföra redundansväxlingen och hämta lagrings kontot online igen. Tiden för att utföra redundansväxlingen omfattar följande åtgärder:

- Tiden tills kunden initierar redundansväxlingen av lagrings kontot från den primära till den sekundära regionen.
- Tiden som krävs av Azure för att utföra redundansväxlingen genom att ändra de primära DNS-posterna så att de pekar på den sekundära platsen.

## <a name="paired-regions"></a>Länkade regioner

När du skapar ett lagrings konto väljer du den primära regionen för kontot. Den kopplade sekundära regionen bestäms utifrån den primära regionen och kan inte ändras. Uppdaterad information om regioner som stöds av Azure finns i [verksamhets kontinuitet och haveri beredskap (BCDR): Azure-kopplade regioner](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Se också

- [Azure Storage-replikering](storage-redundancy.md)
- [Lokalt redundant lagring (LRS): låg kostnads data redundans för Azure Storage](storage-redundancy-lrs.md)
- [Zone-redundant lagring (ZRS): Azure Storage program med hög tillgänglighet](storage-redundancy-zrs.md)
