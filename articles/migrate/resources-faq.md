---
title: Azure migrera – vanliga frågor och svar (FAQ) | Microsoft Docs
description: Vanliga frågor och svar om Azure migrera adresser
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: snehaa
ms.openlocfilehash: a18cab73a019039bf5e5829ad1faa4b8f1a70391
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209953"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure migrera – och vanliga frågor svar (FAQ)

Den här artikeln innehåller vanliga frågor och svar om Azure migrera. Om du har några ytterligare frågor när du har läst den här artikeln publicera dem på den [Azure migrera forum](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Allmänt

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Hur skiljer Azure migrera från Azure Site Recovery?

Azure migrera är en bedömning-tjänst som hjälper dig att identifiera lokala arbetsbelastningar och planera migreringen till Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), tillsammans med som en lösning för katastrofåterställning, kan du migrera lokala arbetsbelastningar till virtuella IaaS-datorer i Azure. 

### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Hur skiljer Azure migrera från Azure Site Recovery-distribution Planner?

Azure migrera är verktyget för migreringsplanering och Azure Site Recovery-distribution Planner är en katastrofåterställning (DR) planera verktyget.

**Migrering från VMware till Azure**: Om du planerar att migrera dina lokala arbetsbelastningar till Azure, Använd Azure migrera för migreringsplanering. Azure migrera utvärderar lokala arbetsbelastningar och ger information och insikter mekanismer för att hjälpa dig att migrera till Azure. Du kan använda tjänster som Azure Site Recovery och Azure databastjänsten migrering för att migrera datorer till Azure när du är klar med din migreringsplan.

**Migrering från Hyper-V till Azure**: Azure migrera stöder för närvarande endast bedömning av virtuella VMware-datorer för migrering till Azure. Det finns stöd för Hyper-V på Översikt över Azure migrera. Under tiden kan använda du ASR distribution Planner. När Hyper-V-stöd är aktiverat i Azure migrera kan använda du Azure migrera för att planera migrering av Hyper-V-arbetsbelastningar.

**Katastrofåterställning från VMware/Hyper-V till Azure**: Om du vill göra katastrofåterställning (DR) på Azure med hjälp av Azure Site Recovery (ASR) använder ASR distribution Planner för Katastrofåterställning planering. ASR distribution Planner har en djupgående, ASR-specifika bedömning av din lokala miljö. Det ger rekommendationer som krävs av ASR för lyckade DR-åtgärder, till exempel replikering, redundans för de virtuella datorerna.  

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Behöver Azure migrera vCenter Server för att identifiera en VMware-miljön?

Ja, Azure migrera kräver vCenter Server för att identifiera en VMware-miljön. Det stöder inte identifiering av ESXi-värdar som inte hanteras av en vCenter Server.

## <a name="discovery"></a>Identifiering

### <a name="what-data-is-collected-by-azure-migrate"></a>Vilka data som samlas in av Azure migrera?

Azure migrera stöder två typer av, installation baserad identifiering och identifiering agent-baserade.
Installation baserad identifieringen samlar in metadata om de lokala virtuella datorerna, en fullständig lista över metadata som samlas in av installationen visas nedan:

**Konfigurationsdata för den virtuella datorn**
- VM visningsnamn (på vCenter)
- VM inventering sökväg (värden/klustret/mapp i vCenter)
- IP-adress
- MAC-adress
- Operativsystem
- Antal kärnor, diskar, nätverkskort
- Minnesstorleken diskstorlekar

**Prestandadata för den virtuella datorn**
- Processoranvändning
- Minnesanvändning
- För varje disk som är ansluten till den virtuella datorn:
  - Disk-lästa genomflöde
  - Genomströmning för skrivning till disk
  - Disk läsåtgärder per sekund
  - Disk skrivåtgärder per sekund
- För varje nätverkskort som är kopplade till den virtuella datorn:
  - Nätverk i
  - Nätverk ut

Agent-baserad identifiering är ett alternativ som är tillgängliga på enhet-baserad identifiering och hjälper kunder att [visualisera beroenden](how-to-create-group-machine-dependencies.md) för de lokala virtuella datorerna. Beroende agenter samla in information som FQDN, OS, IP-adress, MAC-adress, processer som körs i den virtuella datorn och den inkommande/utgående TCP-anslutningar från den virtuella datorn. Agent-baserad identifiering är valfri och du kan välja att inte installera agenter om du inte vill visualisera beroenden för de virtuella datorerna.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Var finns insamlade data lagras och hur länge?

