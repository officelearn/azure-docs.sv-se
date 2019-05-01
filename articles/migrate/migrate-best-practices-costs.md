---
title: Metodtips för kostnadshantering och ändra storlek på arbetsbelastningar migreras till Azure | Microsoft Docs
description: Få metodtips för kostnadshantering och ändra storlek på arbetsbelastningar migreras till Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 48ce99bd830d2c35e5cb9703d2ef754a602d534b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926157"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Metodtips för tar och storlek arbetsbelastningar migreras till Azure

Som du planerar och utformar för migrering säkerställer att fokusera på kostnaderna långsiktig framgång för din Azure-migrering. Under ett migreringsprojekt är det viktigt att alla team (ekonomi, hantering, appteamen osv) förstå associerade kostnader.
- Uppskatta migreringen molnkostnader, med en baslinje för månadsvis, kvartalsvis, före migreringen, och årliga budget mål är nyckeln till framgång.
- Efter migreringen bör du optimera kostnaderna, kontinuerligt övervaka arbetsbelastningar och planera för framtida användningsmönster. Migrerade resurser kan starta som en typ av arbetsbelastning, men utvecklas till en annan typ, baserat på användning, kostnader och flyttade affärskrav.

Den här artikeln beskriver Metodtips för kostnadshantering och ändra storlek på före och efter migreringen.  

> [!IMPORTANT]
> Bästa praxis och yttranden som beskrivs i den här artikeln baseras på Azure-plattformen och bearbeta funktioner som är tillgängliga vid tidpunkten för skrivning. Funktioner och möjligheter ändras med tiden. Inte alla rekommendationer gäller för din distribution, så Välj vad som passar dig.


## <a name="before-migration"></a>Före migreringen 

Uppskatta månadskostnaden för att köra dem i Azure innan du flyttar dina arbetsbelastningar till molnet. Proaktiv hantering av molnkostnader hjälper dig att följa dina administrativa kostnader (OpEx)-budget. Om budget är begränsat, ta hänsyn före migreringen.  Överväg att konvertera arbetsbelastningar till Azure utan Server-teknik, där det är lämpligt att minska kostnaderna.

De bästa metoderna i det här avsnittet hjälper dig att beräkna kostnaderna, utföra rätt storlek för virtuella datorer och lagring, utnyttja Azure Hybrid-förmåner, använda reserverade virtuella datorer och beräkna molnet utgifter mellan prenumerationer.



## <a name="best-practice-estimate-monthly-workload-costs"></a>Rekommendation: Varje månad beräkna kostnaderna för arbetsbelastning
 
Det finns ett antal verktyg som du kan använda för att beräkna din månatliga faktura för migrerade arbetsbelastningar.

- **Azures priskalkylator**: Du kan välja de produkter som du vill beräkna måste till exempel virtuella datorer och lagring. Du kan ange kostnader till priskalkylatorn att skapa en beräkning.

  ![Azures priskalkylator](./media/migrate-best-practices-costs/pricing.png) *Azures priskalkylator*

- **Azure Migrate**: Om du vill beräkna kostnaderna för måste måste du granska och ta hänsyn till alla resurser som krävs för att köra dina arbetsbelastningar i Azure. Om du vill hämta dessa data, skapa inventering av dina tillgångar, inklusive servrar, virtuella datorer, databaser och lagring. Du kan använda Azure Migrate för att samla in den här informationen.

  - Azure Migrate identifierar och utvärderar din lokala miljö för att visa en förteckning.
  - Azure Migrate kan mappa och visa beroenden mellan virtuella datorer så att du har en bild.
  - Ett Azure Migrate-utvärdering innehåller uppskattade kostnaden.
    - Beräkna kostnader: Med Virtuella Azure-datorstorlek som rekommenderas när du skapar en utvärdering använder Azure Migrate Billing-API för att beräkna uppskattade månatliga kostnader för virtuell dator. Uppskattningen tar hänsyn till de operativsystem, software assurance, reserverade instanser, VM drifttid, plats och valutainställningar. Den sammanställer kostnaden för samtliga virtuella datorer i utvärderingen och beräknar en totala månadskostnaden för beräkning.
    - Kostnaden för lagring: Azure Migrate beräknar totala månatliga kostnader för lagring genom att sammanställa lagringskostnaderna för alla virtuella datorer i en utvärdering. Du kan beräkna månatliga lagringskostnaden för en specifik dator genom att sammanställa månadskostnaden för alla diskar som är anslutna till den. 

    ![Azure Migrate](./media/migrate-best-practices-costs/assess.png)
    *Azure Migrate-utvärdering*

