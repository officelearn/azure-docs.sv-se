---
title: Replikering i Azure Storage | Microsoft Docs
description: "Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Replikeringsalternativ är lokalt redundant lagring (LRS), zonredundant lagring (ZRS), geo-redundant lagring (GRS) och geo-redundant lagring med läsbehörighet (RA-GRS)."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 1514750b13f5108c480d5dd234132d35dc82fdd2
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="azure-storage-replication"></a>Azure Storage-replikering

Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Replikering kopierar data så att den är skyddad från tillfälliga maskinvarufel, bevarar dina program drifttid. 

Du kan välja att replikera data inom samma Datacenter mellan datacenter inom samma region eller över regioner. Om dina data replikeras mellan flera Datacenter eller över regioner, skyddas den också från ett oåterkalleligt fel på en enda plats.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

När du skapar ett lagringskonto kan du välja något av följande replikeringsalternativ:

* [Lokalt redundant lagring (LRS)](#locally-redundant-storage)
* [Zonredundant lagring (ZRS)](#zone-redundant-storage)
* [Geo-redundant lagring (GRS)](#geo-redundant-storage)
* [Geo-redundant lagring med läsbehörighet (RA-GRS)](#read-access-geo-redundant-storage)

Geo-redundant lagring med läsbehörighet (RA-GRS) är standardalternativet när du skapar ett lagringskonto.

Följande tabell ger en snabb överblick över skillnaderna mellan LRS-, ZRS-, GRS- och RA-GRS. Följande avsnitt i den här artikeln adress varje typ av replikering i detalj.

| Replikeringsstrategi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data replikeras mellan flera datacenter. |Nej |Ja |Ja |Ja |
| Data kan läsas från en sekundär plats som den primära platsen. |Nej |Nej |Nej |Ja |
| Utformad för att ge ___ hållbarhet objekt under ett visst år. |minst 99.999999999% (11 9's)|minst 99.9999999999% (12 9's)|minst 99.99999999999999% (16 9's)|minst 99.99999999999999% (16 9's)|

Se [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) för prisuppgifter för olika redundansalternativ.

> [!NOTE]
> Premium-lagring stöder endast lokalt redundant lagring (LRS). Information om Premium-lagring finns [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Lokalt redundant lagring
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Zonredundant lagring

Zonredundant lagring (ZRS) (förhandsversion) är utformad för att förenkla utvecklingen av hög tillgänglighet program. ZRS ger hållbarhet för lagringsobjekt minst 99.9999999999% (12 9's) över ett visst år. ZRS replikeras synkront data över flera tillgänglighet zoner. Överväg att ZRS för scenarier som transaktionella program där driftstopp inte är godkända.

ZRS ger kunder möjlighet att läsa och skriva data, även om en zon är tillgänglig eller så är oåterkalleligt. Infogar och uppdateringar av data görs synkront och är starkt konsekvent.   

ZRS är för närvarande tillgängliga för förhandsgranskning i följande regioner med flera regioner kommer snart:

- Östra USA 2 
- Centrala USA 
- Frankrike Central (den här regionen är för närvarande under förhandsgranskning. Se [förhandsgranskning av Microsoft Azure med Azure tillgänglighet zoner är nu öppen i Frankrike](https://azure.microsoft.com/blog/microsoft-azure-preview-with-azure-availability-zones-now-open-in-france) att begära åtkomst.)

### <a name="zrs-classic-accounts"></a>ZRS klassiska konton

Den befintliga ZRS-kapaciteten är nu kallas ZRS klassiska. ZRS klassiska konton är bara tillgängligt för blockblobbar i allmänna V1 storage-konton. 

ZRS klassiska replikerar data asynkront i datacenter inom en till två regioner. En replik kanske inte tillgängligt om inte Microsoft initierar redundans för sekundärt. 

ZRS klassiska konton kan inte konverteras till eller från LRS-, GRS- eller RA-GRS. ZRS klassiska konton stöder inte heller mått eller loggning.   

När ZRS är allmänt tillgänglig i en region kan du inte längre kommer att kunna skapa ett ZRS klassiska konto från portalen i regionen, men du kan skapa en på annat sätt.  
En process för automatisk migrering från den klassiska ZRS att ZRS anges i framtiden.

Du kan migrera ZRS kontodata manuellt till eller från ett LRS-, ZRS klassiska, GRS eller RAGRS-konto. Du kan utföra den här manuell migrering med hjälp av AzCopy, Azure Lagringsutforskaren, Azure PowerShell, Azure CLI eller ett Azure Storage-klientbibliotek.

> [!NOTE]
> ZRS klassiska konton planeras för nödvändiga migrering 31 mars 2021 och utfasningen. Microsoft skickar mer information till den klassiska ZRS kunder innan utfasningen.

Andra frågor behandlas i den [vanliga frågor och svar](#frequently-asked-questions) avsnitt. 

## <a name="geo-redundant-storage"></a>Geografiskt redundant lagring.
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Geo-redundant lagring med läsbehörighet
Geo-redundant lagring med läsbehörighet (RA-GRS) maximerar tillgänglighet för ditt lagringskonto. RA-GRS tillhandahåller skrivskyddad åtkomst till data i den sekundära platsen, förutom geo-replikering mellan två regioner.

När du aktiverar skrivskyddad åtkomst till dina data i den sekundära regionen är dina data tillgängliga på en sekundär slutpunkt samt på primära slutpunkten för ditt lagringskonto. Sekundär slutpunkt liknar primära slutpunkten men lägger till suffixet `–secondary` i kontonamnet. Om din primära slutpunkt för Blob-tjänsten är till exempel `myaccount.blob.core.windows.net`, sekundära slutpunkten är `myaccount-secondary.blob.core.windows.net`. Snabbtangenter för ditt lagringskonto är samma för både de primära och sekundära slutpunkterna.

Vissa saker du bör tänka på när du använder RA-GRS:

* Programmet måste hantera vilken slutpunkt den interagerar med när du använder RA-GRS.
* Eftersom asynkron replikering innefattar en fördröjning, kan ändringar som ännu inte har replikerats till den sekundära regionen gå förlorade om data inte kan återställas från den primära regionen, till exempel händelse regionala katastrofåterställning.
* Om Microsoft initierar redundans till den sekundära regionen kan du kommer har läs- och skrivbehörighet till dessa data efter växlingen har slutförts. Mer information finns i [Disaster Recovery vägledning](../storage-disaster-recovery-guidance.md).
* RA-GRS är avsedd för hög tillgänglighet. Riktlinjer för skalbarhet, granska den [prestanda checklista](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Hur kan jag ändra typen geo-replikering med storage-konto?

   Du kan ändra typen av geo-replikering för ditt lagringskonto med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), eller ett Azure Storage-klientbibliotek.

   > [!NOTE]
   > ZRS-konton får inte vara konverterade LRS eller GRS. Ett befintligt LRS eller GRS-konto kan inte konverteras till ett ZRS-konto.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. Stöder ändrar resultatet min storage-konto i stillestånd replikering?

   Nej, ändra replikeringstyp för ditt lagringskonto resulterar inte i stillestånd.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. Finns det ytterligare kostnader att ändra replikeringstyp för mitt lagringskonto?

   Ja. Om du ändrar från LRS till GRS eller RA-GRS för ditt lagringskonto innebära en extra kostnad för utgående trafik som ingår i kopiera befintliga data från primär plats till den sekundära platsen. Efter den första informationen kopieras, finns det inga ytterligare ytterligare utgång avgifter för geo-replikering från primär till sekundär plats. Mer information om bandbredd avgifter finns [priser för Azure Storage sidan](https://azure.microsoft.com/pricing/details/storage/blobs/).

   Om du ändrar från GRS till LRS finns utan extra kostnad, men data tas bort från den sekundära platsen.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Hur kan RA-GRS hjälpa mig?

   GRS storage tillhandahåller replikering av data från en primär till en sekundär region hundratals mil bort från den primära regionen är. Med GRS dina data skyddas även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas. RA-GRS lagringsmedia erbjuder GRS replikering och lägger till möjligheten att läsa data från den sekundära platsen. Förslag på hur du utformar för hög tillgänglighet finns [skapar hög tillgängliga program använder RA-GRS lagring](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Finns det ett sätt att ta reda på hur lång tid det tar för att replikera data från primärt till den sekundära regionen?

   Om du använder RA-GRS-lagring kan kontrollera du tid för senaste synkronisering för ditt lagringskonto. Tid för senaste synkronisering är ett GMT datum/tid-värde. Alla primära skrivningar innan tid för senaste synkronisering har skrivits till den sekundära platsen, vilket innebär att de är tillgängliga för att läsa från den sekundära platsen. Primär skriver när tid för senaste synkronisering kanske eller kanske inte tillgänglig för läsningar ännu. Du kan fråga efter detta värde med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), eller från en Azure Storage-klientbibliotek.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. Om det finns ett avbrott i den primära regionen, hur jag växla till den sekundära regionen?

   Mer information finns i [vad du ska göra om ett Azure Storage-avbrott inträffar](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Vad är Återställningspunktmål och Återställningstidsmål med GRS?

   **Återställningspunktmål för återställningspunkt (RPO):** i GRS och RA-GRS-lagringen tjänsten asynkront geo replikerar data från primär till sekundär plats. Vid en större regionala katastrof på den primära regionen utför Microsoft en växling till den sekundära regionen. Om en redundansväxling sker kan ändringar som ännu inte har georeplikerad gå förlorade. Antal minuter potentiella data förlorade kallas Återställningspunktmålet och det anger punkten i tiden som data kan återställas. Azure Storage normalt har en Återställningspunktmålet för mindre än 15 minuter, även om det finns för närvarande inga SLA på hur länge geo-replikering tar.

   **Återställning tid mål för Återställningstid:** Återställningstidsmålet är ett mått på hur lång tid det tar att utföra en redundansväxling och hämta storage-konto online igen. Tid för att utföra växling vid fel innehåller följande åtgärder:

   * Tiden Microsoft kräver för att avgöra om data kan återställas när den primära platsen eller om det krävs en växling vid fel.
   * Tid att genomföra redundans för lagringskontot genom att ändra de primära DNS-posterna så att den pekar till den sekundära platsen.

   Microsoft ansvarar för att bevara data allvarligt. Om det finns några risken för att återställa data i den primära regionen, Microsoft fördröjning för växling vid fel och fokusera på att återställa dina data. En framtida version av tjänsten kan du utlösa redundansväxling på en kontonivå så att du kan styra Återställningstidsmålet själv.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. Vilka Azure Storage-objekt stöder ZRS? 
Blockblobbar, sidblobbar (förutom dessa stödjande VM-diskar), tabeller, filer och köer. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. Innehåller ZRS också geo-replikering? 
ZRS stöder för närvarande inte geo-replikering. Om din situation kräver replikering mellan region för katastrofåterställning, använder du ett GRS eller RA-GRS-lagringskonto.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. Vad händer när en eller flera ZRS zoner kraschar? 
När den första zonen skulle krascha, fortsätter ZRS att skriva repliker av data mellan de två återstående zonerna i region. Om en zon för andra kraschar är läs- och skrivbehörighet inte tillgänglig förrän minst två zoner är tillgänglig igen. 

## <a name="next-steps"></a>Nästa steg
* [Utforma högtillgänglig program som använder RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
* [Prissättning för Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Om Azure storage-konton](../storage-create-storage-account.md)
* [Skalbarhets- och prestandamål i Azure Storage](storage-scalability-targets.md)
* [Microsoft Azure Storage redundans alternativ och läsbehörighet geo-redundant lagring](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper - Azure Storage: En högtillgänglig lagring molntjänst med stark konsekvens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
