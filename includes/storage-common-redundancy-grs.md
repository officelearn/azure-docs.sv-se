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
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399965"
---
GEO-redundant lagring (GRS) är utformad för att tillhandahålla minst 99,99999999999999% (16 9) objektshållbarhet under ett givet år genom att replikera dina data till en sekundär region som ligger hundratals mil bort från den primära regionen. Om ditt lagringskonto har GRS aktiverat, sedan dina data är beständiga även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas.

Om du väljer för GRS har två relaterade alternativ att välja mellan:

* GRS replikerar dina data till ett annat datacenter i en sekundär region utan att data kan vara skrivskyddad om Microsoft initierar en växling från primär till sekundär region. 
* Läsåtkomst till geografiskt redundant lagring (RA-GRS) är baserad på GRS. RA-GRS replikerar data till ett annat datacenter i en sekundär region och ger dig också möjlighet att läsa från den sekundära regionen. Du kan läsa från sekundärt oavsett om Microsoft initierar en växling från primär till sekundär med RA-GRS. 

Alla data för ett lagringskonto med GRS eller RA-GRS aktiveras replikeras först med lokalt redundant lagring (LRS). En uppdatering först är allokerad till den primära platsen som replikeras med LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen som med GRS. När data skrivs till den sekundära platsen, replikeras det också på den platsen med hjälp av LRS. 

Båda primära och sekundära regionerna hantera repliker i separata feldomäner och uppgraderingsdomäner i en lagringsskalningsenhet. Skala lagringsenheten är grundläggande kvarhållningsenheten inom datacentret. Replikering på den här nivån kommer från LRS; Mer information finns i [lokalt redundant lagring (LRS): låg kostnad dataredundans för Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Ha de här punkterna i åtanke när du bestämmer vilket replikeringsalternativ du använder:

* Zonredundant lagring (ZRS) ger hög tillgänglighet med synkron replikering och kan vara ett bättre alternativ för vissa scenarier än GRS eller RA-GRS. Mer information om ZRS finns i [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Eftersom asynkron replikering innebär en fördröjning, i händelse av ett regionalt haveri är det möjligt att ändringar som ännu inte har replikerats till den sekundära regionen kommer att försvinna om data inte kan återställas från den primära regionen.
* Med GRS finns repliken inte om inte Microsoft initierar redundans till den sekundära regionen. Om Microsoft påbörja en växling till den sekundära regionen, du har läs- och skrivåtkomst till dessa data efter växlingen har slutförts. Mer information finns i [vägledning om Haveriberedskap](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Om programmet behöver för att läsa från den sekundära regionen, aktivera RA-GRS.

## <a name="read-access-geo-redundant-storage"></a>Läsåtkomst till geografiskt redundant lagring

Läsåtkomst till geografiskt redundant lagring (RA-GRS) maximerar tillgängligheten för ditt lagringskonto. RA-GRS tillhandahåller skrivskyddad åtkomst till data i den sekundära platsen, förutom geo-replikering mellan två regioner.

När du aktiverar skrivskyddad åtkomst till dina data i den sekundära regionen kan är dina data tillgängliga på en sekundär slutpunkt och på den primära slutpunkten för ditt lagringskonto. Den sekundära slutpunkten liknar den primära slutpunkten men lägger till suffixet `–secondary` i kontonamnet. Om din primära slutpunkt för Blob-tjänsten är till exempel `myaccount.blob.core.windows.net`, och sedan på den sekundära slutpunkten är `myaccount-secondary.blob.core.windows.net`. Åtkomstnycklarna för ditt lagringskonto är samma för både primära och sekundära slutpunkter.

Några saker att tänka på när du använder RA-GRS:

* Programmet måste hantera vilken slutpunkt som den interagerar med när du använder RA-GRS.
* Eftersom asynkron replikering innebär en fördröjning, ändringar som ännu inte har replikerats till den sekundära regionen kan gå förlorade om data inte kan återställas från den primära regionen, till exempel i händelse av ett regionalt haveri.
* Du kan kontrollera den senaste synkronisering för storage-kontot. Senaste synkroniseringstid är ett GMT datum/tid-värde. Alla primära skrivningar innan den senaste synkronisering har skrivits till den sekundära platsen, vilket innebär att de blir tillgängliga för att läsa från den sekundära platsen. Primär skriver efter den senaste synkronisering kanske eller kanske inte tillgänglig för läsningar ännu. Du kan fråga efter detta värde med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), eller från en Azure Storage-klientbibliotek.
* Om Microsoft initierar redundans till den sekundära regionen, du har läs- och skrivåtkomst till dessa data efter växlingen har slutförts. Mer information finns i [vägledning om Haveriberedskap](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Information om hur du växlar till den sekundära regionen finns [vad du gör om ett avbrott i Azure Storage inträffar](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS är avsedd för hög tillgänglighet. Skalbarhetsguide, granska de [checklista för prestanda](../articles/storage/common/storage-performance-checklist.md).
* Förslag på hur du utformar för hög tillgänglighet med RA-GRS finns i [utforma högtillgängliga program med hjälp av RA-GRS-lagring](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Vad är RPO och RTO med GRS?
**Mål för återställningspunkt (RPO):** i GRS och RA-GRS-lagringen tjänst asynkront geo-replikeras data från primärt till den sekundära platsen. I händelse av en regional större katastrof på den primära regionen utför Microsoft en växling till den sekundära regionen. Om en redundans sker gå ändringar som inte ännu har geo-replikerade förlorade. Hur många minuter av potentiella data förlorade kallas för rpo-MÅLET, och det visar punkten i tiden som data kan återställas. Azure Storage vanligtvis har ett Återställningspunktmål på mindre än 15 minuter, även om det finns för närvarande inga serviceavtal för hur länge geo-replikering tar.

**Återställningstid (RTO):** RTO är ett mått på hur lång tid det tar att utföra redundansväxlingen och hämta lagringskontot online igen. Tid för att utföra redundansväxlingen innehåller följande åtgärder:

   * Tiden Microsoft kräver för att avgöra om data kan återställas när den primära platsen eller om en redundansväxling är nödvändigt.
   * Tid att utföra redundans för storage-konto genom att ändra de primära DNS-posterna så att den pekar till den sekundära platsen.

   Microsoft ansvarar för att bevara dina data på allvar. Om det finns några risken för att återställa data i den primära regionen, Microsoft fördröjning växling vid fel och fokusera på att återställa dina data. En framtida version av tjänsten kan du utlösa redundans på kontonivå så att du kan styra RTO själv.

## <a name="paired-regions"></a>Länkade regioner 

När du skapar ett lagringskonto, väljer du den primära regionen för kontot. Den kopplade sekundära regionen fastställs baserat på den primära regionen och kan inte ändras. Uppdaterad information om regioner som stöds av Azure finns i [företag affärskontinuitet och haveriberedskap recovery (BCDR): parade Azure-regioner](../articles/best-practices-availability-paired-regions.md).
