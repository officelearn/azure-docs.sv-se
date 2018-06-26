---
title: SQL Server-program mönster på virtuella datorer | Microsoft Docs
description: Den här artikeln beskriver programmet mönster för SQL Server på virtuella Azure-datorer. Det ger lösningsarkitekter och utvecklare en grund för bra programarkitektur och utformning.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: 6bfb3092d6b4776499f957183613c951174b1a50
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754589"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Programmönster och utvecklingsstrategier för SQL Server i Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Sammanfattning:
Bestämma vilka programmönster eller mönster som ska användas för din SQL-Server-baserade program i Azure-miljö är ett viktigt beslut och kräver en god förståelse av hur SQL Server och varje komponent i nätverksinfrastrukturen Azure tillsammans. Med SQL Server i Azure Infrastructure Services kan du enkelt migrera, underhålla och övervaka dina befintliga SQL Server-program som bygger på Windows Server till virtuella datorer i Azure.

Syftet med den här artikeln är att ge lösningsarkitekter och utvecklare en grund för bra programarkitektur och utformning som de kan följa när du migrerar befintliga program för Azure samt utveckla nya program i Azure.

För varje program hittar du en lokal scenariot, dess respektive moln-aktiverad lösning och relaterade tekniska rekommendationer. Dessutom beskrivs strategier för utveckling av Azure-specifika så att du kan utforma dina program på rätt sätt. På grund av de många möjliga programmet mönster rekommenderas att arkitekter och utvecklare bör välja det lämpligaste mönstret för sina program och användare.

**Teknisk deltagare:** Thomas Carlos Vargas sill, Madhan Arumugam Ramakrishnan

**Teknisk granskare:** Corey slipmaskiner, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introduktion
Du kan utveckla många typer av flera nivåer program genom att avgränsa komponenterna i olika program lagren på olika datorer samt separata komponenter. Exempelvis kan du placera klientprogrammet och företag regler komponenter i en dator, frontend-webbnivån och data access nivå komponenter i en annan dator och en backend-databasens nivå i en annan dator. Den här typen av strukturera kan isolera varje nivå från varandra. Om du ändrar där data hämtas från behöver du inte ändra klient- eller programmet, men endast data access nivå komponenter.

En typisk *n-nivå* program innehåller presentationsnivå affärsnivå och datanivå:

| Nivå | Beskrivning |
| --- | --- |
| **Presentation** |Den *presentationsnivå* (web lager, frontend) är lagret där användarna samverkar med ett program. |
| **Företag** |Den *affärsnivå* (mellannivå) är lagret som presentationen tjänstnivån och datanivå använder för att kommunicera med varandra och innehåller huvudfunktionerna i systemet. |
| **Data** |Den *datanivå* är i praktiken den server som lagrar data i ett program (till exempel en server som kör SQL Server). |

