---
title: Utvärderingar i utvärdering av Azure Migrate Server
description: Lär dig mer om utvärderingar i Utvärdering av Azure Migrate Server
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769926"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Utvärderingar i Azure Migrera: Serverutvärdering

Den här artikeln innehåller en översikt över utvärderingar i [verktyget Azure Migrate: Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Verktyget kan bedöma lokala virtuella VMware-datorer, virtuella hyper-virtuella datorer och fysiska servrar för migrering till Azure.

## <a name="whats-an-assessment"></a>Vad är en bedömning?

En utvärdering med verktyget Serverutvärdering mäter beredskapen och beräknar effekten av att migrera lokala servrar till Azure.

> [!NOTE]
> I Azure Government granskar du de [målutvärderingsplatser som stöds.](migrate-support-matrix.md#supported-geographies-azure-government) Observera att rekommendationer för vm-storlek i utvärderingar använder VM-serien specifikt för government cloud-regioner. [Läs mer](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) om vm-typer.

## <a name="types-of-assessments"></a>Typer av bedömningar

Utvärderingar som du skapar med Serverutvärdering är en ögonblicksbild av data i viss tid. Serverutvärdering innehåller två typer av utvärderingar.

**Typ av bedömning** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar som gör rekommendationer baserade på insamlade resultatdata | Rekommendationen vm-storlek baseras på CPU- och RAM-användningsdata.<br/><br/> Disk-typ rekommendationen baseras på indata/utdataåtgärder per sekund (IOPS) och dataflöde för lokala diskar. Disktyper är Azure Standard HDD, Azure Standard SSD och Azure Premium-diskar.
**Som är lokalt** | Bedömningar som inte använder prestandadata för att ge rekommendationer | Storleksrekommendationen för virtuella datorer baseras på den lokala vm-storleken.<br/><br> Den rekommenderade disktypen baseras på den valda lagringstypen för utvärderingen.

## <a name="how-do-i-run-an-assessment"></a>Hur kör jag en bedömning?

Det finns ett par sätt att göra en bedömning.

- Utvärdera datorer med hjälp av servermetadata som samlas in av en lätt Azure Migrate-installation. Apparaten upptäcker lokala maskiner. Den skickar sedan datormetadata och prestandadata till Azure Migrate.
- Utvärdera datorer med hjälp av servermetadata som importeras i ett CSV-format (comma-separated values).

## <a name="how-do-i-assess-with-the-appliance"></a>Hur bedömer jag med apparaten?

Om du distribuerar en Azure Migrate-installation för att identifiera lokala servrar gör du följande:

1. Konfigurera Azure och din lokala miljö så att de fungerar med Serverutvärdering.
1. Skapa ett Azure-projekt och lägga till serverbedömningsverktyget i den för den första utvärderingen.
1. Distribuera en lätt Azure Migrate-apparat. Installationen identifierar kontinuerligt lokala datorer och skickar datorns metadata och prestandadata till Azure Migrate. Distribuera apparaten som en virtuell dator eller en fysisk dator. Du behöver inte installera något på maskiner som du vill bedöma.

När installationen börjar datoridentifiera kan du samla in datorer som du vill utvärdera i en grupp och köra en utvärdering för gruppen.

Följ våra tutorials för [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)eller [fysiska servrar](tutorial-prepare-physical.md) för att prova dessa steg.

## <a name="how-do-i-assess-with-imported-data"></a>Hur bedömer jag med importerade data?

Om du bedömer servrar med hjälp av en CSV-fil behöver du ingen installation. Gör i stället följande steg:

1. Konfigurera Azure så att det fungerar med Serverutvärdering.
1. Skapa ett Azure-projekt och lägga till serverbedömningsverktyget i den för den första utvärderingen.
1. Ladda ner en CSV-mall och lägg till serverdata i den.
1. Importera mallen till Serverutvärdering.
1. Upptäck servrar som läggs till med importen, samla dem i en grupp och kör en utvärdering för gruppen.

## <a name="what-data-does-the-appliance-collect"></a>Vilka data samlar apparaten in?

Om du använder Azure Migrate-enheten för bedömning kan du läsa mer om metadata och prestandadata som samlas in för [VMware](migrate-appliance.md#collected-data---vmware) och [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hur beräknar apparaten prestandadata?

Om du använder installationen för identifiering samlar den in prestandadata för beräkningsinställningar med följande steg:

1. Apparaten samlar in en provpunkt i realtid.

    - **Virtuella datorer med vmware:** En exempelpunkt samlas in var 20:e sekund.
    - **Hyper-V virtuella datorer:** En exempelpunkt samlas in var 30:e sekund.
    - **Fysiska servrar**: En exempelpunkt samlas in var femte minut.

1. Apparaten kombinerar provpunkterna för att skapa en enda datapunkt var 10:e minut. Om du vill skapa datapunkten väljer apparaten toppvärdena från alla exempel. Den skickar sedan datapunkten till Azure.
1. Serverbedömning lagrar alla 10-minuters datapunkter för den senaste månaden.
1. När du skapar en utvärdering identifierar Serverutvärdering lämplig datapunkt som ska användas för att rightsizing. Identifieringen baseras på percentilvärdena för *prestandahistorik* och *percentilutnyttjande*.

    - Om prestandahistoriken till exempel är en vecka och percentilutnyttjandet är den 95:e percentilen sorterar Serverutvärdering av 10-minutersprovpunkterna för den senaste veckan. Den sorterar dem i stigande ordning och plockar den 95: e percentilen värde för rightsizing.
    - Det 95:e percentilvärdet ser till att du ignorerar eventuella extremvärden, vilket kan inkluderas om du valde den 99:e percentilen.
    - Om du vill välja maximal användning för perioden och inte vill missa några avvikare väljer du den 99:e percentilen för percentilutnyttjande.

1. Det här värdet multipliceras med komfortfaktorn för att få effektiva prestandautnyttjandedata för dessa mätvärden som enheten samlar in:

    - CPU-användning
    - RAM-användning
    - Disk IOPS (läs och skriv)
    - Diskdataflöde (läsa och skriva)
    - Nätverksdataflöde (in och ut)

## <a name="how-are-assessments-calculated"></a>Hur beräknas bedömningarna?

Serverutvärdering använder de lokala datorernas metadata och prestandadata för att beräkna utvärderingar. Om du distribuerar Azure Migrate-enheten använder utvärderingen de data som enheten samlar in. Men om du kör en bedömning som importeras med hjälp av en CSV-fil anger du metadata för beräkningen.

Beräkningarna sker i dessa tre steg:

1. **Beräkna Azure-beredskap:** Utvärdera om datorer är lämpliga för migrering till Azure.
1. **Beräkna storleksrekommendationer:** Uppskatta beräkning, lagring och nätverksstorlek.
1. **Beräkna månadskostnader**: Beräkna uppskattade månatliga beräknings- och lagringskostnader för att köra datorerna i Azure efter migreringen.

Beräkningarna är i föregående ordning. En maskinserver flyttas till ett senare skede endast om den passerar den föregående. Om en server till exempel misslyckas i Azure-beredskapsfasen markeras den som olämplig för Azure. Storleks- och kostnadsberäkningar görs inte för den servern.

## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

Det här ingår i en utvärdering i Serverutvärdering:

Egenskap | Information
--- | ---
**Målplats** | Den plats som du vill migrera till. Serverutvärdering stöder för närvarande dessa Azure-målregioner:<br/><br/> Australien Öst, Australien Sydost, Brasilien Syd, Kanada Central, Kanada Öst, Centralasien, Centrala USA, Kina Öst, Kina Nord, Östra Asien, Östra USA, Östra USA 2, Tyskland Central, Tyskland Nordost, Japan Öst, Japan Väst, Korea Central, Korea Syd, Norra centrala USA, Nordeuropa, Södra centrala USA, Sydostasien, Södra Indien, Storbritannien Syd, Storbritannien Väst, US Gov Arizona, US Gov Texas, US Gov Virginia , Västra centrala USA, Västeuropa, Västra Indien, västra USA och västra USA 2.
**Mållagringsdisk (storlekssortering)** | Den typ av disk som ska användas för lagring i Azure. <br/><br/> Ange mållagringsdisken som Premium-hanterad, Standard SSD-hanterad eller Standard HDD-hanterad.
**Mållagringsdisk (prestandabaserad storlek)** | Anger vilken typ av mållagringsdisk som automatisk, Premium-hanterad, Standard HDD-hanterad eller Standard SSD-hanterad.<br/><br/> **Automatisk**: Diskrekommendationen baseras på diskarnas prestandadata, vilket innebär IOPS och dataflöde.<br/><br/>**Premium eller Standard**: Utvärderingen rekommenderar en disk SKU inom den valda lagringstypen.<br/><br/> Om du vill ha ett SERVICEAVTAL PÅ 99,9 % för en instans på VM-servicenivå kan du använda Premium-hanterade diskar. Den här användningen säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar.<br/><br/> Azure Migrate stöder endast hanterade diskar för migreringsutvärdering.
**Azure reserverade instanser för virtuella datorer** | Anger [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) så att kostnadsuppskattningar i bedömningen tar hänsyn till dem.<br/><br/> Azure Migrate stöder för närvarande Azure Reserved VM-instanser endast för erbjudanden som gäller för användningsbaserad betalning.
**Storlekskriterier** | Används för att rightsize den virtuella Azure-datorn.<br/><br/> Använd storleksändring eller prestandabaserad storlek.
**Prestandahistorik** | Används med prestandabaserad storlek. Prestandahistorik anger den varaktighet som används när prestandadata utvärderas.
**Percentilutnyttjande** | Används med prestandabaserad storlek. Percentilutnyttjande anger percentilvärdet för prestandaexemplet som används för att rightsizing.
**VM-serie** | Azure VM-serien som du vill överväga för att rätta till. Om du till exempel inte har en produktionsmiljö som behöver virtuella datorer i A-serien i Azure kan du utesluta A-serier från listan över serier.
**Komfortfaktor** | Den buffert som används vid bedömning. Den tillämpas på processor-, RAM-, disk- och nätverksanvändningsdata för virtuella datorer. Den står för problem som säsongsanvändning, kort prestandahistorik och sannolikt ökningar i framtida användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % utnyttjande normalt i en virtuell dator med två kärnor. Med en komfortfaktor på 2,0 är resultatet en virtuell dator med fyra kärnor istället.
**Erbjudande** | [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) där du är registrerad. Serverbedömningen beräknar kostnaden för det erbjudandet.
**Valuta** | Faktureringsvalutan för ditt konto.
**Rabatt (%)** | Alla prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet. Standardinställningen är 0%.
**VM-drifttid** | Varaktigheten i dagar per månad och timmar per dag för virtuella Azure-datorer som inte körs kontinuerligt. Kostnadsuppskattningar baseras på den varaktigheten.<br/><br/> Standardvärdena är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har programvarusäkring och är berättigad till [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen har standardvärdet "Ja" beaktas Azure-priser för andra operativsystem än Windows för virtuella Windows-datorer.

[Granska metodtipsen](best-practices-assessment.md) för att skapa en utvärdering med serverutvärdering.

## <a name="calculate-readiness"></a>Beräkna beredskap

Alla datorer är inte lämpliga att köra i Azure. Serverbedömning bedömer alla lokala datorer och tilldelar dem en beredskapskategori.

- **Klar för Azure:** Datorn kan migreras som den är till Azure utan några ändringar. Det börjar i Azure med fullständigt Azure-stöd.
- **Villkorligt klar för Azure**: Datorn kan starta i Azure men kanske inte har fullt Azure-stöd. Azure stöder till exempel inte en dator som kör en gammal version av Windows Server. Du måste vara försiktig innan du migrerar dessa datorer till Azure. För att åtgärda eventuella beredskapsproblem, följ den reparationsvägledning som utvärderingen föreslår.
- **Inte redo för Azure:** Datorn startar inte i Azure. Om till exempel en lokal dators disk lagrar mer än 64 TB kan Azure inte vara värd för datorn. Följ reparationsvägledningen för att åtgärda problemet före migreringen.
- **Okänd beredskap**: Azure Migrate kan inte avgöra datorns beredskap på grund av otillräckliga metadata.

För att beräkna beredskapen granskar Serverutvärdering de datoregenskaper och operativsystemets inställningar som sammanfattas i följande tabeller.

### <a name="machine-properties"></a>Maskinegenskaper

Serverutvärdering granskar följande egenskaper för en lokal virtuell dator för att avgöra om den kan köras på Azure.

Egenskap | Information | Azure-beredskapsstatus
--- | --- | ---
**Starttyp** | Azure stöder virtuella datorer med en starttyp av BIOS, inte UEFI. | Villkorligt klar om starttypen är UEFI
**Kärnor** | Varje dator får inte ha fler än 128 kärnor, vilket är det maximala antalet som en Azure VM stöder.<br/><br/> Om prestandahistorik är tillgänglig tar Azure Migrate hänsyn till de använda kärnorna som jämförelse. Om bedömningsinställningarna anger en komfortfaktor multipliceras antalet utnyttjade kärnor med komfortfaktorn.<br/><br/> Om det inte finns någon prestandahistorik använder Azure Migrate de allokerade kärnorna utan att använda komfortfaktorn. | Klar om antalet kärnor ligger inom gränsen
**Ram** | Varje dator får inte ha mer än 3 892 GB RAM, vilket är den&nbsp;maximala storleken som en Azure M-serie Standard_M128m<sup>2</sup> VM stöder. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om prestandahistorik är tillgänglig tar Azure Migrate hänsyn till det använda RAM-minnet som jämförelse. Om en komfortfaktor anges multipliceras det utnyttjade RAM-minnet med komfortfaktorn.<br/><br/> Om det inte finns någon historia används det tilldelade RAM-minnet utan att en komfortfaktor används.<br/><br/> | Klar om mängden RAM-minne ligger inom gränsen
**Lagringsdisk** | Den allokerade storleken på en disk får inte vara mer än 32 TB. Även om Azure stöder 64 TB-diskar med Azure Ultra SSD-diskar, söker Azure Migrate: Server Assessment för närvarande efter 32 TB som diskstorleksgränsen eftersom den inte stöder Ultra SSD ännu. <br/><br/> Antalet diskar som är anslutna till datorn, inklusive OS-disken, måste vara 65 eller färre. | Klar om diskstorleken och disknumret ligger inom gränserna
**Nätverk** | En maskin får inte ha fler än 32 nätverksgränssnitt (NAC) anslutna till den. | Klar om antalet nätverkskort ligger inom gränsen

### <a name="guest-operating-system"></a>Gästoperativsystem

Tillsammans med granskning av vm-egenskaper tittar Server Assessment på gästoperativsystemet för en dator för att avgöra om det kan köras på Azure.

> [!NOTE]
> För att hantera gästanalys för virtuella datorer med VMware använder Server Assessment det operativsystem som angetts för den virtuella datorn i vCenter Server. För virtuella Linux-datorer som körs på VMware identifierar serverutvärdering för närvarande inte kärnversionen av gästoperativsystemet.

Serverutvärdering använder följande logik för att identifiera Azure-beredskap baserat på operativsystemet:

**Operativsystem** | **Detaljer** | **Azure-beredskapsstatus**
--- | --- | ---
Windows Server 2016 och alla SPs-adresser | Azure ger fullt stöd. | Redo för Azure.
Windows Server 2012 R2 och alla SPs | Azure ger fullt stöd. | Redo för Azure.
Windows Server 2012 och alla SPs-adresser | Azure ger fullt stöd. | Redo för Azure.
Windows Server 2008 R2 med alla SPs | Azure ger fullt stöd.| Redo för Azure.
Windows Server 2008 (32- och 64-bitars) | Azure ger fullt stöd. | Redo för Azure.
Windows Server 2003 och Windows Server 2003 R2 | Dessa operativsystem har passerat sina slutdatum för support och behöver ett [csa-avtal (Custom Support Agreement)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt klar för Azure. Överväg att uppgradera operativsystemet innan du migrerar till Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 och MS-DOS | Dessa operativsystem har passerat sina supportdatum. Datorn kan starta i Azure, men Azure tillhandahåller inget os-stöd. | Villkorligt klar för Azure. Vi rekommenderar att du uppgraderar operativsystemet innan du migrerar till Azure.
Windows 7, Windows 8 och Windows 10 | Azure tillhandahåller endast support med en [Visual Studio-prenumeration.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Villkorligt klar för Azure.
Windows 10 Pro | Azure ger support med [multitenant hostingrättigheter.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Villkorligt klar för Azure.
Windows Vista och Windows XP Professional | Dessa operativsystem har passerat sina supportdatum. Datorn kan starta i Azure, men Azure tillhandahåller inget os-stöd. | Villkorligt klar för Azure. Vi rekommenderar att du uppgraderar operativsystemet innan du migrerar till Azure.
Linux | Se de [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md) som Azure stöder. Andra Linux-operativsystem kan starta i Azure. Men vi rekommenderar att du uppgraderar operativsystemet till en godkänd version innan du migrerar till Azure. | Redo för Azure om versionen är godkänd.<br/><br/>Villkorligt klar om versionen inte är godkänd.
Andra operativsystem som Oracle Solaris, Apple macOS och FreeBSD | Azure stöder inte dessa operativsystem. Datorn kan starta i Azure, men Azure tillhandahåller inget os-stöd. | Villkorligt klar för Azure. Vi rekommenderar att du installerar ett operativsystem som stöds innan du migrerar till Azure.  
OS angivet som **Annat** i vCenter Server | Azure Migrate kan inte identifiera operativsystemet i det här fallet. | Okänd beredskap. Se till att Azure stöder operativsystemet som körs i den virtuella datorn.
32-bitars operativsystem | Datorn kan starta i Azure, men Azure kanske inte ger fullt stöd. | Villkorligt klar för Azure. Överväg att uppgradera till ett 64-bitars operativsystem innan du migrerar till Azure.

## <a name="calculating-sizing"></a>Beräkna storlek

När datorn har markerats som klar för Azure ger Server Assessment storleksrekommendationer. Dessa rekommendationer identifierar Azure VM och disk SKU. Storleksberäkningar beror på om du använder lokal storleksstorlek eller prestandabaserad storlek.

### <a name="calculate-sizing-as-is-on-premises"></a>Beräkna storlek (lokalt)

 Om du använder lokal storleksändring tar serverutvärdering inte hänsyn till prestandahistoriken för de virtuella datorerna och diskarna.

- **Beräkningsstorlek:** Serverutvärdering allokerar en Azure VM SKU baserat på storleken som allokeras lokalt.
- **Lagring och diskstorlek:** Serverutvärdering tittar på den lagringstyp som anges i bedömningsegenskaper och rekommenderar lämplig disktyp. Möjliga lagringstyper är Standard HDD, Standard SSD och Premium. Standardlagringstypen är Premium.
- **Nätverksstorlek:** Serverutvärdering tar hänsyn till nätverkskortet på den lokala datorn.

### <a name="calculate-sizing-performance-based"></a>Beräkna storlek (prestandabaserad)

Om du använder prestandabaserad storlek gör Server Assessment storleksrekommendationer enligt följande:

- Serverutvärdering tar hänsyn till datorns prestandahistorik för att identifiera den virtuella datorns storlek och disktyp i Azure.
- Om du importerar servrar med hjälp av en CSV-fil används de värden du anger. Den här metoden är särskilt användbar om du har överlagrat den lokala datorn, användningen är låg och du vill rightsize den virtuella Azure-datorn för att spara kostnader.
- Om du inte vill använda prestandadata återställer du storlekskriterierna till lokalt lokalt, enligt beskrivningen i föregående avsnitt.

#### <a name="calculate-storage-sizing"></a>Beräkna lagringsstorlek

För lagringsstorlek försöker Azure Migrate mappa varje disk som är ansluten till datorn till en Azure-disk. Storleksändring fungerar enligt följande:

1. Serverutvärdering lägger till läsa och skriva IOPS för en disk för att få den totala IOPS som krävs. På samma sätt läggs läs- och skrivdataflödesvärdena till för att få det totala dataflödet för varje disk.
1. Om du har angett lagringstypen som automatisk baseras den valda typen på de gällande IOPS- och dataflödesvärdena. Serverutvärdering avgör om disken ska mappas till en standard-HÅRDDISK, Standard SSD eller Premium-disk i Azure. Om lagringstypen är inställd på en av dessa disktyper försöker Server assessment hitta en disk SKU inom den valda lagringstypen.
1. Diskar väljs på följande sätt:
    - Om Serverutvärdering inte kan hitta en disk med den nödvändiga IOPS och dataflödet markerar den datorn som olämplig för Azure.
    - Om Serverutvärdering hittar en uppsättning lämpliga diskar väljer den de diskar som stöder den plats som anges i bedömningsinställningarna.
    - Om det finns flera kvalificerade diskar väljer Serverbedömning disken med den lägsta kostnaden.
    - Om prestandadata för en disk inte är tillgänglig används konfigurationsdiskstorleken för att hitta en Standard SSD-disk i Azure.

#### <a name="calculate-network-sizing"></a>Beräkna nätverksstorlek

Serverutvärdering försöker hitta en virtuell Azure-dator som stöder antalet och den prestanda som krävs för nätverkskort som är anslutna till den lokala datorn.

- För att få den gällande nätverksprestandan för den lokala virtuella datorn sammanställer Server Assessment dataöverföringshastigheten från datorn (nätverk ut) över alla nätverkskort. Det applicerar sedan komfortfaktorn. Den använder det resulterande värdet för att hitta en Azure VM som kan stödja den nödvändiga nätverksprestanda.
- Tillsammans med nätverksprestanda tar Server Assessment också upp om den virtuella Azure-datorn kan stödja det antal nätverkskort som krävs.
- Om nätverksprestandadata inte är tillgängliga tar Server assessment endast hänsyn till antalet nätverkskort för vm-storlek.

#### <a name="calculate-compute-sizing"></a>Beräkna beräkningsstorlek

När den har beräknat lagrings- och nätverkskrav tar Server Assessment hänsyn till CPU- och RAM-krav för att hitta en lämplig VM-storlek i Azure.

- Azure Migrate tittar på de effektiva använda kärnorna och RAM-minnet för att hitta en lämplig Azure VM-storlek.
- Om ingen lämplig storlek hittas markeras datorn som olämplig för Azure.
- Om en lämplig storlek hittas tillämpar Azure Migrate lagrings- och nätverksberäkningarna. Den tillämpar sedan plats- och prisnivåinställningar för den slutliga rekommendationen för vm-storlek.
- Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.

## <a name="confidence-ratings-performance-based"></a>Konfidensklassificeringar (prestationsbaserade)

Varje prestandabaserad utvärdering i Azure Migrate är associerad med en konfidensklassificering. Betyget varierar från en (lägsta) till fem (högsta) stjärnor. Förtroendeklassificeringen hjälper dig att uppskatta tillförlitligheten i storleksrekommendationerna som Azure Migrate tillhandahåller.

- Förtroendebetyget tilldelas en bedömning. Klassificeringen baseras på tillgängligheten för datapunkter som behövs för att beräkna utvärderingen.
- För prestandabaserad storlek behöver serverbedömningen:
    - Användningsdata för CPU och VM RAM.
    - Disk-IOPS- och dataflöde för varje disk som är ansluten till den virtuella datorn.
    - Nätverks-I/O för att hantera prestandabaserad storlek för varje nätverkskort som är anslutet till en virtuell dator.

Om något av dessa användningsnummer inte är tillgängligt kan storleksrekommendationerna vara otillförlitliga.

> [!NOTE]
> Konfidensklassificeringar tilldelas inte för servrar som bedöms med hjälp av en importerad CSV-fil. Betyg är inte heller tillämpliga för lokal bedömning.

### <a name="ratings"></a>Klassificeringar

Den här tabellen visar bedömningsbefåhetsklassificeringarna, som beror på procentandelen tillgängliga datapunkter:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0-20% | 1 stjärna
   21-40% | 2 stjärnor
   41-60% | 3 stjärnor
   61-80% | 4 stjärnor
   81-100% | 5 stjärnor

### <a name="low-confidence-ratings"></a>Låga konfidensbetyg

Här är några anledningar till varför en bedömning kan få ett lågt förtroende betyg:

- Du har inte profilera din miljö under den tid du skapar utvärderingen. Om du till exempel skapar utvärderingen med prestandavaraktighet inställd på en dag måste du vänta minst en dag efter att du har börjat identifiera för att alla datapunkter ska samlas in.
- Vissa virtuella datorer stängdes av under den tid som bedömningen beräknades för. Om några virtuella datorer är avstängda under en viss tid kan serverutvärderingen inte samla in prestandadata för den perioden.
- Vissa virtuella datorer skapades under den tid som bedömningen beräknades. Anta till exempel att du har skapat en utvärdering för prestandahistoriken för den senaste månaden, men vissa virtuella datorer skapades för bara en vecka sedan. Prestandahistoriken för de nya virtuella datorerna kommer inte att finnas under hela varaktigheten.

> [!NOTE]
> Om konfidensklassificeringen för en bedömning är mindre än fem stjärnor rekommenderar vi att du väntar minst en dag på att apparaten ska profilera miljön och sedan beräkna om bedömningen. Annars kan prestandabaserad storlek vara otillförlitlig. I så fall rekommenderar vi att du byter bedömning till lokal storlek.

## <a name="calculate-monthly-costs"></a>Beräkna månadskostnader

När storleksrekommendationerna är klara beräknar Azure Migrate beräknings- och lagringskostnader för efter migreringen.

- **Beräkningskostnad:** Azure Migrate använder den rekommenderade Azure VM-storleken och Azure Billing API för att beräkna månadskostnaden för den virtuella datorn.

    Beräkningen tar hänsyn till följande:
    - Operativsystem
    - Programvarusäkring
    - Reserverade instanser
    - VM-drifttid
    - Location
    - Valutainställningar

    Serverutvärderingen sammanställer kostnaden för alla datorer för att beräkna den totala månatliga beräkningskostnaden.

- **Lagringskostnad:** Den månatliga lagringskostnaden för en dator beräknas genom att samla den månatliga kostnaden för alla diskar som är kopplade till datorn.

    Serverbedömning beräknar de totala månatliga lagringskostnaderna genom att samla lagringskostnaderna för alla datorer. För närvarande tar beräkningen inte hänsyn till erbjudanden som anges i bedömningsinställningarna.

Kostnader visas i den valuta som anges i bedömningsinställningarna.

## <a name="next-steps"></a>Nästa steg

[Granska](best-practices-assessment.md) metodtips för att skapa utvärderingar. 

- Lär dig mer om att köra utvärderingar för [virtuella datorer,](tutorial-prepare-vmware.md) [virtuella datorer med hyper-v och](tutorial-prepare-hyper-v.md)fysiska [servrar](tutorial-prepare-physical.md).
- Lär dig mer om hur du bedömer servrar [som importeras med en CSV-fil](tutorial-assess-import.md).
- Lär dig mer om hur du ställer in [beroendevisualisering](concepts-dependency-visualization.md).
