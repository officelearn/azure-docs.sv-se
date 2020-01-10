---
title: Dataredundans i Azure Storage | Microsoft Docs
description: Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Alternativen för redundans inkluderar lokalt redundant lagring (LRS), zon-redundant lagring (ZRS), Geo-redundant lagring (GRS), Geo-redundant lagring med Läs behörighet (RA-GRS), Geo-redundant lagring (GZRS) (för hands version) och Read-Access geo-Zone-redundant lagring (RA-GZRS) (för hands version).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7517c4d9b3f9b58d9cf745f5001078837e1fbfea
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748172"
---
# <a name="azure-storage-redundancy"></a>Azure Storage redundans

Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage kopierar dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Du kan välja att replikera dina data inom samma data Center, över zonindelade Data Center inom samma region eller mellan geografiskt åtskilda regioner.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

Azure Storage verifierar regelbundet integriteten för data som lagras med hjälp av cyklisk redundans kontroll (CRCs). Om skadade data upptäcks, repare ras den med hjälp av redundanta data. Azure Storage beräknar också kontroll summor på all nätverks trafik för att identifiera skadade data paket när data lagras eller hämtas.

## <a name="choosing-a-redundancy-option"></a>Välja ett alternativ för redundans

När du skapar ett lagrings konto kan du välja något av följande alternativ för redundans:

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

Följande tabell ger en snabb överblick över omfattningen av hållbarhet och tillgänglighet som varje replikeringsprincip ger dig en specifik typ av händelse (eller händelse av liknande påverkan).

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (för hands version)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Noden är inte tillgänglig i ett Data Center                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ett helt data Center (zonindelade eller icke-zonindelade) blir otillgängligt                                           | Inga                              | Ja                              | Ja                                  | Ja                                  |
| Ett områdes omfattande avbrott                                                                                     | Inga                              | Inga                               | Ja                                  | Ja                                  |
| Läs åtkomst till dina data (i en fjärran sluten geo-replikerad region) i händelse av regions omfattande otillgänglighet | Inga                              | Inga                               | Ja (med RA-GRS)                                   | Ja (med RA-GZRS)                                 |
| Utformad för att tillhandahålla \_\_ hållbarhet för objekt under ett angivet år<sup>1</sup>                                          | minst 99,999999999% (11 9) | minst 99,9999999999% (12 9-) | minst 99.99999999999999% (16 9) | minst 99.99999999999999% (16 9) |
| Lagrings konto typer som stöds<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Tillgänglighets-SLA för Läs begär Anden<sup>1</sup>  | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) för GRS<br /><br />Minst 99,99% (99,9% för låg frekvent åtkomst nivå) för RA-GRS | Minst 99,9% (99% för låg frekvent åtkomst nivå) för GZRS<br /><br />Minst 99,99% (99,9% för låg frekvent åtkomst nivå) för RA-GZRS |
| Tillgänglighets-SLA för Skriv begär Anden<sup>1</sup>  | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) |

<sup>1</sup> information om Azure Storage garantier för hållbarhet och tillgänglighet finns i [service avtalet för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).   

<sup>2</sup> information om lagrings konto typer finns i [Översikt över lagrings konto](storage-account-overview.md).

Alla data för alla typer av lagrings konton replikeras, inklusive block-blobbar, tillägg av blobbar, sid blobbar, köer, tabeller och filer.

Pris information för varje alternativ för redundans finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium-Disklagring har för närvarande endast stöd för lokalt redundant lagring (LRS). Azure Premium-Block Blob Storage har stöd för lokalt redundant lagring (LRS) och Zone-redundant lagring (ZRS) i vissa regioner.

## <a name="changing-replication-strategy"></a>Ändra replikeringsprincip

Du kan ändra lagrings kontots replikeringsprincip genom att använda [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](storage-powershell-guide-full.md), [azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)eller något av Azure Storage- [klient biblioteken](https://docs.microsoft.com/azure/index#pivot=sdkstools). Att ändra replikerings typen för ditt lagrings konto leder inte till en tids gräns.

> [!NOTE]
> För närvarande kan du inte använda Azure Portal eller Azure Storage klient bibliotek för att konvertera ditt konto till ZRS, GZRS eller RA-GZRS. Om du vill migrera ditt konto till ZRS, se [Zone-redundant lagring (ZRS) för att skapa hög tillgängliga Azure Storage program](storage-redundancy-zrs.md) för mer information. För att migrera GZRS eller RA-GZRS, se [geo-Zone-redundant lagring för hög tillgänglighet och maximal hållbarhet (för hands version)](storage-redundancy-zrs.md) för mer information.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Finns det några kostnader för att ändra mitt kontos replikeringsprincip?

Det beror på din konverterings Sök väg. Beställ från minst till dyra, Azure Storage redundans erbjudanden LRS, ZRS, GRS, RA-GRS, GZRS och RA-GZRS. Om du till exempel går *från* LRS till en annan typ av replikering debiteras ytterligare avgifter eftersom du flyttar till en mer avancerad redundans nivå. Migrering *till* GRS eller RA-GRS innebär en utgående bandbredds avgift eftersom dina data (i din primära region) replikeras till din sekundära fjärrregion. Den här avgiften är en engångs kostnad vid första konfigurationen. När data har kopierats sker inga ytterligare migreringar. Du debiteras bara för att replikera nya eller uppdateringar av befintliga data. Mer information om kostnader för bandbredd finns på [sidan Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

Om du migrerar ditt lagrings konto från GRS till LRS finns det ingen ytterligare kostnad, men dina replikerade data tas bort från den sekundära platsen.

Om du migrerar ditt lagrings konto från RA-GRS till GRS eller LRS debiteras det kontot som RA-GRS under ytterligare 30 dagar efter det datum då det konverterades.

## <a name="see-also"></a>Se också

- [Översikt över lagrings konto](storage-account-overview.md)
- [Lokalt redundant lagring (LRS): låg kostnads data redundans för Azure Storage](storage-redundancy-lrs.md)
- [Zone-redundant lagring (ZRS): Azure Storage program med hög tillgänglighet](storage-redundancy-zrs.md)
- [Geo-redundant lagring (GRS): replikering mellan regioner för Azure Storage](storage-redundancy-grs.md)
- [Geo-Zone-redundant lagring (GZRS) för hög tillgänglighet och maximal hållbarhet (för hands version)](storage-redundancy-gzrs.md)
- [Skalbarhets-och prestanda mål för standard lagrings konton](scalability-targets-standard-account.md)
- [Designa program med hög tillgänglighet med hjälp av RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundans alternativ och Geo-redundant lagring med Läs behörighet](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper – Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
