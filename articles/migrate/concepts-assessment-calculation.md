---
title: Bedömnings beräkningar i Azure Migrate | Microsoft Docs
description: Innehåller en översikt över bedömnings beräkningar i Azure Migrates tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234295"
---
# <a name="assessment-calculations"></a>Utvärderingsberäkningar

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga moln instanser till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

Server utvärdering är ett verktyg i Azure Migrate som bedömer lokala servrar för migrering till Azure. Den här artikeln innehåller information om hur utvärderingar beräknas.

## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/> Server utvärderingen stöder för närvarande dessa mål regioner: Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra, östra USA, östra 2; USA, Tyskland, centrala, Tyskland nordöstra, Östra Japan, västra Japan, centrala Korea, Nord Korea Centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra, US Gov, Arizona, US Gov, Texas, US Gov, Virginia, västra centrala USA, Västeuropa, västra Indien, västra USA och västra 2; USA.
**Lagringstyp** | Du kan använda den här egenskapen för att ange vilken typ av diskar du vill flytta till i Azure. <br/><br/> För lokal storleks ändring kan du ange mål lagrings typ antingen som Premium-hanterade diskar, Standard SSD-hanterade diskar eller Standard HDD-hanterade diskar. För prestandabaserade storleks ändringar kan du ange mål disk typen antingen som automatiskt, Premium-hanterade diskar, Standard HDD-hanterade diskar eller Standard SSD-hanterade diskar.<br/><br/> När du anger lagrings typen som automatisk, utförs disk rekommendationer baserat på diskens prestanda data (IOPS och data flöde). Om du anger lagrings typ som Premium/standard, rekommenderar utvärderingen en disk-SKU i den valda lagrings typen. Om du vill uppnå ett service avtal för en enda instans av virtuell dator på 99,9%, kan du ange lagrings typ som Premium-hanterade diskar. Detta säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar. Observera att Azure Migrate endast stöder hanterade diskar för migreringsutvärdering.
**Reserverade instanser (RI)** | Med den här egenskapen kan du ange om du har [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure och kostnads uppskattningar i utvärderingen sedan görs i RI-rabatter. Reserverade instanser stöds för närvarande bara för erbjudandet betala per användning i Azure Migrate.
**Ändra storlek på kriterium** | Det kriterium som ska användas för att ändra storlek på virtuella datorer i Azure. Du kan antingen göra *prestandabaserade* storleks ändringar eller ändra storlek på de virtuella datorerna *lokalt*, utan att behöva beakta prestanda historiken.
**Prestandahistorik** | Varaktigheten för att utvärdera prestanda data för datorer. Den här egenskapen gäller endast när storleks kriteriet är *prestanda-baserat*.
**Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen gäller endast när storlek är *prestanda beroende*.
**VM-serie** |     Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Om du till exempel har en produktions miljö som du inte planerar att migrera till virtuella datorer i A-serien i Azure, kan du undanta en-serien från listan eller serien och den högra storleks ändringen görs bara i den valda serien.
**Komfortfaktor** | Azure Migrate Server-utvärderingen anses vara en buffert (bekvämlighets faktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor.
**Erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) du har registrerat dig för. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Faktureringsvalutan.
**Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet.<br/> Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte kommer att köras dygnet runt i Azure, kan du ange varaktigheten (antalet dagar per månad och antalet timmar per dag) som de ska köras och kostnads uppskattningarna skulle utföras i enlighet med detta.<br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Ange om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. Standardvärdet är Ja.

## <a name="how-are-assessments-calculated"></a>Hur beräknas utvärderingarna?

En utvärdering i Azure Migrate Server-utvärderingen beräknas med hjälp av de metadata som samlats in om de lokala servrarna. Bedömnings beräkningen görs i tre steg. för varje server börjar bedömnings beräkningen med Azures analys av lämplighet, följt av storlek och slutligen en uppskattning per månad. En server flyttas bara till ett senare steg om den tidigare har passerat. Om en server till exempel inte uppfyller Azures lämpligaste kontroll, är den markerad som olämplig för Azure, och storleks sortering och kostnads redovisning görs inte för samma.

## <a name="azure-suitability-analysis"></a>Analys av lämplighet i Azure

Alla datorer är inte lämpliga för att köras i Azure. Azure Migrate Server utvärderingen bedömer var och en av de lokala datorerna för migrering av lämplighet till Azure, och kategoriserar de utvärderade datorerna till någon av följande kategorier för lämplighet:
- **Redo för Azure** – datorn kan migreras som den är till Azure utan några ändringar. Den startas i Azure med fullständig support för Azure.
- **Villkorligt redo för Azure** – datorn kan starta i Azure, men det kanske inte har fullständig support för Azure. En dator med en äldre version av Windows Server OS stöds till exempel inte i Azure. Du måste vara försiktig innan du migrerar de här datorerna till Azure och följer den reparations vägledning som föreslås i utvärderingen för att åtgärda beredskaps problemen innan du migrerar.
- **Inte redo för Azure** – datorn kommer inte att starta i Azure. Om en lokal dator till exempel har en disk med en storlek på mer än 64 TB som är kopplad till den, kan den inte finnas på Azure. Du måste följa den reparations vägledning som föreslås i utvärderingen för att åtgärda beredskaps problemet innan du migrerar till Azure. Höger-och kostnads uppskattning görs inte för datorer som marker ATS som ej redo för Azure.
- **Beredskap okänd** -Azure Migrate kunde inte hitta datorns beredskap på grund av otillräckliga metadata som samlats in från den lokala miljön.

Azure Migrate Server utvärderingen granskar datorns egenskaper och gäst operativ systemet för att identifiera Azure-beredskap för den lokala datorn.

### <a name="machine-properties"></a>Dator egenskaper

Server utvärderingen granskar följande egenskaper för den lokala virtuella datorn för att identifiera om den kan köras på Azure.

**Egenskap** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
**Start typ** | Azure har stöd för virtuella datorer med start typ som BIOS och inte UEFI. | Villkoret klart om start typen är UEFI.
**Kärnor** | Antalet kärnor på datorerna måste vara lika med eller mindre än det maximala antalet kärnor (128 kärnor) som stöds för en virtuell Azure-dator.<br/><br/> Om prestanda historiken är tillgänglig, Azure Migrate beakta de använda kärnorna för jämförelse. Om en bekvämlighets faktor anges i utvärderings inställningarna multipliceras antalet använda kärnor av den praktiska faktorn.<br/><br/> Om det inte finns någon prestanda historik använder Azure Migrate tilldelade kärnor, utan att använda den praktiska faktorn. | Redo om det är mindre än eller lika med begränsningar.
**Minnesoptimerade** | Datorns minnes storlek måste vara lika med eller mindre än det maximala minne (3892 GB på Azure M-serien&nbsp;Standard_M128m<sup>2</sup>) som tillåts för en virtuell Azure-dator. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om prestanda historiken är tillgänglig kan Azure Migrate använda det använda minnet för jämförelse. Om en bekvämlighets faktor anges multipliceras det använda minnet av den praktiska faktorn.<br/><br/> Om det inte finns någon historik används allokerat minne, utan att du använder den praktiska faktorn.<br/><br/> | Redo om det är inom gränserna.
**Lagrings disk** | Den allokerade storleken på en disk måste vara 32 TB eller mindre.<br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disken. | Redo om det är inom gränserna.
**Nätverk** | En dator måste ha 32 eller färre nätverkskort kopplade till sig. | Redo om det är inom gränserna.

### <a name="guest-operating-system"></a>Gäst operativ system
Tillsammans med VM-egenskaper kontrollerar Azure Migrate Server utvärderingen på datorns gäst operativ system för att identifiera om den kan köras på Azure.

> [!NOTE]
> För virtuella VMware-datorer använder Azure Migrate Server utvärdering det operativ system som har angetts för den virtuella datorn i vCenter Server för att göra gäst operativ system analyser. För virtuella Linux-datorer som körs på VMware kan för närvarande inte identifiera den exakta kernel-versionen av gäst operativ systemet.

Följande logik används av Azure Migrate Server-utvärdering för att identifiera Azure-beredskap baserat på operativ systemet.

**Operativ system** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
Windows Server 2016 & alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2012 R2 & alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2012 & alla SPs | Azure ger fullständig support. | Redo för Azure
Windows Server 2008 R2 med alla SPs | Azure ger fullständig support.| Redo för Azure
Windows Server 2008 (32-bitars och 64-bitars) | Azure ger fullständig support. | Redo för Azure
Windows Server 2003, 2003 R2 | Dessa operativ system har passerat slut på support datumet och behöver ett [anpassat support avtal (CSA)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt redo för Azure bör du överväga att uppgradera operativ systemet innan du migrerar till Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | De här operativ systemen har passerat slut på support datumet, datorn kan starta i Azure, men det finns inget stöd för operativ systemet i Azure. | Villkorligt redo för Azure, vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Windows-klient 7, 8 och 10 | Azure har endast stöd för [Visual Studio-prenumeration.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Villkorligt redo för Azure
Windows 10 Pro Desktop | Azure har stöd för [värd rättigheter för flera innehavare.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Villkorligt redo för Azure
Windows Vista, XP Professional | De här operativ systemen har passerat slut på support datumet, datorn kan starta i Azure, men det finns inget stöd för operativ systemet i Azure. | Villkorligt redo för Azure, vi rekommenderar att du uppgraderar operativ systemet innan du migrerar till Azure.
Linux | Azure godkänner dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men vi rekommenderar att du uppgraderar operativ systemet till en godkänd version innan du migrerar till Azure. | Redo för Azure om versionen är påtecknad.<br/><br/>Villkorligt klar om versionen inte har påbörjats.
Andra operativ system<br/><br/> Till exempel, Oracle Solaris, Apple Mac OS osv., FreeBSD osv. | Azure kan inte påteckna dessa operativ system. Datorn kan starta i Azure, men det finns inget stöd för operativ systemet i Azure. | Villkorligt redo för Azure, vi rekommenderar att du installerar ett operativ system som stöds innan du migrerar till Azure.  
Operativ systemet har angetts som **annat** i vCenter Server | Azure Migrate kan inte identifiera operativ systemet i det här fallet. | Okänd beredskap. Kontrol lera att operativ systemet som körs i den virtuella datorn stöds i Azure.
32-bitars operativ system | Datorn kan starta i Azure, men Azure har kanske inte fullständig support. | Villkorligt redo för Azure bör du överväga att uppgradera datorns operativ system från 32-bitars operativ system till 64-bitars operativ system innan du migrerar till Azure.

## <a name="sizing"></a>Storleksändring

När en dator har marker ATS som klar för Azure, utför Server utvärderingen storlek, vilket innebär att du kan identifiera rätt Azure VM och disk-SKU för den lokala virtuella datorn. Storleks rekommendationerna varierar beroende på vilka bedömnings egenskaper som anges.

- Om utvärderingen använder *prestandabaserade storleks*ändringar, förbrukar Azure Migrate datorns prestanda historik för att identifiera VM-storleken och disk typen i Azure. Den här metoden är särskilt användbar om du har överallokerat den lokala virtuella datorn, men användningen är låg och du vill ha rätt storlek på den virtuella datorn i Azure för att spara kostnader. Den här metoden hjälper dig att optimera storlekarna under migreringen.
- Om du inte vill ta hänsyn till prestanda data för VM-storlek och vill använda de lokala datorerna som de är i Azure, kan du ange storleks kriteriet som *lokalt* och Server utvärderingen storlek på de virtuella datorerna baserat på lokala konfigurationen utan att ta hänsyn till användnings data. Disk storlek, i det här fallet, görs baserat på den lagrings typ som du anger i utvärderings egenskaperna (Standard HDD diskar, Standard SSD diskar eller Premium diskar).

### <a name="performance-based-sizing"></a>Prestanda-baserad storlek

För prestandabaserade storleks ändringar börjar Azure Migrate Server utvärderingen med de diskar som är anslutna till den virtuella datorn följt av nätverkskort och mappar sedan en Azure VM-SKU baserat på beräknings kraven för den lokala virtuella datorn. Azure Migrates enheten profiler den lokala miljön för att samla in prestanda data för CPU, minne, diskar och nätverkskort.

**Insamling av prestanda data**

- För virtuella VMware-datorer samlar Azure Migrate-installationen in en exempel punkt i real tid med ett intervall om 20 sekunder, för virtuella Hyper-V-datorer. exempel punkten för real tids insamling samlas in varje 30 sekunders intervall.
- Enheten sammanställer sedan de exempel punkter som samlats in var tionde minut och skickar det högsta värdet under de senaste 10 minuterna till Azure Migrate Server bedömning.
- Azure Migrate Server bedömning lagrar alla exempel punkter på 10 minuter under den senaste månaden och beroende på de utvärderings egenskaper som anges för *prestanda historik* och *percentils användning*, identifierar den lämplig data punkt som ska användas för höger storlek. Om prestanda historiken till exempel är inställd på en dag och percentilen är 95 percentil, används 10 minuters exempel punkter för den senaste dagen, sorteras de i stigande ordning och väljer 95 percentilvärdet för höger storlek.
- Värdet ovan multipliceras sedan med den praktiska faktorn för att få en effektiv prestanda användnings data för varje mått (processor användning, minnes användning, disk-IOPS (läsning och skrivning), disk data flöde (läsning och skrivning), nätverks data flöde (in och ut)) som enheten samlas in.
- När det effektiva användning svärdet har identifierats görs beräkning, lagring och nätverks storlek på följande sätt:

**Lagrings storlek**: Azure Migrate försöker mappa varje disk som är ansluten till datorn till en disk i Azure.

> [!NOTE]
> Azure Migrate: Server utvärderingen stöder endast hanterade diskar för utvärdering.

  - Läs-och skriv-IOPS för en disk läggs till för att få det totala antalet IOPS som krävs, på samma sätt som att läsa och skriva data flödes värden läggs till för att hämta det totala data flödet för varje disk
  - Om du har angett lagrings typen som automatisk, baserat på effektiva IOPS-och data flödes värden, identifierar Azure Migrate Server utvärderingen om disken ska mappas till en standard-HDD, standard SSD eller en Premium-disk i Azure. Om lagrings typen är inställd på Standard HDD/SSD/Premium försöker den hitta en disk-SKU i den valda lagrings typen (Standard HDD/SSD/Premium Disks).
  - Om Server utvärderingen inte kan hitta en disk med den nödvändiga IOPS &-dataflödet markerar den datorn som olämplig för Azure.
  - Om en uppsättning lämpliga diskar hittas, väljs den som stöder den plats som anges i utvärderings inställningarna.
  - Om det finns flera tillgängliga diskar väljer den en med den lägsta kostnaden.
  - Om prestanda data för en disk inte är tillgängliga, används konfigurations data för disken (disk storlek) för att hitta en standard SSD-disk i Azure.

**Nätverks storlek**: Azure Migrate Server utvärderingen försöker hitta en virtuell Azure-dator som har stöd för antalet nätverkskort som är anslutna till den lokala datorn och de prestanda som nätverkskorten kräver.
    - För att få effektiv nätverks prestanda för den lokala virtuella datorn sammanställer Server utvärderingen de data som skickas per sekund (Mbit/s) av datorn (ut från nätverket), över alla nätverkskort och använder komfort faktorn. Det här numret används för att hitta en virtuell Azure-dator som har stöd för den nödvändiga nätverks prestandan.
    - Tillsammans med nätverks prestanda, anser det också om den virtuella Azure-datorn kan stödja det antal nätverkskort som krävs.
    - Om inga data för nätverks prestanda är tillgängliga, beaktas bara antalet nätverkskort för storleks ändring av virtuella datorer.

**Beräknings storlek**: När lagrings-och nätverks kraven har beräknats tar Azure Migrate Server bedömning av processor-och minnes krav för att hitta en lämplig VM-storlek i Azure.
    - Azure Migrate tittar på effektiva använda kärnor och minne för att hitta en lämplig VM-storlek i Azure.
    - Om ingen lämplig storlek hittas markeras datorn som olämplig för Azure.
    - Om en lämplig storlek hittas använder Azure Migrate beräkningarna för lagring och nätverk. Sedan tillämpas inställningarna för plats-och pris nivå för den slutliga rekommendationen för VM-storlek.
    - Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.

### <a name="as-on-premises-sizing"></a>Som lokal storlek

Om du använder som *lokal storleks ändring*, tar Server utvärderingen inte hänsyn till prestanda historiken för de virtuella datorerna och diskarna och allokerar en VM-SKU i Azure baserat på den storlek som tilldelas lokalt. På samma sätt som för disk storlek, ser den lagrings typ som anges i bedömnings egenskaper (Standard HDD/SSD/Premium) och rekommenderar disk typen. Standard lagrings typen är Premium diskar.

## <a name="confidence-ratings"></a>Tillförlitlighets klassificering
Varje prestandabaserade utvärdering i Azure Migrate associeras med en förtroende grad som sträcker sig från ett (lägst) till fem Starter (högst).
- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.
- Säkerhets betyg är inte tillämpligt för den lokala utvärderingen.
- För prestandabaserade storleks ändringar måste Azure Migrate Server bedömning:
    - Användnings data för CPU och VM-minne.
    - För varje disk som är ansluten till den virtuella datorn krävs dessutom information om IOPS och dataflöden.
    - På samma sätt för varje nätverkskort som är kopplat till en virtuell dator, behöver förtroende klassificeringen nätverks-I/O för att göra en prestanda-baserad storlek.
    - Om något av ovanstående användnings nummer inte är tillgängligt i vCenter Server kanske storleks rekommendationen inte är tillförlitlig.

Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen, som du ser nedan:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

### <a name="low-confidence-ratings"></a>Värderingar med låg exakthet

Några av orsakerna till varför en utvärdering kan få en låg exakthet:

- Du har inte profilerat din miljö under den tid som du skapar utvärderingen. Om du till exempel skapar utvärderingen med varaktigheten inställd på en dag måste du vänta i minst en dag efter att du har startat identifieringen av alla data punkter som ska samlas in.
- Vissa virtuella datorer stängdes ned under den period då utvärderingen beräknades. Om några virtuella datorer har stängts av under en viss tid kan Azure Migrate Server utvärderingen inte samla in prestanda data för den perioden.
- Vissa virtuella datorer skapades i mellan den period då utvärderingen beräknas. Om du till exempel skapar en utvärdering för prestanda historiken för den senaste månaden, men vissa virtuella datorer endast har skapats i miljön för en vecka sedan, så kommer prestanda historiken för de nya virtuella datorerna inte att vara där hela tiden.

> [!NOTE]
> Om förtroende omdömet för en utvärdering är under fem stjärnor, rekommenderar vi att du väntar minst en dag för att enheten ska profilera miljön och sedan beräkna utvärderingen på annat sätt. Om du inte gör det kanske prestandabaserade storleks ändringar inte är tillförlitliga och vi rekommenderar att du växlar utvärderingen så att den används som lokal storlek.

## <a name="monthly-cost-estimation"></a>Månatlig kostnads uppskattning

När storleks rekommendationerna har slutförts beräknar Azure Migrate beräknings-och lagrings kostnader för efter migreringen.

- **Beräknings kostnad**: Med den rekommenderade storleken för virtuella Azure-datorer använder Azure Migrate fakturerings-API: et för att beräkna månads kostnaden för den virtuella datorn.
    - Beräkningen använder operativ systemet, Software Assurance, reserverade instanser, VM-drift tid, plats och valuta inställningar i kontot.
    - Den sammanställer kostnaden för alla datorer, för att beräkna den totala månads beräknings kostnaden.
- **Lagrings kostnad**: Den månatliga lagrings kostnaden för en dator beräknas genom att aggregera månads kostnaden för alla diskar som är anslutna till datorn
    - Azure Migrate Server utvärderingen beräknar den totala lagrings kostnaden per månad genom att aggregera lagrings kostnaderna för alla datorer.
    - För närvarande tar beräkningen inte med erbjudanden som anges i bedömnings inställningarna i kontot.

Kostnaderna visas i den valuta som anges i utvärderings inställningarna.


## <a name="next-steps"></a>Nästa steg

Skapa en utvärdering för virtuella [VMware-datorer](tutorial-assess-vmware.md) eller [virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md).
