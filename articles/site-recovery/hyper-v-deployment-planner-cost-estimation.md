---
title: "Azure Site Recovery-distribution Planner kostnad beräkning av information för Hyper-V-till-Azure | Microsoft Docs"
description: "Den här artikeln beskriver kostnaden beräkning av information om en rapport som genereras med hjälp av Azure Site Recovery-distribution Planner för Hyper-V till Azure-scenariot."
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: 31461e70e81f0f48a8d67e31b98cfae2dd627a54
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Rapporten kostnad uppskattning av Azure Site Recovery-distribution Planner 

Azure Site Recovery distribution Planner rapporten innehåller den sammanfattning i uppskattningen [rekommendationer](hyper-v-deployment-planner-analyze-report.md#recommendations) blad och detaljerad kostnadsanalys i bladet uppskattning. Där ges en detaljerad kostnadsanalys per virtuell dator. 

### <a name="cost-estimation-summary"></a>Sammanfattning av kostnadsuppskattning 
Diagrammet visar sammanfattning av kostnaden för beräknade totala disaster recovery (DR) till Azure för din valda målregionen och valutor som du angav för rapportgenerering av.

![Sammanfattning av kostnadsuppskattning](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Sammanfattningen hjälper dig att förstå den kostnad som du behöver betala för lagring, beräkning, nätverk och licens när du skyddar dina kompatibla virtuella datorer med hjälp av Azure Site Recovery. Den beräknade kostnaden för kompatibla virtuella datorer och inte på alla profilerad virtuella datorer. 
 
Du kan visa kostnaden per månad eller per år. Läs mer om [målregioner som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) och [valutor som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Kostnaden med komponenter**: den totala kostnaden för Katastrofåterställning är indelat i fyra komponenter: bearbetning, lagring, nätverk och Site Recovery licens kostnaden. Kostnaden beräknas baserat på förbrukningen som uppstår vid replikering och DR-återställningsgranskning för närvarande. Beräkning, lagring (premium och standard), ExpressRoute/VPN som har konfigurerats mellan lokal plats och Azure Site Recovery-licens används för beräkningar.

**Kostnad utfärda**: kategorin totala disaster recovery (DR) kostnaden baseras på två olika lägen: replikering och DR-test. 

**Replikering kostnaden**: kostnaden som uppstår vid replikering. Det täcker kostnaden för lagring, nätverk och Site Recovery-licens. 

**Kostnaden för DR-Återställningsgranskning**: kostnaden som uppstår under redundanstestning. Site Recovery startar virtuella datorer under redundanstestningen. Kostnaden för DR-återställningsgranskning omfattar körs VMs beräknings- och kostnader. 

**Azure storage kostnad per år-månad-**: den totala lagringsutrymme kostnad som uppstår för premium och standardlagring för replikering och DR-test.

## <a name="detailed-cost-analysis"></a>Detaljerad kostnadsanalys
Azure priser för bearbetning, lagring och nätverk variera mellan olika Azure-regioner. Du kan generera en beräkning av rapporten med de senaste Azure priserna baserat på din prenumeration, erbjudandet som är associerade med din prenumeration och det angivna målet Azure-region i en viss valuta. Som standard används Azure-regionen USA, västra 2 och valutan amerikanska dollar (USD) i verktyget. Om du använder andra region och valuta, nästa gång du generera en rapport utan prenumerations-ID, erbjudande-ID, målregionen och valuta, verktyget använder priserna för senast använda målregionen och valuta för kostnad uppskattning.

Detta avsnitt visar prenumerations-ID och erbjudande-ID som du använde för att generera rapporten. Om de inte används, är den tom.

I rapporten hela är celler som markerats i grått skrivskyddade. Celler i vitt kan ändras efter dina behov.

![Kostnad beräkning av information](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Övergripande kostnader för Katastrofåterställning av komponenter
I det första avsnittet visas den sammanlagda kostnaden för haveriberedskap per komponent och kostnaden för haveriberedskap per tillstånd. 

**Beräkna**: kostnaden för virtuella IaaS-datorer som körs på Azure för DR-behov. Den innehåller virtuella datorer som har skapats av Site Recovery under DR flyttar (redundanstestningar). Den omfattar också virtuella datorer som körs på Azure, till exempel SQL Server med domänkontrollanter och alltid på Tillgänglighetsgrupper eller namnservrar för domänen.

**Lagring**: kostnaden för Azure storage-förbrukningen för DR-behov. Här ingår användningen av lagringsutrymme för replikering under DR-tester.

**Nätverket**: ExpressRoute- och plats-till-plats VPN kostnad för DR-behov. 

**ASR licens**: I Site Recovery licens kostnaden för alla kompatibla virtuella datorer. Om du har angett en virtuell dator manuellt i tabellen Detaljerad kostnad analys, ingår Site Recovery-licensen kostnad också för den virtuella datorn.

### <a name="overall-dr-costs-by-states"></a>Övergripande kostnader för Katastrofåterställning av tillstånd
Totalkostnad för DR kategoriseras baserat på två olika lägen: replikering och DR-test.

**Replikering**: kostnaden vid tidpunkten för replikering. Det täcker kostnaden för lagring, nätverk och Site Recovery-licens. 

**DR-Återställningsgranskning**: kostnaden vid tidpunkten för DR övningar. Site Recovery startar under DR flyttar virtuella datorer. Den DR-detaljerade kostnad omfattar beräkning och lagring kostnaden för de virtuella datorerna som körs.

* Total DR-återställningsgranskning varaktighet i ett år = antal DR flyttar x varje DR ökad varaktighet (dagar)
* Genomsnittlig DR-återställningsgranskning kostnad (per månad) = totalkostnad DR-återställningsgranskning / 12

### <a name="storage-cost-table"></a>Lagringstabellen kostnad
Den här tabellen visar premium och standardlagring kostnader för replikering och DR-övningar med och utan rabatter.

### <a name="site-to-azure-network"></a>Lokalt till Azure-nätverket
Välj lämplig inställning enligt dina krav. 

**ExpressRoute**: som standard väljer verktyget den närmaste ExpressRoute-plan som matchar nätverksbandbredden som krävs för deltareplikering. Du kan ändra planen enligt dina krav.

**VPN Gateway-typ**: Välj Azure VPN-gatewayen om du har något i din miljö. Som standard är värdet Saknas.

**Målregionen**: angivna Azure-region för Katastrofåterställning. Priset som används i rapporten för bearbetning, lagring, nätverk och licenser baseras på Azure-priserna i den angivna regionen. 

### <a name="vm-running-on-azure"></a>Virtuella datorer som körs i Azure
Du kanske har en domänkontrollant eller DNS-VM eller SQL Server-VM med Always On-Tillgänglighetsgrupper körs på Azure för Katastrofåterställning. Du kan ange antalet virtuella datorer och storleken för att tänka på sina datorer kostnaden i den totala kostnaden för Katastrofåterställning. 

### <a name="apply-overall-discount-if-applicable"></a>Tillämpa totalrabatt i förekommande fall
Du kan använda det här fältet om du är en Azure-partner eller en kund och har rätt att eventuell rabatt på övergripande Azure priser. Verktyget gäller rabatten (i procent) på alla komponenter.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Antal virtuella datorer av olika typer och beräkningskostnad (per år)
Den här tabellen visar hur många av Windows och Windows VMs och DR-återställningsgranskning beräkna kostnaden för dessa.

### <a name="settings"></a>Inställningar 
**Med hjälp av hanterade disken**: den här inställningen anger om hanterade diskar används vid tidpunkten för DR övningar. Standardvärdet är **Ja**. Om du ställer in **- UseManagedDisks** till **nr**, ohanterad disk priset används för beräkning.

**Valuta**: valutan som rapporten ska genereras i.

**Kostnad varaktighet**: du kan visa alla kostnader för månaden eller för hela året. 

## <a name="detailed-cost-analysis-table"></a>Tabell med detaljerad kostnadsanalys
![Detaljerad kostnadsanalys](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

Tabellen innehåller arbete och omkostnader för varje VM som kompatibel. Du kan också använda den här tabellen för att hämta Azure DR uppskattade kostnaden för nonprofiled virtuella datorer genom att manuellt lägga till virtuella datorer. Den här informationen är användbart i fall där du behöver uppskatta Azure kostnader för en ny DR-distribution utan detaljerad profilering.

Så här lägger du till virtuella datorer manuellt:

1. Välj **infogningsraden** att infoga en ny rad mellan den **starta** och **End** rader.

2. Fyll i följande kolumner baserat på ungefärlig VM-storlek och antalet virtuella datorer som matchar den här konfigurationen: 

    a. **Antal virtuella datorer**

    b. **IaaS storlek (valet)**

    c. **Lagringstyp Standard/Premium**

    d. **VM totalt lagringsutrymme (GB)**

    e. **Antal DR-flyttar i ett år**

    f. **Varje DR-Återställningsgranskning varaktighet (dagar)**

    g. OS-typen

    h. **Dataredundans**

    i. **Hybridrapportering i Azure används förmån**

3. Du kan använda samma värde för alla virtuella datorer i tabellen genom att välja **Använd för alla** för **numret av DR-flyttar till ett år**, **varje DR-Återställningsgranskning varaktighet (dagar)**, **Data redundans**, och **Azure Hybrid Använd förmån**.

4. Välj **beräkna kostnaden** att uppdatera kostnaden.

**Namn på virtuell dator**: namnet på den virtuella datorn.

**Antal virtuella datorer**: antalet virtuella datorer som matchar konfigurationen. Om en liknande konfiguration av virtuella datorer inte är i listan men skyddas kan du uppdatera antalet befintliga virtuella datorer.

**IaaS storlek (rekommendation)**: den virtuella datorns rollstorlek av kompatibel VM som rekommenderas av verktyget. 

**IaaS storlek (valet)**: som standard storleken är samma som den rekommenderade storleken för VM-roll. Du kan ändra rollen efter behov. Den beräknade kostnaden baseras på vald storlek för rollen.

**Lagringstyp**: typer av lagring som används av den virtuella datorn. Det är standard eller premium-lagring.

**Totalt lagringsutrymme (GB) för virtuella datorer**: det totala lagringsutrymmet för den virtuella datorn.

**Antal DR-flyttar i ett år**: antalet gånger som du utför DR går till ett år. Som standard är fyra gånger i ett år. Du kan ändra perioden för specifika virtuella datorer eller använda det nya värdet i alla virtuella datorer. Ange det nya värdet i den översta raden och välj **Använd för alla**. Totalkostnad för DR-återställningsgranskning beräknas baserat på antalet DR flyttar i ett år och varje DR-återställningsgranskning varaktighet. 

**Varje DR-Återställningsgranskning varaktighet (dagar)**: varaktighet för varje DR-test. Som standard är den 7 dagar efter 90 dagar enligt den [Disaster Recovery Software Assurance-förmån](https://azure.microsoft.com/en-in/pricing/details/site-recovery). Du kan ändra perioden för specifika virtuella datorer eller du kan använda ett nytt värde för alla virtuella datorer. Ange ett nytt värde i den översta raden och välj **Använd för alla**. Totalkostnad för DR-återställningsgranskning beräknas baserat på antalet DR flyttar i ett år och varje DR-återställningsgranskning varaktighet.
 
**OS-typen**: typ av operativsystem (OS) på den virtuella datorn. Det är Windows eller Linux. Om Windows OS-typen är kan Azure Hybrid Använd förmån tillämpas på den virtuella datorn. 

**Dataredundans**: Det kan vara lokalt redundant lagring, geo-redundant lagring eller geo-redundant lagring med läsbehörighet. Standardvärdet är lokalt redundant lagring. Du kan ändra typen baserat på ditt lagringskonto för specifika virtuella datorer eller du kan använda den nya typen för alla virtuella datorer. Ändra typen av den översta raden och välj **Använd för alla**. Kostnaden för lagring för replikering beräknas baserat på priset för dataredundans som du har valt. 

**Azure Hybrid Använd förmån**: du kan använda Azure Hybrid Använd fördelen på virtuella Windows-datorer, om tillämpligt. Standardvärdet är **Ja**. Du kan ändra inställningen för specifika virtuella datorer eller så kan du uppdatera alla virtuella datorer. Välj **Använd för alla**.

**Totalt antal Azure-förbrukningen**: bearbetning, lagring och Site Recovery licens kostnaden för en Katastrofåterställning. Baserat på ditt val visas kostnaden per månad eller år.

**Stabilt tillstånd replikering kostnaden**: lagringskostnaden för replikering.

**Totalkostnad DR-Återställningsgranskning (medelvärde)**: beräkning och lagring kostnaden för DR övningar.

**ASR licens kostnaden**: I Site Recovery licens kostnaden.

## <a name="supported-target-regions"></a>Målregioner som stöds
Site Recovery-distribution Planner ger uppskattning för följande Azure-regioner. Om din region inte finns med här kan använda du någon av följande regioner vars priser ligger närmast din region:

usaöstra, usaöstra2, usavästra, usacentrala, usanorracentrala, usasödracentrala, europanorra, europavästra, asienöstra, sydostasien, japanöstra, japanvästra, australienöstra, australiensydöstra, brasiliensödra, indiensödra, indiencentrala, indienvästra, kanadacentrala, kanadaöstra, usavästra2, usavästracentrala, södrastorbritannien, västrastorbritannien, koreacentrala, koreasödra 

## <a name="supported-currencies"></a>Valutor som stöds
Site Recovery-distribution Planner kan generera kostnadsrapporten med någon av följande valutor.

|Valuta|Namn||Valuta|Namn||Valuta|Namn|
|---|---|---|---|---|---|---|---|
|ARS|Argentinska peso ($)||AUD|Australiska dollar ($)||BRL|Brasilianska real (R$)|
|CAD|Kanadensiska dollar ($)||CHF|Schweizerfranc (chf)||DKK|Danska kronor (kr)|
|EUR|Euro (€)||GBP|Brittiska pund (£)||HKD|Hongkong dollar (HK$)|
|IDR|Indonesiska rupier (Rp)||INR|Indiska rupier (₹)||JPY|Japanska yen (¥)|
|KRW|Koreanska vann (₩)||MXN|Mexiko peso (MX$)||MYR|Malaysiska ringgit (RM$)|
|NOK|Norska kronor (kr)||NZD|Nya Zeeland dollar ($)||RUB|Ryska rubel (руб)|
|SAR|Saudi riyal (SR)||SEK|Svenska kronor (kr)||TWD|Taiwan dollar (NT$)|
|TRY|Turkiska lira (TL)||USD| USA dollar ($)||ZAR|Sydafrikanska randen (R)|

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skyddar [Hyper-V virtuella datorer till Azure med hjälp av Site Recovery](hyper-v-azure-tutorial.md).