Data som samlas in av insamlaren anordningen lagras i Azure-plats som du anger när du skapar migreringsprojekt. Data lagras på ett säkert sätt i en Microsoft-prenumeration och tas bort när användaren tar bort Azure migrera projektet.

För beroende visualiseringen om du installerar agenter på virtuella datorer, lagras data som samlas in av beroende agenter i USA i en OMS-arbetsyta som skapats i användarens prenumeration. Dessa data tas bort när användaren tar bort OMS-arbetsytan i sitt eller sin prenumeration. [Läs mer](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Hur insamlaren kommunicerar med vCenter-servern och tjänsten Azure migrera?

Insamlaren enheten ansluter till vCenter-servern (port 443) med de autentiseringsuppgifter som anges av användaren i anordningen. Frågor till vCenter-servern med hjälp av VMware PowerCLI att samla in metadata om de virtuella datorerna som hanteras av vCenter-servern. Den samlar in båda konfigurationsdata om virtuella datorer (kärnor, minne, diskar, nätverkskort o.s.v.) samt prestandahistorik för varje virtuell dator för senaste en månad från vCenter-servern. Insamlade metadata sedan skickas till tjänsten Azure migrera (via internet via https) för utvärdering. [Läs mer](concepts-collector.md)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Krypteras data i vila och under överföringen?

Ja, den insamlade krypteras data i vila och under överföringen. Metadata som samlas in av anordningen skickas på ett säkert sätt till tjänsten Azure migrera via internet via https. Insamlade metadata lagras i [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) och i [Azure-blobblagring](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) i en Microsoft-prenumeration och krypterat i vila.

Data som samlas in av beroende agenter är också krypterade i överföringen (säker https-kanal) och lagras i logganalys-arbetsytan i användarens prenumeration. Det är också krypterat i vila.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hur kan jag för att identifiera en miljö med flera innehavare i Azure migrera?

Om du har en miljö som delas mellan klienter och du inte vill identifiera de virtuella datorerna i en klient i en annan innehavarens prenumeration, kan du använda fältet Scope i insamlaren anordningen att definiera omfattningen av identifieringen. Om klienterna delar värdar, skapar du autentiseringsuppgifter som har skrivskyddad åtkomst till endast de virtuella datorerna som hör till specifika klienten och använder dessa autentiseringsuppgifter i anordningen insamlaren och ange omfånget som värd för att göra identifieringen. Alternativt kan du också skapa mappar i vCenter Server (anta att Mapp1 för tenant1 och mapp2 för tenant2), under delade värden, flytta de virtuella datorerna för tenant1 i Mapp1 och tenant2 till Mapp2 och omfång identifieringar i insamlaren i enlighet med detta genom att ange rätt mapp.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Hur många virtuella datorer kan identifieras i ett enda migreringsprojekt?

Du kan identifiera 1500 virtuella datorer i ett enda migreringsprojekt. Om du har flera datorer i din lokala miljö [mer](how-to-scale-assessment.md) om hur du kan identifiera en stor miljön i Azure migrera.

## <a name="dependency-visualization"></a>Visualisering av beroenden

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Behöver jag betala för att använda funktionen beroende visualiseringen?

Azure Migrate är tillgänglig utan extra kostnad. Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan jag använda en befintlig arbetsyta för beroende visualiseringen?

Azure migrera stöder inte användning av en befintlig arbetsyta för beroende visualisering, men Microsoft Monitoring Agent (MMA) stöder multihoming och gör att du kan skicka data till flera arbetsytor. Så om du redan har agenter distribution och konfiguration till en arbetsyta kan du utnyttja multihoming i MMA agenten och konfigurera den till arbetsytan Azure migrera (förutom befintlig arbetsyta) och få det att fungera. [Här](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) är en blogg på hur du kan aktivera multihoming i en MMA agent.

## <a name="next-steps"></a>Nästa steg

- Läs den [Azure migrera översikt](migrate-overview.md)
- Lär dig hur du kan [identifiera och utvärdera](tutorial-assessment-vmware.md) VMware-miljön
