---
title: GEO-redundant lagring (GRS) för tvärregional tillförlitlighet i Azure Storage | Microsoft Docs
description: GEO-redundant lagring (GRS) replikerar data mellan två regioner som ligger hundratals mil ifrån varandra. GRS skyddar mot maskinvarufel i datacenter samt nationella katastrofer.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 85d69db2f94e4bddf1258233c34c64dcf78a3eeb
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219230"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>GEO-redundant lagring (GRS): tvärregional replikering för Azure Storage
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Läsåtkomst till geografiskt redundant lagring
Läsåtkomst till geografiskt redundant lagring (RA-GRS) maximerar tillgängligheten för ditt lagringskonto. RA-GRS tillhandahåller skrivskyddad åtkomst till data i den sekundära platsen, förutom geo-replikering mellan två regioner.

När du aktiverar skrivskyddad åtkomst till dina data i den sekundära regionen kan är dina data tillgängliga på en sekundär slutpunkt och på den primära slutpunkten för ditt lagringskonto. Den sekundära slutpunkten liknar den primära slutpunkten men lägger till suffixet `–secondary` i kontonamnet. Om din primära slutpunkt för Blob-tjänsten är till exempel `myaccount.blob.core.windows.net`, och sedan på den sekundära slutpunkten är `myaccount-secondary.blob.core.windows.net`. Åtkomstnycklarna för ditt lagringskonto är samma för både primära och sekundära slutpunkter.

Några saker att tänka på när du använder RA-GRS:

* Programmet måste hantera vilken slutpunkt som den interagerar med när du använder RA-GRS.
* Eftersom asynkron replikering innebär en fördröjning, ändringar som ännu inte har replikerats till den sekundära regionen kan gå förlorade om data inte kan återställas från den primära regionen.
* Du kan kontrollera den senaste synkronisering för storage-kontot. Senaste synkroniseringstid är ett GMT datum/tid-värde. Alla primära skrivningar innan den senaste synkronisering har skrivits till den sekundära platsen, vilket innebär att de blir tillgängliga för att läsa från den sekundära platsen. Primär skriver efter den senaste synkronisering kanske eller kanske inte tillgänglig för läsningar ännu. Du kan fråga efter detta värde med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), eller från en Azure Storage-klientbibliotek.
* Om Microsoft initierar redundans till den sekundära regionen, du kommer har läs- och skrivåtkomst till dessa data efter växlingen har slutförts. Mer information finns i [vägledning om Haveriberedskap](storage-disaster-recovery-guidance.md).
* Information om hur du växlar till den sekundära regionen finns [vad du gör om ett avbrott i Azure Storage inträffar](storage-disaster-recovery-guidance.md).
* RA-GRS är avsedd för hög tillgänglighet. Skalbarhetsguide, granska de [checklista för prestanda](storage-performance-checklist.md).
* Förslag på hur du utformar för hög tillgänglighet med RA-GRS finns i [utforma högtillgängliga program med hjälp av RA-GRS-lagring](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Vad är RPO och RTO med GRS?
**Mål för återställningspunkt (RPO):** i GRS och RA-GRS-lagringen tjänst asynkront geo-replikeras data från primärt till den sekundära platsen. I händelse av en regional större katastrof på den primära regionen utför Microsoft en växling till den sekundära regionen. Om en redundans sker gå ändringar som ännu inte har geo-replikerade förlorade. Hur många minuter av eventuella data som har förlorat kallas rpo-MÅLET. Återställningspunktmålet anger punkten i tiden som data kan återställas. Azure Storage vanligtvis har ett Återställningspunktmål på mindre än 15 minuter, även om det finns för närvarande inga serviceavtal för hur länge geo-replikering tar.

**Återställningstid (RTO):** RTO är ett mått på hur lång tid det tar att utföra redundansväxlingen och hämta lagringskontot online igen. Tid för att utföra redundansväxlingen innehåller följande åtgärder:

   * Tiden Microsoft kräver för att avgöra om data kan återställas när den primära platsen eller om det krävs en redundansväxling
   * Tid för att utföra redundans för storage-konto genom att ändra de primära DNS-posterna så att den pekar till den sekundära platsen

Microsoft ansvarar för att bevara dina data på allvar. Om det finns några risken för att återställa data i den primära regionen, Microsoft fördröjer växling vid fel och fokuserar på att återställa dina data. 

## <a name="paired-regions"></a>Länkade regioner 
När du skapar ett lagringskonto, väljer du den primära regionen för kontot. Den kopplade sekundära regionen fastställs baserat på den primära regionen och kan inte ändras. Uppdaterad information om regioner som stöds av Azure finns i [företag affärskontinuitet och haveriberedskap recovery (BCDR): parade Azure-regioner](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Se också
- [Azure Storage-replikering](storage-redundancy.md)
- [Lokalt redundant lagring (LRS): låg kostnad dataredundans för Azure Storage](storage-redundancy-lrs.md)
- [Zonredundant lagring (ZRS): Azure Storage-program med hög tillgänglighet](storage-redundancy-zrs.md)