---
title: Dataredundans i Azure Storage | Microsoft Docs
description: Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Alternativen för redundans inkluderar lokalt redundant lagring (LRS), zon-redundant lagring (ZRS), Geo-redundant lagring (GRS), Geo-redundant lagring med Läs behörighet (RA-GRS), Geo-redundant lagring (GZRS) (för hands version) och Read-Access geo-Zone-redundant lagring (RA-GZRS) (för hands version).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 17d1bd95067c15bd67f80f3713f0e497bff8a68d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516119"
---
# <a name="azure-storage-redundancy"></a>Azure Storage redundans

Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage kopierar dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Du kan välja att replikera dina data inom samma data Center, över zonindelade Data Center inom samma region eller mellan geografiskt åtskilda regioner.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

Azure Storage verifierar regelbundet integriteten för data som lagras med hjälp av cyklisk redundans kontroll (CRCs). Om skadade data upptäcks, repare ras den med hjälp av redundanta data. Azure Storage beräknar också kontroll summor på all nätverks trafik för att identifiera skadade data paket när data lagras eller hämtas.

## <a name="choosing-a-redundancy-option"></a>Välja ett alternativ för redundans

När du skapar ett lagrings konto kan du välja något av följande alternativ för redundans:

* [Lokalt redundant lagring (LRS)](storage-redundancy-lrs.md)
* [Zonredundant lagring (ZRS)](storage-redundancy-zrs.md)
* [Geo-redundant lagring (GRS)](storage-redundancy-grs.md)
* [Geo-redundant lagring med läsbehörighet (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)
* [GZRS (geo-Zone-redundant lagring)](storage-redundancy-gzrs.md)
* [Read-Access geo-Zone-redundant lagring (RA-GZRS)](storage-redundancy-gzrs.md)

Följande tabell ger en snabb överblick över omfattningen av hållbarhet och tillgänglighet som varje replikeringsprincip ger dig en specifik typ av händelse (eller händelse av liknande påverkan).

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Noden är inte tillgänglig i ett Data Center                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ett helt data Center (zonindelade eller icke-zonindelade) blir otillgängligt                                           | Nej                              | Ja                              | Ja                                  | Ja                                  |
| Ett områdes omfattande avbrott                                                                                     | Nej                              | Nej                               | Ja                                  | Ja                                  |
| Läs åtkomst till dina data (i en fjärran sluten geo-replikerad region) i händelse av regions omfattande otillgänglighet | Nej                              | Nej                               | Ja (med RA-GRS)                                   | Ja (med RA-GZRS)                                 |
| Utformad för att \_ tillhandahålla \_ hållbarhet för objekt under ett år                                          | minst 99,999999999% (11 9) | minst 99,9999999999% (12 9-) | minst 99.99999999999999% (16 9) | minst 99.99999999999999% (16 9) |
| Typer av lagrings konton som stöds                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2                     |
| Tillgänglighets-SLA för Läs begär Anden | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,99% (99,9% för låg frekvent åtkomst nivå) |
| Tillgänglighets-SLA för Skriv förfrågningar | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) |

Alla data i ditt lagrings konto replikeras, inklusive block-blobbar och tillägg av blobbar, sid blobbar, köer, tabeller och filer. Alla typer av lagrings konton replikeras, även om ZRS kräver ett allmänt-syfte v2-lagrings konto.

Pris information för varje alternativ för redundans finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/). 

Information om Azure Storage garantier för hållbarhet och tillgänglighet finns i [service avtalet för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Azure Premium Storage har endast stöd för lokalt redundant lagring (LRS).

## <a name="changing-replication-strategy"></a>Ändra replikeringsprincip

Du kan ändra lagrings kontots replikeringsprincip genom att använda [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](storage-powershell-guide-full.md), [azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)eller något av Azure Storage- [klient biblioteken](https://docs.microsoft.com/azure/index#pivot=sdkstools). Att ändra replikerings typen för ditt lagrings konto leder inte till en tids gräns.

   > [!NOTE]
   > För närvarande kan du inte använda Azure Portal eller Azure Storage klient bibliotek för att konvertera ditt konto till ZRS, GZRS eller RA-GZRS. Om du vill migrera ditt konto till ZRS, se [Zone-redundant lagring (ZRS) för att skapa hög tillgängliga Azure Storage program](storage-redundancy-zrs.md) för mer information. För att migrera GZRS eller RA-GZRS, se [geo-Zone-redundant lagring för hög tillgänglighet och maximal hållbarhet (för hands version)](storage-redundancy-zrs.md) för mer information.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Finns det några kostnader för att ändra mitt kontos replikeringsprincip?

Det beror på din konverterings Sök väg. Beställ från minst till dyra, Azure Storage redundans erbjudanden LRS, ZRS, GRS, RA-GRS, GZRS och RA-GZRS. Om du till exempel går *från* LRS till en annan typ av replikering debiteras ytterligare avgifter eftersom du flyttar till en mer avancerad redundans nivå. Migrering *till* GRS eller RA-GRS innebär en utgående bandbredds avgift eftersom dina data (i din primära region) replikeras till din sekundära fjärrregion. Den här avgiften är en engångs kostnad vid första konfigurationen. När data har kopierats sker inga ytterligare migreringar. Du debiteras bara för att replikera nya eller uppdateringar av befintliga data. Mer information om kostnader för bandbredd finns på [sidan Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

Om du migrerar ditt lagrings konto från GRS till LRS finns det ingen ytterligare kostnad, men dina replikerade data tas bort från den sekundära platsen.

Om du migrerar ditt lagrings konto från RA-GRS till GRS eller LRS debiteras det kontot som RA-GRS under ytterligare 30 dagar efter det datum då det konverterades.

## <a name="see-also"></a>Se också

- [Lokalt redundant lagring (LRS): Data redundans för låg kostnad för Azure Storage](storage-redundancy-lrs.md)
- [Zon-redundant lagring (ZRS): Azure Storage program med hög tillgänglighet](storage-redundancy-zrs.md)
- [Geo-redundant lagring (GRS): Replikering mellan regioner för Azure Storage](storage-redundancy-grs.md)
- [Geo-Zone-redundant lagring (GZRS) för hög tillgänglighet och maximal hållbarhet (för hands version)](storage-redundancy-gzrs.md)
- [Skalbarhets- och prestandamål i Azure Storage](storage-scalability-targets.md)
- [Designa program med hög tillgänglighet med hjälp av RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundans alternativ och Geo-redundant lagring med Läs behörighet](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper – Azure Storage: En moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
