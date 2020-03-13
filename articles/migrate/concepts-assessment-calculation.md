---
title: Utvärderingar i Azure Migrate
description: Lär dig mer om utvärderingar i Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0cf933dd1c8c61edfcea20ea954c5813f3848b28
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269697"
---
# <a name="about-assessments-in-azure-migrate"></a>Om utvärderingar i Azure Migrate

Den här artikeln beskriver hur utvärderingar beräknas i [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool). Du kan köra utvärderingar på grupper av lokala datorer för att ta reda på om de är redo för migrering till Azure Migrate.

## <a name="how-do-i-run-an-assessment"></a>Hur gör jag för att köra en utvärdering?
Du kan köra en utvärdering med hjälp av Azure Migrate: Server utvärdering eller något annat Azure-verktyg eller från tredje part. När du har skapat ett Azure Migrate-projekt lägger du till det verktyg du behöver. [Läs mer](how-to-add-tool-first-time.md)

### <a name="collect-compute-data"></a>Samla in beräknings data

Prestanda data för beräknings inställningar samlas in på följande sätt:

1. [Azure Migrates apparaten](migrate-appliance.md) samlar in en exempel punkt i real tid:

    - **Virtuella VMware-datorer**: för virtuella VMware-datorer samlar Azure Migrate-utrustningen in en exempel punkt i real tid med ett intervall på 20 sekunder.
    - **Virtuella Hyper-v-datorer**: för virtuella Hyper-v-datorer samlas exempel punkten i real tid in med varje 30-sekunders intervall.
    - **Fysiska servrar**: för fysiska servrar samlas exempel punkten i real tid in med var femte minut. 
    
2. Enheten lyfter upp exempel punkterna (20 sekunder, 30 sekunder, fem minuter) för att skapa en enda data punkt var tionde minut. Om du vill skapa en enskild data punkt väljer du det högsta värdet från alla exempel och skickar det sedan till Azure.
3. Server utvärderingen lagrar alla exempel punkter på 10 minuter för den senaste månaden.
4. När du skapar en utvärdering identifierar Server utvärderingen lämplig data punkt som ska användas för höger storlek, baserat på percentilvärdet för *prestanda historik* och *percentils användning*.

    - Om prestanda historiken till exempel är inställd på en vecka och percentilvärdet använder den 95: e percentilen, sorterar Server utvärderingen 10 minuters exempel punkter under den senaste veckan i stigande ordning och väljer 95 percentilvärdet för höger storlek. 
    - Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du väljer 99-percentilen.
    - Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden, bör du välja den 99 percentilen för percentils användning.

