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
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
GEO-redundant lagring (GRS) är utformad att ge minst 99.99999999999999% (16 9's) hållbarhet objekt under ett visst år genom att replikera data till en sekundär region hundratals mil bort från den primära regionen är. Om ditt lagringskonto har GRS aktiverat, sedan dina data skyddas även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas.

Om du väljer för GRS har två relaterade alternativ att välja mellan:

* GRS replikeras dina data till en annan datacenter i en sekundär region men som data kan vara skrivskyddad om Microsoft initierar en redundansväxling från primär till sekundär region. 
* Geo-redundant lagring med läsbehörighet (RA-GRS) baseras på GRS. RA-GRS replikeras dina data till en annan datacenter i en sekundär region och ger dig möjlighet att läsa från den sekundära regionen. Du kan läsa från sekundärt oavsett om Microsoft initierar en redundansväxling från primär till sekundär med RA-GRS. 

Alla data för ett lagringskonto med GRS eller RA-GRS aktiverad replikeras först med lokalt redundant lagring (LRS). En uppdatering är först allokeras till den primära platsen som replikeras med hjälp av LRS. Uppdateringen sedan replikeras asynkront till den sekundära regionen med GRS. När data skrivs till den sekundära platsen replikeras den också inom platsen med hjälp av LRS. 

Både de primära och sekundära regionerna hantera repliker i separata feldomäner och uppgradera domäner inom en lagringsenhet för skalan. Skala lagringsenheten är den grundläggande replikering enheten inom datacentret. Replikering på den här nivån tillhandahålls av LRS; Mer information finns i [lokalt redundant lagring (LRS): prisvärda dataredundans för Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Tänk dessa punkter när replikeringsalternativ ska användas:

* Zonredundant lagring (ZRS) ger hög tillgänglighet med synkron replikering och kan vara ett bättre alternativ för vissa scenarier än GRS eller RA-GRS. Mer information om ZRS finns [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Eftersom asynkron replikering innefattar en fördröjning, händelse regionala katastrofåterställning är det möjligt att ändringar som ännu inte har replikerats till den sekundära regionen kommer att förloras om data inte kan återställas från den primära regionen.
* Med GRS finns repliken inte om inte Microsoft initierar redundans till den sekundära regionen. Om Microsoft initierat en växling till den sekundära regionen kan du kommer har läs- och skrivbehörighet till dessa data efter växlingen har slutförts. Mer information finns [Disaster Recovery vägledning](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Om ditt program behöver läsa från den sekundära regionen, aktivera RA-GRS.

## <a name="read-access-geo-redundant-storage"></a>Geo-redundant lagring med läsbehörighet

Geo-redundant lagring med läsbehörighet (RA-GRS) maximerar tillgänglighet för ditt lagringskonto. RA-GRS tillhandahåller skrivskyddad åtkomst till data i den sekundära platsen, förutom geo-replikering mellan två regioner.

När du aktiverar skrivskyddad åtkomst till dina data i den sekundära regionen är dina data tillgängliga på en sekundär slutpunkt samt på primära slutpunkten för ditt lagringskonto. Sekundär slutpunkt liknar primära slutpunkten men lägger till suffixet `–secondary` i kontonamnet. Om din primära slutpunkt för Blob-tjänsten är till exempel `myaccount.blob.core.windows.net`, sekundära slutpunkten är `myaccount-secondary.blob.core.windows.net`. Snabbtangenter för ditt lagringskonto är samma för både de primära och sekundära slutpunkterna.

Vissa saker du bör tänka på när du använder RA-GRS:

* Programmet måste hantera vilken slutpunkt den interagerar med när du använder RA-GRS.
* Eftersom asynkron replikering innefattar en fördröjning, kan ändringar som ännu inte har replikerats till den sekundära regionen gå förlorade om data inte kan återställas från den primära regionen, till exempel händelse regionala katastrofåterställning.
* Du kan kontrollera tid för senaste synkronisering för ditt lagringskonto. Tid för senaste synkronisering är ett GMT datum/tid-värde. Alla primära skrivningar innan tid för senaste synkronisering har skrivits till den sekundära platsen, vilket innebär att de är tillgängliga för att läsa från den sekundära platsen. Primär skriver när tid för senaste synkronisering kanske eller kanske inte tillgänglig för läsningar ännu. Du kan fråga efter detta värde med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), eller från en Azure Storage-klientbibliotek.
* Om Microsoft initierar redundans till den sekundära regionen kan du kommer har läs- och skrivbehörighet till dessa data efter växlingen har slutförts. Mer information finns i [Disaster Recovery vägledning](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Mer information om hur du växlar till den sekundära regionen finns [vad du ska göra om ett Azure Storage-avbrott inträffar](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS är avsedd för hög tillgänglighet. Riktlinjer för skalbarhet, granska den [prestanda checklista](../articles/storage/common/storage-performance-checklist.md).
* Förslag på hur du utformar för hög tillgänglighet med RA-GRS finns [skapar hög tillgängliga program använder RA-GRS lagring](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Vad är Återställningspunktmål och Återställningstidsmål med GRS?
**Mål för återställningspunkt (RPO):** i GRS och RA-GRS-lagringen tjänsten asynkront geo replikerar data från primär till sekundär plats. Vid en större regionala katastrof på den primära regionen utför Microsoft en växling till den sekundära regionen. Om en redundansväxling sker kan ändringar som ännu inte har georeplikerad gå förlorade. Antal minuter potentiella data förlorade kallas Återställningspunktmålet och det anger punkten i tiden som data kan återställas. Azure Storage normalt har en Återställningspunktmålet för mindre än 15 minuter, även om det finns för närvarande inga SLA på hur länge geo-replikering tar.

**Återställning tid mål för Återställningstid:** Återställningstidsmålet är ett mått på hur lång tid det tar att utföra en redundansväxling och hämta storage-konto online igen. Tid för att utföra växling vid fel innehåller följande åtgärder:

   * Tiden Microsoft kräver för att avgöra om data kan återställas när den primära platsen eller om det krävs en växling vid fel.
   * Tid att genomföra redundans för lagringskontot genom att ändra de primära DNS-posterna så att den pekar till den sekundära platsen.

   Microsoft ansvarar för att bevara data allvarligt. Om det finns några risken för att återställa data i den primära regionen, Microsoft fördröjning för växling vid fel och fokusera på att återställa dina data. En framtida version av tjänsten kan du utlösa redundansväxling på en kontonivå så att du kan styra Återställningstidsmålet själv.

## <a name="paired-regions"></a>Parad regioner 

När du skapar ett lagringskonto, väljer du den primära regionen för kontot. Den sekundära regionen parad bestäms baserat på den primära regionen och kan inte ändras. Uppdaterad information om regioner som stöds av Azure finns [Business affärskontinuitet och haveriberedskap återställning (BCDR): parad Azure-regioner](../articles/best-practices-availability-paired-regions.md).
