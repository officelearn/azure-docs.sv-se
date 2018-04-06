---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f6d437e4ad0e05d55c408ad8f9defe5385b52050
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
Zonen Redundant lagring (ZRS) replikerar synkront data mellan tre lagringskluster i en region. Varje lagringsklustret separeras fysiskt från de andra och finns i en egen tillgänglighet zon (AZ). Varje zon för tillgänglighet och ZRS-kluster i denna är självständiga, med separata verktyg och nätverksfunktioner.

Lagra data i ett konto för ZRS garanterar att du kommer att använda och hantera dina data i händelse av att en zon blir otillgänglig. ZRS ger utmärkt prestanda och mycket låg latens.

Mer information om tillgänglighet zoner finns [tillgänglighet zoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="zrs-for-high-availability"></a>ZRS för hög tillgänglighet 

Överväg att ZRS för scenarier som kräver stark konsekvens, starkt hållbarhet och hög tillgänglighet, även om ett avbrott eller en naturkatastrof återger ett datacenter inte tillgänglig. ZRS ger hållbarhet för lagringsobjekt minst 99.9999999999% (12 9's) över ett visst år.

ZRS stöder för närvarande standard, generell v2 (GPv2) kontotyper. ZRS är tillgängligt för blockblobbar, sidblobbar-disk, filer, tabeller och köer. 

ZRS är allmänt tillgänglig i följande områden:

- Östra USA 2
- Centrala USA
- Europa, norra
- Västeuropa
- Centrala Frankrike
- Sydostasien

Microsoft fortsätter att aktivera ZRS i andra Azure-regioner.

### <a name="what-happens-when-a-zone-becomes-unavailable"></a>Vad händer när en zon blir otillgänglig?

Dina data förblir flexibel om en zon blir otillgänglig. Microsoft rekommenderar att du fortsätter att följa metoder för att tillfälligt fel hantering, till exempel implementera principer för återförsök med exponentiell inte. När en zon inte är tillgänglig åtar uppdateringar, till exempel DNS-repointing i Azure. Dessa uppdateringar kan påverka ditt program om du ansluter till dina data innan de har slutförts.

ZRS kan inte skydda dina data mot en regionala katastrof där flera zoner permanent påverkas. ZRS ger i stället återhämtning för dina data när det gäller temporala inte finns. För skydd mot regionala katastrofer Microsoft rekommenderar att du använder [Geo-redundant lagring (GRS): mellan regionala replikering för Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS klassisk: Ett äldre alternativ för redundans för block-BLOB
> [!NOTE]
> ZRS klassiska konton planeras för nödvändiga migrering 31 mars 2021 och utfasningen. Microsoft skickar mer information till den klassiska ZRS kunder innan utfasningen. Microsoft planerar att tillhandahålla en process för automatisk migrering från den klassiska ZRS till ZRS i framtiden.

ZRS klassiska är bara tillgängligt för blockblobbar i allmänna V1 (GPv1) storage-konton. ZRS klassiska replikerar data asynkront mellan datacenter inom en till två regioner. En replik kanske inte är tillgänglig om inte Microsoft initierar sekundär redundans. Ett ZRS Classic-konto går inte att konvertera till eller från LRS eller GRS, och har inte kapacitet för mått eller loggning.

ZRS klassiska konton kan inte konverteras till eller från LRS-, GRS- eller RA-GRS. ZRS klassiska konton stöder inte heller mått eller loggning.

När ZRS är allmänt tillgänglig i en region kan du inte längre kommer att kunna skapa ett ZRS klassiska konto från portalen i den regionen, men du kan skapa en på annat sätt som PowerShell, CLI och så vidare.

Om du vill migrera ZRS kontodata manuellt till eller från ett LRS-, ZRS klassiska, GRS eller RA-GRS-konto, Använd AzCopy, Azure Lagringsutforskaren, Azure PowerShell eller Azure CLI. Du kan också skapa din egen migreringslösning med en Azure Storage-klientbibliotek för.