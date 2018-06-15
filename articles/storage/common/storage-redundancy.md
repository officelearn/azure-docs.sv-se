---
title: Replikering i Azure Storage | Microsoft Docs
description: Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Replikeringsalternativ är lokalt redundant lagring (LRS), zonredundant lagring (ZRS), geo-redundant lagring (GRS) och geo-redundant lagring med läsbehörighet (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 6c2c6979d56eb19ff2ba4fb647c7c51e52e51ac6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076222"
---
# <a name="azure-storage-replication"></a>Azure Storage-replikering

Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage-replikering kopierar data så att den är skyddad från planerade och oplanerade händelser från tillfälliga maskinvarufel, nätverk eller strömavbrott, massiv naturkatastrof och så vidare. Du kan välja att replikera data inom samma Datacenter mellan zonal datacenter inom samma region och i olika regioner.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

## <a name="choosing-a-replication-option"></a>Om du väljer ett replikeringsalternativ för

När du skapar ett lagringskonto kan du välja något av följande replikeringsalternativ:

* [Lokalt redundant lagring (LRS)](storage-redundancy-lrs.md)
* [Zonredundant lagring (ZRS)](storage-redundancy-zrs.md)
* [Geo-redundant lagring (GRS)](storage-redundancy-grs.md)
* [Geo-redundant lagring med läsbehörighet (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Följande tabell ger en snabb överblick över omfånget för hållbarhet och tillgänglighet varje replikeringsstrategi ger dig för en viss typ av händelse (eller händelse av liknande effekt).

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Noden inte finns inom ett datacenter                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ett helt datacenter (zonal eller icke-zonal) blir otillgänglig                                           | Nej                              | Ja                              | Ja                                  | Ja                                  |
| En region hela avbrott                                                                                     | Nej                              | Nej                               | Ja                                  | Ja                                  |
| Läsbehörighet till dina data (i en fjärransluten, georeplikerad region) vid region hela otillgänglighet | Nej                              | Nej                               | Nej                                   | Ja                                  |
| Utformad för att tillhandahålla ___ hållbarhet objekt under ett visst år                                          | minst 99.999999999% (11 9's) | minst 99.9999999999% (12 9's) | minst 99.99999999999999% (16 9's) | minst 99.99999999999999% (16 9's) |
| Kontot lagringstyper som stöds                                                                   | GPv1, GPv2, Blob                | GPv2                             | GPv1, GPv2, Blob                     | GPv1, GPv2, Blob                     |

Se [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) för prisuppgifter på olika redundansalternativ.

> [!NOTE]
> Premium-lagring stöder endast lokalt redundant lagring (LRS). Information om Premium-lagring finns [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Ändra replikeringsstrategi
Vi kan du ändra replikeringsstrategi för ditt lagringskonto med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), eller en av många [ Azures klientbibliotek](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Ändra replikeringstyp för ditt lagringskonto resulterar inte i stillestånd.

   > [!NOTE]
   > För närvarande kan använda du inte portalen eller API för att konvertera ditt konto till ZRS. Om du vill konvertera ditt konto replikering till ZRS [zonen-redundant lagring (ZRS)](storage-redundancy-zrs.md) mer information.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Finns det några kostnader för att ändra replikeringsstrategi mitt konto?
Det beror på din konvertering sökväg. Beställer från billigaste till de dyraste redundans erbjudandet har vi LRS-, ZRS-, GRS- och RA-GRS. Gå till exempel *från* LRS till något medför ytterligare kostnader eftersom du kommer att en mer avancerad nivå för redundans. Gå *till* GRS eller RA-GRS påförs en avgift för utgående bandbredd eftersom dina data (i din primära region) replikeras till fjärranslutna sekundär region. Det här är en enstaka kostnad vid installationen. När data kopieras, finns det inga ytterligare avgifter för konvertering. Du kommer bara att debiteras för att replikera alla nya eller uppdateringar av befintliga data. Mer information om bandbredd avgifter finns [priser för Azure Storage sidan](https://azure.microsoft.com/pricing/details/storage/blobs/).

Om du ändrar från GRS till LRS finns utan extra kostnad, men replikerade data tas bort från den sekundära platsen.

## <a name="see-also"></a>Se också

- [Lokalt redundant lagring (LRS): prisvärda dataredundans för Azure Storage](storage-redundancy-lrs.md)
- [Zonredundant lagring (ZRS): hög tillgänglighet Azure Storage-program](storage-redundancy-zrs.md)
- [GEO-redundant lagring (GRS): mellan regionala replikering för Azure Storage](storage-redundancy-grs.md)
- [Skalbarhets- och prestandamål i Azure Storage](storage-scalability-targets.md)
- [Utforma högtillgänglig program som använder RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundans alternativ och läsbehörighet geo-redundant lagring ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper - Azure Storage: En högtillgänglig lagring molntjänst med stark konsekvens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