5. Det här värdet multipliceras med den praktiska faktorn för att få en effektiv prestanda användnings data för varje mått (processor belastning, minnes användning, disk-IOPS (läsning och skrivning), disk data flöde (läsning och skrivning) och nätverks data flöde (in och ut) som enheten samlas in.

Om du vill köra utvärderingar i Server utvärdering förbereder du utvärdering lokalt och i Azure och konfigurerar Azure Migrate-enheten för att kontinuerligt identifiera lokala datorer. När datorerna har identifierats samlar du in dem i grupper för att utvärdera dem. För mer detaljerade utvärderingar av hög exakthet kan du visualisera och mappa beroenden mellan datorer, för att ta reda på hur du migrerar dem.

- Lär dig mer om att köra utvärderingar för [virtuella VMware-datorer](tutorial-prepare-vmware.md), [virtuella Hyper-V-datorer](tutorial-prepare-hyper-v.md)och [fysiska servrar](tutorial-prepare-physical.md).
- Lär dig mer om att utvärdera servrar [som importer ATS med en CSV-fil](tutorial-assess-import.md).
- Lär dig mer om att konfigurera [beroende visualisering](concepts-dependency-visualization.md).

## <a name="assessments-in-server-assessment"></a>Utvärderingar i Server utvärdering 

Utvärderingar som du skapar med Azure Migrate Server utvärdering är en tidpunkts ögonblicks bild av data. Verktyget Server bedömning tillhandahåller två typer av utvärderingar.

**Bedömnings typ** | **Detaljer** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar som gör rekommendationer baserat på insamlade prestanda data | Rekommendationen för VM-storlek baseras på processor-och minnes användnings data.<br/><br/> Disk typs rekommendation (standard HDD/SSD eller Premium-Managed Disks) baseras på IOPS och data flödet för lokala diskar.
**Som lokal** | Utvärderingar som inte använder prestanda data för att göra rekommendationer. | Rekommendationen för VM-storlek baseras på den lokala virtuella dator storleken<br/><br> Den rekommenderade disk typen baseras på den valda lagrings typen för utvärderingen.

## <a name="collecting-performance-data"></a>Samlar in prestanda data

Prestanda data samlas in på följande sätt:

1. [Azure Migrates apparaten](migrate-appliance.md) samlar in en exempel punkt i real tid:

    - **Virtuella VMware-datorer**: för virtuella VMware-datorer samlar Azure Migrate-utrustningen in en exempel punkt i real tid med ett intervall på 20 sekunder.
    - **Virtuella Hyper-v-datorer**: för virtuella Hyper-v-datorer samlas exempel punkten i real tid in med varje 30-sekunders intervall.
    - **Fysiska servrar**: för fysiska servrar samlas exempel punkten i real tid in med var femte minut. 
    
2. Enheten lyfter upp exempel punkterna (20 sekunder, 30 sekunder, fem minuter) för att skapa en enda data punkt var tionde minut. Om du vill skapa en enskild data punkt väljer du det högsta värdet från alla exempel och skickar det sedan till Azure.
3. Server utvärderingen lagrar alla exempel punkter på 10 minuter för den senaste månaden.
4. När du skapar en utvärdering identifierar Server utvärderingen lämplig data punkt som ska användas för höger storlek, baserat på percentilvärdet för *prestanda historik* och *percentils användning*.

    - Om prestanda historiken till exempel är inställd på en vecka och percentilvärdet använder den 95: e percentilen, sorterar Server utvärderingen 10 minuters exempel punkter under den senaste veckan i stigande ordning och väljer 95 percentilvärdet för höger storlek. 
    - Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du väljer 99-percentilen.
    - Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden, bör du välja den 99 percentilen för percentils användning.

5. Det här värdet multipliceras med den praktiska faktorn för att få en effektiv prestanda användnings data för varje mått (processor belastning, minnes användning, disk-IOPS (läsning och skrivning), disk data flöde (läsning och skrivning) och nätverks data flöde (in och ut) som enheten samlas in.
## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

Det här är vad som ingår i en utvärdering i Azure Migrate: Server bedömning.

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Den plats som du vill migrera till. Server utvärderingen stöder för närvarande följande Azure-regioner:<br/><br/> Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra, östra USA, östra 2; USA, Tyskland, centrala, Tyskland nordöstra, Östra Japan, västra Japan, centrala Korea, Nord Korea Centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra, US Gov, Arizona, US Gov, Texas, US Gov, Virginia, västra centrala USA, Västeuropa, västra Indien, västra USA och västra 2; USA.
*Mål lagrings disk (i storlek)* * | Den typ av diskar som ska användas för lagring i Azure. <br/><br/> Ange mål lagrings disken som Premium-hanterad, standard SSD-hanterad eller standard-HDD-hanterad.
**Mål lagrings disk (prestanda baserad storlek)** | Ange typen av mål lagrings disk som automatisk, Premium-hanterad, standard-HDD-hanterad eller standard SSD-hanterad.<br/><br/> **Automatisk**: disk rekommendationen baseras på diskens prestanda data (in-/utdata-åtgärder per sekund (IOPS) och data flöde).<br/><br/>**Premium/standard**: utvärderingen rekommenderar en disk-SKU i den valda lagrings typen.<br/><br/> Om du vill uppnå ett service avtal för en enda instans av virtuell dator på 99,9%, Överväg att använda Premium Managed disks. Detta säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar.<br/><br/> Azure Migrate stöder endast hanterade diskar för migreringsutvärdering.
**Reserverade instanser (RIs)** | Ange [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure så att kostnads uppskattningar i utvärderingen tar RI-rabatter till kontot.<br/><br/> Fjärrinstallationstjänster stöds för närvarande endast för betala per användning-erbjudanden i Azure Migrate.
**Storleks villkor** | Används för att anpassa den virtuella datorn i Azure.<br/><br/> Använd som-är storleks ändring eller prestanda-baserad storlek.
**Prestandahistorik** | Används med prestanda-baserad storlek. Ange den varaktighet som ska användas för att utvärdera prestanda data.
**Percentilutnyttjande** | Används med prestanda-baserad storlek. Anger percentilvärdet för det prestanda exempel som ska användas för höger storleks ändring. 
**VM-serie** | Ange den Azure VM-serie som du vill ta hänsyn till för rätt storlek. Om du till exempel inte har en produktions miljö som behöver en-seriens virtuella datorer i Azure kan du undanta en-serien från listan eller serien.
**Komfortfaktor** | Buffert som används under utvärderingen. Används ovanpå datorns användnings data för virtuella datorer (CPU, minne, disk och nätverk). IT-konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20% användning vanligt vis i en virtuell dator med två kärnor. Med en bekvämlighets faktor på 2,0 x är resultatet en virtuell dator med fyra kärnor i stället.
**Erbjudande** | Visar det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Server utvärderingen beräknar kostnaden på motsvarande sätt.
**Valuta** | Fakturerings valuta för ditt konto.
**Rabatt (%)** | Visar en lista med eventuella prenumerationer som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
**VM-drifttid** | Om virtuella Azure-datorer inte körs 24 timmar per dag, 7 dagar i veckan, kan du ange varaktigheten (dagar per månad och timmar per dag) som de ska köras. Kostnads uppskattningar hanteras i enlighet med detta.<br/><br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om det är inställt på Ja (standardinställningen), betraktas icke-Windows Azure-priser för virtuella Windows-datorer.

[Gå igenom metod tipsen](best-practices-assessment.md) för att skapa utvärdering med Server utvärdering.

## <a name="how-are-assessments-calculated"></a>Hur beräknas utvärderingarna? 

Utvärderingar i Azure Migrate: Server utvärderingen beräknas med hjälp av de metadata som samlats in om de lokala datorerna. Om du kör en utvärdering på datorer som importer ATS med hjälp av en. CSV-fil anger du metadata för beräkningen. Beräkningar sker i tre steg:

1. **Beräkna Azure-beredskap**: Bedöm om datorerna är lämpliga för migrering till Azure.
2. **Beräkna storleks rekommendationer**: beräkna beräkning, lagring och nätverks storlek. 
2. **Beräkna månads kostnader**: beräkna de uppskattade kostnaderna för beräkning och lagring för att köra datorerna i Azure efter migreringen.

Beräkningarna är i ordning och en dator server flyttas bara till ett senare steg om den tidigare har passerat. Om en server till exempel inte klarar Azure-beredskap, är den markerad som olämplig för Azure och storleks sortering och kostnads redovisning görs inte för den servern.



## <a name="calculate-readiness"></a>Beräkna beredskap

Alla datorer är inte lämpliga att köra i Azure. Server utvärderingen bedömer varje lokal dator och tilldelar den en beredskaps kategori. 
- **Redo för Azure**: datorn kan migreras som den är till Azure utan några ändringar. Det kommer att starta i Azure med fullständig support för Azure.
- **Villkorligt redo för Azure**: datorn kan starta i Azure, men det kanske inte har fullständig support för Azure. En dator som kör en äldre version av Windows Server stöds till exempel inte i Azure. Du måste vara försiktig innan du migrerar de här datorerna till Azure. Följ den reparations vägledning som föreslås i utvärderingen för att åtgärda beredskaps problemen.
- **Inte redo för Azure**: datorn startar inte i Azure. Om till exempel en lokal dator disk är mer än 64-TBs, kan den inte finnas i Azure. Följ reparations vägledningen för att åtgärda problemet innan du migrerar. 
- **Beredskap okänd**: Azure Migrate kunde inte fastställa en dators beredskap på grund av otillräckliga metadata.

För att beräkna beredskap granskar Server utvärderingen datorns egenskaper och de inställningar för operativ systemet som sammanfattas i följande tabeller. 

### <a name="machine-properties"></a>Dator egenskaper

Server utvärderingen granskar följande egenskaper för den lokala virtuella datorn för att avgöra om den kan köras på Azure.

**Egenskap** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
**Start typ** | Azure har stöd för virtuella datorer med en start typ av BIOS, inte UEFI. | Villkorligt redo om start typen är UEFI.
**Kärnor** | Antalet kärnor på datorerna måste vara lika med eller mindre än det maximala antalet kärnor (128) som stöds för en virtuell Azure-dator.<br/><br/> Om prestanda historiken är tillgänglig, Azure Migrate beakta de använda kärnorna för jämförelse. Om en bekvämlighets faktor anges i utvärderings inställningarna multipliceras antalet använda kärnor av den praktiska faktorn.<br/><br/> Om det inte finns någon prestanda historik använder Azure Migrate tilldelade kärnor utan att använda den praktiska faktorn. | Redo om det är mindre än eller lika med begränsningar.
**Minnesoptimerade** | Datorns minnes storlek måste vara lika med eller mindre än det maximala minne (3892 gigabyte [GB] i Azure M-serien Standard_M128m&nbsp;<sup>2</sup>) som tillåts för en virtuell Azure-dator. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om prestanda historiken är tillgänglig kan Azure Migrate använda det använda minnet för jämförelse. Om en bekvämlighets faktor anges multipliceras det använda minnet av den praktiska faktorn.<br/><br/> Om det inte finns någon historik används allokerat minne utan att du använder den praktiska faktorn.<br/><br/> | Redo om det är inom gränserna.
**Lagrings disk** | Den allokerade storleken på en disk måste vara 32 TB eller mindre. Även om Azure har stöd för 64-TB-diskar med Ultra SSD diskar, Azure Migrate: Server utvärdering söker för närvarande efter 32 TB som disk storleks gränser eftersom de inte stöder Ultra SSD än. <br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disken. | Redo om det är inom gränserna.
**Nätverk** | En dator måste ha 32 eller färre nätverks gränssnitt (NIC) anslutna till den. | Redo om det är inom gränserna.

### <a name="guest-operating-system"></a>Gäst operativ system
Tillsammans med VM-egenskaper tittar Server utvärderingen på datorns gäst operativ system för att avgöra om den kan köras på Azure.

> [!NOTE]
> För virtuella VMware-datorer använder Server utvärderingen det operativ system som har angetts för den virtuella datorn i vCenter Server för att hantera gäst operativ system analys. För virtuella Linux-datorer som körs på VMware kan för närvarande inte identifiera den exakta kernel-versionen av gäst operativ systemet.

Följande logik används av Server utvärderingen för att identifiera Azure-beredskap baserat på operativ systemet.

**Operativ system** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
Windows Server 2016 & alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2012 R2 & alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2012 & alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2008 R2 med alla SPs | Azure ger fullständig support.| Redo för Azure
Windows Server 2008 (32-bitars och 64-bitars) | Azure ger fullständig support. | Redo för Azure
Windows Server 2003, 2003 R2 | Dessa operativ system har passerat sitt slutdatum och behöver ett [anpassat support avtal (CSA)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt redo för Azure. Överväg att uppgradera operativ systemet innan du migrerar till Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Dessa operativ system har passerat sitt slut för ande support datum. Datorn kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Windows-klient 7, 8 och 10 | Azure har endast stöd för [Visual Studio-prenumeration.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Villkorligt redo för Azure
Windows 10 Pro Desktop | Azure har stöd för [värd rättigheter för flera innehavare.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Villkorligt redo för Azure
Windows Vista, XP Professional | Dessa operativ system har passerat sitt slut för ande support datum. Datorn kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Linux | Azure godkänner dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men vi rekommenderar att du uppgraderar operativ systemet till en godkänd version innan du migrerar till Azure. | Redo för Azure om versionen är påtecknad.<br/><br/>Villkorligt klar om versionen inte har påbörjats.
Andra operativ system<br/><br/> Till exempel Oracle Solaris, Apple macOS osv., FreeBSD osv. | Azure förser inte dessa operativ system. Datorn kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du installerar ett operativ system som stöds innan du migrerar till Azure.  
Operativ systemet har angetts som **annat** i vCenter Server | Azure Migrate kan inte identifiera operativ systemet i det här fallet. | Okänd beredskap. Kontrol lera att operativ systemet som körs i den virtuella datorn stöds i Azure.
32-bitars operativ system | Datorn kan starta i Azure, men Azure kanske inte ger fullständig support. | Villkorligt redo för Azure. Överväg att uppgradera datorns operativ system från 32-bitars operativ system till 64-bitars operativ system innan du migrerar till Azure.

## <a name="calculate-sizing-as-is-on-premises"></a>Beräkna storlek (lokalt)

När datorn har marker ATS som klar för Azure, gör Server utvärderingen det möjligt att identifiera Azure VM och disk SKU. Om du använder samma storlek som lokal storlek, anser Server utvärderingen inte prestanda historiken för de virtuella datorerna och diskarna.

**Beräknings storlek**: den allokerar en Azure VM-SKU baserat på den storlek som tilldelas lokalt.
**Storlek för lagring/disk**: Server utvärdering kontrollerar den lagrings typ som anges i bedömnings egenskaper (standard HDD/SSD/Premium) och rekommenderar disk typen. Standard lagrings typen är Premium diskar.
**Nätverks storlek**: Server utvärderingen tar hänsyn till nätverkskortet på den lokala datorn.


## <a name="calculate-sizing-performance-based"></a>Beräkna storlek (prestanda-baserad)

När en dator har marker ATS som klar för Azure, om du använder prestanda-och-storleks förändring, gör Server utvärderingen att storleks rekommendationerna blir följande:

- Server utvärderingen beaktar datorns prestanda historik för att identifiera VM-storlek och disk typ i Azure.
- Om servrarna har importer ATS med en CSV-fil används de värden du anger. Den här metoden är särskilt användbar om du har överallokerat den lokala datorn, om användningen verkligen är låg och du vill ha rätt storlek på den virtuella datorn i Azure för att spara kostnader. 
- Om du inte vill använda prestanda data återställer du storleks kriterierna till som-är lokalt, enligt beskrivningen i föregående avsnitt.

### <a name="calculate-storage-sizing"></a>Beräkna lagrings storlek

Vid lagrings storlek försöker Azure Migrate mappa varje disk som är ansluten till datorn till en disk i Azure och fungerar på följande sätt:

1. Server utvärderingen lägger till Läs-och skriv-IOPS för en disk för att få total IOPS som krävs. På samma sätt lägger den till Skriv-och Skriv data flödes värden för att hämta det totala data flödet för varje disk.
2. Om du har angett lagrings typen som automatisk, baserat på effektiva IOPS-och data flödes värden, avgör Server utvärderingen om disken ska mappas till en standard-HDD, standard SSD eller en Premium-disk i Azure. Om lagrings typen är inställd på Standard HDD/SSD/Premium försöker server utvärderingen hitta en disk-SKU i den valda lagrings typen (Standard HDD/SSD/Premium Disks).
3. Diskarna är markerade enligt följande:
    - Om Server utvärderingen inte kan hitta en disk med nödvändiga IOPS och data flöde markerar den datorn som olämplig för Azure.
    - Om Server utvärderingen hittar en uppsättning lämpliga diskar väljer den de diskar som har stöd för den plats som anges i utvärderings inställningarna.
    - Om det finns flera tillgängliga diskar väljer Server utvärderingen disken med den lägsta kostnaden.
    - Om prestanda data för en disk inte är tillgänglig används diskens konfigurations data (disk storlek) för att hitta en standard SSD-disk i Azure.

### <a name="calculate-network-sizing"></a>Beräkna nätverks storlek

Server utvärderingen försöker hitta en virtuell Azure-dator som har stöd för antalet nätverkskort som är anslutna till den lokala datorn och de prestanda som nätverkskorten kräver.
- För att få effektiv nätverks prestanda för den lokala virtuella datorn sammanställer Server utvärderingen de data som skickas per sekund (Mbit/s) av datorn (ut från nätverket), över alla nätverkskort och använder komfort faktorn. Den använder det här numret för att hitta en virtuell Azure-dator som har stöd för nödvändiga nätverks prestanda.
- Tillsammans med nätverks prestanda anser Server utvärderingen även om den virtuella Azure-datorn har stöd för det antal nätverkskort som krävs.
- Om det inte finns några data för nätverks prestanda, tar Server utvärderingen endast hänsyn till antalet nätverkskort för storleks ändring av virtuella datorer.


### <a name="calculate-compute-sizing"></a>Beräkna beräknings storlek

När lagrings-och nätverks kraven har beräknats, tar Server utvärderingen hänsyn till processor-och minnes krav för att hitta en lämplig VM-storlek i Azure.
- Azure Migrate tittar på effektiva använda kärnor och minne för att hitta en lämplig VM-storlek i Azure.
- Om ingen lämplig storlek hittas markeras datorn som olämplig för Azure.
- Om en lämplig storlek hittas använder Azure Migrate beräkningarna för lagring och nätverk. Sedan tillämpas inställningarna för plats-och pris nivå för den slutliga rekommendationen för VM-storlek.
- Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.


### <a name="calculate-confidence-ratings"></a>Beräkna Tillförlitlighets klassificeringar

Varje prestandabaserade utvärdering i Azure Migrate associeras med en förtroende grad som sträcker sig från ett (lägst) till fem stjärnor (högst).
- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.
- Tillförlitlighets klassificeringar är inte tillämpliga för *den lokala* utvärderingen.
- För prestanda-baserad storleks kontroll behöver Server utvärderingen:
    - Användnings data för CPU-och VM-minne.
    - Disk-IOPS och data flödes data för varje disk som är ansluten till den virtuella datorn.
    - Nätverks-I/O för att hantera prestandabaserade storleks ändringar för varje nätverkskort som är kopplat till en virtuell dator.

   Om något av dessa användnings nummer är otillgängligt i vCenter Server kanske storleks rekommendationen inte är tillförlitlig.

Beroende på hur stor procent andel data punkter som är tillgängliga, så går förtroendet för utvärderingen enligt följande.

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0-20% | 1 stjärna
   21-40% | 2 stjärnor
   41-60% | 3 stjärnor
   61-80% | 4 stjärnor
   81-100% | 5 stjärnor

> [!NOTE]
> Tillförlitlighets klassificeringar har inte tilldelats utvärderingar av servrar som importer ATS med. CSV-fil i Azure Migrate. 

#### <a name="low-confidence-ratings"></a>Värderingar med låg exakthet

Här följer några skäl till varför en utvärdering kan få en låg exakthet:

- Du har inte profilerat din miljö under den tid som du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta i minst en dag efter att du har startat identifieringen för alla data punkter som ska samlas in.
- Vissa virtuella datorer stängdes ned under den period då utvärderingen beräknades. Om några virtuella datorer är inaktiverade under en viss tid kan Server utvärderingen inte samla in prestanda data för den perioden.
- Vissa virtuella datorer skapades under den period då utvärderingen beräknades. Om du till exempel har skapat en utvärdering för prestanda historiken för den senaste månaden, men vissa virtuella datorer endast har skapats i miljön för en vecka sedan, så finns det inga prestanda historik för de nya virtuella datorerna för den fullständiga varaktigheten.

> [!NOTE]
> Om en utvärderings grad är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag för att enheten ska profilera miljön och sedan beräkna utvärderingen på annat sätt. Om du inte gör det kanske prestandabaserade storleks ändringar inte är pålitliga. I så fall rekommenderar vi att du växlar utvärderingen till lokal storlek.

## <a name="calculate-monthly-costs"></a>Beräkna månads kostnader

När storleks rekommendationerna har slutförts beräknar Azure Migrate beräknings-och lagrings kostnader för efter migreringen.

- **Beräknings kostnad**: om du använder den rekommenderade storleken för virtuella Azure-datorer använder Azure Migrate fakturerings-API: et för att beräkna månads kostnaden för den virtuella datorn.
    - Beräkningen använder operativ systemet, Software Assurance, reserverade instanser, VM-drift tid, plats och valuta inställningar i kontot.
    - Den sammanställer kostnaden på alla datorer för att beräkna den totala månads beräknings kostnaden.
- **Lagrings kostnad**: kostnaden för den månatliga lagringen för en dator beräknas genom att aggregera månads kostnaden för alla diskar som är anslutna till datorn enligt följande:
    - Server utvärderingen beräknar den totala lagrings kostnaden per månad genom att aggregera lagrings kostnaderna för alla datorer.
    - För närvarande anser beräkningen inte några erbjudanden som anges i utvärderings inställningarna.

Kostnaderna visas i den valuta som anges i utvärderings inställningarna.


## <a name="next-steps"></a>Nästa steg

[Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar. 
