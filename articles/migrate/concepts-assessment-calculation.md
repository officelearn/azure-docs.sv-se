---
title: Bedömning beräkningar i Azure migrera | Microsoft Docs
description: En översikt över assessment beräkningar i tjänsten Azure migrera.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: be4fb15d96f5598d4b1ddbbaa4befe7f6530152c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="assessment-calculations"></a>Utvärderingsberäkningar

[Azure migrera](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Den här artikeln innehåller information om hur bedömningar beräknas.


## <a name="overview"></a>Översikt

En Azure migrera bedömning har tre steg. Bedömning som börjar med en lämplighet analys, följt av storlek, och slutligen ett månatligt cost uppskattning. En dator flyttar endast till ett senare tillfälle om föregående överförs. Till exempel om en dator misslyckas kontrollen Azure lämplighet, har den markerats som lämpar sig inte för Azure, och ange storlek och kostnadskrävande inte göras.


## <a name="azure-suitability-analysis"></a>Azure lämplighet analys

Inte alla datorer som är lämpliga för körs i molnet som molnet har sin egen begränsningar och krav. Azure migrera utvärderar varje lokal dator för migrering lämplighet till Azure och kategoriserar datorerna som något av följande kategorier:
- **Redo för Azure** -datorn kan migreras som-är att Azure utan några ändringar. Den kommer att starta i Azure med fullständig Azure-supporten.
- **Villkorligt redo för Azure** -datorn kan starta i Azure, men kanske inte har fullständig Azure-supporten. En dator med en äldre version av Windows Server-Operativsystemet stöds inte i Azure. Du måste vara försiktig innan du migrerar de här datorerna till Azure och följer du anvisningarna för reparation som föreslås i assessment för beredskapsproblem innan du migrerar.
- **Ej redo för Azure** -startar inte datorn i Azure. Till exempel om en lokal dator har en disk av fler än 4 TB anslutna storlek, får den inte finnas på Azure. Du måste följa riktlinjerna för reparation som föreslås i bedömningen att åtgärda problemet är klar innan du migrerar till Azure. Rätt storlek och kostnad uppskattning är inte klar för datorer som är markerade som ej redo för Azure.
- **Beredskap för okänd** -Azure migrera kunde inte hitta datorn på grund av otillräckliga data som är tillgängliga i vCenter Server är klar.

Azure migrera granskar datoregenskaperna och gästoperativsystemet för att identifiera Azure beredskap för den lokala datorn.

### <a name="machine-properties"></a>Datoregenskaperna
Azure migrera går igenom följande egenskaper för lokala virtuella datorn att identifiera om en virtuell dator kan köras på Azure.

**Egenskap** | **Detaljer** | **Azure beredskapsstatusen**
--- | --- | ---
**Start-typ** | Virtuella datorer med start-typ som BIOS och UEFI inte har stöd för Azure. | Villkorligt redo för Azure om start av typen UEFI.
**Kärnor** | Antal kärnor på datorerna måste vara lika med eller mindre än det högsta antalet kärnor (32) som stöds för en Azure VM.<br/><br/> Om det finns prestandahistorik anser Azure migrera utnyttjade kärnor för jämförelse. Om en bekvämlighet faktor anges i inställningarna för assessment multipliceras antal utnyttjade kärnor med bekvämlighet faktorn.<br/><br/> Om det finns ingen prestandahistorik, använder Azure migrera allokerade kärnor, utan att använda bekvämlighet faktorn. | Ej redo om antalet kärnor är större än 32.
**Minne** | Storleken på datorn minne måste vara lika med eller mindre än den maximala mängd minne (448 GB) tillåten för en Azure VM. <br/><br/> Om det finns prestandahistorik anser Azure migrera utnyttjade minne för jämförelse. Om du anger en bekvämlighet faktor multipliceras utnyttjade minne med bekvämlighet faktorn.<br/><br/> Om det finns ingen historik allokerat minne används, utan att använda bekvämlighet faktorn.<br/><br/> | Ej redo om minnesstorleken är större än 448 GB.
**Lagringsdisken** | Allokerade storleken på en disk måste vara 4 TB (4096 GB) eller mindre.<br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disk. | Ej redo om alla diskar har större än 4 TB, eller om det finns fler än 65 diskar som är anslutna till datorn.
**Nätverk** | En dator måste ha 32 eller mindre nätverkskort anslutna. | Ej redo om datorn har fler än 32 nätverkskort

### <a name="guest-operating-system"></a>Gästoperativsystemet
Tillsammans med egenskaper för Virtuella datorer tittar Azure migrera också på gästoperativsystemet på den lokala virtuella datorn att identifiera om den virtuella datorn kan köra på Azure.

> [!NOTE]
> Azure migrera anser OS som anges i vCenter Server för att göra följande analys. Eftersom identifiering av Azure migrera installation-baserade, har inte ett sätt att kontrollera om Operativsystemet som körs på den virtuella datorn är samma som en angiven i vCenter Server.

Följande logik används av Azure migrera för att identifiera Azure beredskap för den virtuella datorn baserat på operativsystemet.

**Operativsystem** | **Detaljer** | **Azure beredskapsstatusen**
--- | --- | ---
Windows Server 2016 & alla Service Pack | Azure tillhandahåller fullt stöd. | Redo för Azure
Windows Server 2012 R2 & alla Service Pack | Azure tillhandahåller fullt stöd. | Redo för Azure
Windows Server 2012 & alla Service Pack | Azure tillhandahåller fullt stöd. | Redo för Azure
Windows Server 2008 R2 med alla Service Pack | Azure tillhandahåller fullt stöd.| Redo för Azure
Windows Server 2003 2008 R2 | Dessa operativsystem har överfört sina slutet av stöd för datum och behovet av en [anpassad stöder avtal (CSA)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt redo för Azure bör du överväga att uppgradera Operativsystemet innan du migrerar till Azure.
Windows 2000, 98, 95, NT 3.1, MS-DOS | Dessa operativsystem har överfört sina slutet av support datum, datorn kan starta i Azure, men ingen OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderas att uppgradera Operativsystemet innan du migrerar till Azure.
Klienten för Windows 7, 8 och 10 | Azure tillhandahåller support med Visual Studio-prenumeration. | Villkorligt redo för Azure
Windows Vista, XP Professional | Dessa operativsystem har överfört sina slutet av support datum, datorn kan starta i Azure, men ingen OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderas att uppgradera Operativsystemet innan du migrerar till Azure.
Linux | Azure rekommenderar dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men det rekommenderas att uppgradera Operativsystemet till en påtecknade version innan du migrerar till Azure. | Redo för Azure om versionen som är godkända.<br/><br/>Villkorligt redo om versionen som inte är godkända.
Andra operativsystem<br/><br/> t.ex. Oracle Solaris, Apple Mac OS etc., FreeBSD osv. | Azure du inte stöder dessa operativsystem. Datorn kan starta i Azure, men ingen OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderas att installera ett operativsystem som stöds innan du migrerar till Azure.  
Operativsystem som angetts som *andra* i vCenter Server | Migrera Azure kan inte identifiera Operativsystemet i det här fallet. | Beredskap för okänd. Kontrollera att Operativsystemet som körs på den virtuella datorn stöds i Azure.
32-bitars operativsystem | Datorn kan starta i Azure, men Azure kan inte ger fullt stöd. | Villkorligt redo för Azure bör du överväga att uppgradera Operativsystemet på datorn från 32-bitars operativsystem till 64-bitars operativsystem innan du migrerar till Azure.

## <a name="sizing"></a>Storleksändring

När en dator markeras som redo för Azure, storlek Azure migrera den virtuella datorn och dess diskar för Azure. Om sizing kriteriet som angetts i egenskaperna för bedömning är att göra prestandabaserad storlek, anser Azure migrera prestandahistorik för datorn att identifiera en VM-storlek i Azure. Den här metoden är användbart i scenarier där du har tilldelat den lokala virtuella datorn över men användningen är låg och du vill få rätt storlek på virtuella datorer i Azure för att spara kostnader.

> [!NOTE]
> Azure migrera samlar in prestandahistorik över lokala virtuella datorer från vCenter-servern. För att säkerställa exakt rätt storlek för att inställningen statistik i vCenter-servern är inställd på nivå 3 och vänta minst en dag före lanseras identifiering av de lokala virtuella datorerna. Om inställningen statistik i vCenter Server är under nivå 3, samlas inte prestandadata för disk och nätverk.

Om du inte vill överväga historik för minnesprestanda för VM-storlek och vill göra den virtuella datorn som-är till Azure, kan du ange storlek kriterium som *som lokala* och Azure migrera sedan ändra de virtuella datorerna baserat på lokalt konfiguration av utan att överväga användningsdata. Ändra storlek på disken i det här fallet kommer fortfarande att baseras på prestandadata.

### <a name="performance-based-sizing"></a>Prestandabaserad storlek

Prestandabaserad förstoras Azure migrera börjar med diskar som är kopplade till den virtuella datorn, följt av nätverkskort och sedan maps en Azure VM baserat på kraven för beräkning av den lokala virtuella datorn.

- **Lagring**: Azure migrera försöker mappa alla diskar som är anslutna till datorn till en disk i Azure.

    > [!NOTE]
    > Azure migrera stöder endast hanterade diskar för utvärdering.

    - För att få gällande disk-i/o per sekund (IOPS) och genomströmning (MBps), multiplicerar Azure migrera IOPS för disk och dataflöde med bekvämlighet faktorn. Baserat på effektiva IOPS och genomströmning värden, identifierar Azure migrera om disken som ska mappas till en standard- eller premium disk i Azure.
    - Om Azure migrerar inte hittar en disk med nödvändiga IOPS & genomströmning, markerar datorn som lämpar sig inte för Azure. [Lär dig mer](../azure-subscription-service-limits.md#storage-limits) om Azure begränsar per disk- och VM.
    - Om den hittar en uppsättning lämpliga diskar väljer Azure migrera de som stöd för redundans lagringsmetod och den plats som anges i inställningarna för utvärdering.
    - Om det finns flera tillgängliga diskar, väljs med lägst kostnad.
    - Om prestandadata för diskar i tillgänglig, alla diskar som är mappade till standarddiskar i Azure.

- **Nätverket**: Azure migrera försöker hitta en virtuell Azure-dator som har stöd för antalet nätverkskort som är anslutna till den lokala datorn och prestanda som krävs av dessa nätverkskort.
    - För att få gällande nätverkets prestanda på den lokala virtuella datorn, Azure migrera samlar in data som överförs per sekund (MBps) utanför datorn (nätverk ut) över alla nätverkskort och gäller bekvämlighet faktorn. Numret används för att hitta en virtuell Azure-dator som har stöd för nödvändiga nätverkets prestanda.
    - Tillsammans med nätverkets prestanda även betraktar om den virtuella Azure-datorn stöder det obligatoriska antalet nätverkskort.
    - Om det finns inga nätverk prestandadata anses endast nätverkskort antalet för VM-storlek.

- **Beräkna**: när krav för lagring och nätverk beräknas Azure migrera anser CPU och minne kraven för att hitta en lämplig VM-storlek i Azure.
    - Azure migrera tittar på utnyttjade kärnor och minne och gäller bekvämlighet faktor för att få gällande kärnor och minne. Baserat på det antalet försök att hitta en lämplig VM-storlek i Azure.
    - Om det finns ingen lämplig storlek, markeras datorn som lämpar sig inte för Azure.
    - Om det finns en lämplig storlek, gäller Azure migrera beräkningar för lagring och nätverk. Det gäller plats och prissättning nivån inställningar för slutlig VM storlek rekommendationen.
    - Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.

### <a name="as-on-premises-sizing"></a>Som lokalt storlek
Om sizing kriteriet är *som lokalt storlek*, Azure migrerar inte anser prestandahistorik för de virtuella datorerna och allokerar virtuella datorer baserat på den lokala allokerade storleken. Det emellertid tänka prestandahistorik över diskarna att rekommenderar Standard eller Premium diskar för disk-storlek.  
- **Lagring**: migrera Azure mappar varje disk som är ansluten till datorn som en disk i Azure.

    > [!NOTE]
    > Azure migrera stöder endast hanterade diskar för utvärdering.

    - För att få gällande disk-i/o per sekund (IOPS) och genomströmning (MBps), multiplicerar Azure migrera IOPS för disk och dataflöde med bekvämlighet faktorn. Baserat på effektiva IOPS och genomströmning värden, identifierar Azure migrera om disken som ska mappas till en standard- eller premium disk i Azure.
    - Om Azure migrerar inte hittar en disk med nödvändiga IOPS & genomströmning, markerar datorn som lämpar sig inte för Azure. [Lär dig mer](../azure-subscription-service-limits.md#storage-limits) om Azure begränsar per disk- och VM.
    - Om den hittar en uppsättning lämpliga diskar väljer Azure migrera de som stöd för redundans lagringsmetod och den plats som anges i inställningarna för utvärdering.
    - Om det finns flera tillgängliga diskar, väljs med lägst kostnad.
    - Om prestandadata för diskar i tillgänglig, alla diskar som är mappade till standarddiskar i Azure.
- **Nätverket**: för varje nätverkskort, ett nätverkskort i Azure rekommenderas.
- **Beräkna**: Azure migrera tittar på antal kärnor och minnesstorleken för den lokala virtuella datorn och rekommenderar en Azure-dator med samma konfiguration. Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste. Användningsdata för CPU och minne för anses inte för som lokalt storlek.

### <a name="confidence-rating"></a>Säkerhetsomdöme

Varje utvärdering i Azure Migrate är kopplad till ett säkerhetsomdöme i intervallet 1 stjärna till 5 stjärnor (1 stjärna är lägst och 5 stjärnor är högst). Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen. Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.

För prestandabaserade storleksändringar av virtuella datorer behöver Azure Migrate användningsdata för CPU och minne. Dessutom för storleksändring av varje disk som är ansluten till den virtuella datorn, den måste läsning och skrivning IOPS och genomflöde. Precis som för varje nätverkskort som är kopplat till den virtuella datorn så måste Azure Migrate ha åtkomst till nätverkets in-/utdata för att utföra prestandabaserade storleksändringar. Om några av ovanstående användningsnummer inte är tillgängliga i vCenter Server så är kanske storleksrekommendationen från Azure Migrate inte är tillförlitlig. Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls förtroende klassificering för att bedöma enligt nedan:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

En utvärdering kanske inte har tillgång till alla datapunkter på grund av någon av följande orsaker:
- Inställningen statistik i vCenter Server är inte inställt på nivå 3. Om statistikinställningen i vCenter Server är lägre än nivå 3 så görs ingen insamling av prestandadata för disk och nätverk från vCenter Server. I det här fallet är rekommendationen från Azure Migrate för disk och nätverk inte användningsbaserad. Utan att överväga IOPS/dataflöde på disken, migrera Azure kan inte identifiera om disken behöver en premium disk i Azure rekommenderar därför i det här fallet Azure migrera standarddiskar för alla diskar.
- Statistikinställningen i vCenter Server var inställd på nivå 3 under en kort period, innan identifieringen drog igång. Vi tänker oss exempelvis ett scenario där du ändrar statistikinställningen till nivå 3 i dag och sätter igång identifieringen med insamlingsprogrammet i morgon (efter 24 timmar). Om du skapar en utvärdering för en dag, har du alla datapunkter och säkerhetsomdömet för utvärderingen blir 5 stjärnor. Men om du ändrar varaktigheten i utvärderingsegenskaperna till en månad går säkerhetsomdömet ned om prestandadata från disken och nätverket för den senaste månaden inte är tillgängliga. Om du vill undersöka prestandadata för den senaste månaden rekommenderar vi att du behåller statistikinställningen för vCenter Server på nivå 3 i en månad innan du startar identifieringen.
- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer stängdes av under en viss period så kommer vCenter Server inte att ha prestandadata för den perioden.
- Några virtuella datorer skapades under perioden som utvärderingen utförs. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I sådana fall är prestandahistoriken för de nya virtuella datorerna inte med för hela perioden.

> [!NOTE]
> Om säkerhetsomdömet för någon utvärdering är lägre än 4 stjärnor rekommenderar vi att du ändrar nivån för statistikinställningar i vCenter Server till 3, väntar så länge som du vill att utvärderingen ska utvärdera (en dag/en vecka/en månad) och sedan utför en identifiering och en utvärdering. Om det föregående inte kan utföras kan prestandabaserade storleksändringar vara mindre tillförlitliga och därför rekommenderar vi att du byter till *storleksändringar av typen "som lokalt"* genom att ändra utvärderingsegenskaperna.

## <a name="monthly-cost-estimation"></a>Månatliga uppskattning

När sizing rekommendationer är klart, beräknar Azure migrera kostnader för beräkning och lagring av efter migreringen.

- **Beräkna kostnaden**: med den rekommenderade Azure VM-storleken Azure migrera använder fakturerings-API för att beräkna månadskostnaden för den virtuella datorn. Operativsystemet, software assurance, plats och valutainställningar hänsyn tas med i beräkningen. Den sammanställer kostnaden över alla datorer att beräkna den totala månatliga beräkna kostnaden.
- **Lagringskostnaden**: månatliga storage kostnaden för en dator beräknas genom insamling av månadskostnaden för alla diskar som är anslutna till datorn. Azure migrera beräknar de totala månatliga lagringskostnaderna genom att sammanställa kostnader för lagring av alla datorer. Beräkningen tar för närvarande inte erbjudanden som anges i inställningarna för utvärdering i beräkningen.

Kostnaderna visas i valutan som anges i inställningarna för utvärdering.


## <a name="next-steps"></a>Nästa steg

[Skapa en bedömning för lokala virtuella VMware-datorer](tutorial-assessment-vmware.md)
