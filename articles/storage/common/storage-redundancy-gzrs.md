---
title: Bygga Azure Storage program med hög tillgänglighet med geo-Zone-redundant lagring (förhands granskning) | Microsoft Docs
description: Geo-Zone-redundant lagring (GZRS) Marries hög tillgänglighet för ZRS (Zone-redundant lagring) med skydd från regionala avbrott som tillhandahålls av Geo-redundant lagring (GRS). Data i ett GZRS lagrings konto replikeras mellan Azures tillgänglighets zoner i den primära regionen och replikeras också till en sekundär geografisk region för skydd mot regionala haverier.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 737bad504519a2ec7eee9764593245e0fee28cc3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673058"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Bygga hög tillgängliga Azure Storage program med geo-Zone-redundant lagring (GZRS) (för hands version)

Geo-Zone-redundant lagring (GZRS) (för hands version) Marries hög tillgänglighet för [Zone-redundant lagring (ZRS)](storage-redundancy-zrs.md) med skydd från regionala avbrott som tillhandahålls av [geo-REDUNDANT lagring (GRS)](storage-redundancy-grs.md). Data i ett GZRS lagrings konto replikeras över tre [tillgänglighets zoner i Azure](../../availability-zones/az-overview.md) i den primära regionen och replikeras också till en sekundär geografisk region för skydd mot regionala haverier. Varje Azure-region är kopplad till en annan region inom samma geografi, tillsammans med ett regionalt par. Mer information och undantag finns i [dokumentationen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Med ett GZRS lagrings konto kan du fortsätta att läsa och skriva data om en tillgänglighets zon blir otillgänglig eller inte kan återställas. Dessutom är dina data också varaktiga när det gäller ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återskapas. GZRS har utformats för att ge minst 99.99999999999999% (16 9) objekts hållbarhet under ett angivet år. GZRS erbjuder också samma [skalbarhets mål](storage-scalability-targets.md) som LRS, ZRS, GRS eller RA-GRS. Du kan välja att aktivera Läs åtkomst till data i den sekundära regionen med Read-Access geo-Zone-redundant lagring (RA-GZRS) om dina program behöver kunna läsa data i händelse av en katastrof i den primära regionen.

Microsoft rekommenderar att du använder GZRS för program som kräver konsekvens, hållbarhet, hög tillgänglighet, utmärkt prestanda och återhämtning för diaster-återställning. Om du vill ha ytterligare säkerhet för Läs behörighet till den sekundära regionen i händelse av en regional katastrof aktiverar du RA-GZRS för ditt lagrings konto.

## <a name="about-the-preview"></a>Om för hands versionen

Endast General-Purpose v2-lagrings konton stöder GZRS och RA-GZRS. Mer information om lagrings konto typer finns i [Översikt över Azure Storage-konto](storage-account-overview.md). GZRS och RA-GZRS stöder block-blobbar, sid-blobar som inte är virtuella hård diskar, filer, tabeller och köer.

GZRS och RA-GZRS är för närvarande tillgängliga för förhands granskning i följande regioner:

- Nordeuropa
- Västeuropa
- USA, östra
- USA, östra 2
- USA, centrala

Microsoft fortsätter att aktivera GZRS och RA-GZRS i ytterligare Azure-regioner. På sidan [uppdateringar för Azure-tjänsten](https://azure.microsoft.com/updates/)regelbundet hittar du information om regioner som stöds.

Information om priser för för hands versionen finns i GZRS Preview-priser för [blobbar](https://azure.microsoft.com/pricing/details/storage/blobs), [filer](https://azure.microsoft.com/pricing/details/storage/files/), [köer](https://azure.microsoft.com/pricing/details/storage/queues/)och [tabeller](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft rekommenderar att du använder för hands versions funktioner för produktions arbets belastningar.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Så här fungerar GZRS och RA-GZRS

När data skrivs till ett lagrings konto med GZRS eller RA-GZRS aktiverat, replikeras dessa data först synkront i den primära regionen över tre tillgänglighets zoner. Data replikeras sedan asynkront till en andra region som är hundratals mil bort. När data skrivs till den sekundära regionen replikeras de synkront under tre gånger i regionen med hjälp av [Lokalt Redundant lagring (LRS)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> Asynkron replikering innebär en fördröjning mellan den tid som data skrivs till den primära regionen och när de replikeras till den sekundära regionen. I händelse av en regional katastrof kan ändringar som ännu inte har repliker ATS till den sekundära regionen gå förlorade om data inte kan återställas från den primära regionen.

När du skapar ett lagrings konto anger du hur data i det kontot ska replikeras och du kan också ange den primära regionen för det kontot. Den kopplade sekundära regionen för ett geo-replikerat konto bestäms utifrån den primära regionen och kan inte ändras. Aktuell information om regioner som stöds av Azure finns i [verksamhets kontinuitet och haveri beredskap (BCDR): Azure-kopplade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Information om hur du skapar ett lagrings konto med hjälp av GZRS eller RA-GZRS finns i [skapa ett lagrings konto](storage-quickstart-create-account.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Använd RA-GZRS för hög tillgänglighet

När du aktiverar RA-GZRS för ditt lagrings konto kan dina data läsas från den sekundära slut punkten och från den primära slut punkten för ditt lagrings konto. Den sekundära slut punkten lägger till suffixet *– sekundärt* till konto namnet. Till exempel, om den primära slut punkten för BLOB service är `myaccount.blob.core.windows.net`, är `myaccount-secondary.blob.core.windows.net`den sekundära slut punkten. Åtkomst nycklarna för ditt lagrings konto är desamma för både den primära och den sekundära slut punkten.

Om du vill dra nytta av RA-GZRS i händelse av ett regionalt avbrott måste du utforma ditt program i förväg för att hantera det här scenariot. Ditt program bör läsa från och skriva till den primära slut punkten, men växla till att använda den sekundära slut punkten i händelse av att den primära regionen blir otillgänglig. Vägledning om hur du utformar för hög tillgänglighet med RA-GZRS finns i [utforma hög tillgängliga program med hjälp av ra-GZRS eller RA-GRS](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Eftersom data replikeras till den sekundära regionen asynkront är den sekundära regionen ofta bakom den primära regionen. För att avgöra vilka Skriv åtgärder som har repliker ATS till den sekundära regionen kontrollerar programmet den senaste synkroniseringen för ditt lagrings konto. Alla Skriv åtgärder som skrivs till den primära regionen före den senaste synkroniseringstid-tiden har repliker ATS till den sekundära regionen, vilket innebär att de är tillgängliga för läsning från den sekundära. Eventuella Skriv åtgärder som skrivs till den primära regionen efter den senaste synkroniseringen kanske inte har repliker ATS till den sekundära regionen, vilket innebär att de inte är tillgängliga för Läs åtgärder.

Du kan fråga värdet för den **senaste synkroniseringstid** -egenskapen med Azure PowerShell, Azure CLI eller något av Azure Storage-klient biblioteken. Den **senaste synkroniseringstid** -egenskapen är ett GMT-datum/tid-värde.

För ytterligare vägledning om prestanda och skalbarhet med RA-GZRS, se [Check lista för Microsoft Azure Storage prestanda och skalbarhet](storage-performance-checklist.md).

### <a name="availability-zone-outages"></a>Avbrott i tillgänglighets zon

Om det skulle uppstå ett problem som påverkar en tillgänglighets zon i den primära regionen kan dina program sömlöst fortsätta att läsa från och skriva till ditt lagrings konto med hjälp av de andra tillgänglighets zonerna för den regionen. Microsoft rekommenderar att du fortsätter att följa praxis för hantering av tillfälliga fel när du använder GZRS eller ZRS. Dessa metoder omfattar implementering av principer för återförsök med exponentiell säkerhets kopiering.

När en tillgänglighets zon blir otillgänglig gör Azure nätverks uppdateringar, till exempel DNS-ompekare. De här uppdateringarna kan påverka ditt program om du får åtkomst till data innan uppdateringarna har slutförts.

### <a name="regional-outages"></a>Regionala avbrott

Om ett haveri påverkar hela den primära regionen försöker Microsoft först att återställa den primära regionen. Om återställningen inte är möjlig kommer Microsoft att redundansväxla till den sekundära regionen, så att den sekundära regionen blir den nya primära regionen. Om lagrings kontot har RA-GZRS aktiverat, kan program som har utformats för det här scenariot läsa från den sekundära regionen när de väntar på redundansväxling. Om lagrings kontot inte har RA-GZRS aktiverat kommer program inte att kunna läsa från den sekundära platsen förrän redundansväxlingen är klar.

> [!NOTE]
> GZRS och RA-GZRS är för närvarande endast i för hands version i regionen USA, östra. Kundhanterade konto redundans (för hands version) är ännu inte tillgängligt i östra USA 2, så kunder kan för närvarande inte hantera redundansväxling av konton med GZRS-och RA-GZRS-konton. Under för hands versionen hanterar Microsoft alla redundansväxlings händelser som påverkar GZRS-och RA-GZRS-konton.

Eftersom data replikeras till den sekundära regionen asynkront kan ett haveri som påverkar den primära regionen leda till data förlust om den primära regionen inte kan återställas. Intervallet mellan de senaste skrivningarna till den primära regionen och den senaste skrivningen till den sekundära regionen kallas för återställnings punkt mål. Återställnings punkten anger tidpunkten då data kan återställas. Azure Storage vanligt vis har en återställning på mindre än 15 minuter, även om det inte finns något service avtal för hur lång tid det tar att replikera data till den sekundära regionen.

Återställnings tids målet (RTO) är ett mått på hur lång tid det tar att utföra redundansväxlingen och hämta lagrings kontot online igen. Det här måttet anger tiden som krävs av Azure för att utföra redundansväxlingen genom att ändra de primära DNS-posterna så att de pekar på den sekundära platsen.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Migrera ett lagrings konto till GZRS eller RA-GZRS

Du kan migrera alla befintliga lagrings konton till GZRS eller RA-GZRS. Det är enkelt att migrera från ett befintligt ZRS-konto till GZRS eller RA-GZRS, samtidigt som du migrerar från ett LRS-, GRS-eller RA-GRS-konto. I följande avsnitt beskrivs hur du migrerar i båda fallen.

### <a name="migrating-from-a-zrs-account"></a>Migrera från ett ZRS-konto

Om du vill konvertera ett befintligt ZRS-konto till RA-GZRS, använder du cmdleten [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att ändra SKU: n för kontot. Kom ihåg att ersätta plats hållarnas värden med dina egna värden:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Migrera från ett LRS-, GRS-eller RA-GRS-konto

Det finns två alternativ för att migrera till GZRS eller RA-GZRS från ett konto av LRS, GRS eller RA-GRS:

- Du kan manuellt kopiera eller flytta data till ett nytt GZRS-eller RA-GZRS-konto från ett befintligt konto.
- Du kan begära en direktmigrering.

#### <a name="perform-a-manual-migration"></a>Utföra en manuell migrering

Om du vill att migreringen ska slutföras vid ett visst datum kan du överväga att utföra en manuell migrering. Manuell migrering ger större flexibilitet än direktmigrering. Med en manuell migrering har du kontroll över tidsaspekten.

Om du vill migrera data manuellt från ett befintligt konto till ett GZRS-eller RA-GZRS-konto, använder du ett verktyg som kan kopiera data på ett effektivt sätt. Några exempel är:

- Använd ett verktyg som AzCopy eller ett tillförlitligt verktyg från tredje part. Information om AzCopy finns i [Kom igång med AZCopy](storage-use-azcopy-v10.md).
- Om du är bekant med Hadoop eller HDInsight ansluter du både käll-och mål lagrings kontona till klustret. Därefter parallellisera data kopierings processen med ett verktyg som DistCp.
- Bygg ditt eget verktyg med ett av de Azure Storage klient biblioteken.

#### <a name="perform-a-live-migration"></a>Utför en Direktmigrering

En manuell migrering kan resultera i avbrott i programmet. Om ditt program kräver hög tillgänglighet tillhandahåller Microsoft även ett alternativ för direktmigrering. Direktmigrering är migrering på plats utan driftavbrott.

Under en direktmigrering kan du använda ditt lagrings konto medan dina data migreras mellan käll-och mål lagrings konton. Under direktmigreringen fortsätter ditt konto att uppfylla sitt service avtal för hållbarhet och tillgänglighet. Det finns ingen stillestånds tid eller data förlust som orsakas av direktmigrering.

Endast General-Purpose v2-konton stöder GZRS/RA-GZRS, så innan du skickar en begäran om Direktmigrering till GZRS/RA-GZRS måste du uppgradera ditt konto till General-Purpose v2. Mer information finns i  [Översikt över Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview)och [Uppgradera till ett allmänt-syfte v2-lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

När migreringen är klar uppdateras lagrings kontots replikeringsstatus till **geo-Zone-redundant lagring (GZRS)** eller **Geo-redundant lagring med Läs åtkomst (ra-GZRS)** . Tjänst slut punkter, åtkomst nycklar, signaturer för delad åtkomst (SAS) och andra alternativ för konto konfiguration förblir oförändrade och intakta.

Tänk på följande begränsningar för direktmigrering:

- Microsoft hanterar din begäran om direktmigrering utan dröjsmål, men det finns inte någon garanti om när den slutförs. Om dina data behöver migreras till GZRS eller RA-GZRS vid ett visst datum, rekommenderar Microsoft att du utför en manuell migrering i stället. Ju mer data du har på ditt konto, desto längre tid tar det vanligtvis att migrera dina data.
- Ditt konto måste innehålla data.
- Du kan bara migrera data inom samma region.
- Endast standard lagrings konto typer stöder Direktmigrering. Premium Storage-konton måste migreras manuellt.
- Direktmigrering från ett GZRS-eller RA-GZRS-konto till ett konto av LRS, GRS eller RA-GRS stöds inte. Du måste flytta data manuellt till ett nytt eller ett befintligt lagrings konto.
- Du kan begära en Direktmigrering från RA-GRS till RA-GZRS. Migrering från RA-GRS till GZRS stöds dock inte. I det här fallet måste du begära en Direktmigrering till RA-GZRS och sedan manuellt konvertera lagrings kontot till att använda GZRS.
- Managed disks stöder endast LRS och kan inte migreras till GZRS eller RA-GZRS. För integrering med tillgänglighets uppsättningar, se [Introduktion till Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Du kan lagra ögonblicks bilder och avbildningar för Standard SSD Managed Disks på Standard HDD lagring och [välja mellan alternativen LRS, ZRS, GZRS och ra-GZRS](https://azure.microsoft.com/pricing/details/managed-disks/).
- Konton som innehåller stora fil resurser stöds inte för GZRS.

Om du vill begära en Direktmigrering använder du [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Från portalen väljer du det lagrings konto som ska migreras till GZRS eller RA-GZRS och följer dessa anvisningar:

1. Välj **ny supportbegäran**.
2. Slutför **grunderna** baserat på din konto information. I avsnittet **tjänst** väljer du  **hantering av lagrings konto**och anger det konto som ska migreras.
3. Välj **Nästa**.
4. Ange följande värden i avsnittet **problem** :
    - **Allvarlighets grad**: Låt standardvärdet vara kvar.
    - **Problem typ**: Välj **datamigrering**.
    - **Kategori**: Välj **migrera till (RA-) GZRS i en region**.
    - **Rubrik**: Ange en beskrivande rubrik, till exempel **(RA-) GZRS för konto migrering**.
    - **Information**: Skriv **Mer information i @no__t-** 1box, till exempel "Jag vill MIGRERA till GZRS från [LRS, GRS] i \_ @ no__t-3-regionen." eller "Jag vill migrera till RA-GZRS från [LRS, RA-GRS] i regionen \_ @ no__t-1."
5. Välj **Nästa**.
6. Kontrol lera att kontakt informationen är korrekt på bladet med **kontakt information** .
7. Välj  **Skapa**.

En support representant kommer att kontakta dig för att få hjälp.

## <a name="see-also"></a>Se också

- [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Lokalt redundant lagring (LRS): Data redundans för låg kostnad för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Zon-redundant lagring (ZRS): Azure Storage program med hög tillgänglighet](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
