---
title: Utvärderingsberäkningar i Azure Migrate | Microsoft Docs
description: Översikt över utvärderingsberäkningar i Azure Migrate-tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 012a352b00de2e2d1bf64fd18125ddd10faba5cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679127"
---
# <a name="assessment-calculations"></a>Utvärderingsberäkningar

[Azure Migrate](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Den här artikeln innehåller information om hur utvärderingar beräknas.


## <a name="overview"></a>Översikt

Ett Azure Migrate-utvärdering har tre steg. Utvärderingen börjar med en lämplighet analys, följt av storlek, och slutligen en månatlig kostnadsuppskattning. En dator flyttar endast till senare om den godkänns föregående. Till exempel om en dator misslyckas kontrollen Azure-lämplighet, har den markerats som olämpliga för Azure, och ange storlek och kostnad inte göras.

## <a name="azure-suitability-analysis"></a>Analys av azures lämplighet

Inte alla datorer är lämpliga för att köra på molnet som molnet har sin egen begränsningar och krav. Azure Migrate utvärderar varje lokal dator deras lämplighet för migrering till Azure och kategoriserar datorerna till något av följande kategorier:
- **Redo för Azure** -datorn kan migreras som – är till Azure utan ändringar. Det kommer att starta i Azure med fullständig Azure-support.
- **Villkorligt redo för Azure** -datorn kan starta i Azure, men inte har fullständig Azure-supporten. Till exempel stöds en dator med en äldre version av Windows Server-Operativsystemet inte i Azure. Du måste du vara försiktig innan du migrerar de här datorerna till Azure och följ anvisningarna för reparation som föreslås i utvärdering för att åtgärda beredskapsproblem innan du migrerar.
- **Ej redo för Azure** -datorn kommer inte att starta i Azure. Till exempel om en lokal dator har en disk som mer än 4 TB kopplade till den, kan inte den nås på Azure. Du måste följa åtgärdsvägledning som föreslås i utvärdering för att åtgärda problemet readiness innan du migrerar till Azure. Rätt storlek och kostnadsuppskattning görs inte för datorer som är markerade som ej redo för Azure.
- **Beredskap okänd** – Azure Migrate kunde inte hitta beredskap för datorn på grund av otillräckliga data som är tillgängliga i vCenter Server.

Azure Migrate granskar datoregenskaperna och gästoperativsystemet att identifiera Azure-beredskap för den lokala datorn.

### <a name="machine-properties"></a>Egenskaper för dator
Azure Migrate granskar följande egenskaper för lokala virtuella datorn att identifiera om en virtuell dator kan köra på Azure.

**Egenskap** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
**Starttyp** | Azure har stöd för virtuella datorer med starttypen BIOS och UEFI inte. | Villkorligt redo om starttypen är UEFI.
**Kärnor** | Antalet kärnor på datorerna måste vara lika med eller mindre än det maximala antalet kärnor (128 kärnor) stöds för en Azure-dator.<br/><br/> Om det finns prestandahistorik överväger Azure Migrate utnyttjade kärnor för jämförelse. Om en komfortfaktor anges i inställningarna för utvärdering av multipliceras antalet utnyttjade kärnor med komfortfaktorn.<br/><br/> Om det finns inga prestandahistorik, använder Azure Migrate tilldelade kärnor, utan att tillämpa komfortfaktorn. | Redo om mindre än eller lika med begränsningar.
**Minne** | Storleken på datorn minne måste vara lika med eller mindre än maximalt minne (3892 GB på Azure-M-serien Standard_M128m&nbsp;<sup>2</sup>) tillåts för en Azure-dator. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om det finns prestandahistorik överväger Azure Migrate utnyttjade minne för jämförelse. Om en komfortfaktor anges multipliceras utnyttjade minne med komfortfaktorn.<br/><br/> Om det finns ingen historik allokerat minne används, utan att tillämpa komfortfaktorn.<br/><br/> | Redo om inom gränserna.
**Lagringsdisk** | Allokerade storleken på en disk måste vara 4 TB (4096 GB) eller mindre.<br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disken. | Redo om inom gränserna.
**Nätverk** | En dator måste ha 32 eller färre anslutna nätverkskort till den. | Redo om inom gränserna.

### <a name="guest-operating-system"></a>Gästoperativsystem
Tillsammans med egenskaperna för virtuella datorer tittar Azure Migrate även på gästoperativsystemet på den lokala virtuella datorn att identifiera om den virtuella datorn kan köra på Azure.

> [!NOTE]
> Azure Migrate överväger operativsystem som har angetts i vCenter Server kan utföra följande analyser. Eftersom identifieringen görs av Azure Migrate är baserat på enhet, har inte ett sätt att kontrollera om det operativsystem som körs på den virtuella datorn är samma som en anges i vCenter Server.

Följande logik används av Azure Migrate för att identifiera Azure-beredskap för den virtuella datorn baserat på operativsystemet.

**Operativsystem** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
Windows Server 2016 och alla Service Pack | Azure tillhandahåller fullständig support. | Redo för Azure
Windows Server 2012 R2 & all SPs | Azure tillhandahåller fullständig support. | Redo för Azure
Windows Server 2012 och alla Service Pack | Azure tillhandahåller fullständig support. | Redo för Azure
Windows Server 2008 R2 med alla Service Pack | Azure tillhandahåller fullständig support.| Redo för Azure
Windows Server 2008 (32-bitars och 64-bitars) | Azure tillhandahåller fullständig support. | Redo för Azure
Windows Server 2003, 2003 R2 | De här operativsystemen har klarat sitt slut supportperioden och du behöver en [anpassad stöder avtal (CSA)](https://aka.ms/WSosstatement) för support på Azure. | Villkorligt redo för Azure bör du överväga att uppgradera datorns operativsystem innan du migrerar till Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | De här operativsystemen har klarat sitt slut supportperioden, datorn kan starta i Azure, men någon OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderar vi att du uppgradera Operativsystemet innan du migrerar till Azure.
Klienten för Windows 7, 8 och 10 | Azure ger stöd med [Visual Studio-prenumeration.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Villkorligt redo för Azure
Windows 10 Pro Desktop | Azure ger stöd med [Multitenant som är värd för rättigheter.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Villkorligt redo för Azure
Windows Vista, XP Professional | De här operativsystemen har klarat sitt slut supportperioden, datorn kan starta i Azure, men någon OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderar vi att du uppgradera Operativsystemet innan du migrerar till Azure.
Linux | Azure godkänner dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men det rekommenderas att uppgradera datorns operativsystem till en version som stöds innan du migrerar till Azure. | Redo för Azure om versionen är godkända.<br/><br/>Villkorligt redo om versionen inte är godkända.
Andra operativsystem<br/><br/> t.ex. Oracle Solaris, Apple Mac OS etc., FreeBSD osv. | Azure du inte stöder dessa operativsystem. Datorn kan starta i Azure, men någon OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderar vi att du installerar ett operativsystem som stöds innan du migrerar till Azure.  
OS tillhörigheten **andra** i vCenter Server | Azure Migrate identifiera inte Operativsystemet i det här fallet. | Beredskap för okänd. Se till att det operativsystem som körs på den virtuella datorn stöds i Azure.
32-bitars operativsystem | Datorn kan starta i Azure, men Azure tillhandahålla inte fullständig support. | Villkorligt redo för Azure bör du överväga att uppgradera Operativsystemet på datorn från 32-bitars operativsystem till 64-bitars operativsystem innan du migrerar till Azure.

## <a name="sizing"></a>Storlekar

När en dator markeras som redo för Azure, storlekar Azure Migrate den virtuella datorn och dess diskar för Azure. Om storlekskriteriet som angetts i egenskaperna för utvärdering är att utföra prestandabaserade storleksändringar, överväger Azure Migrate prestandahistoriken för datorn att identifiera VM-storlek och disk-typ i Azure. Den här metoden är användbar i scenarier där du har tilldelat en lokal virtuell dator över men användningen är låg och du vill att storleksanpassa de virtuella datorerna i Azure för att minska kostnaderna.

Om du inte vill överväga prestandahistorik för VM-storlek och vill ta den virtuella datorn som – är till Azure, kan du ange storlekskriteriet som *som lokalt* och Azure Migrate kommer sedan ändra storlek på de virtuella datorerna baserat på lokala platser konfiguration utan att överväga användningsdata. Ändra storlek på disken i det här fallet kommer att göras baserat på vilken lagringstyp som du anger i egenskaperna för utvärdering (Standard disk eller Premium-diskar)

### <a name="performance-based-sizing"></a>Prestandabaserad storleksändring

För prestandabaserade storleksändringar behöver Azure Migrate börjar med diskar som är kopplade till den virtuella datorn, följt av nätverkskort och maps en Azure VM beroende på compute-krav för den lokala virtuella datorn.

- **Storage**: Azure Migrate försöker mappa alla diskar som är anslutna till datorn till en disk i Azure.

    > [!NOTE]
    > Azure Migrate stöder endast hanterade diskar för utvärdering.

    - För att få gällande disk-i/o per sekund (IOPS) och dataflöde (Mbit/s), multiplicerar Azure Migrate disken IOPS och dataflöde med komfortfaktorn. Baserat på effektiv IOPS och dataflöde värden kan identifierar Azure Migrate om disken bör mappas till en standard- eller premium disk i Azure.
    - Om Azure Migrate inte kan hitta en disk med nödvändiga IOPS och dataflöde, markerar den dator som olämpliga för Azure. [Läs mer](../azure-subscription-service-limits.md#storage-limits) om Azure gränser per disk och virtuell dator.
    - Om den hittar en uppsättning lämpliga diskar väljs Azure Migrate de som stöd för vilken lagringsmetod för redundans och den plats som anges i utvärderingsinställningarna för.
    - Om det finns flera berättigade diskar, väljs som har lägst pris.
    - Om prestandadata för diskar i tillgänglig, alla diskar är mappade till standarddiskar i Azure.

- **Nätverk**: Azure Migrate försöker hitta en Azure-dator som har stöd för antalet nätverkskort som är anslutna till den lokala datorn och prestanda som krävs av dessa nätverkskort.
    - För att få gällande nätverkets prestanda för lokala virtuella datorn kan Azure Migrate sammanställer data som överförs per sekund (MBps) från datorn (nätverk ut), över alla nätverkskort och gäller komfortfaktorn. Det här värdet används för att hitta en Azure-dator som har stöd för nödvändig nätverksprestanda.
    - Tillsammans med nätverksprestanda, den tittar också om den virtuella Azure-datorn har stöd för de nödvändiga antalet nätverkskort.
    - Om det finns inga nätverk prestandadata anses endast nätverk nätverkskort antal för VM-storlek.

- **Compute**: När krav för lagring och nätverk beräknas överväger Azure Migrate processor- och kraven för att hitta en lämplig VM-storlek i Azure.
    - Azure Migrate tittar på utnyttjade kärnor och minne och gäller komfortfaktor för att få gällande kärnor och minne. Baserat på det antalet försök att hitta en lämplig VM-storlek i Azure.
    - Om det finns ingen passande storlek, markeras datorn som olämpliga för Azure.
    - Om en lämplig storlek hittas, gäller Azure Migrate lagring och nätverk beräkningar. Den gäller sedan plats och prisnivåinställningarna för den slutliga rekommendationen för VM-storlek.
    - Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.

### <a name="as-on-premises-sizing"></a>Som lokalt storlek
Om storlekskriteriet är *som lokalt storlek*, Azure Migrate beräknar inte prestandahistoriken för virtuella datorer och diskar och allokerar en VM-SKU i Azure baserat på den allokerade lokala storleken. På liknande sätt för disk storlek tittar på lagringstypen som angetts i egenskaperna för utvärdering (Standard/Premium) och rekommenderar disktypen därefter. Standardtypen för lagring är Premium-diskar.

### <a name="confidence-rating"></a>Säkerhetsomdöme
Varje prestandabaserade utvärdering i Azure Migrate är kopplad till ett säkerhetsomdöme i intervallet 1 stjärna till 5 stjärnor (1 stjärna är lägst och 5 stjärnor är högst). Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen. Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate. Säkerhetsomdömet är inte tillämpligt på lokala utvärderingar.

För prestandabaserade storleksändringar behöver Azure Migrate användningsdata för CPU, minne och den virtuella datorn. För varje disk som är ansluten till den virtuella datorn krävs dessutom information om IOPS och dataflöden. Precis som för varje nätverkskort som är kopplat till en virtuell dator så måste Azure Migrate ha åtkomst till nätverkets in-/utdata för att utföra prestandabaserade storleksändringar. Om några av ovanstående användningsnummer inte är tillgängliga i vCenter Server så är kanske storleksrekommendationen från Azure Migrate inte är tillförlitlig. Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen, som du ser nedan:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

   Nedan angående är de orsaker till varför en utvärdering kan hämta ett låga säkerhetsomdöme:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du skapar utvärderingen med varaktigheten inställd på 1 dag, måste du vänta minst en dag efter att du börjar identifieringen för att alla datapunkter ska ha samlats in.

- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer stängdes av under en viss period så kommer vi inte att kunna samla in prestandadata för den perioden.

- Några virtuella datorer skapades under perioden som utvärderingen utförs. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I sådana fall är prestandahistoriken för de nya virtuella datorerna inte med för hela perioden.

  > [!NOTE]
  > Om säkerhetsomdömet för någon utvärdering är lägre än 5 stjärnor, rekommenderar vi att du väntar minst en dag att profilera miljön och sedan *beräkna om* utvärderingen. Om det föregående inte kan utföras kan prestandabaserade storleksändringar vara mindre tillförlitliga och därför rekommenderar vi att du byter till *storleksändringar av typen "som lokalt"* genom att ändra utvärderingsegenskaperna.

## <a name="monthly-cost-estimation"></a>Uppskattning per månad

När storleksrekommendationer har slutförts kan beräknar Azure Migrate efter migrering beräknings- och kostnader.

- **Beräkningskostnaden**: Med den rekommenderade storleken för virtuell Azure-dator kan använder Azure Migrate Billing-API för att beräkna den månatliga kostnaden för den virtuella datorn. Det operativsystem, software assurance, reserverade instanser, VM drifttid, plats och valutainställningar hänsyn tas med i beräkningen. Sammanställer kostnaden för samtliga datorer att beräkna den totala månadskostnaden för beräkning.
- **Kostnaden för lagring**: Månatliga storage-kostnaden för en dator beräknas genom att sammanställa månadskostnaden för alla diskar som är anslutna till datorn. Azure Migrate beräknar de totala månatliga kostnaderna för lagring genom att sammanställa lagringskostnaderna för alla datorer. Beräkningen tar för närvarande inte erbjudanden som anges i utvärderingsinställningarna för i kontot.

Kostnader visas i den valuta som anges i utvärderingsinställningarna för.


## <a name="next-steps"></a>Nästa steg

[Skapa en utvärdering för lokala virtuella VMware-datorer](tutorial-assessment-vmware.md)
