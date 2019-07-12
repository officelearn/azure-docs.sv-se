---
title: Utvärderingsberäkningar i Azure Migrate | Microsoft Docs
description: Översikt över utvärderingsberäkningar i Azure Migrate-tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805991"
---
# <a name="assessment-calculations"></a>Utvärderingsberäkningar

Azure Migrate Server-utvärdering utvärderar lokala arbetsbelastningar för migrering till Azure. Den här artikeln innehåller information om hur utvärderingar beräknas.


[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga moln-instanser till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.

## <a name="overview"></a>Översikt

En utvärdering av Azure Migrate Server-utvärdering har tre steg. Utvärderingen börjar med en lämplighet analys, följt av storlek, och slutligen en månatlig kostnadsuppskattning. En dator flyttar endast till senare om den godkänns föregående. Till exempel om en dator misslyckas kontrollen Azure-lämplighet, har den markerats som olämpliga för Azure, och ange storlek och kostnad inte göras.


## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/><br/> Azure Migrate stöder för närvarande dessa målregioner: Östra Australien, sydöstra Australien, södra Brasilien, Kanada-centrala, Kanada, östra, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra USA, östra usa2, centrala Tyskland, nordöstra Tyskland, Japan östra, Japan, västra centrala Korea, södra Nord Centrala USA, Nordeuropa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra, Virginia (USA-förvaltad region Arizona USA Gov Texas, USA-förvaltad region), USA, västra centrala, Västeuropa, västra Indien, västra USA och USA, västra 2.<br/> Målregionen är som standard angiven som USA, västra 2.
**Lagringstyp** | Premium/standard HHD diskar/Standard SSD-diskar.<br/><br/> När du anger lagringstypen som automatisk i en utvärdering, baseras disk-rekommendationen på prestandadata för diskar (IOPS och dataflöde).<br/><br/> Om du anger lagringstypen som Premium/Standard valt utvärderingen rekommenderar en disk SKU i lagringstyp.<br/><br/> Om du vill få en enda instans VM SLA på 99,9%, kan du ange lagringstypen som Premium-hanterade diskar. Alla diskar i utvärderingen kommer sedan att rekommenderas som Premium-hanterade diskar. <br/> Azure Migrate stöder endast hanterade diskar för migreringsutvärdering.<br/> 
**Reserverade instanser (RI)** | Ange den här egenskapen om du har reserverade instanser i Azure. Kostnad få uppskattningar i utvärderingen ska beakta RI rabatter. Reserverade instanser är för närvarande stöds endast för användningsbaserade erbjudanden i Azure Migrate.
**Ändra storlek på kriterium** | Används för att storleksanpassa virtuella datorer. Storlek kan vara prestandabaserad, eller som – lokalt, utan att överväga prestandahistorik.
**Prestandahistorik** | Tidsperioden att beakta för att utvärdera prestanda för virtuella datorer. Den här egenskapen gäller endast när storlek är prestandabaserad.
**Percentilutnyttjande** | Percentilvärdet för prestanda-exemplet som används för rätt storlek för virtuella datorer. Den här egenskapen gäller endast när storlek är prestandabaserad.
**VM-serie** | Den virtuell dator-serie som används för storleksuppskattningar. Om du till exempel har en produktionsmiljö som du inte planerar att migrera till A-seriens virtuella datorer i Azure kan du utesluta A-serien från listan eller serien. Storleken baseras bara på den valda serien.
**Komfortfaktor** | Azure Migrate Server-utvärdering överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor.
**Erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) du har registrerat dig för. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Faktureringsvalutan. 
**Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet.<br/> Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte ska som körs 24 x 7 i Azure måste du ange varaktighet (antal dagar per månad) och antalet timmar per dag för som de skulle köra och kostnadsuppskattningar utförs i enlighet med detta.<br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har software assurance och att du är berättigad till [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. 



## <a name="azure-suitability-analysis"></a>Analys av azures lämplighet

Inte alla datorer är lämpliga för att köra i Azure. Azure Migrate Server-utvärdering utvärderar varje lokal dator för migrering och kategoriserar datorer i en av följande lämplighet kategorier:
- **Redo för Azure** -datorn kan migreras som – är till Azure utan ändringar. Det kommer att starta i Azure med fullständig Azure-support.
- **Villkorligt redo för Azure** -datorn kan starta i Azure, men inte har fullständig Azure-supporten. Till exempel stöds en dator med en äldre version av Windows Server-Operativsystemet inte i Azure. Du måste du vara försiktig innan du migrerar de här datorerna till Azure och följ anvisningarna för reparation som föreslås i utvärdering för att åtgärda beredskapsproblem innan du migrerar.
- **Ej redo för Azure** -datorn kommer inte att starta i Azure. Till exempel om en lokal dator har en disk som mer än 4 TB kopplade till den, kan inte den nås på Azure. Du måste följa åtgärdsvägledning som föreslås i utvärdering för att åtgärda problemet readiness innan du migrerar till Azure. Rätt storlek och kostnadsuppskattning görs inte för datorer som är markerade som ej redo för Azure.
- **Beredskap okänd** – Azure Migrate kunde inte hitta beredskap för datorn på grund av otillräckliga data som är tillgängliga i vCenter Server.



### <a name="machine-properties"></a>Egenskaper för dator

Azure Migrate granskar följande egenskaper för lokala virtuella datorn att identifiera om den kan köras i Azure.

**Egenskap** | **Detaljer** | **Status för Azure-beredskap**
--- | --- | ---
**Starttyp** | Azure har stöd för virtuella datorer med starttypen BIOS och UEFI inte. | Villkorligt redo om starttypen är UEFI.
**Kärnor** | Antalet kärnor på datorerna måste vara lika med eller mindre än det maximala antalet kärnor (128 kärnor) stöds för en Azure-dator.<br/><br/> Om det finns prestandahistorik överväger Azure Migrate utnyttjade kärnor för jämförelse. Om en komfortfaktor anges i inställningarna för utvärdering av multipliceras antalet utnyttjade kärnor med komfortfaktorn.<br/><br/> Om det finns inga prestandahistorik, använder Azure Migrate tilldelade kärnor, utan att tillämpa komfortfaktorn. | Redo om mindre än eller lika med begränsningar.
**Minne** | Storleken på datorn minne måste vara lika med eller mindre än maximalt minne (3892 GB på Azure-M-serien Standard_M128m&nbsp;<sup>2</sup>) tillåts för en Azure-dator. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om det finns prestandahistorik överväger Azure Migrate utnyttjade minne för jämförelse. Om en komfortfaktor anges multipliceras utnyttjade minne med komfortfaktorn.<br/><br/> Om det finns ingen historik allokerat minne används, utan att tillämpa komfortfaktorn.<br/><br/> | Redo om inom gränserna.
**Lagringsdisk** | Allokerade storleken på en disk måste vara 4 TB (4096 GB) eller mindre.<br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disken. | Redo om inom gränserna.
**Nätverk** | En dator måste ha 32 eller färre anslutna nätverkskort till den. | Redo om inom gränserna.

### <a name="guest-operating-system"></a>Gästoperativsystem
Tillsammans med egenskaperna för virtuella datorer tittar Azure Migrate Server-utvärdering på gästoperativsystemet av datorer och identifiera om den kan köras i Azure.

> [!NOTE]
> Azure Migrate Server-utvärdering använder operativsystemet som angetts för den virtuella datorn i vCenter Server för analys. Azure Migrate Server-utvärdering är installationen-baserad för identifieringen av virtuella datorer och det inte att verifiera om det operativsystem som körs på den virtuella datorn är samma som den som angetts i vCenter Server.

Följande logik används av Azure Migrate Server-utvärdering, för att identifiera Azure-beredskap baserat på operativsystemet.

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

## <a name="sizing"></a>Storleksändring

När en dator markeras som redo för Azure, storlekar Azure Migrate den virtuella datorn och dess diskar för Azure.

- Om utvärderingen använder prestandabaserade storleksändringar, överväger Azure Migrate prestandahistoriken för datorn att identifiera VM-storlek och disk-typ i Azure. Den här metoden är särskilt användbart om du har tilldelat en lokal virtuell dator över men användningen är låg och du vill justera storleken den virtuella datorn i Azure för att sänka kostnaderna.
- Om du använder en som en lokal utvärdering, Azure Migrate Server-utvärdering ska ändra storlek på de virtuella datorerna baserat på de lokala inställningarna, utan att överväga användningsdata. Ändra storlek på disken i det här fallet baseras på lagringstypen som du anger i egenskaperna för utvärdering (Standard disk eller Premium-diskar).

### <a name="performance-based-sizing"></a>Prestandabaserad storleksändring

För prestandabaserade storleksändringar behöver Azure Migrate börjar med diskar som är kopplade till den virtuella datorn, följt av nätverkskort, och maps en Azure VM beroende på compute-krav för den lokala virtuella datorn.

- **Storage**: Azure Migrate försöker mappa alla diskar som är anslutna till datorn till en disk i Azure.
    - För att få gällande disk-i/o per sekund (IOPS) och dataflöde (Mbit/s), multiplicerar Azure Migrate disken IOPS och dataflöde med komfortfaktorn. Baserat på effektiv IOPS och dataflöde värden kan identifierar Azure Migrate om disken bör mappas till en standard- eller premium disk i Azure.
    - Om Azure Migrate inte kan hitta en disk med nödvändiga IOPS och dataflöde, markerar den dator som olämpliga för Azure. [Läs mer](../azure-subscription-service-limits.md#storage-limits) om Azure gränser per disk och virtuell dator.
    - Om den hittar en uppsättning lämpliga diskar väljs Azure Migrate de som stöd för vilken lagringsmetod för redundans och den plats som anges i utvärderingsinställningarna för.
    - Om det finns flera berättigade diskar, väljs som har lägst pris.
    - Om prestandadata för diskar är tillgänglig, mappas alla diskar till standarddiskar i Azure.

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

Om du använder som lokala storlek, allokerar en VM-SKU i Azure baserat på de storleken på lokalerna Server-utvärdering. På liknande sätt för disk storlek tittar på lagringstypen som angetts i egenskaperna för utvärdering (Standard/Premium) och rekommenderar disktypen därefter. Lagringstyp standard är Premium-diskar.

## <a name="confidence-ratings"></a>Förtroende-klassificeringar
Varje prestandabaserad utvärdering i Azure Migrate är kopplad till ett säkerhetsomdöme från en (lägsta) till fem startar (högst).
- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.
- Säkerhetsomdömet kan inte användas för som lokala utvärderingar.
- För prestandabaserade storleksändringar behöver Azure Migrate Server-utvärdering
    - Användningsdata för CPU, och VM-minne.
    - För varje disk som är ansluten till den virtuella datorn krävs dessutom information om IOPS och dataflöden.
    - För varje nätverkskort som är anslutet till en virtuell dator, måste på samma sätt säkerhetsomdömet nätverks-i/o att utföra prestandabaserade storleksändringar.
    - Om någon av ovanstående användningsnummer inte är tillgängliga i vCenter Server kanske rekommenderar vi storleken inte tillförlitlig. 

Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen, som du ser nedan:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

### <a name="low-confidence-ratings"></a>Låg förtroende betyg

Några av de orsaker till varför en utvärdering kan hämta ett låga säkerhetsomdöme:

- Du inte har Profilerar din miljö för hela som du skapar utvärderingen. Om du skapar utvärderingen med varaktigheten inställt på 1 dag, måste du vänta minst en dag efter att du startar en identifiering för alla datapunkter att hämta samlas in.
- Vissa virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om alla virtuella datorer stängdes av under en viss period, Azure Migrate Server-utvärdering kan inte samla in prestandadata för den perioden.
- Vissa virtuella datorer skapades under perioden som utvärderingen utfördes. Till exempel om du skapar en utvärdering för prestandahistoriken för den senaste månaden, men vissa virtuella datorer har skapats i miljön en vecka sedan, prestandahistoriken för de nya virtuella datorerna inte det för hela perioden.

  > [!NOTE]
  > Om säkerhetsomdömet för någon utvärdering är lägre än fem stjärnor, rekommenderar vi att du väntar minst en dag att profilera miljön och beräkna om utvärderingen. Om du inte prestandabaserade storleksändringar kanske inte är tillförlitlig, och vi och rekommenderas att byta att utvärderingen ska använda som lokala storlek.
  
## <a name="monthly-cost-estimation"></a>Uppskattning per månad

När storleksrekommendationer har slutförts kan beräknar Azure Migrate beräknings- och kostnaderna för efter migreringen.

- **Beräkningskostnaden**: Med den rekommenderade storleken för virtuell Azure-dator kan använder Azure Migrate Billing-API för att beräkna den månatliga kostnaden för den virtuella datorn.
    - Det operativsystem, software assurance, reserverade instanser, VM drifttid, plats och valutainställningar hänsyn tas med i beräkningen.
    - Sammanställer kostnaden för samtliga datorer att beräkna den totala månadskostnaden för beräkning.
- **Kostnaden för lagring**: Månatliga storage kostnaden för en dator beräknas genom att sammanställa månadskostnaden för alla diskar som är anslutna till datorn
    - Azure Migrate Server-utvärdering beräknar de totala månatliga kostnaderna för lagring genom att sammanställa lagringskostnaderna för alla datorer.
    - Beräkningen tar för närvarande inte erbjudanden som anges i utvärderingsinställningarna för i kontot.

Kostnader visas i den valuta som anges i utvärderingsinställningarna för.


## <a name="next-steps"></a>Nästa steg

Skapa en utvärdering för [virtuella VMware-datorer](tutorial-assess-vmware.md) eller [Hyper-V-datorer](tutorial-assess-hyper-v.md).
