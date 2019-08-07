---
title: Bedömnings beräkningar i Azure Migrate | Microsoft Docs
description: Innehåller en översikt över bedömnings beräkningar i Azure Migrates tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 4511c42514a5399d41029b61297bd4c1b0b63d9a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827540"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Bedömnings beräkningar i Azure Migrate

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar. Den spårar också dina privata och offentliga moln instanser till Azure. Hubben erbjuder Azure Migrate verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV).

Server utvärdering är ett verktyg i Azure Migrate som bedömer lokala servrar för migrering till Azure. Den här artikeln innehåller information om hur utvärderingar beräknas.

## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Anger den Azure-plats som du vill migrera till.<br/><br/>Server utvärderingen stöder för närvarande dessa mål regioner: Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra, östra USA, östra 2; USA, Tyskland, centrala, Tyskland nordöstra, Östra Japan, västra Japan, centrala Korea, Nord Korea Centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra, US Gov, Arizona, US Gov, Texas, US Gov, Virginia, västra centrala USA, Västeuropa, västra Indien, västra USA och västra 2; USA.
**Lagringstyp** | Anger vilken typ av diskar som du vill använda för lagring i Azure. <br/><br/> För lokal storleks ändring kan du ange typen av mål lagrings disk som Premium-hanterad, Standard SSD-hanterad eller Standard HDD-hanterad. För prestandabaserade storleks ändringar kan du ange typen av mål lagrings disk som automatisk, Premium-hanterad, Standard HDD-hanterad eller Standard SSD-hanterad. När du anger lagrings typen som automatisk, baseras disk rekommendationen på diskens prestanda data: in-/utdata-åtgärder per sekund (IOPS) och data flöde. <br/><br/>Om du anger lagrings typen som Premium eller standard kommer utvärderingen att rekommendera en disk-SKU i den valda lagrings typen. Om du vill uppnå ett service avtal för virtuell dator med 99,9% kanske du vill ange lagrings typ som Premium-hanterade diskar. Detta säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar. Observera att Azure Migrate endast stöder hanterade diskar för migreringsutvärdering.
**Reserverade instanser (RIs)** | Med den här egenskapen kan du ange [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure. Kostnads uppskattningar i utvärderingen tar sedan med i beräkningen av RI-rabatter. Fjärrinstallationstjänster stöds för närvarande endast för betala per användning-erbjudanden i Azure Migrate.
**Storleks villkor** | Anger de kriterier som ska användas för att *ändra storlek* på virtuella datorer i Azure. Du kan välja *prestandabaserade* storleks ändringar eller storlek på de virtuella datorerna *som lokala* utan att behöva beakta prestanda historiken.
**Prestandahistorik** | Ställer in varaktigheten för att utvärdera prestanda data för datorer. Den här egenskapen gäller bara när storleks kriteriet är *prestanda-baserat*.
**Percentilutnyttjande** | Anger percentilvärdet för den prestanda exempel uppsättning som ska beaktas för höger storlek. Den här egenskapen gäller bara när storleken är prestanda-baserad.
**VM-serie** | Gör att du kan ange den VM-serie som du vill ta hänsyn till i rätt storlek. Om du till exempel har en produktions miljö som du inte planerar att migrera till virtuella datorer i A-serien i Azure, kan du undanta en-serien från listan eller serien och rätt storlek görs bara i den valda serien.
**Komfortfaktor** | Azure Migrate Server-utvärderingen anses vara en buffert (bekvämlighets faktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor.
**Erbjudande** | Visar [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Visar fakturerings valutan för ditt konto.
**Rabatt (%)** | Visar en lista över den prenumerations information som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte körs 24 timmar per dag, 7 dagar i veckan i Azure, kan du ange varaktigheten (antalet dagar per månad och antal timmar per dag) som de ska köras för, och kostnads uppskattningarna hanteras därefter. Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. Standardinställningen är ja.

## <a name="how-are-assessments-calculated"></a>Hur beräknas utvärderingarna?

En utvärdering i Azure Migrate Server-utvärderingen beräknas med hjälp av de metadata som samlats in om de lokala servrarna. Bedömnings beräkningen hanteras i tre steg. För varje-server börjar bedömnings beräkningen med en Azure-undersöknings analys, följt av storlek och slutligen en uppskattning per månad. En server flyttas bara till ett senare steg om den tidigare har passerat. Om en server till exempel inte uppfyller Azures lämpligaste kontroll, är den markerad som olämplig för Azure, och storleks sortering och kostnads redovisning görs inte för den servern.

## <a name="azure-suitability-analysis"></a>Analys av lämplighet i Azure

Alla datorer är inte lämpliga att köra i Azure. Server utvärderingen bedömer varje lokal dator för lämplighet för Azure-migrering. Den tilldelar också varje utvärderad dator till någon av följande kategorier för lämplighet:
- **Redo för Azure**: Datorn kan migreras som den är till Azure utan några ändringar. Det kommer att starta i Azure med fullständig support för Azure.
- **Villkorligt redo för Azure**: Datorn kan starta i Azure men kanske inte har fullständig support för Azure. En dator som kör en äldre version av Windows Server stöds till exempel inte i Azure. Du måste vara försiktig innan du migrerar de här datorerna till Azure och följer den reparations vägledning som föreslås i utvärderingen för att åtgärda beredskaps problemen.
- **Inte redo för Azure**: Datorn kommer inte att starta i Azure. Om till exempel en lokal dator har en disk på mer än 64 terabyte (TB) som är ansluten till den, kan den inte finnas på Azure. Du måste följa den reparations vägledning som föreslås i utvärderingen för att åtgärda beredskaps problemet innan du migrerar datorn till Azure. Höger-och kostnads uppskattning görs inte för datorer som marker ATS som ej redo för Azure.
- **Beredskap okänd**: Azure Migrate inte att fastställa datorns beredskap på grund av otillräckliga metadata som samlats in från den lokala miljön.

Server utvärderingen granskar datorns egenskaper och gäst operativ systemet för att fastställa Azure-beredskap för den lokala datorn.

### <a name="machine-properties"></a>Dator egenskaper

Server utvärderingen granskar följande egenskaper för den lokala virtuella datorn för att avgöra om den kan köras på Azure.

**Egenskap** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
**Start typ** | Azure har stöd för virtuella datorer med en start typ av BIOS, inte UEFI. | Villkorligt redo om start typen är UEFI.
**Kärnor** | Antalet kärnor på datorerna måste vara lika med eller mindre än det maximala antalet kärnor (128) som stöds för en virtuell Azure-dator.<br/><br/> Om prestanda historiken är tillgänglig, Azure Migrate beakta de använda kärnorna för jämförelse. Om en bekvämlighets faktor anges i utvärderings inställningarna multipliceras antalet använda kärnor av den praktiska faktorn.<br/><br/> Om det inte finns någon prestanda historik använder Azure Migrate tilldelade kärnor utan att använda den praktiska faktorn. | Redo om det är mindre än eller lika med begränsningar.
**Minnesoptimerade** | Datorns minnes storlek måste vara lika med eller mindre än det maximala minne (3892 gigabyte [GB] i Azure M-serien&nbsp;Standard_M128m<sup>2</sup>) som tillåts för en virtuell Azure-dator. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om prestanda historiken är tillgänglig kan Azure Migrate använda det använda minnet för jämförelse. Om en bekvämlighets faktor anges multipliceras det använda minnet av den praktiska faktorn.<br/><br/> Om det inte finns någon historik används allokerat minne utan att du använder den praktiska faktorn.<br/><br/> | Redo om det är inom gränserna.
**Lagrings disk** | Den allokerade storleken på en disk måste vara 32 TB eller mindre. Även om Azure stöder 64 TB-diskar med Ultra SSD diskar Azure Migrate: Server utvärderingen kontrollerar för närvarande 32 TB som disk storleks gränser eftersom den inte stöder Ultra SSD än. <br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disken. | Redo om det är inom gränserna.
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
Andra operativ system<br/><br/> Till exempel, Oracle Solaris, Apple Mac OS osv., FreeBSD osv. | Azure förser inte dessa operativ system. Datorn kan starta i Azure, men Azure ger inget operativ system stöd. | Villkorligt redo för Azure. Vi rekommenderar att du installerar ett operativ system som stöds innan du migrerar till Azure.  
Operativ systemet har angetts som **annat** i vCenter Server | Azure Migrate kan inte identifiera operativ systemet i det här fallet. | Okänd beredskap. Kontrol lera att operativ systemet som körs i den virtuella datorn stöds i Azure.
32-bitars operativ system | Datorn kan starta i Azure, men Azure kanske inte ger fullständig support. | Villkorligt redo för Azure. Överväg att uppgradera datorns operativ system från 32-bitars operativ system till 64-bitars operativ system innan du migrerar till Azure.

## <a name="sizing"></a>Storleksändring

När en dator har marker ATS som klar för Azure, gör Server utvärderingen ett storleks rekommendationer, vilket innefattar att identifiera rätt Azure VM och disk-SKU för den lokala virtuella datorn. De här rekommendationerna varierar beroende på vilka bedömnings egenskaper som anges.

- Om utvärderingen använder *prestandabaserade storleks*ändringar, förbrukar Azure Migrate datorns prestanda historik för att identifiera VM-storleken och disk typen i Azure. Den här metoden är särskilt användbar om du har överallokerat den lokala virtuella datorn, men användningen är låg och du vill ha rätt storlek på den virtuella datorn i Azure för att spara kostnader. Den här metoden hjälper dig att optimera storlekarna under migreringen.
- Om du inte vill tänka på prestanda data för storleks ändring av virtuella datorer och vill ta med de lokala datorerna som de är i Azure, kan du ställa in storleks kriterierna till *som lokalt*. Sedan kommer Server utvärderingen att ändra de virtuella datorerna baserat på den lokala konfigurationen utan att ta hänsyn till användnings data. I det här fallet baseras disk storleks aktiviteter på den lagrings typ som du anger i utvärderings egenskaperna (Standard HDD, Standard SSD eller Premium diskar).

### <a name="performance-based-sizing"></a>Prestanda-baserad storlek

För prestandabaserade storleks ändringar börjar Server utvärderingen med de diskar som är anslutna till den virtuella datorn följt av nätverkskort. Sedan mappas en Azure VM-SKU baserat på beräknings kraven för den lokala virtuella datorn. Azure Migrates enheten profiler den lokala miljön för att samla in prestanda data för CPU, minne, diskar och nätverkskort.

**Steg för insamling av prestanda data:**

1. För virtuella VMware-datorer samlar Azure Migrate-installationen in en exempel punkt i real tid med varje 20-sekunders intervall. För virtuella Hyper-V-datorer samlas exempel punkten i real tid in med varje 30 sekunders intervall.
1. Enheten samlar in de exempel punkter som samlats in var tionde minut och skickar det högsta värdet för de senaste 10 minuterna till Server utvärderingen.
1. Server utvärderingen lagrar alla exempel punkter på 10 minuter under den senaste månaden. Beroende på vilka bedömnings egenskaper som anges för *prestanda historik* och percentiler, identifierar den lämplig data punkt som ska användas för höger storlek. Om prestanda historiken till exempel är inställd på 1 dag och percentilvärdet är den 95: e percentilen, använder Server utvärderingen 10 minuters exempel punkter under den senaste dagen, sorterar dem i stigande ordning och väljer värdet för 95 percentilvärdet för höger storlek.
1. Det här värdet multipliceras med den praktiska faktorn för att få en effektiv prestanda användnings data för varje mått (processor belastning, minnes användning, disk-IOPS (läsning och skrivning), disk data flöde (läsning och skrivning) och nätverks data flöde (in och ut) som enheten samlas in.

När det effektiva användning svärdet har fastställts hanteras lagring, nätverk och beräknings storlek enligt följande.

**Lagrings storlek**: Azure Migrate försöker mappa varje disk som är ansluten till datorn till en disk i Azure.

> [!NOTE]
> Azure Migrate Server utvärderingen stöder endast hanterade diskar för utvärdering.

  - Server utvärderingen lägger till Läs-och skriv-IOPS för en disk för att få total IOPS som krävs. På samma sätt lägger den till Skriv-och Skriv data flödes värden för att hämta det totala data flödet för varje disk.
  - Om du har angett lagrings typen som automatisk, baserat på effektiva IOPS-och data flödes värden, avgör Server utvärderingen om disken ska mappas till en standard-HDD, standard SSD eller en Premium-disk i Azure. Om lagrings typen är inställd på Standard HDD/SSD/Premium försöker server utvärderingen hitta en disk-SKU i den valda lagrings typen (Standard HDD/SSD/Premium Disks).
  - Om Server utvärderingen inte kan hitta en disk med nödvändiga IOPS och data flöde markerar den datorn som olämplig för Azure.
  - Om Server utvärderingen hittar en uppsättning lämpliga diskar väljer den de diskar som har stöd för den plats som anges i utvärderings inställningarna.
  - Om det finns flera tillgängliga diskar väljer Server utvärderingen disken med den lägsta kostnaden.
  - Om prestanda data för en disk inte är tillgänglig används diskens konfigurations data (disk storlek) för att hitta en standard SSD-disk i Azure.

**Nätverks storlek**: Server utvärderingen försöker hitta en virtuell Azure-dator som har stöd för antalet nätverkskort som är anslutna till den lokala datorn och de prestanda som nätverkskorten kräver.
- För att få effektiv nätverks prestanda för den lokala virtuella datorn sammanställer Server utvärderingen de data som skickas per sekund (Mbit/s) av datorn (ut från nätverket), över alla nätverkskort och använder komfort faktorn. Den använder det här numret för att hitta en virtuell Azure-dator som har stöd för nödvändiga nätverks prestanda.
- Tillsammans med nätverks prestanda anser Server utvärderingen även om den virtuella Azure-datorn har stöd för det antal nätverkskort som krävs.
- Om det inte finns några data för nätverks prestanda, tar Server utvärderingen endast hänsyn till antalet nätverkskort för storleks ändring av virtuella datorer.

**Beräknings storlek**: När lagrings-och nätverks kraven har beräknats, tar Server utvärderingen hänsyn till processor-och minnes krav för att hitta en lämplig VM-storlek i Azure.
- Azure Migrate tittar på effektiva använda kärnor och minne för att hitta en lämplig VM-storlek i Azure.
- Om ingen lämplig storlek hittas markeras datorn som olämplig för Azure.
- Om en lämplig storlek hittas använder Azure Migrate beräkningarna för lagring och nätverk. Sedan tillämpas inställningarna för plats-och pris nivå för den slutliga rekommendationen för VM-storlek.
- Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.

### <a name="as-on-premises-sizing"></a>Som lokal storlek

Om du använder *som lokal storlek*, anser Server utvärderingen inte prestanda historiken för de virtuella datorerna och diskarna. I stället allokeras en VM-SKU i Azure baserat på den storlek som tilldelas lokalt. På samma sätt som för disk storlek, kontrollerar Server utvärderingen den lagrings typ som anges i bedömnings egenskaper (Standard HDD/SSD/Premium) och rekommenderar disk typen. Standard lagrings typen är Premium diskar.

## <a name="confidence-ratings"></a>Tillförlitlighets klassificering
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

### <a name="low-confidence-ratings"></a>Värderingar med låg exakthet

Här följer några skäl till varför en utvärdering kan få en låg exakthet:

- Du har inte profilerat din miljö under den tid som du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta i minst en dag efter att du har startat identifieringen för alla data punkter som ska samlas in.
- Vissa virtuella datorer stängdes ned under den period då utvärderingen beräknades. Om några virtuella datorer är inaktiverade under en viss tid kan Server utvärderingen inte samla in prestanda data för den perioden.
- Vissa virtuella datorer skapades under den period då utvärderingen beräknades. Om du till exempel har skapat en utvärdering för prestanda historiken för den senaste månaden, men vissa virtuella datorer endast har skapats i miljön för en vecka sedan, så finns det inga prestanda historik för de nya virtuella datorerna för den fullständiga varaktigheten.

> [!NOTE]
> Om en utvärderings grad är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag för att enheten ska profilera miljön och sedan beräkna utvärderingen på annat sätt. Om du inte gör det kanske prestandabaserade storleks ändringar inte är pålitliga. I så fall rekommenderar vi att du växlar utvärderingen till lokal storlek.

## <a name="monthly-cost-estimation"></a>Månatlig kostnads uppskattning

När storleks rekommendationerna har slutförts beräknar Azure Migrate beräknings-och lagrings kostnader för efter migreringen.

- **Beräknings kostnad**: Med den rekommenderade storleken för virtuella Azure-datorer använder Azure Migrate fakturerings-API: et för att beräkna månads kostnaden för den virtuella datorn.
    - Beräkningen använder operativ systemet, Software Assurance, reserverade instanser, VM-drift tid, plats och valuta inställningar i kontot.
    - Den sammanställer kostnaden på alla datorer för att beräkna den totala månads beräknings kostnaden.
- **Lagrings kostnad**: Den månatliga lagrings kostnaden för en dator beräknas genom att aggregera månads kostnaden för alla diskar som är anslutna till datorn enligt följande:
    - Server utvärderingen beräknar den totala lagrings kostnaden per månad genom att aggregera lagrings kostnaderna för alla datorer.
    - För närvarande anser beräkningen inte några erbjudanden som anges i utvärderings inställningarna.

Kostnaderna visas i den valuta som anges i utvärderings inställningarna.


## <a name="next-steps"></a>Nästa steg

Skapa en utvärdering för virtuella [VMware-datorer](tutorial-assess-vmware.md) eller [virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md).
