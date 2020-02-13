---
title: Dataredundans
titleSuffix: Azure Storage
description: Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Bland konfigurationerna för redundans ingår lokalt redundant lagring (LRS), zon-redundant lagring (ZRS), Geo-redundant lagring (GRS), Geo-redundant lagring med Läs behörighet (RA-GRS), geo-Zone-redundant lagring (GZRS) (för hands version) och Läs åtkomst Geo-Zone-redundant lagring (RA-GZRS) (för hands version).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 0e612dbecb9f215a90f728afb0f06a65db09764b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162930"
---
# <a name="azure-storage-redundancy"></a>Azure Storage redundans

Azure Storage lagrar alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Redundans garanterar att ditt lagrings konto uppfyller [service nivå avtalet (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) även om det är i händelse av problem.

När du bestämmer vilket alternativ för redundans som passar bäst för ditt scenario bör du fundera över kompromisserna mellan lägre kostnader och högre tillgänglighet och hållbarhet. De faktorer som hjälper dig att avgöra vilket alternativ för redundans som du bör välja bland:  

- Hur dina data replikeras i den primära regionen
- Om dina data replikeras till en andra plats som är geografiskt avlägsen till den primära regionen, för att skydda mot regionala haverier
- Huruvida programmet kräver Läs behörighet till replikerade data i den sekundära regionen om den primära regionen blir otillgänglig av någon anledning

## <a name="redundancy-in-the-primary-region"></a>Redundans i den primära regionen

Data i ett Azure Storage-konto replikeras alltid tre gånger i den primära regionen. Azure Storage erbjuder två alternativ för hur dina data replikeras i den primära regionen:

- **Lokalt Redundant lagring (LRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen. LRS är det minst dyra replikeringsalternativ, men det rekommenderas inte för program som kräver hög tillgänglighet.
- **Zone-redundant lagring (ZRS)** kopierar dina data synkront över tre tillgänglighets zoner i Azure i den primära regionen. För program som kräver hög tillgänglighet rekommenderar Microsoft att du använder ZRS i den primära regionen och även replikerar till en sekundär region.

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring

Lokalt redundant lagring (LRS) replikerar dina data tre gånger inom en enda fysisk plats i den primära regionen. LRS tillhandahåller minst 99,999999999% (11 nio) objekts hållbarhet under ett angivet år.

LRS är det lägsta alternativet för redundans och ger minsta möjliga hållbarhet jämfört med andra alternativ. LRS skyddar dina data mot Server rack och enhets problem. Men om en katastrof, till exempel Fire eller översvämning sker i data centret, kan alla repliker av ett lagrings konto med LRS gå förlorade eller oåterkalleligt. För att minska den här risken rekommenderar Microsoft att använda [zon-redundant lagring](#zone-redundant-storage) (ZRS), [Geo-redundant lagring](#geo-redundant-storage) (GRS) eller [geo-Zone-redundant lagring (för hands version)](#geo-zone-redundant-storage-preview) (GZRS).

En Skriv förfrågan till ett lagrings konto som använder LRS sker synkront. Skriv åtgärden returneras bara efter att data har skrivits till alla tre repliker.

LRS är ett bra val för följande scenarier:

- Om ditt program lagrar data som enkelt kan rekonstrueras om data går förlorade, kan du välja LRS.
- Om ditt program är begränsat till att bara replikera data inom ett land eller en region på grund av data styrnings krav, kan du välja LRS. I vissa fall kan de kopplade regionerna som data är geo-replikerade till finnas i ett annat land eller en annan region. Mer information om kopplade regioner finns i Azure- [regioner](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zonredundant lagring

Zone-redundant lagring (ZRS) replikerar dina Azure Storage data synkront över tre tillgänglighets zoner i Azure i den primära regionen. Varje tillgänglighets zon är en separat fysisk plats med oberoende strömförsörjning, kylning och nätverk. ZRS erbjuder hållbarhet för Azure Storage data objekt på minst 99,9999999999% (12 9) under ett angivet år.

Med ZRS är dina data fortfarande tillgängliga för både Läs-och skriv åtgärder även om en zon blir otillgänglig. Om en zon blir otillgänglig gör Azure nätverks uppdateringar, till exempel DNS-ompekare. De här uppdateringarna kan påverka ditt program om du har åtkomst till data innan uppdateringarna har slutförts. När du skapar program för ZRS följer du praxis för hantering av tillfälliga fel, inklusive implementering av principer för återförsök med exponentiell säkerhets kopiering.

En Skriv förfrågan till ett lagrings konto som använder ZRS sker synkront. Skriv åtgärden returneras bara efter att data har skrivits till alla repliker i de tre tillgänglighets zonerna.

Microsoft rekommenderar att du använder ZRS i den primära regionen för scenarier som kräver konsekvens, hållbarhet och hög tillgänglighet. ZRS tillhandahåller utmärkta prestanda, låg latens och återhämtning för dina data om de blir tillfälligt otillgängliga. Men ZRS i sig kanske inte skyddar dina data mot en regional katastrof där flera zoner ständigt påverkas. För skydd mot regionala katastrofer rekommenderar Microsoft att använda [geo-Zone-redundant lagring](#geo-zone-redundant-storage-preview) (GZRS), som använder ZRS i den primära regionen och även geo-replikerar dina data till en sekundär region.

Följande tabell visar vilka typer av lagrings konton som stöder ZRS i vilka regioner:

|    Storage Account-typ    |    Regioner som stöds    |    Tjänster som stöds    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Allmänt-syfte v2<sup>1</sup>    | Sydostasien<br /> Nordeuropa<br />  Västeuropa<br /> Frankrike, centrala<br /> Japan, östra<br /> Storbritannien, södra<br /> USA, centrala<br /> USA, Östra<br /> USA, östra 2<br /> USA, västra 2    |    Blockblobar<br /> Page blobbar<sup>2</sup><br /> Fil resurser (standard)<br /> Tabeller<br /> Köer<br /> |
|    BlockBlobStorage<sup>1</sup>    | Västeuropa<br /> USA, Östra    |    Blockera endast blobbar    |
|    FileStorage    | Västeuropa<br /> USA, Östra    |    Endast Azure Files    |

<sup>1</sup> Arkiv nivån stöds för närvarande inte för ZRS-konton.<br />
<sup>2</sup> Azure-diskar för virtuella datorer, inklusive både hanterade och ohanterade diskar, stöder endast LRS. De stöder inte ZRS eller GZRS. Mer information om hanterade diskar finns i [prissättning för Azure Managed disks](/pricing/details/managed-disks/).

Information om vilka regioner som stöder ZRS finns i **tjänster support efter region** i [Vad är Azure-tillgänglighetszoner?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundans i en sekundär region

För program som kräver hög tillgänglighet kan du också välja att kopiera data i ditt lagrings konto till en sekundär region som är hundratals mil bort från den primära regionen. Om ditt lagrings konto kopieras till en sekundär region, är dina data varaktiga, även om det är ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte går att återvinna.

När du skapar ett lagrings konto väljer du den primära regionen för kontot. Den kopplade sekundära regionen bestäms utifrån den primära regionen och kan inte ändras. Mer information om regioner som stöds av Azure finns i [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Storage erbjuder två alternativ för att kopiera data till en sekundär region:

- **Geo-redundant lagring (GRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med hjälp av LRS. Därefter kopieras data asynkront till en enda fysisk plats i den sekundära regionen.
- **Geo-Zone-redundant lagring (GZRS)** (för hands version) kopierar dina data synkront över tre tillgänglighets zoner i Azure i den primära regionen med hjälp av ZRS. Därefter kopieras data asynkront till en enda fysisk plats i den sekundära regionen.

Den främsta skillnaden mellan GRS och GZRS är hur data replikeras i den primära regionen. På den sekundära platsen replikeras alltid data synkront under tre gånger med hjälp av LRS.

Med GRS eller GZRS är data på den sekundära platsen inte tillgängliga för Läs-eller skriv åtkomst, såvida det inte finns någon redundansväxling till den sekundära regionen. För Läs behörighet till den sekundära platsen konfigurerar du ditt lagrings konto så att det använder Read-Access Geo-redundant lagring (RA-GRS) eller Läs åtkomst till geo-Zone-redundant lagring (RA-GZRS). Mer information finns i [Läs åtkomst till data i den sekundära regionen](#read-access-to-data-in-the-secondary-region).

Om den primära regionen blir otillgänglig kan du välja att redundansväxla till den sekundära regionen (förhands granskning). När redundansväxlingen har slutförts blir den sekundära regionen den primära regionen och du kan läsa och skriva data igen. Mer information om haveri beredskap och om hur du växlar till den sekundära regionen finns i [haveri beredskap och konto redundans (för hands version)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Eftersom data replikeras till den sekundära regionen asynkront kan ett haveri som påverkar den primära regionen leda till data förlust om den primära regionen inte kan återställas. Intervallet mellan de senaste skrivningarna till den primära regionen och den senaste skrivningen till den sekundära regionen kallas för återställnings punkt mål. Återställnings punkten anger tidpunkten då data kan återställas. Azure Storage vanligt vis har en återställning på mindre än 15 minuter, även om det inte finns något service avtal för hur lång tid det tar att replikera data till den sekundära regionen.

### <a name="geo-redundant-storage"></a>Geografiskt redundant lagring

Geo-redundant lagring (GRS) kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med hjälp av LRS. Den kopierar sedan dina data asynkront till en enda fysisk plats i en sekundär region som är hundratals mil bort från den primära regionen. GRS erbjuder hållbarhet för Azure Storage data objekt på minst 99.99999999999999% (16 9) under ett angivet år.

En Skriv åtgärd allokeras först till den primära platsen och replikeras med hjälp av LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen. När data skrivs till den sekundära platsen replikeras de också inom den platsen med hjälp av LRS.

### <a name="geo-zone-redundant-storage-preview"></a>Geo-Zone-redundant lagring (för hands version)

Geo-Zone-redundant lagring (GZRS) (för hands version) kombinerar hög tillgänglighet från redundans över tillgänglighets zoner med skydd från regionala drifts avbrott som tillhandahålls av geo-replikering. Data i ett GZRS lagrings konto kopieras över tre [tillgänglighets zoner i Azure](../../availability-zones/az-overview.md) i den primära regionen och replikeras också till en sekundär geografisk region för skydd mot regionala haverier. Microsoft rekommenderar att du använder GZRS för program som kräver maximal konsekvens, hållbarhet och tillgänglighet, utmärkt prestanda och återhämtning för haveri beredskap.

Med ett GZRS lagrings konto kan du fortsätta att läsa och skriva data om en tillgänglighets zon blir otillgänglig eller inte kan återställas. Dessutom är dina data också varaktiga när det gäller ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återskapas. GZRS har utformats för att ge minst 99.99999999999999% (16 9) objekts hållbarhet under ett angivet år.

Endast General-Purpose v2-lagrings konton stöder GZRS och RA-GZRS. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md). GZRS och RA-GZRS stöder block-blobbar, Page blobbar (utom för VHD-diskar), filer, tabeller och köer.

GZRS och RA-GZRS är för närvarande tillgängliga för förhands granskning i följande regioner:

- Sydostasien
- Nordeuropa
- Västeuropa
- Storbritannien, södra
- USA, Östra
- USA, östra 2
- USA, centrala

Microsoft fortsätter att aktivera GZRS och RA-GZRS i ytterligare Azure-regioner. På sidan [uppdateringar för Azure-tjänsten](https://azure.microsoft.com/updates/) regelbundet hittar du information om regioner som stöds.

Information om priser för för hands versionen finns i GZRS Preview-priser för [blobbar](https://azure.microsoft.com/pricing/details/storage/blobs), [filer](https://azure.microsoft.com/pricing/details/storage/files/), [köer](https://azure.microsoft.com/pricing/details/storage/queues/)och [tabeller](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft rekommenderar att du använder för hands versions funktioner för produktions arbets belastningar.

## <a name="read-access-to-data-in-the-secondary-region"></a>Läs åtkomst till data i den sekundära regionen

Geo-redundant lagring (med GRS eller GZRS) replikerar dina data till en annan fysisk plats i den sekundära regionen för att skydda mot regionala avbrott. Men dessa data är tillgängliga för läsning endast om kunden eller Microsoft initierar en redundansväxling från den primära till den sekundära regionen. När du aktiverar Läs åtkomst till den sekundära regionen är dina data tillgängliga för läsning om den primära regionen blir otillgänglig. Om du vill ha Läs behörighet till den sekundära regionen ska du aktivera Geo-redundant lagring med Läs behörighet (RA-GRS) eller Läs åtkomst till geo-Zone-redundant lagring (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Utforma dina program för Läs behörighet till den sekundära

Om ditt lagrings konto har kon figurer ATS för Läs åtkomst till den sekundära regionen kan du utforma dina program för att sömlöst växla till att läsa data från den sekundära regionen, om den primära regionen inte är tillgänglig av någon anledning. Den sekundära regionen är alltid tillgänglig för Läs behörighet, så du kan testa programmet för att se till att det kommer att läsa från den sekundära händelsen vid ett avbrott. Mer information om hur du utformar dina program för hög tillgänglighet finns i [utforma hög tillgängliga program med hjälp av Geo-redundant lagring med Läs behörighet](storage-designing-ha-apps-with-ragrs.md).

När Läs åtkomst till den sekundära är aktive rad kan dina data läsas från den sekundära slut punkten och från den primära slut punkten för ditt lagrings konto. Den sekundära slut punkten lägger till suffixet *– sekundärt* till konto namnet. Om din primära slut punkt för Blob Storage exempelvis är `myaccount.blob.core.windows.net`, är den sekundära slut punkten `myaccount-secondary.blob.core.windows.net`. Konto åtkomst nycklarna för ditt lagrings konto är desamma för både den primära och den sekundära slut punkten.

### <a name="check-the-last-sync-time-property"></a>Kontrol lera den senaste synkroniseringstid-egenskapen

Eftersom data replikeras till den sekundära regionen asynkront är den sekundära regionen ofta bakom den primära regionen. Om ett haveri inträffar i den primära regionen är det troligt att alla skrivningar till den primära inte ännu har repliker ATS till den sekundära.

För att avgöra vilka Skriv åtgärder som har repliker ATS till den sekundära regionen, kan programmet kontrol lera den **senaste synkroniseringstid** -egenskapen för ditt lagrings konto. Alla Skriv åtgärder som skrivs till den primära regionen före den senaste synkroniseringstid-tiden har repliker ATS till den sekundära regionen, vilket innebär att de är tillgängliga för läsning från den sekundära. Eventuella Skriv åtgärder som skrivs till den primära regionen efter den senaste synkroniseringen kanske inte har repliker ATS till den sekundära regionen, vilket innebär att de inte är tillgängliga för Läs åtgärder.

Du kan fråga värdet för den **senaste synkroniseringstid** -egenskapen med Azure PowerShell, Azure CLI eller något av Azure Storage-klient biblioteken. Den **senaste synkroniseringstid** -egenskapen är ett GMT-datum/tid-värde. Mer information finns i [kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Sammanfattning av alternativ för redundans

I följande tabell visas hur hållbar och tillgänglig data finns i ett specifikt scenario, beroende på vilken typ av redundans som gäller för ditt lagrings konto:

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (för hands version)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| En nod i ett Data Center blir otillgänglig                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ett helt data Center (zonindelade eller icke-zonindelade) blir otillgängligt                                           | Nej                              | Ja                              | Ja                                  | Ja                                  |
| Ett områdes omfattande avbrott inträffar                                                                                     | Nej                              | Nej                               | Ja                                  | Ja                                  |
| Läs åtkomst till data i den sekundära regionen om den primära regionen blir otillgänglig | Nej                              | Nej                               | Ja (med RA-GRS)                                   | Ja (med RA-GZRS)                                 |
| Procentuell hållbarhet för objekt under ett angivet år<sup>1</sup>                                          | minst 99,999999999% (11 9) | minst 99,9999999999% (12 9-) | minst 99.99999999999999% (16 9) | minst 99.99999999999999% (16 9) |
| Lagrings konto typer som stöds<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Tillgänglighets-SLA för Läs begär Anden<sup>1</sup>  | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) för GRS<br /><br />Minst 99,99% (99,9% för låg frekvent åtkomst nivå) för RA-GRS | Minst 99,9% (99% för låg frekvent åtkomst nivå) för GZRS<br /><br />Minst 99,99% (99,9% för låg frekvent åtkomst nivå) för RA-GZRS |
| Tillgänglighets-SLA för Skriv begär Anden<sup>1</sup>  | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) |

<sup>1</sup> information om Azure Storage garantier för hållbarhet och tillgänglighet finns i [service avtalet för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> information om lagrings konto typer finns i [Översikt över lagrings konto](storage-account-overview.md).

Alla data för alla typer av lagrings konton kopieras enligt alternativen för redundans för lagrings kontot. Objekt som innehåller block-blobbar, tillägg av blobbar, sid blobbar, köer, tabeller och filer kopieras.

Pris information för varje alternativ för redundans finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium-Disklagring har för närvarande endast stöd för lokalt redundant lagring (LRS). Blockera Blob Storage-konton har stöd för lokalt redundant lagring (LRS) och zon redundant lagring (ZRS) i vissa regioner.

## <a name="data-integrity"></a>Data integritet

Azure Storage verifierar regelbundet integriteten för data som lagras med hjälp av cyklisk redundans kontroll (CRCs). Om skadade data upptäcks, repare ras den med hjälp av redundanta data. Azure Storage beräknar också kontroll summor på all nätverks trafik för att identifiera skadade data paket när data lagras eller hämtas.

## <a name="see-also"></a>Se även

- [Kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md)
- [Ändra redundans alternativ för ett lagrings konto](redundancy-migration.md)
- [Designa program med hög tillgänglighet med hjälp av RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
- [Haveri beredskap och konto redundans (för hands version)](storage-disaster-recovery-guidance.md)
