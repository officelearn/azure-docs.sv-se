---
title: Utvärderingar i utvärdering av Azure Migrate Server
description: Lär dig mer om utvärderingar i Utvärdering av Azure Migrate Server
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d1f32eea0ec6a8a4877fd1dc134344cfe68dcaba
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537771"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Utvärderingar i Azure Migrate:Server-utvärdering

Den här artikeln innehåller en översikt över utvärderingar i verktyget [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Serverbedömningsverktyget kan bedöma lokala virtuella datorer med VMware, virtuella hyper-v-datorer och fysiska servrar för migrering till Azure.

## <a name="whats-an-assessment"></a>Vad är en bedömning?

En utvärdering med verktyget Serverutvärdering mäter beredskapen och beräknar effekten av att migrera lokala servrar till Azure.

> [!NOTE]
> I Azure Government granskar du de [målutvärderingsplatser som stöds.](migrate-support-matrix.md#supported-geographies-azure-government) Observera att rekommendationer för vm-storlek i utvärderingar använder VM-serien specifikt för government cloud-regioner. [Läs mer](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) om vm-typer.

## <a name="types-of-assessments"></a>Typer av bedömningar

Utvärderingar som du skapar med Serverutvärdering är en ögonblicksbild av data i viss tid. Serverutvärdering innehåller två typer av utvärderingar.

**Typ av bedömning** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar som gör rekommendationer baserade på insamlade resultatdata | Rekommendationen vm-storlek baseras på CPU- och minnesanvändningsdata.<br/><br/> Disktypsrekommendationen (standard-HDD/SSD eller premiumhanterade diskar) baseras på IOPS och dataflöde för de lokala diskarna.
**Som är lokalt** | Utvärderingar som inte använder prestandadata för att ge rekommendationer. | Vm-storleksrekommendationen baseras på den lokala vm-storleken<br/><br> Den rekommenderade disktypen baseras på den valda lagringstypen för utvärderingen.

## <a name="how-do-i-run-an-assessment"></a>Hur kör jag en bedömning?

Det finns ett par sätt att köra en bedömning:

- Utvärdera datorer med hjälp av servermetadata som samlats in av en lätt Azure Migrate-installation. Installationen identifierar lokala datorer och skickar datormetadata/prestandadata till Azure Migrate.
- Utvärdera datorer med hjälp av servermetadata som importeras i ett CSV-format (comma-separated values).

## <a name="how-do-i-assess-with-the-appliance"></a>Hur bedömer jag med apparaten?

Om du distribuerar en Azure Migrate-installation för att identifiera lokala servrar gör du följande:

1. Du konfigurerar Azure och din lokala miljö så att de fungerar med Serverutvärdering.
2. För din första utvärdering skapar du ett Azure-projekt och lägger till serverbedömningsverktyget i det.
3. Du distribuerar en lätt Azure Migrate-installation. Installationen identifierar kontinuerligt lokala datorer och skickar datorns metadata och prestandadata till Azure Migrate. Apparaten distribueras som en virtuell dator eller en fysisk dator. Det finns ingen anledning att installera något på maskiner som du vill bedöma.
4. När installationen börjar datoridentifiera kan du samla in datorer som du vill utvärdera i en grupp och köra en utvärdering för gruppen.

Du kan följa våra tutorials för [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)eller [fysiska servrar](tutorial-prepare-physical.md) för att prova dessa steg.

## <a name="how-do-i-assess-with-imported-data"></a>Hur bedömer jag med importerade data?

Om du bedömer servrar med hjälp av en CSV-fil behöver du ingen installation. I stället gör du följande:

1. Du konfigurerar Azure för att fungera med Serverutvärdering.
2. För din första utvärdering skapar du ett Azure-projekt och lägger till serverbedömningsverktyget i det.
3. Du hämtar en CSV-mall och lägger till serverdata i den.
4. Du importerar mallen till Serverutvärdering.
5. Du upptäcker servrar som lagts till med importen, samlas sedan i en grupp och kör en utvärdering för gruppen.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlar apparaten in?

Om du använder Azure Migrate-enheten för bedömning kan du läsa mer om metadata och prestandadata som samlas in för [VMware](migrate-appliance.md#collected-data---vmware) och [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hur beräknar apparaten prestandadata?

Om du använder installationen för identifiering samlas prestandadata för beräkningsinställningar in på följande sätt:

1. Apparaten samlar in en provpunkt i realtid:

    - **Virtuella datorer med vmware**: Apparaten samlar in en provpunkt i realtid vid varje 20-sekundersintervall.
    - Virtuella datorer med **hyper-V:** Provpunkten i realtid samlas in vid varje 30-sekundersintervall.
    - **Fysiska servrar**: Provpunkten i realtid samlas in vart femte minuts intervall. 
    
2. Apparaten rullar upp provpunkterna (20 sekunder, 30 sekunder, fem minuter) för att skapa en enda datapunkt var tionde minut. Om du vill skapa den enda punkten väljer enheten toppvärdet från alla exempel och skickar den sedan till Azure.
3. Serverbedömning lagrar alla 10-minutersprovpunkter för den senaste månaden.
4. När du skapar en utvärdering identifierar Serverutvärdering lämplig datapunkt som ska användas för rätt storlek, baserat på percentilvärdena för *prestandahistorik* och *percentilutnyttjande*.

    - Om prestandahistoriken till exempel är inställd på en vecka och percentilutnyttjandet är den 95:e percentilen sorterar Serverutvärdering 10-minutersprovpunkterna för den senaste veckan i stigande ordning och väljer det 95:e percentilvärdet för högerstorlek. 
    - Det 95:e percentilvärdet ser till att du ignorerar eventuella extremvärden, som kan inkluderas om du väljer den 99:e percentilen.
    - Om du vill välja toppanvändning för perioden och inte vill missa några avvikare, bör du välja den 99: e percentilen för percentilutnyttjande.

5. Det här värdet multipliceras med komfortfaktorn för att få effektiva prestandautnyttjandedata för varje mått (CPU-användning, minnesanvändning, disk-IOPS (läsa och skriva), diskdataflöde (läs och skriv) och nätverksdataflöde (in och ut) som enheten samlar in.



## <a name="how-are-assessments-calculated"></a>Hur beräknas bedömningarna? 

Utvärderingar i serverutvärdering beräknas med hjälp av metadata/prestandadata för lokala datorer. Om du distribuerar Azure Migrate-enheten kan du bedöma med hjälp av de data som samlas in av installationen. Om du kör en bedömning för datorer som importeras med hjälp av en . CSV-filen anger du metadata för beräkningen. Beräkningar sker i tre steg:

1. **Beräkna Azure-beredskap:** Utvärdera om datorer är lämpliga för migrering till Azure.
2. **Beräkna storleksrekommendationer:** Uppskatta beräkning, lagring och nätverksstorlek. 
2. **Beräkna månadskostnader**: Beräkna uppskattade månatliga beräknings- och lagringskostnader för att köra datorerna i Azure efter migreringen.

Beräkningarna är i sin ordning och en maskinserver flyttas bara till ett senare stadium om den passerar den föregående. Om en server till exempel misslyckas med Azure-beredskapen markeras den som olämplig för Azure och storlek och kostnadsredovisning görs inte för den servern.


## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

Det här ingår i en utvärdering i Serverutvärdering.

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Den plats som du vill migrera till. Serverutvärdering stöder för närvarande dessa Azure-målregioner:<br/><br/> Australien Öst, Australien Sydost, Brasilien Syd, Kanada Central, Kanada Öst, Centralasien, Centrala USA, Kina Öst, Kina Nord, Östra Asien, Östra USA, Östra USA2, Tyskland Central, Tyskland Nordost, Japan Öst, Japan Väst, Korea Central, Korea Syd, Norra Centrala USA, Nordeuropa, Södra centrala USA, Sydostasien, Södra Indien, Storbritannien Syd, Storbritannien Väst, US Gov Arizona, US Gov Texas, US Gov Virginia , Västra centrala USA, Västeuropa, Västra Indien, västra USA och västra US2.
*Mållagringsdisk (storlekssortering)** | Den typ av diskar som ska användas för lagring i Azure. <br/><br/> Ange mållagringsdisken som premiumhanterad, standardhanterad SSD-hanterad eller standard hdd-hanterad.
**Mållagringsdisk (prestandabaserad storlek)** | Ange vilken typ av mållagringsdisk som automatisk, premiumhanterad, standardad hdd-hanterad eller standard-SSD-hanterad.<br/><br/> **Automatisk**: Diskrekommendationen baseras på diskarnas prestandadata (in-/utdataåtgärder per sekund (IOPS) och dataflöde).<br/><br/>**Premium/standard**: Utvärderingen rekommenderar en disk SKU inom den valda lagringstypen.<br/><br/> Om du vill uppnå en enda instans VM SLA på 99,9%, med tanke på att använda premium hanterade diskar. Detta säkerställer att alla diskar i utvärderingen rekommenderas som premiumhanterade diskar.<br/><br/> Azure Migrate stöder endast hanterade diskar för migreringsutvärdering.
**Reserverade instanser (RIs)** | Ange [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure, så att kostnadsuppskattningar i utvärderingen tar hänsyn till RI-rabatter.<br/><br/> Ã fors stöds endast för pay-as-you-go-erbjudanden i Azure Migrate.
**Storlekskriterier** | Används för att rättsstora den virtuella datorn i Azure.<br/><br/> Använd storleksändring eller prestandabaserad storlek.
**Prestandahistorik** | Används med prestandabaserad storlek. Ange hur länge prestandadata utvärderas.
**Percentilutnyttjande** | Används med prestandabaserad storlek. Anger percentilvärdet för det prestandaprov som ska användas för rätt storlek. 
**VM-serie** | Ange azure VM-serien som du vill överväga för rätt storlek. Om du till exempel inte har en produktionsmiljö som behöver virtuella datorer i A-serien i Azure kan du utesluta A-serier från listan eller serien.
**Komfortfaktor** | Buffert som används under bedömningen. Används ovanpå datoranvändningsdata för virtuella datorer (CPU, minne, disk och nätverk). Den står för problem som säsongsanvändning, kort prestandahistorik och sannolikt ökningar i framtida användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % utnyttjande normalt i en virtuell dator med två kärnor. Med en komfortfaktor på 2,0 x blir resultatet en virtuell dator med fyra kärnor istället.
**Erbjudande** | Visar [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) där du är registrerad. Serverbedömningen beräknar kostnaden i enlighet med detta.
**Valuta** | Faktureringsvalutan för ditt konto.
**Rabatt (%)** | Visar alla prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet. Standardinställningen är 0%.
**VM-drifttid** | Om virtuella Azure-datorer inte körs 24 timmar om dygnet, 7 dagar i veckan, kan du ange hur länge (dagar per månad och timmar per dag) som de ska köras. Kostnadsuppskattningar hanteras därefter.<br/><br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har programvarusäkring och är berättigad till [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om värdet är Ja (standardinställningen) beaktas icke-Windows Azure-priser för virtuella Windows-datorer.

[Granska metodtipsen](best-practices-assessment.md) för att skapa utvärdering med serverutvärdering.


## <a name="calculate-readiness"></a>Beräkna beredskap

Alla datorer är inte lämpliga att köra i Azure. Serverutvärdering bedömer varje lokal dator och tilldelar den en beredskapskategori. 
- **Klar för Azure:** Datorn kan migreras som den är till Azure utan några ändringar. Det börjar i Azure med fullständigt Azure-stöd.
- **Villkorligt klar för Azure**: Datorn kan starta i Azure, men kanske inte har fullständig Azure-support. En dator som kör en äldre version av Windows Server stöds till exempel inte i Azure. Du måste vara försiktig innan du migrerar dessa datorer till Azure. Följ den reparationsvägledning som föreslås i utvärderingen för att åtgärda beredskapsproblemen.
- **Inte redo för Azure:** Datorn startar inte i Azure. Om till exempel en lokal datordisk är mer än 64-TBs kan den inte finnas i Azure. Följ reparationsvägledningen för att åtgärda problemet före migreringen. 
- **Okänd beredskap**: Azure Migrate kunde inte avgöra en dators beredskap på grund av otillräckliga metadata.

För att beräkna beredskapen granskar Serverutvärdering de datoregenskaper och operativsystemets inställningar som sammanfattas i följande tabeller. 

### <a name="machine-properties"></a>Maskinegenskaper

Serverutvärdering granskar följande egenskaper för den lokala virtuella datorn för att avgöra om den kan köras på Azure.

**Egenskap** | **Detaljer** | **Azure-beredskapsstatus**
--- | --- | ---
**Starttyp** | Azure stöder virtuella datorer med en starttyp av BIOS, inte UEFI. | Villkorligt klar om starttypen är UEFI.
**Kärnor** | Antalet kärnor i datorerna måste vara lika med eller mindre än det maximala antalet kärnor (128) som stöds för en Azure VM.<br/><br/> Om prestandahistorik är tillgänglig tar Azure Migrate hänsyn till de använda kärnorna som jämförelse. Om en komfortfaktor anges i bedömningsinställningarna multipliceras antalet utnyttjade kärnor med komfortfaktorn.<br/><br/> Om det inte finns någon prestandahistorik använder Azure Migrate de allokerade kärnorna utan att använda komfortfaktorn. | Redo om den är mindre än eller lika med gränserna.
**Minne** | Datorns minnesstorlek måste vara lika med eller mindre än det maximala minnet (3892&nbsp;GIGABYTE [GB] på Azure M-serien Standard_M128m<sup>2</sup>) som tillåts för en Azure VM. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om prestandahistorik är tillgänglig tar Azure Migrate hänsyn till det använda minnet som jämförelse. Om en komfortfaktor anges multipliceras det utnyttjade minnet med komfortfaktorn.<br/><br/> Om det inte finns någon historia används det tilldelade minnet utan att använda komfortfaktorn.<br/><br/> | Redo om inom gränser.
**Lagringsdisk** | Allokerad storlek på en disk måste vara 32 TB eller mindre. Även om Azure stöder 64 TB-diskar med Ultra SSD-diskar, söker Azure Migrate: Server Assessment för närvarande efter 32 TB som diskstorleksgränser eftersom det inte stöder Ultra SSD ännu. <br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller färre, inklusive OS-disken. | Redo om inom gränser.
**Nätverk** | En dator måste ha 32 eller färre nätverksgränssnitt (NAC) anslutna till den. | Redo om inom gränser.

### <a name="guest-operating-system"></a>Gästoperativsystem
Tillsammans med vm-egenskaper tittar Server Assessment på datorernas gästoperativsystem för att avgöra om det kan köras på Azure.

> [!NOTE]
> För virtuella datorer med VMware använder Server Assessment det operativsystem som angetts för den virtuella datorn i vCenter Server för att hantera gästoperativsystemets analys. För virtuella Linux-datorer som körs på VMware identifierar den för närvarande inte den exakta kärnversionen av gästoperativsystemet.

Följande logik används av Server Assessment för att identifiera Azure-beredskap baserat på operativsystemet.

**Operativsystem** | **Detaljer** | **Azure-beredskapsstatus**
--- | --- | ---
Windows Server 2016 & alla SPs-adresser | Azure ger fullt stöd. | Redo för Azure
Windows Server 2012 R2 & alla SPs | Azure ger fullt stöd. | Redo för Azure
Windows Server 2012 & alla SPs-adresser | Azure ger fullt stöd. | Redo för Azure
Windows Server 2008 R2 med alla SPs | Azure ger fullt stöd.| Redo för Azure
Windows Server 2008 (32- och 64-bitars) | Azure ger fullt stöd. | Redo för Azure
Windows Server 2003, 2003 R2 | Dessa operativsystem har passerat sitt slutdatum för support och behöver ett [csa-avtal (Custom Support Agreement)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt klar för Azure. Överväg att uppgradera operativsystemet innan du migrerar till Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Dessa operativsystem har passerat sitt supportdatum. Datorn kan starta i Azure, men Azure tillhandahåller inget os-stöd. | Villkorligt klar för Azure. Vi rekommenderar att du uppgraderar operativsystemet innan du migrerar till Azure.
Windows-klient 7, 8 och 10 | Azure tillhandahåller endast support med [Visual Studio-prenumeration.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Villkorligt redo för Azure
Skrivbordet i Windows 10 Pro | Azure ger support med [multitenant hostingrättigheter.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Villkorligt redo för Azure
Windows Vista, XP Professional | Dessa operativsystem har passerat sitt supportdatum. Datorn kan starta i Azure, men Azure tillhandahåller inget os-stöd. | Villkorligt klar för Azure. Vi rekommenderar att du uppgraderar operativsystemet innan du migrerar till Azure.
Linux | Azure stöder dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men vi rekommenderar att du uppgraderar operativsystemet till en godkänd version innan du migrerar till Azure. | Redo för Azure om versionen är godkänd.<br/><br/>Villkorligt klar om versionen inte är godkänd.
Andra operativsystem<br/><br/> Till exempel Oracle Solaris, Apple macOS etc., FreeBSD, etc. | Azure stöder inte dessa operativsystem. Datorn kan starta i Azure, men Azure tillhandahåller inget os-stöd. | Villkorligt klar för Azure. Vi rekommenderar att du installerar ett operativsystem som stöds innan du migrerar till Azure.  
OS angivet som **Annat** i vCenter Server | Azure Migrate kan inte identifiera operativsystemet i det här fallet. | Okänd beredskap. Kontrollera att operativsystemet som körs i den virtuella datorn stöds i Azure.
32-bitars operativsystem | Datorn kan starta i Azure, men Azure kanske inte ger fullt stöd. | Villkorligt klar för Azure. Överväg att uppgradera datorns operativsystem från 32-bitars operativsystem till 64-bitars operativsystem innan du migrerar till Azure.

## <a name="calculating-sizing"></a>Beräkna storlek


När datorn har markerats som klar för Azure ger Server Assessment storleksrekommendationer för att identifiera Azure VM och disk SKU. Storleksberäkningar beror på om du använder lokal storlek eller prestandabaserad storlek.

### <a name="calculate-sizing-as-is-on-premises"></a>Beräkna storlek (lokalt)

 Om du använder lokal storleksändring tar serverutvärdering inte hänsyn till prestandahistoriken för de virtuella datorerna och diskarna.

- **Beräkningsstorlek:** Den allokerar en Azure VM SKU baserat på storleken som allokeras lokalt.
- **Lagring/diskstorlek:** Serverutvärdering tittar på den lagringstyp som anges i bedömningsegenskaper (standard HDD/SSD/premium) och rekommenderar disktypen därefter. Standardlagringstypen är premiumdiskar.
- **Nätverksstorlek:** Serverutvärdering tar hänsyn till nätverkskortet på den lokala datorn.


### <a name="calculate-sizing-performance-based"></a>Beräkna storlek (prestandabaserad)

Om du använder storleksändring med prestandabasing gör Serverutvärderingsrekommendationer enligt följande:

- Serverutvärdering tar hänsyn till datorns prestandahistorik för att identifiera den virtuella datorns storlek och disktyp i Azure.
- Om servrar har importerats med hjälp av en CSV-fil används de värden du anger. Den här metoden är särskilt användbar om du har överallokerat den lokala datorn, användningen är låg och du vill rätt storlek på den virtuella datorn i Azure för att spara kostnader. 
- Om du inte vill använda prestandadata återställer du storlekskriterierna till lokalt lokalt, enligt beskrivningen i föregående avsnitt.

#### <a name="calculate-storage-sizing"></a>Beräkna lagringsstorlek

För lagringsstorlek försöker Azure Migrate mappa alla diskar som är anslutna till datorn till en disk i Azure och fungerar på följande sätt:

1. Serverutvärdering lägger till läsa och skriva IOPS för en disk för att få den totala IOPS som krävs. På samma sätt läggs läs- och skrivdataflödesvärdena till för att få det totala dataflödet för varje disk.
2. Om du har angett lagringstyp som Automatisk, baserat på de gällande IOPS- och dataflödesvärdena, avgör Server Assessment om disken ska mappas till en vanlig hårddisk, standard-SSD eller en premiumdisk i Azure. Om lagringstypen är inställd på Standard HDD/SSD/Premium försöker Server Assessment hitta en disk SKU inom den valda lagringstypen (Standard HDD/SSD/Premium-diskar).
3. Diskar väljs på följande sätt:
    - Om Serverutvärdering inte kan hitta en disk med den nödvändiga IOPS och dataflödet markerar den datorn som olämplig för Azure.
    - Om Serverutvärdering hittar en uppsättning lämpliga diskar väljer den de diskar som stöder den plats som anges i bedömningsinställningarna.
    - Om det finns flera kvalificerade diskar väljer Serverbedömning disken med den lägsta kostnaden.
    - Om prestandadata för en disk inte är tillgängliga används konfigurationsdata för disken (diskstorlek) för att hitta en standard SSD-disk i Azure.

#### <a name="calculate-network-sizing"></a>Beräkna nätverksstorlek

Serverutvärdering försöker hitta en virtuell Azure-dator som kan stödja antalet nätverkskort som är anslutna till den lokala datorn och den prestanda som krävs av dessa nätverkskort.
- För att få den gällande nätverksprestandan för den lokala virtuella datorn sammanställer Server Assessment de data som överförs per sekund (MBps) ut ur datorn (nätverk ut), över alla nätverkskort och tillämpar komfortfaktorn. Den använder det här numret för att hitta en Virtuell Azure-dator som kan stödja den nödvändiga nätverksprestandan.
- Tillsammans med nätverksprestanda tar Server Assessment också upp om den virtuella Azure-datorn kan stödja antalet nätverkskort som krävs.
- Om det inte finns några data om nätverksprestanda är tillgängliga tar Server assessment endast hänsyn till antalet nätverkskort för vm-storlek.


#### <a name="calculate-compute-sizing"></a>Beräkna beräkningsstorlek

När den har beräknat lagrings- och nätverkskrav tar Server Assessment hänsyn till CPU- och minneskrav för att hitta en lämplig VM-storlek i Azure.
- Azure Migrate tittar på de effektiva använda kärnorna och minnet för att hitta en lämplig VM-storlek i Azure.
- Om ingen lämplig storlek hittas markeras datorn som olämplig för Azure.
- Om en lämplig storlek hittas tillämpar Azure Migrate lagrings- och nätverksberäkningarna. Den tillämpar sedan plats- och prisnivåinställningar för den slutliga rekommendationen för vm-storlek.
- Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.


## <a name="confidence-ratings-performance-based"></a>Konfidensklassificeringar (prestationsbaserade)

Varje prestandabaserad utvärdering i Azure Migrate är associerad med en konfidensklassificering som sträcker sig från en (lägsta) till fem stjärnor (högst). Förtroendeklassificeringen hjälper dig att uppskatta tillförlitligheten i storleksrekommendationerna från Azure Migrate.

- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- För prestandabaserad storlek behöver serverbedömningen:
    - Användningsdata för CPU- och VM-minne.
    - Disk-IOPS- och dataflöde för varje disk som är ansluten till den virtuella datorn.
    - Nätverks-I/O för att hantera prestandabaserad storlek för varje nätverkskort som är anslutet till en virtuell dator.
    - Om något av dessa användningsnummer inte är tillgängligt kanske storleksrekommendationerna inte är tillförlitliga.

> [!NOTE]
> Konfidensklassificeringar tilldelas inte för servrar som bedöms med hjälp av en importerad . CSV-fil. Betyg är inte heller tillämpliga för lokal bedömning.
   
### <a name="ratings"></a>Klassificeringar

Beroende på hur många procent av tillgängliga datapunkter går konfidensgraden för bedömningen så här.

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0-20% | 1 stjärna
   21-40% | 2 stjärnor
   41-60% | 3 stjärnor
   61-80% | 4 stjärnor
   81-100% | 5 stjärnor

### <a name="low-confidence-ratings"></a>Låga konfidensbetyg

Här är några anledningar till varför en bedömning kan få ett lågt förtroende betyg:

- Du har inte profilera din miljö under den tid som du skapar utvärderingen. Om du till exempel skapar utvärderingen med prestandavaraktighet inställd på en dag måste du vänta minst en dag efter att du har börjat identifiera för att alla datapunkter ska samlas in.
- Vissa virtuella datorer stängdes under den period för vilken bedömningen beräknades. Om några virtuella datorer är avstängda under en viss tid kan serverutvärderingen inte samla in prestandadata för den perioden.
- Vissa virtuella datorer skapades under den period för vilken bedömningen beräknades. Om du till exempel skapade en utvärdering för prestandahistoriken för den senaste månaden, men vissa virtuella datorer skapades i miljön för bara en vecka sedan, finns inte prestandahistoriken för de nya virtuella datorerna under hela varaktigheten.

> [!NOTE]
> Om konfidensklassificeringen för en bedömning är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag på att apparaten ska profilera miljön och sedan beräkna om bedömningen. Om du inte gör det kanske prestandabaserad storlek inte är tillförlitlig. I så fall rekommenderar vi att du byter bedömning till lokal storlek.

## <a name="calculate-monthly-costs"></a>Beräkna månadskostnader

När storleksrekommendationerna är klara beräknar Azure Migrate beräknings- och lagringskostnader för efter migreringen.

- **Beräkningskostnad:** Med den rekommenderade Azure VM-storleken använder Azure Migrate fakturerings-API:et för att beräkna månadskostnaden för den virtuella datorn.
    - Beräkningen tar hänsyn till operativsystemet, programvarusäkring, reserverade instanser, VM-drifttid, plats och valutainställningar.
    - Den aggregat kostnaden för alla datorer för att beräkna den totala månatliga beräkningskostnaden.
- **Lagringskostnad**: Den månatliga lagringskostnaden för en dator beräknas genom att den månatliga kostnaden för alla diskar som är kopplade till maskinen sammanställs enligt följande:
    - Serverbedömning beräknar de totala månatliga lagringskostnaderna genom att samla lagringskostnaderna för alla datorer.
    - För närvarande tar beräkningen inte hänsyn till erbjudanden som anges i bedömningsinställningarna.

Kostnader visas i den valuta som anges i bedömningsinställningarna.


## <a name="next-steps"></a>Nästa steg

[Granska](best-practices-assessment.md) metodtips för att skapa utvärderingar. 


- Lär dig mer om att köra utvärderingar för [virtuella datorer,](tutorial-prepare-vmware.md) [virtuella datorer med hyper-v och](tutorial-prepare-hyper-v.md)fysiska [servrar](tutorial-prepare-physical.md).
- Lär dig mer om hur du bedömer servrar [som importeras med en CSV-fil](tutorial-assess-import.md).
- Lär dig mer om hur du ställer in [beroendevisualisering](concepts-dependency-visualization.md).
