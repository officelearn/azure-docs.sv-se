---
title: Replikering av data i Azure Storage | Microsoft Docs
description: Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Replikering till exempel lokalt redundant lagring (LRS), zonredundant lagring (ZRS), geo-redundant lagring (GRS) och read-access geo-redundant lagring (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/08/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3fb3860cbda2e1d46505711d7a175e5d42ec7018
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455515"
---
# <a name="azure-storage-replication"></a>Azure Storage-replikering

Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage-replikering kopierar dina data så att den är skyddad från planerade och oplanerade händelser som sträcker sig från tillfälliga maskinvarufel, nätverket eller strömavbrott, massiv naturkatastrofer och så vidare. Du kan välja att replikera dina data inom samma Datacenter över zonindelad datacenter inom samma region och i olika regioner.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

## <a name="choosing-a-replication-option"></a>Välja ett replikeringsalternativ

När du skapar ett lagringskonto kan du välja något av följande replikeringsalternativ:

* [Lokalt redundant lagring (LRS)](storage-redundancy-lrs.md)
* [Zonredundant lagring (ZRS)](storage-redundancy-zrs.md)
* [Geo-redundant lagring (GRS)](storage-redundancy-grs.md)
* [Geo-redundant lagring med läsbehörighet (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

I följande tabell ger en snabb överblick över omfånget för hållbarhet och tillgänglighet som varje replikeringsstrategi ger dig för en viss typ av händelse (eller händelse liknande effektnivå).

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Noden otillgänglighet inom ett datacenter                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ett helt datacenter (zonindelad eller icke-zonindelad) blir otillgänglig                                           | Nej                              | Ja                              | Ja                                  | Ja                                  |
| Ett avbrott på regionomfattande                                                                                     | Nej                              | Nej                               | Ja                                  | Ja                                  |
| Läsåtkomst till dina data (på en fjärransluten, geo-replikerad region) i händelse av hela otillgänglighet | Nej                              | Nej                               | Nej                                   | Ja                                  |
| Utformad för att ge \_ \_ objektshållbarhet under ett givet år                                          | minst 99,999999999% (11 9) | minst 99,9999999999% (12 9) | minst 99,99999999999999% (16 9) | minst 99,99999999999999% (16 9) |
| Stöds storage-kontotyper                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| Tillgänglighet för läsbegäranden | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,99% (99,9% för kalla åtkomstnivåer) |
| Tillgänglighet för skrivbegäranden | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) |

Information om priser för varje alternativ för dataredundans, finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Information om Azure Storage-garantier för hållbarhet och tillgänglighet, finns i den [serviceavtal för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Premium Storage stöder endast lokalt redundant lagring (LRS). Information om Premium-lagring finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar för virtuella Azure-datorer](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Ändra replikeringsstrategi
Vi kan du ändra replikeringsstrategi i ditt storage-konto med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), eller en av många [ Azure-klientbiblioteken](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Ändra typen för replikering av ditt lagringskonto resulterar inte i driftstopp.

   > [!NOTE]
   > För närvarande kan använda du inte portalen eller API för att omvandla ditt konto till ZRS. Om du vill konvertera ditt kontos replikering till ZRS [zonen-redundant lagring (ZRS)](storage-redundancy-zrs.md) mer information.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Finns det några kostnader för att ändra replikeringsstrategi för mitt konto?
Det beror på din sökväg till konvertering. Sorteringen från billigaste på dyraste redundans-erbjudandet och besvarar LRS, ZRS, GRS och RA-GRS. Till exempel kommer *från* LRS till något tillkommer ytterligare kostnader eftersom du kommer att en mer avancerad redundansnivå. Gå *till* GRS eller RA-GRS påförs en avgift för utgående bandbredd eftersom dina data (på din primära region) replikeras till din fjärranslutna sekundär region. Detta är en enstaka dig vid installationen. När data kopieras, finns det inga ytterligare avgifter för konvertering. Du debiteras bara för att replikera alla nya eller uppdateringar av befintliga data. Mer information om kostnader för bandbredd finns [prissättning för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Om du ändrar från GRS till LRS ingår utan extra kostnad, men din replikerade data tas bort från den sekundära platsen.

## <a name="see-also"></a>Se också

- [Lokalt redundant lagring (LRS): Dataredundans med låg kostnad för Azure Storage](storage-redundancy-lrs.md)
- [Zonredundant lagring (ZRS): Azure Storage-program med hög tillgänglighet](storage-redundancy-zrs.md)
- [GEO-redundant lagring (GRS): Tvärregional replikering för Azure Storage](storage-redundancy-grs.md)
- [Skalbarhets- och prestandamål i Azure Storage](storage-scalability-targets.md)
- [Utforma högtillgängliga program med hjälp av RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundans alternativ och läsåtkomst geografiskt redundant lagring ](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper - Azure Storage: En högtillgänglig molnlagringstjänst med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
