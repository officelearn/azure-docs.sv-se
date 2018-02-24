---
title: "Bedömning beräkningar i Azure migrera | Microsoft Docs"
description: "En översikt över assessment beräkningar i tjänsten Azure migrera."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: b264e2ceac4e76faa37d21972b94cfe323aa3ce5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
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
Windows Server 2003-2008 R2 | Dessa operativsystem har överfört sina slutet av stöd för datum och behovet av en [anpassad stöder avtal (CSA)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt redo för Azure bör du överväga att uppgradera Operativsystemet innan du migrerar till Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Dessa operativsystem har överfört sina slutet av support datum, datorn kan starta i Azure, men ingen OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderas att uppgradera Operativsystemet innan du migrerar till Azure.
Klienten för Windows 7, 8 och 10 | Azure tillhandahåller support med Visual Studio-prenumeration. | Villkorligt redo för Azure
Windows Vista, XP Professional | Dessa operativsystem har överfört sina slutet av support datum, datorn kan starta i Azure, men ingen OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderas att uppgradera Operativsystemet innan du migrerar till Azure.
Linux | Azure rekommenderar dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men det rekommenderas att uppgradera Operativsystemet till en påtecknade version innan du migrerar till Azure. | Redo för Azure om versionen som är godkända.<br/><br/>Villkorligt redo om versionen som inte är godkända.
Andra operativsystem<br/><br/> t.ex. Oracle Solaris, Apple Mac OS etc., FreeBSD osv. | Azure du inte stöder dessa operativsystem. Datorn kan starta i Azure, men ingen OS-support tillhandahålls av Azure. | Villkorligt redo för Azure, rekommenderas att installera ett operativsystem som stöds innan du migrerar till Azure.  
Operativsystem som angetts som *andra* i vCenter Server | Migrera Azure kan inte identifiera Operativsystemet i det här fallet. | Beredskap för okänd. Kontrollera att Operativsystemet som körs på den virtuella datorn stöds i Azure. 
32-bitars operativsystem | Datorn kan starta i Azure, men Azure kan inte ger fullt stöd. | Villkorligt redo för Azure bör du överväga att uppgradera Operativsystemet på datorn från 32-bitars operativsystem till 64-bitars operativsystem innan du migrerar till Azure.

## <a name="sizing"></a>Storlek

När en dator markeras som redo för Azure, storlek Azure migrera den virtuella datorn och dess diskar för Azure. Om sizing kriteriet som angetts i egenskaperna för bedömning är att göra prestandabaserad storlek, anser Azure migrera prestandahistorik för datorn att identifiera en VM-storlek i Azure. Den här metoden är användbart i scenarier där du har tilldelat den lokala virtuella datorn över men användningen är låg och du vill få rätt storlek på virtuella datorer i Azure för att spara kostnader.

> [!NOTE]
> Azure migrera samlar in prestandahistorik över lokala virtuella datorer från vCenter-servern. För att säkerställa exakt rätt storlek för att inställningen statistik i vCenter-servern är inställd på nivå 3 och vänta minst en dag före lanseras identifiering av de lokala virtuella datorerna. Om inställningen statistik i vCenter Server är under nivå 3, samlas inte prestandadata för disk och nätverk. 

Om du inte vill överväga historik för minnesprestanda och vill göra den virtuella datorn som-är till Azure, kan du ange storlek kriterium som *som lokala* och Azure migrera sedan ändra de virtuella datorerna baserat på den lokala konfigurationen utan med tanke på användningsdata.

### <a name="performance-based-sizing"></a>Prestandabaserad storlek

Prestandabaserad förstoras Azure migrera börjar med diskar som är kopplade till den virtuella datorn, följt av nätverkskort och sedan maps en Azure VM baserat på kraven för beräkning av den lokala virtuella datorn. 
 
- **Diskar**: Azure migrera försöker mappa alla diskar som är anslutna till datorn till en disk i Azure. 
    
    > [!NOTE]
    > Azure migrera stöder endast hanterade diskar för utvärdering.
    
    - För att få gällande disk-i/o per sekund (IOPS) och genomströmning (MBps), multiplicerar Azure migrera IOPS för disk och dataflöde med bekvämlighet faktorn. Baserat på effektiva IOPS och genomströmning värden, identifierar Azure migrera om disken som ska mappas till en standard- eller premium disk i Azure.
    - Om Azure migrerar inte hittar en disk med nödvändiga IOPS & genomströmning, markerar datorn som lämpar sig inte för Azure. [Lär dig mer](../azure-subscription-service-limits.md#storage-limits) om Azure begränsar per disk- och VM.
    - Om den hittar en uppsättning lämpliga diskar väljer Azure migrera de som stöd för redundans lagringsmetod och den plats som anges i inställningarna för utvärdering.
    - Om det finns flera tillgängliga diskar, väljs med lägst kostnad.
    - Om prestandadata för diskar i tillgänglig, alla diskar som är mappade till standarddiskar i Azure.

- **Nätverkskort**: Azure migrera försöker hitta en virtuell Azure-dator som har stöd för antalet nätverkskort som är anslutna till den lokala datorn och prestanda som krävs av dessa nätverkskort.
    - För att få gällande nätverkets prestanda på den lokala virtuella datorn, Azure migrera samlar in data som överförs per sekund (MBps) utanför datorn (nätverk ut) över alla nätverkskort och gäller bekvämlighet faktorn. Numret används för att hitta en virtuell Azure-dator som har stöd för nödvändiga nätverkets prestanda.
    - Tillsammans med nätverkets prestanda även betraktar om den virtuella Azure-datorn stöder det obligatoriska antalet nätverkskort.
    - Om det finns inga nätverk prestandadata anses endast nätverkskort antalet för VM-storlek.

- **VM-storlek**: när krav för lagring och nätverk beräknas Azure migrera anser CPU och minne kraven för att hitta en lämplig VM-storlek i Azure.
    - Azure migrera tittar på utnyttjade kärnor och minne och gäller bekvämlighet faktor för att få gällande kärnor och minne. Baserat på det antalet försök att hitta en lämplig VM-storlek i Azure.
    - Om det finns ingen lämplig storlek, markeras datorn som lämpar sig inte för Azure.
    - Om det finns en lämplig storlek, gäller Azure migrera beräkningar för lagring och nätverk. Det gäller plats och prissättning nivån inställningar för slutlig VM storlek rekommendationen.
    - Om det finns flera tillgängliga storlekar på Virtuella Azure, rekommenderas det med lägst kostnad.

### <a name="as-on-premises-sizing"></a>Som lokalt storlek
Om sizing kriteriet är *som lokalt storlek*, Azure migrerar inte anser prestandahistorik för de virtuella datorerna och allokerar virtuella datorer och diskar baserat på storleken tilldelas lokalt.
- **Lagring**: för varje disk, rekommenderas en disk som standard i Azure med samma storlek som den lokala disken.
- **Nätverket**: för varje nätverkskort, ett nätverkskort i Azure rekommenderas.
- **Beräkna**: Azure migrera tittar på antal kärnor och minnesstorleken för den lokala virtuella datorn och rekommenderar en Azure-dator med samma konfiguration. Om det finns flera tillgängliga storlekar på Virtuella Azure, rekommenderas det med lägst kostnad.
 
### <a name="confidence-rating"></a>Förtroende klassificering

Varje bedömning i Azure migrera är associerad med en klassificering för förtroende med ett intervall från 1 Stjärna 5 stjärnan (1 stjärna som det lägsta och 5 star som högst). Klassificeringen förtroende har tilldelats en bedömning baserat på tillgängligheten för datapunkter som behövs för att beräkna bedömningen. Förtroende-klassificering av en bedömning hjälper dig att beräkna storleken rekommendationer som tillhandahålls av Azure migrera tillförlitlighet. 

Klassificering av förtroende är användbart när du gör *prestandabaserad storlek* som Azure migrera inte kanske har tillräcklig datapunkter för att göra användning-baserade storlek. För *som lokalt storlek*, förtroende klassificeringen alltid är 5 stjärnor när Azure migrera har alla datapunkter som behövs för att ändra storlek på den virtuella datorn. 

För prestandabaserad storleksändring av den virtuella datorn måste Azure migrera användningsdata för CPU och minne. Dessutom för varje disk som är ansluten till den virtuella datorn, den måste läsning och skrivning IOPS och genomflöde. På samma sätt för varje nätverkskort som är kopplade till den virtuella datorn, måste migrera Azure nätverket in/ut gör prestandabaserad storlek. Om någon av siffrorna för senare användning inte är tillgängliga i vCenter Server kanske storlek rekommendationen genom Azure migrera inte tillförlitligt. Beroende på procentandelen datapunkter som är tillgängliga finns förtroende klassificering för bedömning:

   **Tillgänglighet för datapunkter** | Förtroende klassificering
   --- | ---
   0%-20% | 1 stjärna
   21%-40% | 2 stjärna
   41%-60% | 3 stjärna
   61%-80% | 4 stjärna
   81%-100% | 5 Star

En utvärdering kanske inte alla datapunkter som är tillgänglig på grund av något av följande skäl:
- Statistik-inställningen i vCenter Server inte är inställt på nivå 3 och bedömningen har prestandabaserad storlek som kriteriet storlek. Om inställningen statistik i vCenter Server är lägre än nivå 3 samlas inte prestandadata för disk och nätverk från vCenter-servern. I det här fallet är rekommendationen som tillhandahålls av Azure migrera för disk- och inte baserat på användning. För lagring rekommenderar Azure migrera standarddiskar som utan att överväga IOPS/dataflöde på disken, migrera Azure inte kan identifiera om disken behöver en premium disk i Azure.
- Inställningen statistik i vCenter Server har angetts till nivå 3 för en kortare varaktighet innan lanseras identifieringen. Nu ska vi titta exempelvis ett scenario där du kan ändra statistik anger nivån till 3 idag och kick inaktivera upptäckt med anordningen insamlaren imorgon (efter 24 timmar). Om du skapar en bedömning under en dag, du har alla datapunkter och förtroende klassificering av bedömningen skulle vara 5 stjärnor. Men om du ändrar varaktigheten prestanda i assessment egenskaper till en månad förtroende klassificering som kraschar som disken och nätverk prestandadata för den sista en månaden då inte användas. Om du vill att överväga prestandadata för senaste en månad, rekommenderas att du behåller vCenter Server statistik inställningen till nivå 3 för en månad innan du startar identifieringen. 
- Några VMs stängdes av under den period som bedömningen beräknas. Om virtuella datorer har stängas av för vissa varaktigheten, vCenter-servern inte prestandadata för perioden. 
- Några virtuella datorer har skapats mellan den period som bedömningen beräknas. Till exempel om du skapar en bedömning för prestandahistorik för senaste en månad, men några virtuella datorer har skapats i miljön endast en vecka tidigare. I sådana fall prestandahistorik för de nya virtuella datorerna inte det för hela den tid.

> [!NOTE]
> Om förtroende klassificering av en bedömning är lägre än 4 stjärnor, vi rekommenderar att du vill ändra vCenter servernivå statistik inställningar till 3, vänta tills den varaktighet som du vill överväga för (1 dag i en vecka/1 månad) och gör sedan identifiering och assessment. Om den föregående inte kan utföras, prestandabaserad storlek kanske inte är tillförlitliga och det rekommenderas att växla till *som lokalt storlek* genom att ändra egenskaperna assessment.

## <a name="monthly-cost-estimation"></a>Månatliga uppskattning

När sizing rekommendationer är klart, beräknar Azure migrera kostnader för beräkning och lagring av efter migreringen.

- **Beräkna kostnaden**: med den rekommenderade Azure VM-storleken Azure migrera använder fakturerings-API för att beräkna månadskostnaden för den virtuella datorn. Operativsystemet, software assurance, plats och valutainställningar hänsyn tas med i beräkningen. Den sammanställer kostnaden över alla datorer att beräkna den totala månatliga beräkna kostnaden.
- **Lagringskostnaden**: månatliga storage kostnaden för en dator beräknas genom insamling av månadskostnaden för alla diskar som är anslutna till datorn. Azure migrera beräknar de totala månatliga lagringskostnaderna genom att sammanställa kostnader för lagring av alla datorer. Beräkningen tar för närvarande inte erbjudanden som anges i inställningarna för utvärdering i beräkningen.

Kostnaderna visas i valutan som anges i inställningarna för utvärdering. 


## <a name="next-steps"></a>Nästa steg

[Skapa en bedömning för lokala virtuella VMware-datorer](tutorial-assessment-vmware.md)