**Lära sig mer:**
- [Använd](https://azure.microsoft.com/pricing/calculator/) Azures priskalkylator.
- [Få en översikt](https://docs.microsoft.com/azure/migrate/migrate-overview) av Azure Migrate.
- [Läs mer om](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation) Azure Migrate-utvärderingar.
- [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview) om Database Migration Service (DMS).

## <a name="best-practice-right-size-vms"></a>Rekommendation: Storleksanpassa virtuella datorer

Du kan välja mellan olika alternativ när du distribuerar virtuella datorer i Azure som stöd för arbetsbelastningar. Varje typ av virtuell dator har specifika funktioner och olika kombinationer av CPU, minne och diskar. Virtuella datorer är grupperade på följande sätt.

**Typ** | **Detaljer** | **Använd**
--- | --- | ---
**Generellt syfte** | Balanserat förhållande mellan processor och minne. | Bra för testning och utveckling, små till medelstora databaser, med låg till medelhög trafik webbservrar.
**Optimerad för beräkning** | Högt förhållande mellan processor och minne. | Bra för webbserver för medelhög trafik, nätverkstillämpningar, batchprocesser, app-servrar.
**Minnesoptimerad** | Hög minne-till-processor. | Bra för relationsdatabaser, mellanstora till stora cache, minnesinterna analyser.
**Lagringsoptimerad** | Högt diskgenomflöde och I/O. | Bra för stordata, SQL och NoSQL-databaser.
**GPU-optimerad** | Virtuella specialdatorer. En eller flera grafikprocessorer. | Tung grafik och videoredigering.
**Höga prestanda** | Snabbaste och mest kraftfulla CPU. Virtuella datorer med valfritt högt dataflöde nätverksgränssnitt (RDMA) | Kritiska appar med höga prestanda.

- Det är viktigt att förstå prisskillnaderna mellan dessa virtuella datorer och hur långsiktig budget.
- Varje typ har ett antal VM-serie i den.
- Dessutom när du väljer en virtuell dator i en serie kan skala du endast den virtuella datorn uppåt och nedåt i serien. Till exempel en DSv2\_2 kan skala upp till DSv2\_4, men det kan inte ändras till en annan serie, till exempel Fsv2\_2.

**Lära sig mer:**
- [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) om VM-typer och storlek och kartan storlek till typer.
- [Planera](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs) VM-storlek.
- [Granska](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment) ett exempel på utvärdering för det fiktiva företaget Contoso.

## <a name="best-practice-select-the-right-storage"></a>Rekommendation: Välj rätt lagring

Justera och underhålla en lokal lagring (SAN- eller NAS) och nätverk som stöder dem, kan vara kostsamt och tidskrävande. Fildata (lagring) migreras ofta till molnet för att minska operativa och hanteringsproblem. Microsoft erbjuder flera alternativ för att flytta data till Azure och du behöver fatta beslut om dessa alternativ. Välja rätt lagringstyp för data kan spara din organisation flera tusen dollar varje månad. Några saker:

- Data som inte är används mycket, och inte sina verksamhetskritiska, behöver inte placeras på det dyraste lagringsutrymmet.
- Viktiga affärskritiska data måste däremot finnas på högre nivå lagringsalternativ.
- Under migrering, göra en förteckning över data och klassificera den efter prioritet, för att mappa den till den lämpligaste lagringen. Överväg att budget och kostnader samt prestanda. Kostnad bör inte nödvändigtvis vara den huvudsakliga beslutsfattande faktorn. Välja det billigaste alternativet kan exponera arbetsbelastningen att prestanda och tillgänglighet risker. 

### <a name="storage-data-types"></a>Storage-datatyper

Azure tillhandahåller olika typer av lagringsdata.

| **Datatyp** | **Detaljer** | **Användning** |
|--- | --- |  --- |
|**Blobbar** | Optimerad för att lagra stora mängder Ostrukturerade objekt, till exempel text eller binära data<br/>Åtkomst till data överallt via HTTP/HTTPS. | Använd för scenarier med strömmande och slumpmässig åtkomst. Till exempel för att tillhandahålla bilder och dokument direkt till en webbläsare, strömma video och ljud och lagra återställningsdata för säkerhetskopiering och haveriberedskap.|
|**Filer** | Hanterade filresurser som nås via SMB 3.0 | Använda när du migrerar lokala filresurser, och för att ge flera nätverksanslutningar till fildata.|
|**Diskar** | Baserat på sidblobar.<br/><br/> Disktyp (hastighet): Standard (Hårddisk eller SSD) eller Premium (SSD).<br/><br/>Diskhantering: Ohanterade (du hantera Diskinställningar och lagring) eller hanterad (du väljer typ av disk och Azure hanterar disken åt dig). | Använd Premium-diskar för virtuella datorer. Använda hanterade diskar för enkel hantering och skalning.|
|**köer** | Store och hämta stora mängder meddelanden som kan nås via autentiserade anrop (HTTP eller HTTPS) | Anslut appkomponenter med asynkrona meddelandeköer.|
|**Tabeller** | Store tabeller. | Nu en del av Azure Cosmos DB Table API.|



### <a name="access-tiers"></a>Åtkomstnivåer
Azure storage tillhandahåller olika alternativ för åtkomst till block blob-data. Att välja rätt åtkomstnivå hjälper till att säkerställa att du lagrar block blob-data på det mest kostnadseffektiva sättet.

**Typ** | **Detaljer** | **Användning**
--- | --- | ---
**hot** | Högre lagringskostnad än lågfrekvent. Lägre åtkomstavgifter än lågfrekvent.<br/><br/>Det här är den standardlagringsnivån. | Används för data i aktiv användning som används ofta.
**Lågfrekvent** | Lägre lagringskostnad än hot (frekvent). Högre åtkomstavgifter än hot (frekvent).<br/><br/> Store för minst 30 dagar. | Store kortsiktig, data är tillgänglig men används sällan.
**Arkiv** | Används för enskilda blockblob-objekt.<br/><br/> Mest kostnadseffektiva alternativ för lagring. Åtkomst till data är dyrare än heta och kalla. | Används för data som kan tolerera server timmars svarstid för hämtning och förblir på nivån i minst 180 dagar. 

### <a name="storage-account-types"></a>Storage-kontotyper

Azure tillhandahåller olika typer av lagringskonton och prestandanivåer.

**Kontotyp** | **Detaljer** | **Användning**
--- | --- | ---
**Generell användning v2 Standard** | Har stöd för blobar (blockera, sidan, Lägg till) filer, diskar, köer och tabeller.<br/><br/> Stöder åtkomstnivåerna varm, kall och Arkiv. ZRS stöds. | Används för de flesta scenarier och de flesta typer av data. Standard storage-konton kan vara Hårddisk eller SSD-baserad.
**Generell användning v2 Premium** | Har stöd för Blob storage-data (sidblob). Stöder åtkomstnivåerna varm, kall och Arkiv. ZRS stöds.<br/><br/> Lagras på SSD. | Microsoft rekommenderar för alla virtuella datorer.
**Generell användning v1** | Åtkomst lagringsnivåer stöds inte. Stöder inte ZRS | Använd om apparna måste den klassiska distributionsmodellen.
**Blob** | Specialiserat lagringskonto för lagring av Ostrukturerade objekt. Tillhandahåller blockblobbar och tilläggsblobbar endast (ingen fil, kö, tabell eller Disk storage-tjänster). Innehåller samma tillförlitlighet, tillgänglighet, skalbarhet och prestanda som generell användning v2. | Du kan inte lagra sidblobbar i dessa konton och därför kan inte lagra VHD-filer. Du kan ange en åtkomstnivå till frekvent eller lågfrekvent.

### <a name="storage-redundancy-options"></a>Alternativ för lagringsredundans

Storage-konton kan använda olika typer av redundans för ökad flexibilitet och hög tillgänglighet.

**Typ** | **Detaljer** | **Användning**
--- | --- | ---
**Lokalt Redundant lagring (LRS)** | Skyddar mot en lokal avbrott genom att replikera inom en enda lagringsenhet till en separat feldomän och uppdateringsdomän. Behåller flera kopior av dina data i ett datacenter. Innehåller minst 99,999999999% (11 9\'s) objektshållbarhet under ett givet år. | Överväg om din app lagras data som enkelt rekonstrueras.
**Zonredundant lagring (ZRS)** | Skyddar igen avbrott i ett datacenter genom att replikera mellan tre lagringskluster i en enda region. Varje lagringskluster är fysiskt avgränsade och finns i sin egen tillgänglighetszon. Innehåller minst 99,9999999999% (12 9\'s) objektshållbarhet under ett givet år genom att flera kopior av dina data förvaras i flera Datacenter eller regioner. | Överväg om du behöver konsekvens, hållbarhet och hög tillgänglighet. Kanske inte att skydda mot ett regionalt haveri när flera zoner påverkas permanent.
**Geografiskt Redundant lagring (GRS)** | Skyddar mot en hel region avbrott genom att replikera data till en sekundär region hundratals mil bort från primärt. Innehåller minst 99,99999999999999% (16 9\'s) objektshållbarhet under ett givet år. | Replikdata är inte tillgängligt om inte Microsoft initierar en växling till den sekundära regionen. Läs- och skrivåtkomst är tillgängligt om redundans.
**Läsåtkomst till geografiskt Redundant lagring (RA-GRS)** | Liknar GRS. Innehåller minst 99,99999999999999% (16 9\'s) objektshållbarhet under ett givet år | Innehåller och 99,99% läsningstillgänglighet genom att läsåtkomst tillåts från den andra regionen som används för GRS.

**Lära sig mer:**
- [Granska](https://azure.microsoft.com/pricing/details/storage/) priser för Azure Storage.
- [Lär dig mer om](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Azure Import/Export för migrering stora mängder data till Azure-blobar och filer. 
- [Jämför](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) blobar, filer och datatyper för disk-lagring.
- [Läs mer](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) om åtkomstnivåerna.
- [Granska](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) olika typer av lagringskonton.
- Lär dig mer om [lagringsredundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), och [Read-access GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage).
- [Läs mer](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) om Azure Files.



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>Rekommendation: Utnyttja Azure Hybrid-förmåner

På grund av års programvara investering i system som Windows Server och SQL Server är Microsoft unik möjlighet att erbjuda kunderna värdet i molnet, med avsevärd rabatt med andra molnleverantörer kan inte nödvändigtvis. 

En integrerad Microsoft på lokal/Azure produktportfölj genererar konkurrenskraftiga och kostnad fördelar. Om du har ett operativsystem eller andra programvarulicenser genom software assurance (SA), kan du ta dessa licenser med dig till molnet för med Azure Hybrid-förmånen.

**Lära sig mer:**

- [Ta en titt på](https://azure.microsoft.com/pricing/hybrid-benefit/) kalkylator för besparingar Hybrid-förmånen.
- [Läs mer](https://azure.microsoft.com/pricing/hybrid-benefit/) om Hybrid-förmånen för Windows Server.
- [Granska](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) priser för SQL Server Azure-datorer.


## <a name="best-practice-use-reserved-vm-instances"></a>Rekommendation: Använda reserverade VM-instanser

De flesta plattformar har ställts in som betalar per användning. Den här modellen anger hur många nackdelarna, eftersom du inte nödvändigtvis vet hur dynamiskt arbetsbelastningar blir. När du anger Rensa avsikter för en arbetsbelastning kan bidra du till infrastrukturplanering.

Med hjälp av Azure reserverade VM-instanser, du betalar i förskott för ett eller tre år, VM-instans. 

- Förskottsbetalning ger en rabatt på de resurser du använder.
- Du kan avsevärt minska VM, SQL database-beräkning, Azure Cosmos DB eller andra resurskostnader med upp till 72% jämfört med användningsbaserad betalning. 
- Reservationer ger en rabatt på fakturering och påverka inte körtiden för dina resurser.
- Du kan avbryta reserverade instanser.

![Reserverade instanser](./media/migrate-best-practices-costs/reserve.png)
*reserverade virtuella datorer i Azure*

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) Azure reservationer.
- [Läs](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq) reserverade instanser vanliga frågor och svar.
- [Hämta priser vägledning](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) för SQL Server Azure-datorer.


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>Rekommendation: Sammanställd molnutgift mellan prenumerationer

Det är ofrånkomligt att så småningom du har mer än en Azure-prenumeration. Till exempel behöva en ytterligare prenumeration för att avgränsa utveckling och produktion gränser eller du kanske har en plattform som kräver en separat prenumeration för varje klient. Att ha möjlighet att samla in data rapportering för alla prenumerationer i en enda plattform är en viktig funktion.

Om du vill göra detta måste använda du Azure Cost Management API: er. Sedan efter datainsamling i en enda källa, till exempel Azure SQL, kan du använda verktyg som Power BI till att visa aggregerade data. Du kan skapa aggregerade prenumeration rapporter och detaljerade rapporter. Till exempel för användare som behöver proaktiv insikter om kostnadshantering, kan du skapa specifika vyer av kostnader, baserat på avdelning, resursgrupp osv. Du behöver inte ange dem med fullständig åtkomst till Azure faktureringsinformation.

**Lära sig mer:**

- [Få en översikt](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview) för API i Azure-förbrukning.
- [Lär dig mer om](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights) ansluter till Azure Consumption Insights i Power BI Desktop.
- [Lär dig hur du](https://docs.microsoft.com/azure/billing/billing-manage-access) hantera åtkomst till faktureringsinformation för Azure med hjälp av rollbaserad åtkomstkontroll (RBAC).
 
## <a name="after-migration"></a>Efter migrering

Efter en lyckad migrering av dina arbetsbelastningar och ett par veckors samlar in förbrukningsdata, får du en tydlig bild av resurser kostnader.
- När du analyserar data kan du börja Generera en budget baslinje för Azure-resursgrupper och resurser.
- Du kan sedan analysera hur du ytterligare minska kostnaderna när du förstår där din cloud budget spenderas.

Bästa praxis i det här avsnittet innehåller med Azure Cost Management för Kostnadsbudgetering och analyser, övervakning av resurser och implementera resource group budgetar och optimera övervakning, lagring och virtuella datorer.

## <a name="best-practice-use-azure-cost-management"></a>Rekommendation: Använda Azure Cost Management

Microsoft tillhandahåller Azure Cost Management som hjälper dig att spåra olika utgifter, enligt följande:

- Hjälper dig att övervaka och kontrollera Azure utgifter och optimera användningen av resurser.
- Går igenom hela prenumerationen och alla dess resurser och lämnar rekommendationer.
- Ger en fullständig API för att integrera externa verktyg och finansiella system för rapportering.
- Spårar resursanvändningen och hantera molnkostnader med en enda enhetlig vy.
- Tillhandahåller omfattande drift och ekonomi analyser för att hjälpa dig att fatta välgrundade beslut.

I Cost Management kan du:


- **Skapa en budget**: Skapa en budget för finansiella ansvarstagande.
  - Du kan ta hänsyn till de tjänster du förbrukar eller prenumererar för en viss tidsperiod (månadsvis, kvartalsvis, per år) och ett omfång (prenumerationerna/resursgrupperna). Du kan till exempel skapa en Azure-prenumeration budget för under en månadsvis, Kvartalsvis eller årligen.
  - När du har skapat en budget, visas den i kostnadsanalys. Visa din budget mot aktuell kostnad är en av de första stegen som behövs när du analyserar dina kostnader och utgifter.
  - E-postmeddelanden kan skickas när budgetgränser nås.
  - Du kan exportera kostnaderna för hantering av data till Azure storage för analys.

    ![Hantering av kostnadsbudget](./media/migrate-best-practices-costs/budget.png)
    *Azure Cost Management budget*

- **Gör en kostnadsanalys**: Hämta en kostnadsanalys att utforska och analysera dina organisationens kostnader för att hjälpa dig att förstå hur kostnader påförs och identifiera utgiftstrender.
  - Kostnadsanalys är tillgänglig för EA-användare.
  - Du kan visa cost analysis-data för ett antal scope, inklusive efter avdelning, konto, prenumeration eller resursgrupp.
  - Du kan få en kostnadsanalys som visar totala kostnader för den aktuella månaden och ackumulerade dagliga kostnaderna. 

    ![Hantering av analys av kostnader](./media/migrate-best-practices-costs/analysis.png)
    *Azure Cost Management-analys*
- **Få rekommendationer**: Få Advisor-rekommendationer som visar hur du kan optimera och förbättra effektiviteten.


**Lära sig mer:**

- [Få en översikt](https://docs.microsoft.com/azure/cost-management/overview) av Azure Cost Management.
- [Lär dig hur du](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices) optimera din molninvestering med Azure Cost Management.
- [Lär dig hur du](https://docs.microsoft.com/azure/cost-management/use-reports) Användarrapporter för Azure Cost Management.
- [Hämta en självstudiekurs](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) om hur du optimerar kostnaderna från rekommendationer.
- [Granska](https://docs.microsoft.com/rest/api/consumption/budgets) Azure-förbrukning API.

## <a name="best-practice-monitor-resource-utilization"></a>Rekommendation: Övervaka resursutnyttjande

I Azure betalar du för det du använder, när resurser används, och du behöver inte betala när de inte är. För virtuella datorer sker fakturering när en virtuell dator har allokerats och du debiteras inte när en virtuell dator har frigjorts. Med detta i åtanke bör du övervaka virtuella datorer som används och kontrollera VM-storlek.

- Utvärdera kontinuerligt dina VM-arbetsbelastningar för att fastställa baslinjer.
- Om din arbetsbelastning är används mycket måndag till fredag kl. 8: 00 till 18: 00, men knappt används utanför dessa tider, kan du till exempel nedgradera virtuella datorer utanför Högbelastningstider. Detta kan innebära att ändra VM-storlekar eller virtuell dator använder skalningsuppsättningar för virtuella datorer automatisk skalning upp eller ned.
- Vissa företag ”viloläge”, virtuella datorer genom att sätta dem i en kalender som anger när de ska vara tillgängliga och när de inte behövs. 
- Förutom övervakning av virtuell dator bör du övervaka andra nätverksresurser, till exempel ExpressRoute och virtuella nätverksgatewayer för under och över användning.
- Du kan övervaka VM-användning med hjälp av ett antal Microsoft-verktyg, inklusive Azure kostnadshantering, Azure Monitor och Azure Advisor. Det finns också verktyg från tredje part.  

**Lära sig mer:**
- Få en översikt över [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview).
- [Hämta](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) Advisor kostnad rekommendationer.
- [Lär dig hur du [optimera kostnaderna från rekommendationer](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json), och [förhindra oväntade avgifter](https://docs.microsoft.com/azure/billing/billing-getting-started).
- [Lär dig mer om](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/) verktyget för optimering (ARO) Azure-resurs

## <a name="best-practice-implement-resource-group-budgets"></a>Rekommendation: Implementera resource group budgetar

Resursgrupper används ofta för att representera kostnaden gränser. Tillsammans med det här användningsmönstret fortsätter Azure-teamet att utveckla nya och förbättrade sätt att spåra och analysera resource utgifter på olika nivåer, inklusive möjligheten att skapa budgetar på resursgruppen och resurser.  

- En resurs grupp budget kan du spåra kostnaderna för en resursgrupp.
- Du kan utlösa aviseringar och köra en mängd olika spelböcker som budgeten har uppnåtts eller överskridits. 

**Lära sig mer:**

- [Lär dig hur du](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) hantera kostnader med Azure-budget.
- [Följ en självstudie](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) att skapa och hantera en Azure budget.


## <a name="best-practice-optimize-azure-monitor-retention"></a>Rekommendation: Optimera Azure Monitor kvarhållning

När du flyttar resurser till Azure och aktivera Diagnostisk loggning för dem, skapa ett stort antal loggdata. Den här loggdata skickas vanligtvis till ett lagringskonto som är mappad till en Log Analytics-arbetsyta.

- Ju längre loggkvarhållningsperiod för data, Ju mer data du har.
- Inte alla loggdata är lika, och vissa resurser genererar mer loggdata än andra.
- På grund av bestämmelser och efterlevnad är det troligt att du måste behålla loggdata för vissa resurser som är längre än andra.
- Du bör gå igenom en noggrann rad mellan optimera dina kostnader för lagring av loggen och se till att loggdata som du behöver.
- Vi rekommenderar att utvärdera och konfigurera loggning omedelbart när du har slutfört en migrering, så att du inte är utgiftsgräns pengar behålla loggar av ingen betydelse.

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs) övervaka användning och uppskattade kostnader.
 
## <a name="best-practice-optimize-storage"></a>Rekommendation: Optimera lagring

Om du har följt Metodtips för att välja storage före migreringen, du förmodligen dra nytta vissa fördelarna. Det finns dock förmodligen ytterligare lagringskostnader som du kan optimera. Med tiden blir blobar och filer inaktuella. Data kan inte användas längre, men regelkrav kan innebära att du behöver att hålla en viss tid. Därför kan du inte behöva lagra den på den lagring med höga prestanda som du använde för den ursprungliga migreringen.

Identifiera och flytta inaktuella data till billigare lagring kan ha en stor inverkan på din månatliga storage budget och kostnadsbesparingar. Azure tillhandahåller många sätt att hjälpa dig att identifiera och sedan lagra den här inaktuella data.

- Dra nytta av åtkomstnivåer för lagring för generell användning v2, flytta mindre viktiga data från frekvent till lågfrekvent lagring och arkiverad nivåerna.
- Använd StorSimple för att flytta inaktuella data baserat på anpassade principer. 

**Lära sig mer:**
- [Läs mer](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) om åtkomstnivåerna.
- [Få en översikt](https://docs.microsoft.com/azure/azure-monitor/overview) av StorSimple, och [StorSimple priser](https://azure.microsoft.com/pricing/details/storsimple/).

## <a name="best-practice-automate-vm-optimization"></a>Rekommendation: Automatisera VM optimering

Målet för att köra en virtuell dator i molnet är att maximera den CPU, minne och disk som används. Om du identifiera virtuella datorer som inte är optimerad eller har ofta punkter när virtuella datorer inte används kan det vara bra att stänga dem eller skala ned dem med hjälp av VM-skalningsuppsättningar.

Du kan optimera en virtuell dator med Azure Automation, VM-skalningsuppsättningar, automatisk avstängning och skriptbaserade eller 3 part lösningar. 

**Lära sig mer:**

- [Lär dig hur du](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision) Använd vertikal automatisk skalning.
- [Schema](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/) autostart för en virtuell dator.
- [Lär dig hur du](https://docs.microsoft.com/azure/automation/automation-solution-vm-management) starta eller stoppa virtuella datorer utanför arbetstid i Azure Automation.
- [Hämta mer information] om [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview), och [optimering för Azure-resurs (ARO) Toolkit](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/).

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>Bästa praxis: Använda Logic Apps och runbooks med budgetar API

Azure tillhandahåller ett REST-API som har åtkomst till din klient faktureringsinformation.

- Du kan använda budgetar API: et för att integrera externa system och arbetsflöden som utlöses av mått som du skapar från API-data.
- Du kan hämta användnings-och resurs till din önskade dataanalysverktyg.
- Azures API:er för resursanvändning och RateCard kan hjälpa dig att korrekt förutse och hantera dina kostnader.
- API: er implementeras som en Resursprovider och ingår i API: er som exponeras av Azure Resource Manager.
- Budgetar API kan integreras med Azure Logic Apps och Runbooks.

**Lära sig mer:**

- [Läs mer](https://docs.microsoft.com/rest/api/consumption/budgets) om budgetar API.
- [Få insikter](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) i Azure-användning med fakturerings-API.


## <a name="best-practice-implement-serverless-technologies"></a>Rekommendation: Implementera serverfria tekniken

VM-arbetsbelastningar migreras ofta ”i befintligt skick” nedtiden. Ofta virtuella datorer kan vara värd för uppgifter som är tillfälligt, tar en kort period ska köras, eller också många timmar. Till exempel uppgift virtuella datorer som kör schemalagda aktiviteter, till exempel Windows scheduler eller PowerShell-skript. Om dessa uppgifter inte är igång kan du ändå absorbera VM och disk kostnader för lagring.

Efter migreringen, efter en grundlig genomgång av dessa typer av uppgifter kan du migrera dem utan Server tekniker, till exempel Azure Functions eller Azure Batch-jobb. Med den här lösningen behöver du inte längre att hantera och underhålla de virtuella datorerna, att spara en extra kostnad. 

**Lära sig mer:**
- [Lär dig mer om](https://azure.microsoft.com/services/functions/) Azure Functions
- [Läs mer om](https://azure.microsoft.com/services/batch/) Azure Batch
  
## <a name="next-steps"></a>Nästa steg 

Granska andra metodtips:

- [Bästa praxis](migrate-best-practices-security-management.md) för säkerhet och hantering efter migreringen.
- [Bästa praxis](migrate-best-practices-networking.md) för nätverk efter migreringen.

