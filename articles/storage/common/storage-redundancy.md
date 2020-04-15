---
title: Dataredundans
titleSuffix: Azure Storage
description: Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Redundanskonfigurationer omfattar lokalt redundant lagring (LRS), zonupptraklig lagring (ZRS), geoupptundrad lagring (GRS), geo-åtkomst geo-redundant lagring (RA-GRS), geo-zon-redundant lagring (GZRS) (förhandsversion) och läs-åtkomst geo-zon-redundant lagring (RA-GZRS) (förhandsversion).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 1961797f37a760fe3a31dc8aa3830889965b69b5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379944"
---
# <a name="azure-storage-redundancy"></a>Redundans för Azure Storage

Azure Storage lagrar alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskinvarufel, nätverks- eller strömavbrott och omfattande naturkatastrofer. Redundans säkerställer att ditt lagringskonto uppfyller [servicenivåavtalet (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) även med tanke på fel.

När du bestämmer vilket redundansalternativ som är bäst för ditt scenario bör du överväga avvägningar mellan lägre kostnader och högre tillgänglighet och hållbarhet. De faktorer som hjälper dig att avgöra vilket redundansalternativ du bör välja är:  

- Hur dina data replikeras i den primära regionen
- Om dina data replikeras till en andra plats som är geografiskt avlägsen till den primära regionen, för att skydda mot regionala katastrofer
- Om ditt program kräver läsåtkomst till replikerade data i den sekundära regionen om den primära regionen blir otillgänglig av någon anledning

## <a name="redundancy-in-the-primary-region"></a>Redundans i primärregionen

Data i ett Azure Storage-konto replikeras alltid tre gånger i den primära regionen. Azure Storage erbjuder två alternativ för hur dina data replikeras i den primära regionen:

- **Lokalt redundant lagring (LRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen. LRS är det billigaste replikeringsalternativet, men rekommenderas inte för program som kräver hög tillgänglighet.
- **Zonupptäckt lagring (ZRS)** kopierar dina data synkront över tre Azure-tillgänglighetszoner i den primära regionen. För program som kräver hög tillgänglighet rekommenderar Microsoft att du använder ZRS i den primära regionen och även replikerar till en sekundär region.

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring

Lokalt redundant lagring (LRS) replikerar dina data tre gånger inom en enda fysisk plats i den primära regionen. LRS ger minst 99,99999999% (11 nior) hållbarhet objekt under ett visst år.

LRS är det billigaste redundansalternativet och erbjuder minst hållbarhet jämfört med andra alternativ. LRS skyddar dina data mot serverrack och enhetsfel. Men om en katastrof som brand eller översvämning inträffar i datacentret kan alla repliker av ett lagringskonto som använder LRS gå förlorade eller oåterkalleliga. För att minska den här risken rekommenderar Microsoft att du använder [zonupptundrad lagring](#zone-redundant-storage) (ZRS), [geoupptundrad lagring](#geo-redundant-storage) (GRS) eller [geozons redundant lagring (förhandsversion)](#geo-zone-redundant-storage-preview) (GZRS).

En skrivbegäran till ett lagringskonto som använder LRS sker synkront. Skrivåtgärden returneras först när data har skrivits till alla tre replikerna.

LRS är ett bra val för följande scenarier:

- Om ditt program lagrar data som enkelt kan rekonstrueras om dataförlust inträffar kan du välja LRS.
- Om ditt program är begränsat till att replikera data endast inom ett land eller en region på grund av datastyrningskrav kan du välja LRS. I vissa fall kan de parade regioner där data är geo-replikerade vara i ett annat land eller en region. Mer information om parade regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zonredundant lagring

Zonupptäckt lagring (ZRS) replikerar dina Azure Storage-data synkront över tre Azure-tillgänglighetszoner i den primära regionen. Varje tillgänglighetszon är en separat fysisk plats med oberoende kraft, kylning och nätverk. ZRS erbjuder hållbarhet för Azure Storage-dataobjekt på minst 99,99999999999 % (12 9) under ett visst år.

Med ZRS är dina data fortfarande tillgängliga för både läs- och skrivåtgärder även om en zon blir otillgänglig. Om en zon blir otillgänglig genomför Azure nätverksuppdateringar, till exempel DNS-pekar om. Dessa uppdateringar kan påverka ditt program om du kommer åt data innan uppdateringarna har slutförts. När du utformar program för ZRS följer du metoder för tillfällig felhantering, inklusive implementering av principer för återförsök med exponentiell back-off.

En skrivbegäran till ett lagringskonto som använder ZRS sker synkront. Skrivåtgärden returneras först när data har skrivits till alla repliker i de tre tillgänglighetszonerna.

Microsoft rekommenderar att du använder ZRS i den primära regionen för scenarier som kräver konsekvens, hållbarhet och hög tillgänglighet. ZRS ger utmärkt prestanda, låg latens och återhämtning för dina data om de blir tillfälligt otillgängliga. ZRS i sig kan dock inte skydda dina data mot en regional katastrof där flera zoner påverkas permanent. För skydd mot regionala katastrofer rekommenderar Microsoft att du använder GZRS [(Geo-Zone-redundant storage),](#geo-zone-redundant-storage-preview) som använder ZRS i den primära regionen och även georebarerterar dina data till en sekundär region.

I följande tabell visas vilka typer av lagringskonton som stöder ZRS i vilka regioner:

|    Storage Account-typ    |    Regioner som stöds    |    Tjänster som stöds    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Allmänt ändamål v2<sup>1</sup>    | Sydostasien<br /> Australien, östra<br /> Nordeuropa<br />  Västeuropa<br /> Frankrike, centrala<br /> Japan, östra<br /> Sydafrika North<br /> Storbritannien, södra<br /> USA, centrala<br /> USA, Östra<br /> USA, östra 2<br /> USA, västra 2    |    Blockblobar<br /> Sidblobar<sup>2</sup><br /> Filresurser (standard)<br /> Tabeller<br /> Köer<br /> |
|    BlockBlobStorage<sup>1</sup>    | Västeuropa<br /> USA, Östra    |    Endast blockblobar    |
|    FileStorage (filstora)    | Västeuropa<br /> USA, Östra    |    Endast Azure-filer    |

<sup>1</sup> Arkivnivån stöds för närvarande inte för ZRS-konton.<br />
<sup>2</sup> Lagringskonton som innehåller Azure-hanterade diskar för virtuella datorer använder alltid LRS. Azure ohanterat diskar bör också använda LRS. Det är möjligt att skapa ett lagringskonto för Azure ohanterade diskar som använder GRS, men det rekommenderas inte på grund av potentiella problem med konsekvens över asynkron geo-replikering. Varken hanterade eller ohanterade diskar stöder ZRS eller GZRS. Mer information om hanterade diskar finns i [Priser för Azure-hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

Information om vilka regioner som stöder ZRS finns i **Tjänster support efter region** i Vad är [Azure-tillgänglighetszoner?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundans i en sekundär region

För program som kräver hög tillgänglighet kan du välja att dessutom kopiera data i ditt lagringskonto till en sekundär region som ligger hundratals miles från den primära regionen. Om ditt lagringskonto kopieras till en sekundär region är dina data varaktiga även vid ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återställas.

När du skapar ett lagringskonto väljer du den primära regionen för kontot. Den parade sekundära regionen bestäms baserat på den primära regionen och kan inte ändras. Mer information om regioner som stöds av Azure finns i [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Storage erbjuder två alternativ för att kopiera dina data till en sekundär region:

- **Geo-redundant lagring (GRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med LRS. Sedan kopieras dina data asynkront till en enda fysisk plats i den sekundära regionen.
- **Geo-zon-redundant lagring (GZRS)** (förhandsversion) kopierar dina data synkront över tre Azure-tillgänglighetszoner i den primära regionen med ZRS. Sedan kopieras dina data asynkront till en enda fysisk plats i den sekundära regionen.

Den primära skillnaden mellan GRS och GZRS är hur data replikeras i den primära regionen. Inom den sekundära platsen replikeras data alltid synkront tre gånger med LRS.

Med GRS eller GZRS är data på den sekundära platsen inte tillgängliga för läs- eller skrivåtkomst om det inte finns en redundans till den sekundära regionen. Om du vill ha läsåtkomst till den sekundära platsen konfigurerar du ditt lagringskonto så att det använder geo redundant lagring (RA-GRS) eller geo-zon redundant lagring (LÄSÅTKOMST) eller geo-zon redundant lagring (RA-GZRS). Mer information finns [i Läs åtkomst till data i den sekundära regionen](#read-access-to-data-in-the-secondary-region).

Om den primära regionen blir otillgänglig kan du välja att växla över till den sekundära regionen (förhandsversion). När redundansen har slutförts blir den sekundära regionen den primära regionen och du kan läsa och skriva data igen. Mer information om haveriberedskap och hur du växlar över till den sekundära regionen finns i [Katastrofåterställning och felinformation om kontot (förhandsversion)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Eftersom data replikeras till den sekundära regionen asynkront kan ett fel som påverkar den primära regionen resultera i dataförlust om den primära regionen inte kan återställas. Intervallet mellan de senaste skrivna till den primära regionen och den sista skrivningen till den sekundära regionen kallas återställningspunktmål (RPO). RPO anger den tidpunkt till vilken data kan återställas. Azure Storage har vanligtvis en RPO på mindre än 15 minuter, även om det för närvarande inte finns något serviceavtal om hur lång tid det tar att replikera data till den sekundära regionen.

### <a name="geo-redundant-storage"></a>Geografiskt redundant lagring

Geo-redundant lagring (GRS) kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med LRS. Sedan kopieras dina data asynkront till en enda fysisk plats i en sekundär region som ligger hundratals miles från den primära regionen. GRS erbjuder hållbarhet för Azure Storage-dataobjekt på minst 99,99999999999999999% (16 9) under ett visst år.

En skrivåtgärd har först bekräftats till den primära platsen och replikeras med LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen. När data skrivs till den sekundära platsen replikeras de också på den platsen med LRS.

### <a name="geo-zone-redundant-storage-preview"></a>Geo-zon-redundant lagring (förhandsgranskning)

Geo-zon-redundant lagring (GZRS) (förhandsvisning) kombinerar hög tillgänglighet som tillhandahålls av redundans över tillgänglighetszoner med skydd mot regionala avbrott som tillhandahålls av geo-replikering. Data i ett GZRS-lagringskonto kopieras över tre [Azure-tillgänglighetszoner](../../availability-zones/az-overview.md) i den primära regionen och replikeras också till en sekundär geografisk region för skydd mot regionala katastrofer. Microsoft rekommenderar att du använder GZRS för program som kräver maximal konsekvens, hållbarhet och tillgänglighet, utmärkt prestanda och återhämtningsförmåga för katastrofåterställning.

Med ett GZRS-lagringskonto kan du fortsätta att läsa och skriva data om en tillgänglighetszon blir otillgänglig eller inte kan återupptäckeas. Dessutom är dina data också varaktiga vid ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återställas. GZRS är utformad för att ge minst 99,9999999999999% (16 9) hållbarhet av föremål under ett visst år.

Endast allmänna v2-lagringskonton stöder GZRS och RA-GZRS. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md). GZRS och RA-GZRS stöder blockblobar, sidblobar (förutom VHD-diskar), filer, tabeller och köer.

GZRS och RA-GZRS är för närvarande tillgängliga för förhandsversion i följande regioner:

- Sydostasien
- Nordeuropa
- Västeuropa
- Japan, östra
- Storbritannien, södra
- USA, Östra
- USA, östra 2
- USA, centrala
- USA, västra 2

Microsoft fortsätter att aktivera GZRS och RA-GZRS i ytterligare Azure-regioner. Kontrollera regelbundet om det finns information om regioner som stöds på sidan [Azure Service Updates.](https://azure.microsoft.com/updates/)

Information om förhandsversionspriser finns i förhandsversionen av GZRS för [blobbar,](https://azure.microsoft.com/pricing/details/storage/blobs) [filer,](https://azure.microsoft.com/pricing/details/storage/files/) [köer](https://azure.microsoft.com/pricing/details/storage/queues/)och [tabeller](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft rekommenderar att du inte använder förhandsgranskningsfunktioner för produktionsarbetsbelastningar.

## <a name="read-access-to-data-in-the-secondary-region"></a>Läsåtkomst till data i den sekundära regionen

Geoupptundrad lagring (med GRS eller GZRS) replikerar dina data till en annan fysisk plats i den sekundära regionen för att skydda mot regionala avbrott. Dessa data är dock tillgängliga för skrivskyddad om kunden eller Microsoft initierar en redundans från den primära till sekundära regionen. När du aktiverar läsåtkomst till den sekundära regionen kan dina data läsas om den primära regionen blir otillgänglig. För läsåtkomst till den sekundära regionen aktiverar du geo redundant lagring med läsåtkomst (RA-GRS) eller geo-zon redundant lagring (LÄSÅTKOMST).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Utforma dina program för läsåtkomst till den sekundära

Om ditt lagringskonto är konfigurerat för läsåtkomst till den sekundära regionen kan du utforma dina program så att de sömlöst övergår till att läsa data från den sekundära regionen om den primära regionen blir otillgänglig av någon anledning. Den sekundära regionen är alltid tillgänglig för läsåtkomst, så du kan testa programmet för att se till att det kommer att läsa från den sekundära i händelse av ett avbrott. Mer information om hur du utformar dina program för hög tillgänglighet finns i [Utforma program med hög tillgänglighet med geoupptrorad lagring](storage-designing-ha-apps-with-ragrs.md)med läsåtkomst .

När läsåtkomst till den sekundära är aktiverad kan dina data läsas från den sekundära slutpunkten och från den primära slutpunkten för ditt lagringskonto. Den sekundära slutpunkten lägger till suffixet *– sekundärt* till kontonamnet. Om till exempel din primära slutpunkt `myaccount.blob.core.windows.net`för Blob-lagring `myaccount-secondary.blob.core.windows.net`är , är den sekundära slutpunkten . Kontoåtkomstnycklarna för ditt lagringskonto är desamma för både primära och sekundära slutpunkter.

### <a name="check-the-last-sync-time-property"></a>Kontrollera egenskapen Tidpunkt för senaste synkronisering

Eftersom data replikeras till den sekundära regionen asynkront ligger den sekundära regionen ofta bakom den primära regionen. Om ett fel inträffar i den primära regionen är det troligt att alla skrivningar till primärt ännu inte har replikerats till den sekundära.

För att avgöra vilka skrivåtgärder som har replikerats till den sekundära regionen kan ditt program kontrollera egenskapen **Senaste synkroniseringstid** för ditt lagringskonto. Alla skrivåtgärder som skrivits till den primära regionen före den senaste synkroniseringstiden har replikerats till den sekundära regionen, vilket innebär att de är tillgängliga för att läsas från den sekundära. Alla skrivåtgärder som skrivs till den primära regionen efter den senaste synkroniseringstiden kan ha replikerats till den sekundära regionen, vilket innebär att de kanske inte är tillgängliga för läsåtgärder.

Du kan fråga värdet för egenskapen **Senaste synkroniseringstid** med Azure PowerShell, Azure CLI eller något av Azure Storage-klientbiblioteken. Egenskapen **Last Sync Time** är ett GMT-datum/tidsvärde. Mer information finns [i Kontrollera egenskapen Senaste synkroniseringstid för ett lagringskonto](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Sammanfattning av redundansalternativ

Följande tabell visar hur varaktiga och tillgängliga dina data är i ett visst scenario, beroende på vilken typ av redundans som gäller för ditt lagringskonto:

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (förhandsgranskning)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| En nod i ett datacenter blir otillgänglig                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ett helt datacenter (zon eller icke-zonal) blir otillgängligt                                           | Inga                              | Ja                              | Ja                                  | Ja                                  |
| Ett avbrott i hela regionen inträffar                                                                                     | Inga                              | Inga                               | Ja                                  | Ja                                  |
| Läs åtkomst till data i den sekundära regionen om den primära regionen blir otillgänglig | Inga                              | Inga                               | Ja (med RA-GRS)                                   | Ja (med RA-GZRS)                                 |
| Procenthållbarhet av objekt under ett visst år<sup>1</sup>                                          | minst 99,999999999 % (11 9)99999% (11 9's) | minst 99,9999999999 % (12 9)999999% (12 9's) | minst 99,99999999999999 % (16 9)9999999 % (16 9's) | minst 99,99999999999999 % (16 9)9999999 % (16 9's) |
| Lagringskontotyper som stöds<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2 (på andra sätt)                     |
| SLA för läsning<sup>1</sup>  | Minst 99,9% (99% för cool åtkomstnivå) | Minst 99,9% (99% för cool åtkomstnivå) | Minst 99,9 % (99 % för cool åtkomstnivå) för GRS<br /><br />Minst 99,99% (99,9% för cool åtkomstnivå) för RA-GRS | Minst 99,9 % (99 % för cool åtkomstnivå) för GZRS<br /><br />Minst 99,99% (99,9% för cool åtkomstnivå) för RA-GZRS |
| SLA för skrivarbegäranden<sup>1</sup>  | Minst 99,9% (99% för cool åtkomstnivå) | Minst 99,9% (99% för cool åtkomstnivå) | Minst 99,9% (99% för cool åtkomstnivå) | Minst 99,9% (99% för cool åtkomstnivå) |

<sup>1</sup> Mer information om Azure Storage-garantier för hållbarhet och tillgänglighet finns i [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> Mer information om lagringskontotyper finns i [Översikt över lagringskonto](storage-account-overview.md).

Alla data för alla typer av lagringskonton kopieras enligt redundansalternativet för lagringskontot. Objekt inklusive blockblobar, tilläggsblobar, sidblobar, köer, tabeller och filer kopieras.

Prisinformation för varje redundansalternativ finns i [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium Disk Storage stöder för närvarande endast lokalt redundant lagring (LRS). Block blob storage-konton stöder lokalt redundant lagring (LRS) och zon redundant lagring (ZRS) i vissa regioner.

## <a name="data-integrity"></a>Dataintegritet

Azure Storage verifierar regelbundet integriteten för data som lagras med hjälp av cykliska redundanskontroller (CRC). Om data skada upptäcks repareras den med redundanta data. Azure Storage beräknar också kontrollsummar på all nätverkstrafik för att identifiera skadade datapaket när data lagras eller hämtas.

## <a name="see-also"></a>Se även

- [Kontrollera egenskapen Senaste synkroniseringstid för ett lagringskonto](last-sync-time-get.md)
- [Ändra redundansalternativet för ett lagringskonto](redundancy-migration.md)
- [Designa program med högtillgängliga program med RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
- [Katastrofåterställning och växling av konton (förhandsversion)](storage-disaster-recovery-guidance.md)