Programmet lager beskrivs logiska grupperingar av funktioner och komponenter i ett program. medan nivåer beskriver fysiska distribution av funktioner och komponenter på separata fysiska servrar, datorer, nätverk eller fjärrplatser. Lager för ett program kan finnas på samma fysiska dator (samma nivå) eller kan fördelas på separata datorer (n-nivå) och komponenter i varje lager kommunicerar med komponenter i andra lager via väldefinierade gränssnitt. Du kan se termen nivån som hänvisar till fysiska distribution mönster, till exempel två nivåer, tre nivåer och n-nivå. En **2-nivån, programmönster** innehåller två programnivåerna: programservern och databasserver. Det sker direkt kommunikation mellan programservern och databasservern. Programservern innehåller både webb-nivå och affärsnivå komponenter. I **3-nivåprogram mönster**, det finns tre programnivåerna: webbserver, programserver som innehåller affärslogiknivå och/eller business nivå data access-komponenter, och databasservern. Kommunikationen mellan webbservern och databasservern sker över programservern. Detaljerad information om programmet lager och nivåer finns [Guide för Microsoft-arkitektur](https://msdn.microsoft.com/library/ff650706.aspx).

Innan du börjar läsa den här artikeln bör du ha kunskap om grundläggande begrepp för SQL Server och Azure. Mer information finns i [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) och [Azure.com](https://azure.microsoft.com/).

Den här artikeln beskriver flera program mönster som kan vara lämpligt för dina enkla program samt de komplexa företagsprogram. Innan du med detaljer om varje mönster, rekommenderar vi att du bekanta dig med tillgängliga data storage-tjänster i Azure, t.ex [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md), och [SQL Server i en virtuell Azure-dator](virtual-machines-windows-sql-server-iaas-overview.md). Förstå när du ska använda vilka data lagringstjänsten klart för att fatta de bästa designbeslut för dina program.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Välj SQLServer i en virtuell Azure-dator, när:
* Du behöver behörighet i SQL Server och Windows. Detta kan exempelvis innehålla SQL Server-version, särskilda snabbkorrigeringar, prestanda, konfiguration, osv.
* Du behöver en fullständig kompatibilitet med SQL Server on-premises och vill flytta befintliga program till Azure som-är.
* Du vill utnyttja funktionerna i Azure-miljön, men Azure SQL Database stöder inte alla de funktioner som krävs för ditt program. Det kan innefatta följande områden:
  
  * **Databasstorlek**: vid den tidpunkt som den här artikeln har uppdaterats SQL-databasen har stöd för en databas med upp till 1 TB data. Om ditt program kräver mer än 1 TB data och du inte vill att implementera lösningar för anpassade horisontell partitionering, rekommenderas att du använder SQL Server i en virtuell dator i Azure. Den senaste informationen finns [skala ut Azure SQL-databaser](https://msdn.microsoft.com/library/azure/dn495641.aspx), [DTU-Based köp modellen](../../../sql-database/sql-database-service-tiers-dtu.md), och [vCore-baserade inköp modellen](../../../sql-database/sql-database-service-tiers-vcore.md)(förhandsversion).
  * **HIPAA kompatibilitet**: sjukvården kunder och oberoende programvaruleverantörer (ISV) kan välja [SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) i stället för [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) eftersom SQL Server i en virtuell dator i Azure omfattas av HIPAA (BAA Business associera avtalet). Mer information om kompatibilitet finns [Microsoft Azure Trust Center: kompatibilitet](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funktioner på instansnivå**: just nu, SQL Database stöder funktioner som live utanför databasen (till exempel länkade servrar Agent-jobb, FileStream, Service Broker osv.). Mer information finns i [Azure SQL Database riktlinjer och begränsningar](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1-nivån (enkel): enskild virtuell dator
I det här programmet i mönstret distribuera du din SQL Server-programmet och databasen till en fristående virtuell dator i Azure. Samma virtuella dator innehåller tillämpningsprogrammet-och klienten, business-komponenter, dataåtkomstlagret och databasservern. Presentation-, affärs- och kod för åtkomst av data är logiskt åtskilda men är fysiskt belägna i en enskild server-dator. De flesta kunder börjar med det här programmet mönstret och sedan skala ut genom att lägga till fler webbroller eller virtuella datorer till deras system.

Det här programmet mönstret är användbart när:

* Du vill utföra en enkel migrering till Azure-plattformen att utvärdera om plattformen svar programmets krav eller inte.
* Du vill behålla alla programnivåerna som finns i samma virtuella dator i samma Azure Datacenter att minska fördröjningen mellan nivåer.
* Vill du snabbt etablera utveckling och testmiljöer för korta tidsperioder.
* Du vill utföra stress testa för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer hela tiden.

Följande diagram visar en enkel lokalt scenariot och hur du kan distribuera dess aktiverat molnlösning i en enskild virtuell dator i Azure.

![1-nivån, programmönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Distribuera business lagret (affärslogik och data som har åtkomst till komponenter) på samma fysiska skikt som presentation lager kan du maximera programmets prestanda om du måste använda en separat nivå på grund av problem med skalbarhet eller säkerhetsgrupp.

Eftersom det är ett mycket vanligt mönster börja med användbara i följande artikel på migrering för att flytta data till SQL Server-VM: [migrera en databas till SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-skikts (enkel): flera virtuella datorer
I det här programmet i mönstret distribuera 3-nivåprogram i Azure genom att varje nivå för program i en annan virtuell dator. Detta ger en flexibel miljö för ett enkelt skala upp och skala ut scenarier. När en virtuell dator innehåller tillämpningsprogrammet-och klienten, den andra som är värd för företagskomponenter och den andra som är värd för databasservern.

Det här programmet mönstret är användbart när:

* Du vill utföra en migrering av komplicerade databasprogram till virtuella datorer i Azure.
* Vill du olika programnivåerna måste finnas i olika regioner. Du kan till exempel har delat databaser som distribueras till flera regioner för rapportering.
* Du vill flytta företagsprogram från lokala virtualiserade plattformar till virtuella datorer i Azure. En detaljerad beskrivning på företagsprogram finns [vad är ett företagsprogram](https://msdn.microsoft.com/library/aa267045.aspx).
* Vill du snabbt etablera utveckling och testmiljöer för korta tidsperioder.
* Du vill utföra stress testa för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer hela tiden.

Följande diagram visar hur du kan placera en enkel 3-nivåprogram i Azure genom att varje nivå för program i en annan virtuell dator.

![3-nivåprogram mönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

I det här programmet i mönstret finns bara en virtuell dator (VM) på varje nivå. Om du har flera virtuella datorer i Azure, rekommenderar vi att du konfigurerar ett virtuellt nätverk. [Virtuella Azure-nätverket](../../../virtual-network/virtual-networks-overview.md) skapar en betrodd säkerhetsgräns och låter också virtuella datorer kan kommunicera med varandra i den privata IP-adressen. Dessutom alltid se till att det endast går alla Internet-anslutningar till presentationsnivå. När det här mönstret för programmet, hantera reglerna för nätverkssäkerhetsgrupper för att styra åtkomsten. Mer information finns i [ge extern åtkomst till den virtuella datorn med Azure-portalen](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

I diagrammet skickar vara Internetprotokoll TCP, UDP, HTTP eller HTTPS.

> [!NOTE]
> Ställa in ett virtuellt nätverk i Azure är kostnadsfritt. Dock debiteras för VPN-gatewayen som ansluter till lokala. Det här tillägget baseras på hur lång tid som anslutningen är etablerad och tillgängliga.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>nivå 2 och 3-skikts med presentation nivå skalbar
I det här programmet i mönstret distribuera 2 eller 3 nivåer databasprogram till virtuella datorer i Azure genom att varje nivå för program i en annan virtuell dator. Dessutom kan skala du ut presentationsnivån på grund av ökade mängden inkommande klientbegäranden.

Det här programmet mönstret är användbart när:

* Du vill flytta företagsprogram från lokala virtualiserade plattformar till virtuella datorer i Azure.
* Du vill skala ut presentationsnivån på grund av ökade mängden inkommande klientbegäranden.
* Vill du snabbt etablera utveckling och testmiljöer för korta tidsperioder.
* Du vill utföra stress testa för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer hela tiden.
* Vill du äger en infrastruktur-miljö som kan skalas upp och ned på begäran.

Följande diagram visar hur du kan placera programmet nivåer i flera virtuella datorer i Azure genom att skala ut presentationsnivån på grund av ökade mängden inkommande klientbegäranden. Azure belastningsutjämnare är ansvarig för distribuerar trafik över flera virtuella datorer och även bestämma vilka webbserver för att ansluta till som visas i diagrammet. Med flera instanser av webbservrar bakom en belastningsutjämnare säkerställer hög tillgänglighet för presentationsnivå.

![Programmönster - presentation nivå skala ut](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Metodtips för nivå 2, 3-skikts eller n-nivå mönster som har flera virtuella datorer i ett lager
Vi rekommenderar att du placerar virtuella datorer som tillhör samma nivå i samma molntjänst och i samma tillgängligheten anges. Till exempel placera en uppsättning webbservrar i **CloudService1** och **AvailabilitySet1** och en uppsättning databasservrar i **CloudService2** och  **AvailabilitySet2**. En tillgänglighetsuppsättning i Azure kan du placera noder för hög tillgänglighet i separata feldomäner och uppgradera domäner.

Om du vill använda flera VM-instanser av en nivå, måste du konfigurera Azure belastningsutjämnare mellan programnivåerna. Om du vill konfigurera belastningsutjämnare i varje nivå, skapar du en slutpunkt för Utjämning av nätverksbelastning på varje nivå virtuella datorer separat. För en viss nivå, skapa virtuella datorer i samma molntjänst. Detta säkerställer att de har samma offentliga virtuella IP-adress. Skapa sedan en slutpunkt på en av de virtuella datorerna på nivån. Sedan tilldela samma slutpunkten till andra virtuella datorer på nivån för belastningsutjämning. Genom att skapa en belastningsutjämnad uppsättning distribuerar trafik över flera virtuella datorer och även att belastningsutjämnare att avgöra vilken nod att ansluta när serverdelens VM nodfel. Till exempel säkerställer har flera instanser av webbservrar bakom en belastningsutjämnare hög tillgänglighet för presentationsnivå.

Som bästa praxis bör alltid se till att alla internet-anslutningar först gå till presentationsnivå. Presentation lagret har åtkomst till affärsnivå och sedan affärsnivå har åtkomst till datanivå. Mer information om hur du tillåter åtkomst till lagret med presentation finns [ge extern åtkomst till den virtuella datorn med Azure-portalen](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Observera att belastningsutjämnare i Azure fungerar liknar belastningsutjämnare i en lokal miljö. Mer information finns i [belastningsutjämning för Azure infrastrukturtjänster](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vi rekommenderar dessutom att du konfigurerar ett privat nätverk för virtuella datorer med hjälp av Azure Virtual Network. Detta gör att de kan kommunicera med varandra över den privata IP-adressen. Mer information finns i [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>nivå 2 och 3-skikts med business nivå skalbar
I det här programmet i mönstret distribuera 2 eller 3 nivåer databasprogram till virtuella datorer i Azure genom att varje nivå för program i en annan virtuell dator. Dessutom kanske du vill distribuera programmet server-komponenterna för flera virtuella datorer på grund av svårigheterna för programmet.

Det här programmet mönstret är användbart när:

* Du vill flytta företagsprogram från lokala virtualiserade plattformar till virtuella datorer i Azure.
* Du vill distribuera programmet server-komponenterna för flera virtuella datorer på grund av svårigheterna för programmet.
* Du vill flytta business logic frekventa lokala LOB (line-of-business) program till Azure-datorer. LOB-program är en uppsättning program kritiska datorer som är viktiga för att driva ett företag, till exempel redovisning, personalavdelningen (HR), lön, styrning och resursplanering program.
* Vill du snabbt etablera utveckling och testmiljöer för korta tidsperioder.
* Du vill utföra stress testa för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer hela tiden.
* Vill du äger en infrastruktur-miljö som kan skalas upp och ned på begäran.

Följande diagram visar ett lokalt scenario och dess aktiverat molnlösning. I det här scenariot kan placera du programnivåerna i flera virtuella datorer i Azure genom att skala ut affärsnivå som innehåller affärslogiknivå och komponenter för dataåtkomst. Azure belastningsutjämnare är ansvarig för distribuerar trafik över flera virtuella datorer och även bestämma vilka webbserver för att ansluta till som visas i diagrammet. Med flera instanser av programservrar bakom en belastningsutjämnare säkerställer hög tillgänglighet på affärsnivå. Mer information finns i [bästa praxis för nivå 2, 3-skikts eller n-tier application-mönster som har flera virtuella datorer i ett lager](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Programmet mönster med business nivå skala ut](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>nivå 2 och 3-skikts med presentation och företag nivåer skalbar och HADR
I det här programmet i mönstret distribuera 2 eller 3 nivåer databasprogram till virtuella datorer i Azure genom att distribuera presentationsnivån (webbserver) och företag nivån (program) serverkomponenterna för flera virtuella datorer. Dessutom kan implementera du lösningar för hög tillgänglighet och katastrofåterställning för dina databaser i virtuella Azure-datorer.

Det här programmet mönstret är användbart när:

* Du vill flytta företagsprogram virtualiserade plattformar lokal till Azure genom att implementera och funktioner för katastrofåterställning med hög tillgänglighet för SQL Server.
* Du vill skala ut presentationsnivån och affärsnivån på grund av ökade mängden inkommande klientbegäranden och komplexiteten i ditt program.
* Vill du snabbt etablera utveckling och testmiljöer för korta tidsperioder.
* Du vill utföra stress testa för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer hela tiden.
* Vill du äger en infrastruktur-miljö som kan skalas upp och ned på begäran.

Följande diagram visar ett lokalt scenario och dess aktiverat molnlösning. I det här scenariot kan du skala ut presentationsnivån och företag nivå komponenter i flera virtuella datorer i Azure. Dessutom kan implementera du hög tillgänglighet och haveriberedskap (HADR) återställningsfunktioner för SQL Server-databaser i Azure.

Kör flera kopior av ett program i olika Se virtuella datorer till att du är belastningsutjämning begäranden mellan dem. När du har flera virtuella datorer, måste du se till att dina virtuella datorer är tillgänglig och körs på en punkt i tiden. Om du konfigurerar belastningsutjämning Azure belastningsutjämnare spårar hälsotillståndet för virtuella datorer och dirigerar inkommande samtal till felfritt fungerande Virtuella noder korrekt. Mer information om hur du konfigurerar belastningsutjämning för de virtuella datorerna finns [belastningsutjämning för Azure infrastrukturtjänster](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Flera instanser av webbservrar och programservrar bakom en belastningsutjämnare försäkrar du dig nivåer presentation och hög tillgänglighet.

![Skalbar och hög tillgänglighet](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Metodtips för mönster för program som kräver SQL HADR
När du konfigurerar SQL Server med hög tillgänglighet och katastrofåterställning i Azure Virtual Machines, konfigurera ett virtuellt nätverk för virtuella datorer med hjälp av [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) är obligatoriskt.  Virtuella datorer i ett virtuellt nätverk måste ha en stabil privata IP-adress även efter ett avbrott i tjänsten, vilket du kan undvika uppdateringstiden krävs för DNS-namnmatchning. Dessutom kan du utöka ditt lokala nätverk till Azure det virtuella nätverket och skapar en betrodd säkerhetsgräns. Om programmet har domänbegränsningar för företagets (till exempel Windows-autentisering, Active Directory), till exempel konfigurera [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) krävs.

De flesta kunder som kör kod i Azure, hindrar både primära och sekundära repliker i Azure.

Omfattande information och självstudier om hög tillgänglighet och katastrofåterställning återställningsfunktioner finns [hög tillgänglighet och Haveriberedskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>nivå 2 och 3-skikts med molntjänster och virtuella Azure-datorer
I det här programmet i mönstret du distribuerar 2 eller 3 nivåer program till Azure med hjälp av både [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webb- och arbetsroller roller - plattform som en tjänst (PaaS)) och [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) () Infrastruktur som en tjänst (IaaS)). Med hjälp av [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) för presentation nivå/affärsnivå och SQL Server i [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för data nivå är bra för de flesta program som körs på Azure. Anledningen är att ha en beräknings-instans som körs på molntjänster tillhandahåller en enkel hantering, distribution, övervakning och skalbara.

Med Cloud Services, Azure underhåller infrastrukturen som du utför rutinunderhåll, korrigeringsfiler operativsystemen och försöker återställa från tjänsten och maskinvarufel. När ditt program måste skalbar, är automatisk och manuell skalbar alternativ tillgängliga för ditt molntjänstprojekt genom att öka eller minska antalet instanser eller virtuella datorer som används av ditt program. Dessutom kan du använda lokala Visual Studio för att distribuera programmet till ett molntjänstprojekt i Azure.

Sammanfattningsvis: Om du inte vill att äga omfattande administrativa uppgifter för presentationen/företag tjänstnivån och ditt program inte kräver komplexa konfiguration av programvara eller operativsystemet, använder Azure-molntjänster. Om Azure SQL Database inte stöder alla funktioner som du letar efter, använder du SQL Server i en virtuell dator i Azure för datanivå. Kör ett program på Azure Cloud Services och lagra data i Azure Virtual Machines kombinerar fördelarna med båda tjänsterna. En detaljerad jämförelse finns i avsnittet i det här avsnittet på [jämföra development strategier i Azure](#comparing-web-development-strategies-in-azure).

I det här programmet i mönstret innehåller presentationsnivå webbrollen, vilket är en Cloud Services-komponent som körs i Azure körningsmiljö och den är anpassad för web application programming som stöds av IIS och ASP.NET. Företag eller backend nivån innehåller en arbetsroll som är en Cloud Services-komponent som körs i Azure körningsmiljö och det är användbart för generaliserad utveckling och kan utföra Bakgrundsbearbetning för en webbroll. Databasnivån finns i en SQL Server-dator i Azure. Kommunikationen mellan presentationsnivån och databasnivån sker direkt eller via affärsnivå – komponenter för worker-rollen.

Det här programmet mönstret är användbart när:

* Du vill flytta företagsprogram virtualiserade plattformar lokal till Azure genom att implementera och funktioner för katastrofåterställning med hög tillgänglighet för SQL Server.
* Vill du äger en infrastruktur-miljö som kan skalas upp och ned på begäran.
* Azure SQL Database stöder inte alla de funktioner som ditt program eller en databas måste.
* Du vill utföra stress testa för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer hela tiden.

Följande diagram visar ett lokalt scenario och dess aktiverat molnlösning. I det här scenariot kan du placera presentationsnivå i webbroller affärsnivå i arbetsroller men datanivå i virtuella datorer i Azure. Kör flera kopior av presentationsnivå i olika webbroller garanterar om du vill läsa belastningsutjämna förfrågningar över dem. När du kombinerar Azure Cloud Services med Azure Virtual Machines rekommenderar vi att du ställer in [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) samt. Med [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), du kan ha stabil och beständiga privata IP-adresser inom samma moln tjänst i molnet. När du definierar ett virtuellt nätverk för virtuella datorer och molntjänster kan starta de kommunicera med varandra via den privata IP-adressen. Dessutom har virtuella datorer och Azure web/worker-roller i samma [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) ger mindre fördröjning och säkrare anslutning. Mer information finns i [vad är en molnbaserad tjänst](../../../cloud-services/cloud-services-choose-me.md).

Som visas i diagrammet Azure belastningsutjämnare distribuerar trafik över flera virtuella datorer och avgör också vilken webbservern eller en programserver ansluter till. Med flera instanser av webbservrar och programservrar bakom en belastningsutjämnare säkerställer hög tillgänglighet för presentationsnivån och affärsnivån. Mer information finns i [Metodtips för programmet mönster som kräver SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Programmet mönster med molntjänster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

En annan metod för att implementera den här programmönster är att använda en konsoliderad webbroll som innehåller både presentationsnivån och nivå-komponenterna som visas i följande diagram. Det här programmet mönstret är användbart för program som kräver tillståndskänslig design. Eftersom Azure tillhandahåller tillståndslös compute-noder på webb-och arbetsroller, rekommenderar vi att du implementerar en logik för att lagra sessionstillstånd med hjälp av något av följande tekniker: [Azure Caching](https://azure.microsoft.com/documentation/services/redis-cache/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) eller [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).

![Programmet mönster med molntjänster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Mönster med virtuella Azure-datorer, Azure SQL Database och Azure App Service (webbprogram)
Syftet med det här programmet mönstret är att visa hur du kombinerar Azure-infrastrukturen som en tjänst (IaaS)-komponenter med Azure platform as a service komponenter (PaaS) i din lösning. Det här mönstret fokuserar på Azure SQL-databas för lagring av relationella data. Det omfattar inte SQL Server på en virtuell Azure-dator, som ingår i Azure-infrastrukturen som ett tjänsterbjudande.

I det här programmet i mönstret distribuera du ett databasprogram till Azure genom att placera presentation och nivåer i samma virtuella dator och åtkomst till en databas i Azure SQL Database (SQL Database)-servrar. Du kan implementera presentationsnivån med hjälp av traditionella IIS-baserade webblösningar. Eller, du kan implementera en kombinerad presentation och affärsnivå med hjälp av [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Det här programmet mönstret är användbart när:

* Du har redan en befintlig SQL Database-server som konfigurerats i Azure och du vill testa ditt program snabbt.
* Du vill testa funktionerna i Azure-miljön.
* Vill du snabbt etablera utveckling och testmiljöer för korta tidsperioder.
* Komponenter för åtkomst av logik och data som ditt företag kan vara fristående inom ett webbprogram.

Följande diagram visar ett lokalt scenario och dess aktiverat molnlösning. I det här scenariot kan du placera programmet nivåer i en enskild virtuell dator i Azure och komma åt data i Azure SQL Database.

![Blandad, programmönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Om du väljer att implementera en kombinerad och programmet nivå med hjälp av Azure-Webbappar, rekommenderar vi att du behåller nivån mellannivå eller ett program som dynamiska länkbibliotek (dll) i kontexten för ett webbprogram.

Dessutom granska de rekommendationer som anges i den [jämföra web development strategier i Azure](#comparing-web-development-strategies-in-azure) avsnittet i slutet av den här artikeln om du vill veta mer om hur du programmerar tekniker.

## <a name="n-tier-hybrid-application-pattern"></a>N-nivås hybrid programmönster
I n-nivås hybrid programmönster implementera du ditt program i flera nivåer som distribueras mellan lokala och Azure. Därför kan du skapa ett flexibelt och återanvändbara hybrid-system som du kan ändra eller lägga till en viss nivå utan att ändra de andra nivåerna. Om du vill utöka företagets nätverk till molnet som du använder [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) service.

Det här programmet hybrid-mönstret är användbart när:

* Du vill skapa program som kör delvis i molnet och delvis lokalt.
* Du vill migrera vissa eller alla element i ett befintligt program lokalt till molnet.
* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Azure.
* Vill du äger en infrastruktur-miljö som kan skalas upp och ned på begäran.
* Vill du snabbt etablera utveckling och testmiljöer för korta tidsperioder.
* Vill du ett kostnadseffektivt sätt att göra säkerhetskopior för databasen företagsprogram.

Följande diagram visar en n-nivås hybrid programmönster som sträcker sig över lokalt och Azure. I diagrammet visas lokala infrastruktur innehåller [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) domänkontrollant för stöd av användarautentisering och auktorisering. Observera att diagrammet visar ett scenario där vissa delar av datanivå bor i ett lokalt Datacenter medan vissa delar av datanivå live i Azure. Du kan implementera flera andra hybridscenarion beroende på programmets behov. Exempelvis kan du behålla presentationsnivån och affärsnivån i en lokal miljö men datanivå i Azure.

![N-nivån, programmönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

I Azure, kan du använda Active Directory som en fristående molnkatalog för din organisation och du kan också integrera befintliga lokala Active Directory med [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Som visas i diagrammet business nivå komponenterna har åtkomst till flera datakällor, som till [SQL Server i Azure](virtual-machines-windows-sql-server-iaas-overview.md) via en privat interna IP-adress till lokal SQL Server via [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), eller [SQL-databas](../../../sql-database/sql-database-technical-overview.md) med hjälp av .NET Framework data provider-teknik. I det här diagrammet är en valfri data storage-tjänst i Azure SQL Database.

Du kan implementera följande arbetsflöde i den ordning som anges i flera nivåer hybrid programmet mönster:

1. Identifiera databasen företagsprogram som måste flyttas till molnet med hjälp av den [Microsoft Assessment and Planning (MAP) Toolkit](http://microsoft.com/map). MAP Toolkit samlar in lager-och prestandadata från datorer som du planerar för virtualisering och ger rekommendationer om kapacitet och planera assessment.
2. Planera de resurser och den konfiguration som behövs i Azure-plattformen, till exempel storage-konton och virtuella datorer.
3. Konfigurera nätverksanslutningen mellan företagsnätverket lokal och [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Använd någon av följande två metoder för att ställa in anslutningen mellan företagsnätverket lokal och en virtuell dator i Azure:
   
   1. Upprätta en anslutning mellan lokala och Azure via offentliga slutpunkter på en virtuell dator i Azure. Den här metoden ger ett enkelt installationen och kan du använda SQL Server-autentisering i den virtuella datorn. Konfigurera dessutom dina regler för nätverkssäkerhetsgrupper för att styra trafiken till den virtuella datorn. Mer information finns i [ge extern åtkomst till den virtuella datorn med Azure-portalen](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Upprätta en anslutning mellan lokala och Azure via Azure virtuella privata nätverk (VPN)-tunnel. Den här metoden kan du utöka domänprinciper till en virtuell dator i Azure. Du kan dessutom konfigurera brandväggsregler och använder Windows-autentisering i den virtuella datorn. För närvarande stöder Azure säker plats-till-plats-VPN- och punkt-till-plats VPN-anslutningar:
      
      * Med säker plats-till-plats-anslutning, kan du upprätta nätverksanslutningen mellan ditt lokala nätverk och virtuella nätverk i Azure. Det rekommenderas för att ansluta din lokala miljö till Azure.
      * Du kan använda säker punkt-till-plats-anslutning för att upprätta nätverksanslutningen mellan det virtuella nätverket i Azure och enskilda datorer var som helst med. Det rekommenderas oftast för utveckling och testning.
      
      Mer information om hur du ansluter till SQL Server i Azure finns [Anslut till en SQL Server-dator på Azure](virtual-machines-windows-sql-connect.md).
4. Konfigurera schemalagda jobb och aviseringar som säkerhetskopiera lokala data i en disk för virtuell dator i Azure. Mer information finns i [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx) och [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).
5. Du kan implementera ett av följande tre vanliga scenarier för beroende på programmets behov:
   
   1. Du kan behålla din webbserver och programserver okänsliga data i en databasserver i Azure medan du behåller den känsliga data lokalt.
   2. Du kan behålla din webbserver och programserver lokalt medan databasserver i en virtuell dator i Azure.
   3. Du kan behålla din databasserver, webbserver och programserver lokalt medan du behåller databasreplikerna i virtuella datorer i Azure. Den här inställningen på lokala servrar eller reporting program åtkomst till databasrepliker i Azure. Därför kan du uppnå om du vill sänka arbetsbelastningen i en lokal databas. Vi rekommenderar att du använder det här scenariot för frekventa Läs arbetsbelastningar och dess syfte. Information om hur du skapar databasrepliker i Azure finns i AlwaysOn-Tillgänglighetsgrupper på [hög tillgänglighet och Haveriberedskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Jämföra web development strategier i Azure
Du kan använda något av följande två programmeringsspråk metoder för att implementera och distribuera en flera nivåer SQL Server-baserat program i Azure:

* Ställa in en traditionell webbserver (IIS - Internet Information Services) i Azure och access-databaser i SQL Server i Azure Virtual Machines.
* Implementera och distribuera en tjänst i molnet till Azure. Kontrollerar du att den här Molntjänsten kan komma åt databaser i SQL Server i Azure virtuella datorer. En molnbaserad tjänst kan innehålla flera webb- och arbetsroller roller.

Följande tabell innehåller en jämförelse av traditionella webbutveckling med Azure Cloud Services och Azure Web Apps med avseende på SQL Server i Azure Virtual Machines. Tabellen innehåller Azure Web Apps eftersom det är möjligt att använda SQL Server i Azure VM som datakälla för Azure Web Apps via dess offentliga virtuella IP-adressen eller DNS-namn.

|  | Traditionella webbutveckling i Azure Virtual Machines | Cloud Services i Azure | Webbvärd med Azure Webbappar |
| --- | --- | --- | --- |
| **Programmet migrering från lokalt** |Befintliga program som-är. |Program behöver webb-och arbetsroller. |Befintliga program som – men lämpar sig för fristående webbprogram och webbtjänster som kräver snabb skalbarhet. |
| **Utveckling och distribution** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS-hanteraren, PowerShell. |Visual Studio Azure SDK, TFS, PowerShell. Varje molntjänst har två miljöer som du kan distribuera din servicepaket och konfiguration: mellanlagring och produktion. Du kan distribuera en tjänst i molnet till mellanlagringsmiljön att testa den innan du höjer upp den till produktion. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, ett, TFS, Web distribuera PowerShell. |
| **Installation och administration** |Du är ansvarig för administrativa uppgifter på program, data, brandväggsregler, virtuella nätverk och operativsystem. |Du är ansvarig för administrativa uppgifter på program, data, brandväggsregler och virtuella nätverk. |Du är ansvarig för administrativa uppgifter i programmet och bara data. |
| **Hög tillgänglighet och katastrofåterställning (HADR)** |Vi rekommenderar att du placerar virtuella datorer i samma tillgänglighetsuppsättning och i samma molntjänst. Att hålla dina virtuella datorer i samma kan tillgänglighetsuppsättning Azure för att placera noder för hög tillgänglighet i separata feldomäner och uppgradera domäner. På liknande sätt att hålla dina virtuella datorer i samma molntjänst kan belastningsutjämning och virtuella datorer kan kommunicera direkt med varandra via det lokala nätverket i ett Azure-datacenter.<br/><br/>Du ansvarar för att implementera en lösning för katastrofåterställning för SQL Server och hög tillgänglighet i Azure Virtual Machines att undvika driftavbrott. HADR teknik som stöds, se [hög tillgänglighet och Haveriberedskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Du är ansvarig för säkerhetskopiering av data och program.<br/><br/>Azure kan flytta virtuella datorer om värddatorn i datacentret misslyckas på grund av problem med maskinvara. Dessutom kan det uppstå planerade driftstopp på den virtuella datorn när värddatorn uppdateras för säkerhets- eller uppdateringar. Därför rekommenderar vi att du behåller virtuella minst två datorer i varje nivå i programmet för att säkerställa kontinuerlig tillgänglighet. Azure tillhandahåller inte SLA för en enskild virtuell dator. |Azure hanterar fel som uppstår till följd av den underliggande maskinvara eller programvaran. Vi rekommenderar att du implementera flera instanser av rollen webb eller arbetare för att garantera hög tillgänglighet för ditt program. Mer information finns i [molntjänster, virtuella datorer och virtuella nätverk servicenivåavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Du är ansvarig för säkerhetskopiering av data och program.<br/><br/>Du är ansvarig för att implementera en hög tillgänglighet och haveriberedskap lösning för att undvika eventuella avbrott för databaser som finns i en SQL Server-databas i en Azure VM. HDAR teknik som stöds, finns i hög tillgänglighet och katastrofåterställning för SQL Server i Azure Virtual Machines.<br/><br/>**SQL Server-databasspegling**: användning med Azure Cloud Services (web/worker-roller). SQL Server-datorer och ett molntjänstprojekt kan finnas i samma virtuella Azure-nätverk. Om SQL Server-VM inte är i samma virtuella nätverk måste du skapa ett SQL Server-Alias för att dirigera kommunikation till SQL Server-instansen. Dessutom måste aliasnamnet matcha namnet på SQL Server. |Hög tillgänglighet har ärvts från arbetsroller i Azure, Azure blob storage och Azure SQL Database. Till exempel underhålls Azure lagring tre repliker av alla blob-, tabell- och kön data. När som helst en Azure SQL Database håller tre kopior av data som körs, en primär replik och två sekundära repliker. Mer information finns i [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) och [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>När du använder SQL Server i Azure VM som datakälla för Azure-Webbappar, Kom ihåg att Azure Web Apps inte har stöd för Azure Virtual Network. Med andra ord måste alla anslutningar från Azure Web Apps till SQL Server-datorer i Azure gå igenom offentliga slutpunkterna för virtuella datorer. Detta kan orsaka vissa begränsningar för hög tillgänglighet och katastrofåterställning. Klientprogrammet på Azure-Webbappar som ansluter till SQL Server-VM med databasspegling skulle till exempel inte kan ansluta till den nya primära servern som databasspegling kräver att du konfigurerar Azure Virtual Network mellan SQL Server-värden virtuella datorer i Azure. Därför använder **databasspegling i SQL Server** med Azure Web Apps stöds inte för närvarande.<br/><br/>**SQL Server AlwaysOn-Tillgänglighetsgrupper**: du kan konfigurera AlwaysOn-Tillgänglighetsgrupper när du använder Azure Web Apps med SQL Server-datorer i Azure. Men du måste konfigurera AlwaysOn tillgänglighetsgruppens lyssnare för att dirigera kommunikation till den primära repliken via offentliga belastningsutjämnade slutpunkter. |
| **Korsanslutningar** |Du kan använda virtuella Azure-nätverk för att ansluta till lokalt. |Du kan använda virtuella Azure-nätverk för att ansluta till lokalt. |Virtuella Azure-nätverket stöds. Mer information finns i [Web Apps virtuell nätverksintegration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Skalbarhet** |Skala upp är tillgänglig genom att öka storlekar för virtuella datorer eller att lägga till fler diskar. Mer information om storlekar för virtuella datorer finns [storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**För databasservern**: skalbar är tillgänglig via databasen partitionering tekniker och SQL Server AlwaysOn-Tillgänglighetsgrupper.<br/><br/>Du kan använda för tunga skrivskyddade arbetsbelastningar [AlwaysOn Availability Groups](https://msdn.microsoft.com/library/hh510230.aspx) på flera sekundära noder såväl som SQL Server-replikering.<br/><br/>Du kan implementera horisontell partitionering data över flera fysiska servrar att ge programmet skalbar för tunga arbetsbelastningar.<br/><br/>Dessutom kan du implementera en skalbar med hjälp av [SQL Server med Data beroende routning](https://technet.microsoft.com/library/cc966448.aspx). Med Data beroende routning (DDR), måste du implementera mekanismen för partitionering i klientprogrammet, vanligtvis i företag nivå lager, vidarebefordra databasen begäranden till flera SQL Server-noder. Affärsnivån innehåller mappningar till hur data är partitionerad och vilken nod som innehåller data.<br/><br/>Du kan skala program som körs på virtuella datorer. Mer information finns i [så här skalar du ett program](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Viktigt meddelande**: den **Autoskala** funktionen i Azure kan du automatiskt öka eller minska de virtuella datorerna som används av ditt program. Den här funktionen säkerställer att slutanvändarens upplevelse inte är påverkas negativt under perioder och virtuella datorer måste stängas när efterfrågan är låg. Vi rekommenderar att du inte anger alternativet Autoskala för din molntjänst om den innehåller virtuella SQL Server-datorer. Anledningen är att funktionen Autoskala låter Azure att aktivera en virtuell dator när CPU-användning i den virtuella datorn är högre än tröskelvärdet för vissa och stänga av en virtuell dator när CPU-användningen blir lägre än den. Funktionen Autoskala är användbart för tillståndslösa program, till exempel webbservrar, där någon virtuell dator kan hantera belastningen utan några referenser till alla tidigare tillstånd. Funktionen Autoskala är inte användbar för tillståndskänsliga program som SQL Server, där endast en instans kan skriva till databasen. |Skala upp är tillgänglig genom att använda flera webb- och arbetsroller roller. Mer information om storlekar för virtuella datorer för webb- och arbetsroller finns [konfigurera storlekar för molntjänster](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>När du använder **molntjänster**, kan du definiera flera roller för att distribuera bearbetning och även uppnå flexibel skalning av ditt program. Varje tjänst i molnet innehåller en eller flera webbroller och/eller arbetsroller var och en med sin egen programfiler och konfiguration. Du kan skala upp en tjänst i molnet genom att öka antalet rollinstanser (virtuella datorer) distribueras för en roll och skala ned en tjänst i molnet genom att minska antalet rollinstanser. Detaljerad information finns i [Azure körning modeller](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Skalbar är tillgänglig via inbyggd hög tillgänglighet för Azure-supporten via [molntjänster, virtuella datorer och virtuella nätverk servicenivåavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) och belastningsutjämnare.<br/><br/>För en flernivåapp rekommenderar vi att du ansluter web/worker roller programmet till databasserver virtuella datorer via Azure Virtual Network. Azure tillhandahåller dessutom belastningsutjämning för virtuella datorer i samma molntjänst sprida användarförfrågningar över dem. Virtuella datorer som är anslutna på så sätt kan kommunicera direkt med varandra via det lokala nätverket i ett Azure-datacenter.<br/><br/>Du kan ställa in **Autoskala** på Azure portal samt schema gånger. Mer information finns i [hur du konfigurerar automatisk skalning för en tjänst i molnet i portalen](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skala upp eller ned**: du kan öka/minska storleken på den instansen (VM) som reserverats för din webbplats.<br/><br/>Skala ut: du kan lägga till fler reserverade instanser (VM) för webbplatsen.<br/><br/>Du kan ställa in **Autoskala** på portalen samt schema gånger. Mer information finns i [så att skala webbprogram](../../../app-service/web-sites-scale.md). |

Mer information om att välja mellan metoderna programming finns [Azure Web Apps molntjänster och virtuella datorer: när du ska använda som](../../../app-service/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör SQL Server i Azure virtuella datorer finns [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

