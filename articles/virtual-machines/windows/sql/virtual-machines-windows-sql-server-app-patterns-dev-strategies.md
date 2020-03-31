---
title: SQL Server-programmönster på virtuella datorer | Microsoft-dokument
description: Den här artikeln täcker programmönster för SQL Server på virtuella Azure-datorer. Det ger lösningsarkitekter och utvecklare en grund för god applikationsarkitektur och design.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: cbc2bfbb68910c3eb12352bebb575c4548885a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70124033"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Programmönster och utvecklingsstrategier för SQL Server i virtuella Azure-datorer
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Sammanfattning:
Att bestämma vilket programmönster eller -mönster som ska användas för dina SQL-Server-baserade program i Azure-miljö är ett viktigt designbeslut och det kräver en gedigen förståelse för hur SQL Server och varje infrastrukturkomponent i Azure fungerar tillsammans. Med SQL Server i Azure Infrastructure Services kan du enkelt migrera, underhålla och övervaka dina befintliga SQL Server-program som är inbyggda i Windows Server till virtuella datorer i Azure.

Målet med den här artikeln är att ge lösningsarkitekter och utvecklare en grund för bra programarkitektur och design, som de kan följa när de migrerar befintliga program till Azure samt utvecklar nya program i Azure.

För varje programmönster hittar du ett lokalt scenario, dess respektive molnaktiverade lösning och relaterade tekniska rekommendationer. Dessutom beskrivs i artikeln Azure-specifika utvecklingsstrategier så att du kan utforma dina program korrekt. På grund av de många möjliga programmönstren rekommenderar vi att arkitekter och utvecklare väljer det lämpligaste mönstret för sina program och användare.

**Tekniska bidragsgivare:** Luis Carlos Vargas Sill, Madhan Arumugam Ramakrishnan

**Tekniska granskare:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introduktion
Du kan utveckla många typer av n-nivåprogram genom att separera komponenterna i de olika programlagren på olika datorer samt i separata komponenter. Du kan till exempel placera klientprogram- och affärsreglerskomponenterna i en dator, klientnivå och dataåtkomstnivåkomponenter i en annan dator och en backend-databasnivå i en annan dator. Den här typen av strukturering hjälper till att isolera varje nivå från varandra. Om du ändrar varifrån data kommer behöver du inte ändra klienten eller webbprogrammet, utan bara komponenterna på dataåtkomstnivån.

Ett typiskt *n-nivåprogram* innehåller presentationsnivån, affärsnivån och datanivån:

| Nivå | Beskrivning |
| --- | --- |
| **Presentation** |*Presentationsnivån* (webbnivå, frontend-nivå) är det lager där användarna interagerar med ett program. |
| **Företag** |*Affärsnivån* (mellannivå) är det lager som presentationsnivån och datanivån använder för att kommunicera med varandra och innehåller systemets kärnfunktioner. |
| **Data** |*Datanivån* är i grunden den server som lagrar ett programs data (till exempel en server som kör SQL Server). |

Programlager beskriver de logiska grupperingarna för funktioner och komponenter i ett program. Medan nivåer beskriver den fysiska distributionen av funktioner och komponenter på separata fysiska servrar, datorer, nätverk eller fjärrplatser. Lagren i ett program kan finnas på samma fysiska dator (samma nivå) eller distribueras över separata datorer (n-nivå) och komponenterna i varje lager kommunicerar med komponenter i andra lager genom väldefinierade gränssnitt. Du kan tänka på termnivån som refererar till fysiska distributionsmönster som tvånivåer, trenivåer och n-nivå. Ett **programmönster på två nivåer** innehåller två programnivåer: programserver och databasserver. Den direkta kommunikationen sker mellan programservern och databasservern. Programservern innehåller komponenter på både webbnivå och affärsnivå. I **programmönstret på tre nivåer**finns det tre programnivåer: webbserver, programserver, som innehåller affärslogiknivån och/eller dataåtkomstkomponenter på affärsnivå och/eller affärsnivå, och databasservern. Kommunikationen mellan webbservern och databasservern sker via programservern. Detaljerad information om programlager och nivåer finns i [Microsoft Application Architecture Guide](https://msdn.microsoft.com/library/ff650706.aspx).

Innan du börjar läsa den här artikeln bör du ha kunskap om de grundläggande begreppen SQL Server och Azure. Mer information finns i [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), SQL Server i virtuella [Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md) och [Azure.com](https://azure.microsoft.com/).

I den här artikeln beskrivs flera programmönster som kan vara lämpliga för dina enkla program samt de mycket komplexa företagsprogram. Innan du beskriver varje mönster rekommenderar vi att du bör bekanta dig med tillgängliga datalagringstjänster i Azure, till exempel [Azure Storage,](../../../storage/common/storage-introduction.md) [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md)och SQL Server i en Virtuell [Azure-dator](virtual-machines-windows-sql-server-iaas-overview.md). För att fatta de bästa designbesluten för dina program, förstå när du ska använda vilken datalagringstjänst tydligt.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Välj SQL Server på en virtuell Azure-dator när:
* Du behöver kontroll på SQL Server och Windows. Detta kan till exempel omfatta SQL Server-versionen, särskilda snabbkorrigeringar, prestandakonfiguration osv.
* Du behöver en fullständig kompatibilitet med SQL Server lokalt och vill flytta befintliga program till Azure som det är.
* Du vill utnyttja funktionerna i Azure-miljön, men Azure SQL Database stöder inte alla funktioner som ditt program kräver. Detta kan omfatta följande områden:
  
  * **Databasstorlek**: När den här artikeln uppdaterades stöder SQL Database en databas med upp till 1 TB data. Om ditt program kräver mer än 1 TB data och du inte vill implementera anpassade sharding-lösningar, rekommenderar vi att du använder SQL Server i en virtuell Azure-dator. Den senaste informationen finns i [Skala ut Azure SQL Database,](https://msdn.microsoft.com/library/azure/dn495641.aspx) [DTU-baserad inköpsmodell](../../../sql-database/sql-database-service-tiers-dtu.md)och [vCore-baserad inköpsmodell](../../../sql-database/sql-database-service-tiers-vcore.md)(förhandsversion).
  * **HIPAA-efterlevnad:** Healthcare-kunder och oberoende programvaruleverantörer (ISV: kan välja [SQL Server i virtuella Azure-datorer i](virtual-machines-windows-sql-server-iaas-overview.md) stället för Azure SQL [Database](../../../sql-database/sql-database-technical-overview.md) eftersom SQL Server i en virtuell Azure-dator omfattas av HIPAA Business Associate Agreement (BAA). Information om efterlevnad finns i [Microsoft Azure Trust Center: Compliance](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funktioner på instansnivå:** Sql Database stöder för närvarande inte funktioner som bor utanför databasen (till exempel länkade servrar, agentjobb, FileStream, Service Broker osv.). Mer information finns i [Azure SQL Database Guidelines and Limitations](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1-tier (enkel): enda virtuell dator
I det här programmönstret distribuerar du ditt SQL Server-program och -databas till en fristående virtuell dator i Azure. Samma virtuella dator innehåller klient-/webbprogrammet, affärskomponenter, dataåtkomstlager och databasservern. Presentations-, affärs- och dataåtkomstkoden är logiskt åtskilda men fysiskt placerade i en en serverdator. De flesta kunder börjar med det här programmönstret och skalas sedan ut genom att lägga till fler webbroller eller virtuella datorer i sitt system.

Det här programmönstret är användbart när:

* Du vill utföra en enkel migrering till Azure-plattformen för att utvärdera om plattformen svarar på programmets krav eller inte.
* Du vill behålla alla programnivåer som finns på samma virtuella dator i samma Azure-datacenter för att minska svarstiden mellan nivåer.
* Du vill snabbt etablera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.

Följande diagram visar ett enkelt lokalt scenario och hur du kan distribuera dess molnaktiverade lösning på en enda virtuell dator i Azure.

![Programmönster på 1 nivå](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Distribuera affärsskiktet (affärslogik och dataåtkomstkomponenter) på samma fysiska nivå som presentationslagret kan maximera programprestanda, såvida du inte måste använda en separat nivå på grund av skalbarhet eller säkerhetsproblem.

Eftersom detta är ett mycket vanligt mönster att börja med, kan du hitta följande artikel om migrering användbar för att flytta dina data till din SQL Server VM: [Migrera en databas till SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-tier (enkel): flera virtuella datorer
I det här programmönstret distribuerar du ett program på tre nivåer i Azure genom att placera varje programnivå på en annan virtuell dator. Detta ger en flexibel miljö för en enkel skala upp och skala ut scenarier. När en virtuell dator innehåller klient-/webbprogrammet är den andra värd för dina affärskomponenter och den andra är värd för databasservern.

Det här programmönstret är användbart när:

* Du vill utföra en migrering av komplexa databasprogram till Virtuella Azure-datorer.
* Du vill att olika programnivåer ska finnas i olika regioner. Du kan till exempel ha delade databaser som distribueras till flera regioner i rapporteringssyfte.
* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Virtuella Azure-datorer. En detaljerad diskussion om företagsprogram finns i [Vad är ett företagsprogram](https://msdn.microsoft.com/library/aa267045.aspx).
* Du vill snabbt etablera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.

Följande diagram visar hur du kan placera ett enkelt program på tre nivåer i Azure genom att placera varje programnivå i en annan virtuell dator.

![Programmönster på 3 nivåer](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

I det här programmönstret finns det bara en virtuell dator (VM) på varje nivå. Om du har flera virtuella datorer i Azure rekommenderar vi att du konfigurerar ett virtuellt nätverk. [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) skapar en betrodd säkerhetsgräns och gör det också möjligt för virtuella datorer att kommunicera sinsemellan via den privata IP-adressen. Se dessutom alltid till att alla Internet-anslutningar bara går till presentationsnivån. När du följer det här programmönstret hanterar du reglerna för nätverkssäkerhetsgruppen för att styra åtkomsten. Mer information finns i [Tillåt extern åtkomst till din virtuella dator med Azure-portalen](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

I diagrammet kan Internet-protokoll vara TCP, UDP, HTTP eller HTTPS.

> [!NOTE]
> Det är gratis att konfigurera ett virtuellt nätverk i Azure. Du debiteras dock för VPN-gatewayen som ansluter till lokalt. Den här avgiften baseras på hur lång tid anslutningen har etablerats och är tillgänglig.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>Skala på två nivåer och tre nivåer med utskalning av presentationsnivå
I det här programmönstret distribuerar du databasprogram på två nivåer eller tre nivåer till Virtuella Azure-datorer genom att placera varje programnivå på en annan virtuell dator. Dessutom skalar du ut presentationsnivån på grund av ökad volym av inkommande klientbegäranden.

Det här programmönstret är användbart när:

* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Virtuella Azure-datorer.
* Du vill skala ut presentationsnivån på grund av den ökade volymen inkommande klientbegäranden.
* Du vill snabbt etablera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.
* Du vill äga en infrastrukturmiljö som kan skalas upp och ned på begäran.

Följande diagram visar hur du kan placera programnivåerna i flera virtuella datorer i Azure genom att skala ut presentationsnivån på grund av ökad volym av inkommande klientbegäranden. Som framgår av diagrammet är Azure Load Balancer ansvarig för att distribuera trafik över flera virtuella datorer och även bestämma vilken webbserver som ska anslutas till. Att ha flera instanser av webbservrarna bakom en belastningsutjämnare säkerställer att presentationsnivån är hög.

![Programmönster - presentationsnivå skalas ut](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Metodtips för mönster på 2 nivåer, 3 nivåer eller n-nivå som har flera virtuella datorer på en nivå
Vi rekommenderar att du placerar de virtuella datorer som tillhör samma nivå i samma molntjänst och i samma tillgänglighetsuppsättning. Placera till exempel en uppsättning webbservrar i **CloudService1** och **AvailabilitySet1** och en uppsättning databasservrar i **CloudService2** och **AvailabilitySet2**. En tillgänglighetsuppsättning i Azure gör att du kan placera de högtillgänglighetsnoderna i separata feldomäner och uppgradera domäner.

Om du vill utnyttja flera VM-instanser av en nivå måste du konfigurera Azure Load Balancer mellan programnivåer. Om du vill konfigurera belastningsutjämnare på varje nivå skapar du en belastningsbalanserad slutpunkt på varje nivås virtuella datorer separat. För en viss nivå skapar du först virtuella datorer i samma molntjänst. Detta säkerställer att de har samma offentliga virtuella IP-adress. Skapa sedan en slutpunkt på en av de virtuella datorerna på den nivån. Tilldela sedan samma slutpunkt till andra virtuella datorer på den nivån för belastningsutjämning. Genom att skapa en belastningsbalanserad uppsättning distribuerar du trafik över flera virtuella datorer och tillåter även belastningsutjämnaren att avgöra vilken nod som ska ansluta när en serverdels-VM-nod misslyckas. Om du till exempel har flera instanser av webbservrarna bakom en belastningsutjämnare säkerställer du att presentationsnivån är hög.

Se till att alla internetanslutningar först går till presentationsnivån. Presentationslagret kommer åt affärsnivån och sedan åtkomst till datanivån på affärsnivån. Mer information om hur du tillåter åtkomst till presentationslagret finns i [Tillåt extern åtkomst till den virtuella datorn med Azure-portalen](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Observera att belastningsutjämnaren i Azure fungerar ungefär som belastningsutjämnare i en lokal miljö. Mer information finns i [Belastningsutjämning för Azure-infrastrukturtjänster](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dessutom rekommenderar vi att du konfigurerar ett privat nätverk för dina virtuella datorer med hjälp av Azure Virtual Network. Detta gör det möjligt för dem att kommunicera sinsemellan över den privata IP-adressen. Mer information finns i [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>Skala på 2-nivå och 3 med utskalning på affärsnivå
I det här programmönstret distribuerar du databasprogram på två nivåer eller tre nivåer till Virtuella Azure-datorer genom att placera varje programnivå på en annan virtuell dator. Dessutom kanske du vill distribuera programserverkomponenterna till flera virtuella datorer på grund av programmets komplexitet.

Det här programmönstret är användbart när:

* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Virtuella Azure-datorer.
* Du vill distribuera programserverkomponenterna till flera virtuella datorer på grund av programmets komplexitet.
* Du vill flytta affärslogik tunga lokala LOB-program (line-of-business) till Virtuella Azure-datorer. LOB-program är en uppsättning viktiga datorprogram som är viktiga för att driva ett företag, till exempel redovisning, personal (HR), löner, supply chain management och resursplaneringsprogram.
* Du vill snabbt etablera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.
* Du vill äga en infrastrukturmiljö som kan skalas upp och ned på begäran.

Följande diagram visar ett lokalt scenario och dess molnaktiverade lösning. I det här fallet placerar du programnivåerna i flera virtuella datorer i Azure genom att skala ut affärsnivån, som innehåller affärslogiknivån och dataåtkomstkomponenterna. Som framgår av diagrammet är Azure Load Balancer ansvarig för att distribuera trafik över flera virtuella datorer och även bestämma vilken webbserver som ska anslutas till. Att ha flera instanser av programservrarna bakom en belastningsutjämnare säkerställer hög tillgänglighet för affärsnivån. Mer information finns [i Metodtips för programmönster på två nivåer, 3 nivåer eller n-nivå som har flera virtuella datorer på en nivå](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Programmönster med skala ut affärsnivån](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2-tier och 3-nivå med presentations- och affärsnivåer utskalning och HADR
I det här programmönstret distribuerar du databasprogram på två nivåer eller tre nivåer till Virtuella Azure-datorer genom att distribuera presentationsnivån (webbservern) och komponenterna på affärsnivå (programserver) till flera virtuella datorer. Dessutom implementerar du lösningar för hög tillgänglighet och haveriberedskap för dina databaser i virtuella Azure-datorer.

Det här programmönstret är användbart när:

* Du vill flytta företagsprogram från virtuella plattformar lokalt till Azure genom att implementera SQL Server med hög tillgänglighet och funktioner för haveriberedskap.
* Du vill skala ut presentationsnivån och affärsnivån på grund av den ökade volymen inkommande klientbegäranden och komplexiteten i ditt program.
* Du vill snabbt etablera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.
* Du vill äga en infrastrukturmiljö som kan skalas upp och ned på begäran.

Följande diagram visar ett lokalt scenario och dess molnaktiverade lösning. I det här fallet skalar du ut presentationsnivån och affärsnivåkomponenterna i flera virtuella datorer i Azure. Dessutom implementerar du hadr-tekniker (high availability and disaster recovery) för SQL Server-databaser i Azure.

Köra flera kopior av ett program i olika virtuella datorer se till att du är belastningsutjämningsbegäranden över dem. När du har flera virtuella datorer måste du se till att alla dina virtuella datorer är tillgängliga och körs vid en tidpunkt. Om du konfigurerar belastningsutjämning spårar Azure Load Balancer hälsotillståndet för virtuella datorer och dirigerar inkommande anrop till felfria fungerande VM-noder korrekt. Information om hur du ställer in belastningsutjämning av de virtuella datorerna finns [i Belastningsutjämning för Azure-infrastrukturtjänster](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Att ha flera instanser av webb- och programservrar bakom en belastningsutjämnare säkerställer att presentationen och affärsnivåerna är hög.

![Utskalning och hög tillgänglighet](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Metodtips för programmönster som kräver SQL HADR
När du konfigurerar sql server-lösningar med hög tillgänglighet och haveriberedskap i Virtuella Azure-datorer är det obligatoriskt att konfigurera ett virtuellt nätverk för dina virtuella datorer med [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md)  Virtuella datorer i ett virtuellt nätverk kommer att ha en stabil privat IP-adress även efter en tjänst driftstopp, så du kan undvika uppdateringstid som krävs för DNS-namnmatchning. Dessutom kan det virtuella nätverket utöka ditt lokala nätverk till Azure och skapar en betrodd säkerhetsgräns. Om ditt program till exempel har begränsningar för företagsdomäner (till exempel Windows-autentisering, Active Directory) är det nödvändigt att konfigurera [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md)

De flesta kunder, som kör produktionskod på Azure, behåller både primära och sekundära repliker i Azure.

Omfattande information och självstudier om tekniker för hög tillgänglighet och katastrofåterställning finns [i Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2-nivå och 3-nivå med virtuella Azure-datorer och molntjänster
I det här programmönstret distribuerar du 2-nivå eller 3-nivåprogram till Azure med hjälp av både [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webb- och arbetsroller – Platform as a Service (PaaS)) och Azure Virtual [Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Infrastructure as a Service (IaaS)). Att använda [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) för presentationsnivå/affärsnivå och SQL Server i Virtuella [Azure-datorer](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för datanivån är fördelaktigt för de flesta program som körs på Azure. Anledningen är att en beräkningsinstans som körs på Molntjänster ger en enkel hantering, distribution, övervakning och utskalning.

Med Cloud Services underhåller Azure infrastrukturen för dig, utför rutinunderhåll, korrigerar operativsystemen och försöker återställa från tjänst- och maskinvarufel. När ditt program behöver skalning, automatisk och manuell skalning alternativ är tillgängliga för ditt molntjänstprojekt genom att öka eller minska antalet instanser eller virtuella datorer som används av ditt program. Dessutom kan du använda lokala Visual Studio för att distribuera ditt program till ett molntjänstprojekt i Azure.

Sammanfattningsvis, om du inte vill äga omfattande administrativa uppgifter för presentation / affärsnivå och ditt program inte kräver någon komplex konfiguration av programvara eller operativsystemet, använd Azure Cloud Services. Om Azure SQL Database inte stöder alla funktioner du letar efter använder du SQL Server i en virtuell Azure-dator för datanivån. Om du kör ett program på Azure Cloud Services och lagrar data i virtuella Azure-datorer kombineras fördelarna med båda tjänsterna. En detaljerad jämförelse finns i avsnittet i det här avsnittet om [Hur du jämför utvecklingsstrategier i Azure](#comparing-web-development-strategies-in-azure).

I det här programmönstret innehåller presentationsnivån en webbroll, som är en Cloud Services-komponent som körs i Azure-körningsmiljön och den är anpassad för webbprogramprogramprogram som stöds av IIS och ASP.NET. Affärs- eller serverdelsnivån innehåller en arbetarroll, som är en Cloud Services-komponent som körs i Azure-körningsmiljön och som är användbar för allmän utveckling och kan utföra bakgrundsbearbetning för en webbroll. Databasnivån finns i en virtuell SQL Server-dator i Azure. Kommunikationen mellan presentationsnivån och databasnivån sker direkt eller över affärsnivån – arbetsrollkomponenter.

Det här programmönstret är användbart när:

* Du vill flytta företagsprogram från virtuella plattformar lokalt till Azure genom att implementera SQL Server med hög tillgänglighet och funktioner för haveriberedskap.
* Du vill äga en infrastrukturmiljö som kan skalas upp och ned på begäran.
* Azure SQL Database stöder inte alla funktioner som ditt program eller databas behöver.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.

Följande diagram visar ett lokalt scenario och dess molnaktiverade lösning. I det här fallet placerar du presentationsnivån i webbroller, affärsnivån i arbetsroller men datanivån i virtuella datorer i Azure. Om du kör flera kopior av presentationsnivån i olika webbroller säkerställs att belastningsutjämningsbegäranden läses in över dem. När du kombinerar Azure Cloud Services med Virtuella Azure-datorer rekommenderar vi att du även konfigurerar [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md) Med [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)kan du ha stabila och beständiga privata IP-adresser inom samma molntjänst i molnet. När du har definierat ett virtuellt nätverk för dina virtuella datorer och molntjänster kan de börja kommunicera sinsemellan via den privata IP-adressen. Dessutom ger virtuella datorer och Azure-webb-/arbetsroller i samma [Virtuella Azure-nätverk](../../../virtual-network/virtual-networks-overview.md) låg latens och säkrare anslutning. Mer information finns i [Vad är en molntjänst](../../../cloud-services/cloud-services-choose-me.md).

Som framgår av diagrammet distribuerar Azure Load Balancer trafik över flera virtuella datorer och avgör även vilken webbserver eller programserver som ska anslutas till. Att ha flera instanser av webb- och programservrarna bakom en belastningsutjämnare säkerställer att presentationsnivån och affärsnivån är hög. Mer information finns i [Metodtips för programmönster som kräver SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Programmönster med molntjänster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

En annan metod för att implementera det här programmönstret är att använda en konsoliderad webbroll som innehåller både presentationsnivå och affärsnivåkomponenter som visas i följande diagram. Det här programmönstret är användbart för program som kräver tillståndskänslig design. Eftersom Azure tillhandahåller tillståndslösa beräkningsnoder på webb- och arbetsroller rekommenderar vi att du implementerar en logik för att lagra sessionstillstånd med någon av följande tekniker: [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) eller Azure SQL [Database](../../../sql-database/sql-database-technical-overview.md).

![Programmönster med molntjänster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Mönster med virtuella Azure-datorer, Azure SQL Database och Azure App Service (Web Apps)
Det primära målet med det här programmönstret är att visa hur du kombinerar Azure-infrastruktur som en tjänst (IaaS) komponenter med Azure Platform-as-a-service-komponenter (PaaS) i din lösning. Det här mönstret fokuserar på Azure SQL Database för relationsdatalagring. Det inkluderar inte SQL Server i en virtuell Azure-dator, som är en del av Azure-infrastrukturen som ett tjänsteerbjudande.

I det här programmönstret distribuerar du ett databasprogram till Azure genom att placera presentations- och affärsnivåerna på samma virtuella dator och komma åt en databas i SQL Database-servrar (Azure SQL Database). Du kan implementera presentationsnivån med hjälp av traditionella IIS-baserade webblösningar. Du kan också implementera en kombinerad presentations- och affärsnivå med hjälp av [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Det här programmönstret är användbart när:

* Du har redan konfigurerat en befintlig SQL Database-server i Azure och du vill testa programmet snabbt.
* Du vill testa funktionerna i Azure-miljön.
* Du vill snabbt etablera utvecklings- och testmiljöer under korta tidsperioder.
* Din affärslogik och dataåtkomstkomponenter kan vara fristående i ett webbprogram.

Följande diagram visar ett lokalt scenario och dess molnaktiverade lösning. I det här fallet placerar du programnivåerna på en enda virtuell dator i Azure och kommer åt data i Azure SQL Database.

![Blandat programmönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Om du väljer att implementera en kombinerad webb- och programnivå med hjälp av Azure Web Apps rekommenderar vi att du behåller mellannivå- eller programnivån som DLL-bibliotek (Dynamic-Link Libraries) i samband med ett webbprogram.

Dessutom kan du läsa rekommendationerna i avsnittet [Jämföra webbutvecklingsstrategier i Azure i](#comparing-web-development-strategies-in-azure) slutet av den här artikeln om du vill veta mer om programmeringstekniker.

## <a name="n-tier-hybrid-application-pattern"></a>N-nivå hybrid programmönster
I hybridprogrammönstret på n-nivå implementerar du ditt program på flera nivåer som distribueras mellan lokala och Azure. Därför skapar du ett flexibelt och återanvändbart hybridsystem, som du kan ändra eller lägga till en viss nivå utan att ändra de andra nivåerna. Om du vill utöka företagets nätverk till molnet använder du [Azure Virtual Network-tjänsten.](../../../virtual-network/virtual-networks-overview.md)

Detta hybridprogrammönster är användbart när:

* Du vill bygga program som körs dels i molnet och delvis lokalt.
* Du vill migrera vissa eller alla element i ett befintligt lokalt program till molnet.
* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Azure.
* Du vill äga en infrastrukturmiljö som kan skalas upp och ned på begäran.
* Du vill snabbt etablera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill ha ett kostnadseffektivt sätt att ta säkerhetskopior för företagsdatabasprogram.

Följande diagram visar ett hybridprogrammönster på n-nivå som sträcker sig över lokala och Azure. Som visas i diagrammet innehåller den lokala infrastrukturen Active Directory Domain Services-domänkontrollant för att stödja användarautentisering och auktorisering. [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) Observera att diagrammet visar ett scenario, där vissa delar av datanivån finns i ett lokalt datacenter medan vissa delar av datanivån finns i Azure. Beroende på programmets behov kan du implementera flera andra hybridscenarier. Du kan till exempel behålla presentationsnivån och affärsnivån i en lokal miljö men datanivån i Azure.

![Programmönster på N-nivå](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

I Azure kan du använda Active Directory som en fristående molnkatalog för din organisation, eller så kan du också integrera befintliga lokala Active Directory med [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Som framgår av diagrammet kan affärsnivåkomponenterna komma åt flera datakällor, till exempel till [SQL Server i Azure](virtual-machines-windows-sql-server-iaas-overview.md) via en privat intern IP-adress, till lokal SQL Server via Azure Virtual [Network](../../../virtual-network/virtual-networks-overview.md)eller till [SQL Database](../../../sql-database/sql-database-technical-overview.md) med hjälp av .NET Framework-dataproviderteknikerna. I det här diagrammet är Azure SQL Database en valfri datalagringstjänst.

I hybridprogrammönstret på n-nivå kan du implementera följande arbetsflöde i den angivna ordningen:

1. Identifiera företagsdatabasprogram som måste flyttas upp till molnet med hjälp av [Verktygslådan för Microsoft Assessment and Planning (MAP).](https://microsoft.com/map) MAP Toolkit samlar in inventerings- och prestandadata från datorer som du funderar på för virtualisering och ger rekommendationer om kapacitets- och bedömningsplanering.
2. Planera de resurser och den konfiguration som behövs i Azure-plattformen, till exempel lagringskonton och virtuella datorer.
3. Konfigurera nätverksanslutning mellan företagsnätverket lokalt och [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Om du vill konfigurera anslutningen mellan företagsnätverket lokalt och en virtuell dator i Azure använder du någon av följande två metoder:
   
   1. Upprätta en anslutning mellan lokala och Azure via offentliga slutpunkter på en virtuell dator i Azure. Den här metoden ger en enkel installation och gör att du kan använda SQL Server-autentisering på den virtuella datorn. Ställ dessutom in regler för nätverkssäkerhetsgruppen för att styra den offentliga trafiken till den virtuella datorn. Mer information finns i [Tillåt extern åtkomst till din virtuella dator med Azure-portalen](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Upprätta en anslutning mellan lokal och Azure via Azure Virtual Private Network (VPN) tunnel. Med den här metoden kan du utöka domänprinciper till en virtuell dator i Azure. Dessutom kan du ställa in brandväggsregler och använda Windows-autentisering på den virtuella datorn. För närvarande stöder Azure säker VPN- och punkt-till-plats-VPN-anslutningar från plats till plats:
      
      * Med säker anslutning från plats till plats kan du upprätta nätverksanslutning mellan det lokala nätverket och det virtuella nätverket i Azure. Vi rekommenderar att du ansluter din lokala datacentermiljö till Azure.
      * Med säker punkt-till-plats-anslutning kan du upprätta nätverksanslutning mellan det virtuella nätverket i Azure och dina enskilda datorer som körs var som helst. Det rekommenderas främst för utveckling och teständamål.
      
      Information om hur du ansluter till SQL Server i Azure finns i [Ansluta till en virtuell SQL Server-dator på Azure](virtual-machines-windows-sql-connect.md).
4. Ställ in schemalagda jobb och aviseringar som säkerhetskopierar lokala data i en virtuell datordisk i Azure. Mer information finns i [SQL Server Backup and Restore med Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx) och Backup and Restore for SQL Server i Virtuella [Azure-datorer](virtual-machines-windows-sql-backup-recovery.md).
5. Beroende på programmets behov kan du implementera något av följande tre vanliga scenarier:
   
   1. Du kan behålla webbservern, programservern och okänsliga data i en databasserver i Azure medan du håller känsliga data lokalt.
   2. Du kan hålla webbservern och programservern lokalt medan databasservern i en virtuell dator i Azure.
   3. Du kan hålla databasservern, webbservern och programservern lokalt medan du behåller databasrepliker i virtuella datorer i Azure. Med den här inställningen kan de lokala webbservrarna eller rapportprogrammen komma åt databasrepliker i Azure. Därför kan du uppnå för att minska arbetsbelastningen i en lokal databas. Vi rekommenderar att du implementerar det här scenariot för tunga läsarbetsbelastningar och utvecklingsändamål. Information om hur du skapar databasrepliker i Azure finns i AlwaysOn-tillgänglighetsgrupper med [hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Jämföra webbutvecklingsstrategier i Azure
Om du vill implementera och distribuera ett SQL Server-baserat program på flera nivåer i Azure kan du använda någon av följande två programmeringsmetoder:

* Konfigurera en traditionell webbserver (IIS - Internet Information Services) i Azure och få åtkomst till databaser i SQL Server i Virtuella Azure-datorer.
* Implementera och distribuera en molntjänst till Azure. Kontrollera sedan att den här molntjänsten kan komma åt databaser i SQL Server i Virtuella Azure-datorer. En molntjänst kan innehålla flera webb- och arbetsroller.

Följande tabell ger en jämförelse av traditionell webbutveckling med Azure Cloud Services och Azure Web Apps med avseende på SQL Server i Virtuella Azure-datorer. Tabellen innehåller Azure Web Apps som det är möjligt att använda SQL Server i Azure VM som en datakälla för Azure Web Apps via sin offentliga virtuella IP-adress eller DNS-namn.

|  | Traditionell webbutveckling i virtuella Azure-datorer | Molntjänster i Azure | Webbhotell med Azure Web Apps |
| --- | --- | --- | --- |
| **Migrering av program från lokala** |Befintliga program som de är. |Program behöver webb- och arbetsroller. |Befintliga program som de är men anpassade för fristående webbprogram och webbtjänster som kräver snabb skalbarhet. |
| **Utveckling och driftsättning** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Manager, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Varje molntjänst har två miljöer som du kan distribuera ditt servicepaket och konfiguration: mellanlagring och produktion. Du kan distribuera en molntjänst till mellanlagringsmiljön för att testa den innan du befordrar den till produktion. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Administration och installation** |Du ansvarar för administrativa uppgifter i programmet, data, brandväggsregler, virtuellt nätverk och operativsystem. |Du ansvarar för administrativa uppgifter i programmet, data, brandväggsregler och virtuella nätverk. |Du ansvarar endast för administrativa uppgifter i programmet och data. |
| **Hög tillgänglighet och haveriberedskap (HADR)** |Vi rekommenderar att du placerar virtuella datorer i samma tillgänglighetsuppsättning och i samma molntjänst. Genom att behålla dina virtuella datorer i samma tillgänglighetsuppsättning kan Azure placera de högtillgängliga noderna i separata feldomäner och uppgradera domäner. På samma sätt kan dina virtuella datorer i samma molntjänst belastningsutjämning och virtuella datorer kommunicera direkt med varandra via det lokala nätverket i ett Azure-datacenter.<br/><br/>Du är ansvarig för att implementera en lösning med hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer för att undvika driftstopp. Hadr-tekniker som stöds finns i [Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Du ansvarar för att säkerhetskopiera dina egna data och program.<br/><br/>Azure kan flytta dina virtuella datorer om värddatorn i datacentret misslyckas på grund av maskinvaruproblem. Dessutom kan det finnas planerade driftstopp för den virtuella datorn när värddatorn uppdateras för säkerhets- eller programuppdateringar. Därför rekommenderar vi att du underhåller minst två virtuella datorer på varje programnivå för att säkerställa kontinuerlig tillgänglighet. Azure tillhandahåller inte SLA för en enda virtuell dator. |Azure hanterar fel som uppstår till följd av den underliggande maskinvaran eller operativsystemprogramvaran. Vi rekommenderar att du implementerar flera instanser av en webb- eller arbetarroll för att säkerställa att ditt program är hög. Information finns i [Cloud Services, Virtual Machines och Virtual Network Service Level Agreement](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Du ansvarar för att säkerhetskopiera dina egna data och program.<br/><br/>För databaser som finns i en SQL Server-databas i en virtuell Azure-dator är du ansvarig för att implementera en lösning med hög tillgänglighet och haveriberedskap för att undvika driftstopp. Hdar-tekniker som stöds finns i Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer.<br/><br/>**SQL Server-databasspegling:** Använd med Azure Cloud Services (webb-/arbetsroller). Virtuella SQL Server-datorer och ett molntjänstprojekt kan finnas i samma Virtuella Azure-nätverk. Om SQL Server VM inte finns i samma virtuella nätverk måste du skapa ett SQL Server Alias för att dirigera kommunikation till instansen av SQL Server. Dessutom måste aliasnamnet matcha SQL Server-namnet. |Hög tillgänglighet ärvs från Azure-arbetsroller, Azure blob storage och Azure SQL Database. Azure Storage underhåller till exempel tre repliker av alla blob-, tabell- och ködata. Azure SQL Database håller vid varje tillfälle tre repliker av data som körs – en primär replik och två sekundära repliker. Mer information finns i [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) och Azure [SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>När du använder SQL Server i Azure VM som datakälla för Azure Web Apps bör du tänka på att Azure Web Apps inte stöder Azure Virtual Network. Med andra ord måste alla anslutningar från Azure Web Apps till virtuella SQL Server-datorer i Azure gå igenom offentliga slutpunkter för virtuella datorer. Detta kan orsaka vissa begränsningar för scenarier med hög tillgänglighet och haveriberedskap. Klientprogrammet på Azure Web Apps som ansluter till SQL Server VM med databasspegling skulle till exempel inte kunna ansluta till den nya primära servern eftersom databasspegling kräver att du konfigurerar Azure Virtual Network mellan virtuella SQL Server-värd-virtuella datorer i Azure. Därför stöds inte att använda **SQL Server Database Spegling** med Azure Web Apps för närvarande.<br/><br/>**SQL Server AlwaysOn-tillgänglighetsgrupper:** Du kan konfigurera AlwaysOn-tillgänglighetsgrupper när du använder Virtuella Azure-webbappar med virtuella SQL Server-datorer i Azure. Men du måste konfigurera AlwaysOn Availability Group Listener för att dirigera kommunikationen till den primära repliken via offentliga belastningsbalanserade slutpunkter. |
| **anslutning mellan lokaler** |Du kan använda Azure Virtual Network för att ansluta till lokalt. |Du kan använda Azure Virtual Network för att ansluta till lokalt. |Azure Virtual Network stöds. Mer information finns i [Integrering av virtuella webbappar](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Skalbarhet** |Uppskalning är tillgänglig genom att öka storleken på den virtuella datorn eller lägga till fler diskar. Mer information om storlekar på virtuella datorer finns i [Storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**För Databasserver:** Utskalning är tillgänglig via databaspartitionstekniker och SQL Server AlwaysOn-tillgänglighetsgrupper.<br/><br/>För tunga läsarbetsbelastningar kan du använda [AlwaysOn-tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx) på flera sekundära noder samt SQL Server Replication.<br/><br/>För tunga skrivarbetsbelastningar kan du implementera vågräta partitioneringsdata över flera fysiska servrar för att tillhandahålla programskalning.<br/><br/>Dessutom kan du implementera en utskalning med hjälp av [SQL Server med databeroende routning](https://technet.microsoft.com/library/cc966448.aspx). Med DDR (Data Dependent Routing) måste du implementera partitioneringsmekanismen i klientprogrammet, vanligtvis i affärsnivålagret, för att dirigera databasbegäranden till flera SQL Server-noder. Affärsnivån innehåller mappningar till hur data partitioneras och vilken nod som innehåller data.<br/><br/>Du kan skala program som kör virtuella datorer. Mer information finns i [Så här skalar du ett program](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Viktigt:** Med funktionen **Automatisk skalning** i Azure kan du automatiskt öka eller minska de virtuella datorer som används av ditt program. Den här funktionen garanterar att slutanvändarens upplevelse inte påverkas negativt under högsäsong och virtuella datorer stängs av när efterfrågan är låg. Vi rekommenderar att du inte anger alternativet Automatisk skalning för molntjänsten om det innehåller virtuella SQL Server-datorer. Anledningen är att funktionen Automatisk skalning gör att Azure kan aktivera en virtuell dator när CPU-användningen i den virtuella datorn är högre än vissa tröskelvärden och stänga av en virtuell dator när CPU-användningen går lägre än den. Funktionen Automatisk skalning är användbar för tillståndslösa program, till exempel webbservrar, där alla virtuella datorer kan hantera arbetsbelastningen utan några referenser till något tidigare tillstånd. Funktionen Automatisk skalning är dock inte användbar för tillståndskänsliga program, till exempel SQL Server, där endast en instans tillåter att skriva till databasen. |Uppskalning är tillgänglig med hjälp av flera webb- och arbetsroller. Mer information om storlekar på virtuella datorer för webbroller och arbetsroller finns i [Konfigurera storlekar för Molntjänster](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>När du använder **Molntjänster**kan du definiera flera roller för att distribuera bearbetning och även uppnå flexibel skalning av ditt program. Varje molntjänst innehåller en eller flera webbroller och/eller arbetsroller, var och en med sina egna programfiler och konfiguration. Du kan skala upp en molntjänst genom att öka antalet rollinstanser (virtuella datorer) som distribueras för en roll och skala ned en molntjänst genom att minska antalet rollinstanser. Detaljerad information finns i [Azure Execution Models](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Utskalning är tillgängligt via inbyggd Azure-support med hög tillgänglighet via [molntjänster, virtuella datorer och avtal om virtuell nätverkstjänstnivå](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) och belastningsutjämnare.<br/><br/>För ett program på flera nivåer rekommenderar vi att du ansluter webb-/arbetsrollsprogram till virtuella databasserver-datorer via Azure Virtual Network. Dessutom tillhandahåller Azure belastningsutjämning för virtuella datorer i samma molntjänst, vilket sprider användarbegäranden över dem. Virtuella datorer som är anslutna på detta sätt kan kommunicera direkt med varandra via det lokala nätverket i ett Azure-datacenter.<br/><br/>Du kan ställa in **Automatisk skalning** på Azure-portalen samt schematider. Mer information finns i [Så här konfigurerar du automatisk skalning för en molntjänst i portalen](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skala upp och ned**: Du kan öka/minska storleken på den instans (VM) som är reserverad för webbplatsen.<br/><br/>Skala ut: Du kan lägga till fler reserverade instanser (VMs) för din webbplats.<br/><br/>Du kan ställa in **Automatisk skalning** på portalen samt schematider. Mer information finns i Så här skalar du [webbappar](../../../app-service/manage-scale-up.md). |

Mer information om hur du väljer mellan dessa programmeringsmetoder finns i [Azure Web Apps, Cloud Services och VMs: När du ska använda vilka](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Efterföljande moment
Mer information om hur du kör SQL Server i virtuella Azure-datorer finns i [SQL Server på Översikt över virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).

