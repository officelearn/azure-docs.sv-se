---
title: Mönster för SQL Server på virtuella datorer | Microsoft Docs
description: Den här artikeln beskriver mönster för SQL Server på virtuella Azure-datorer. Det ger lösningsarkitekter och utvecklare en grund för god programarkitektur och design.
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
ms.openlocfilehash: 988acec8d7044afe87523637e46c9a4deb92b55e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477764"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Programmönster och utvecklingsstrategier för SQL Server i Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Sammanfattning:
Avgör vilka mönster eller mönster som du använder för din SQL-Server-baserade program i Azure miljö är ett viktigt beslut och kräver en djupare förståelse av hur SQL Server och varje komponent i nätverksinfrastrukturen i Azure tillsammans. Med SQL Server i Azure Infrastructure Services kan du enkelt migrera, underhåll och övervaka dina befintliga SQL Server-program som bygger på Windows Server på virtuella datorer i Azure.

Målet med den här artikeln är att ger lösningsarkitekter och utvecklare en grund för god programarkitektur och design, som de kan följa när du migrerar befintliga program till Azure samt utveckla nya program i Azure.

För varje programmönster hittar du ett scenario för lokala och dess respektive molnbaserad lösning relaterade tekniska rekommendationer. Dessutom beskrivs även Azure-specifika utvecklingsstrategier så att du kan utforma dina program på rätt sätt. På grund av många möjliga programmönster rekommenderar vi att arkitekter och utvecklare ska välja det lämpligaste mönstret för sina program och användare.

**Teknisk deltagare:** Luis Carlos Vargas sill, Madhan Arumugam Ramakrishnan

**Teknisk granskare:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introduktion
Du kan utveckla många typer av program på n-nivå genom att ange komponenterna i olika programskikt på olika datorer samt olika komponenter. Exempelvis kan du placera klientprogrammet och datavalideringsregler komponenter på en dator, frontend-webbnivån och data access-nivån komponenter i en annan dator och en backend-databasnivå i en annan dator. Den här typen av strukturera hjälper till att isolera varje nivå från varandra. Om du ändrar där data kommer från, behöver du inte ändra klienten eller programmet men bara data access-nivån komponenterna.

En typisk *n-nivå* programmet innehåller presentationsnivån, affärsnivå och datanivå:

| Nivå | Beskrivning |
| --- | --- |
| **Presentation** |Den *presentationsnivå* (webbnivå, klientdelsnivån) är skiktet där användarna samverkar med ett program. |
| **företag** |Den *affärsnivå* (mellannivå) är lagret som presentationen nivå och datanivå använder för att kommunicera med varandra och innehåller huvudfunktionerna i systemet. |
| **Data** |Den *datanivå* är i praktiken den server som lagrar data i ett program (till exempel en server som kör SQL Server). |

Programskikt beskrivs logiska grupperingar av funktioner och komponenter i ett program. medan nivåerna beskriver fysiska distribution av funktioner och komponenter på separata fysiska servrar, datorer, nätverk eller fjärranslutna platser. Lagren i ett program kan finnas på samma fysiska dator (samma nivå) eller kan fördelas på separata datorer (n-nivå) och kommunicerar komponenter i varje lager med komponenter i andra lager via väldefinierade gränssnitt. Du kan se termen-nivå som refererar till fysiska distribution mönster, till exempel två nivåer, tre nivåer och n-nivå. En **nivå 2-mönster** innehåller två nivåer av programmet: programservern och database-server. Det sker direkt kommunikation mellan programservern och databasservern. Application server innehåller komponenter för både webb- och business-nivå. I **3-nivåprogram mönstret**, det finns tre nivåer av programmet: webbserver, programservern, som innehåller affärslogiknivå och/eller business-nivå komponenter för dataåtkomst, och database-server. Kommunikationen mellan servern och databasservern sker över programservern. Detaljerad information om nivåerna och programskikt finns [Microsoft Application Architecture-guiden](https://msdn.microsoft.com/library/ff650706.aspx).

Innan du börjar läsa den här artikeln bör du ha kunskap om grundläggande begrepp för SQL Server och Azure. Mer information finns i [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) och [Azure.com](https://azure.microsoft.com/).

Den här artikeln beskriver flera mönster som kan vara lämplig för din enkla program samt mycket komplexa företagsprogram. Innan du med information om varje mönster, rekommenderar vi att du bör bekanta dig med tillgängliga data storage-tjänsterna i Azure, till exempel [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md), och [SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md). Förstå när du ska använda vilken tjänst för lagring av data tydligt för att fatta de bästa designbeslut för dina program.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Välj SQLServer i en Azure virtuell dator när:
* Du behöver kontroll på SQL Server och Windows. Detta kan exempelvis innehålla SQL Server-version, särskilt snabbkorrigeringar, prestandakonfiguration osv.
* Du behöver en fullständig kompatibilitet med SQL Server lokalt och vill flytta befintliga program till Azure som – är.
* Du vill utnyttja funktionerna i Azure-miljön, men Azure SQL Database stöder inte alla funktioner som krävs för ditt program. Det kan innefatta följande områden:
  
  * **Databasstorlek**: När den här artikeln har uppdaterats, stöder SQL Database en databas med upp till 1 TB data. Om ditt program kräver mer än 1 TB data och du inte vill att implementera lösningar för anpassade horisontell partitionering, rekommenderas att du använder SQL Server i en Azure virtuell dator. Den senaste informationen finns i [skala ut Azure SQL Database](https://msdn.microsoft.com/library/azure/dn495641.aspx), [DTU-Based köpa modellen](../../../sql-database/sql-database-service-tiers-dtu.md), och [vCore-baserade modellen för inköp](../../../sql-database/sql-database-service-tiers-vcore.md)(förhandsversion).
  * **Efterlevnad av HIPAA**: Hälsovård kunder och oberoende programvaruleverantörer (ISV) kan välja [SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) i stället för [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) eftersom SQL Server i en Azure virtuell dator ingår av HIPAA: S Affärspartneravtal (BAA). Information om efterlevnad finns i [Microsoft Azure Trust Center: Efterlevnad](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funktioner på instansnivå**: Just nu, SQL Database stöder inte funktioner som är bosatt utanför databasen (till exempel länkade servrar Agent-jobb, FileStream, Service Broker osv.). Mer information finns i [Azure SQL Database riktlinjer och begränsningar](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1-nivå (enkel): virtuell dator
I det här programmönstret distribuerar du din SQL Server-programmet och databasen till en fristående virtuell dator i Azure. Samma virtuella dator innehåller ditt klient-/ webbprogram, business-komponenterna, dataåtkomstlagret och databasservern. Den presentation, företag och dataåtkomstkoden avgränsas logiskt men är fysiskt belägna i en enskild server-dator. De flesta kunder börjar med det här mönstret för programmet och sedan skala ut genom att lägga till fler web-roller eller virtuella datorer till sina system.

Det här programmet mönstret är användbart om:

* Du vill utföra en enkel migrering till Azure-plattformen för att utvärdera om plattformen får du svar på dina programkrav eller inte.
* Du vill behålla alla programnivåer finns i samma virtuella dator i samma Azure-Datacenter att minska fördröjningen mellan nivåer.
* Du snabbt publicera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer när som helst.

Följande diagram visar en enkel lokala scenario och hur du kan distribuera sin lösning för moln-aktiverat i en virtuell dator i Azure.

![nivå 1-mönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Distribuera business-lagret (affärslogik och data som har åtkomst till komponenter) på samma fysiska nivå som presentationslagret kan maximera programmets prestanda, såvida inte du måste använda en separat nivå på grund av problem med skalbarhet och säkerhet.

Eftersom detta är ett mycket vanligt mönster att börja med kan vara användbara i följande artikel om migrering för att flytta dina data till din SQL Server-VM: [Migrera en databas till SQLServer på en Azure-VM](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-nivå (enkel): flera virtuella datorer
I det här programmönstret distribuerar du en 3-nivåprogram i Azure genom att placera varje programnivå i en annan virtuell dator. Detta ger en flexibel miljö för ett enkelt skala upp och skala ut. När en virtuell dator innehåller ditt klient-/ webbprogram, den andra som är värd för företagskomponenter och den andra som är värd för databasservern.

Det här programmet mönstret är användbart om:

* Du vill utföra en migrering av komplexa databasprogram till Azure Virtual Machines.
* Du vill ha olika programnivåer ska finnas i olika regioner. Du kan till exempel har delat databaser som distribueras till flera regioner för rapportering.
* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Azure Virtual Machines. Detaljerad information om företagsprogram, se [vad är ett företagsprogram](https://msdn.microsoft.com/library/aa267045.aspx).
* Du snabbt publicera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer när som helst.

Följande diagram visar hur du kan placera en enkel 3-nivåprogram i Azure genom att placera varje programnivå i en annan virtuell dator.

![3-nivåprogram mönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

I det här mönstret för programmet finns det bara en virtuell dator (VM) i varje nivå. Om du har flera virtuella datorer i Azure rekommenderar vi att du konfigurerar ett virtuellt nätverk. [Azure-nätverk](../../../virtual-network/virtual-networks-overview.md) skapar en betrodd säkerhetsgräns och också kan virtuella datorer kommunicerar med varandra via privata IP-adress. Dessutom kan alltid se till att det bara går alla Internet-anslutningar till på presentationsnivå. När du följer den här mönster, kan du hantera reglerna för nätverkssäkerhetsgrupper för att styra åtkomsten. Mer information finns i [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

I diagrammet, kan Internetprotokoll vara TCP, UDP, HTTP eller HTTPS.

> [!NOTE]
> Det är kostnadsfritt att sätta upp ett virtuellt nätverk i Azure. Du debiteras dock för VPN-gateway som ansluter till den lokala. Denna kostnad är baserad på mängden tid som anslutningen är upprättad och tillgänglig.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>nivå 2 och 3 nivåer med presentation nivån skalbar
I det här programmönstret distribuerar du nivå 2 eller 3 nivåer databasprogram till Azure Virtual Machines genom att placera varje programnivå i en annan virtuell dator. Dessutom kan skala du ut på presentationsnivå på grund av ökad mängden inkommande klientbegäranden.

Det här programmet mönstret är användbart om:

* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Azure Virtual Machines.
* Du vill skala ut på presentationsnivå på grund av ökad mängden inkommande klientbegäranden.
* Du snabbt publicera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer när som helst.
* Du äger en infrastruktursmiljö som kan skalas upp och ned på begäran.

Följande diagram visar hur du kan placera programskikt i flera virtuella datorer i Azure genom att skala ut på presentationsnivå på grund av ökad mängden inkommande klientbegäranden. Azure Load Balancer är ansvarig för att distribuera trafik över flera virtuella datorer och även bestämma vilka webbserver för att ansluta till som visas i diagrammet. Att ha flera instanser av servrar bakom en belastningsutjämnare säkerställer hög tillgänglighet för presentationsnivån.

![Programmönster - presentation nivån skala ut](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Metodtips för n-nivå, nivå 2 eller 3 nivåer mönster som har flera virtuella datorer i en nivå
Vi rekommenderar att du placerar de virtuella datorer som tillhör samma nivå i samma molntjänst och i samma tillgängligheten ange. Till exempel placera en uppsättning webbservrar i **CloudService1** och **AvailabilitySet1** och en uppsättning databasservrarna på **CloudService2** och  **AvailabilitySet2**. En tillgänglighetsuppsättning i Azure kan du placera noder med hög tillgänglighet i separata feldomäner och uppgraderingsdomäner.

Om du vill använda flera VM-instanser av en nivå, måste du konfigurera Azure Load Balancer mellan programnivåer. Om du vill konfigurera Load Balancer i varje nivå, skapar du en slutpunkt för Utjämning av nätverksbelastning på varje nivå virtuella datorer separat. För en specifik nivå, skapa virtuella datorer i samma molntjänst. Detta säkerställer att de har samma offentliga virtuella IP-adress. Skapa sedan en slutpunkt på en av de virtuella datorerna på den nivån. Tilldela sedan samma slutpunkt för andra virtuella datorer på nivån för belastningsutjämning. Genom att skapa en belastningsutjämnad uppsättning distribuerar trafik över flera virtuella datorer och låter också belastningsutjämnare att avgöra vilken nod att ansluta när en serverdel VM-nod misslyckas. Till exempel säkerställer har flera instanser av servrar bakom en belastningsutjämnare hög tillgänglighet för presentationsnivån.

Ett bra tips är alltid se till att alla internet-anslutningar först går till på presentationsnivå. Presentationslagret har åtkomst till affärsnivå och sedan affärsnivå får åtkomst till datanivån. Läs mer om hur du tillåter åtkomst till presentationslagret [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Observera att belastningsutjämnare i Azure fungerar ungefär som belastningsutjämnare i en lokal miljö. Mer information finns i [belastningsutjämning för Azures infrastrukturtjänster](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vi rekommenderar dessutom att du konfigurerar ett privat nätverk för virtuella datorer med hjälp av Azure Virtual Network. Detta innebär att de ska kunna kommunicera med varandra via privata IP-adress. Mer information finns i [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>nivå 2 och 3 nivåer med företag nivån skalbar
I det här programmönstret distribuerar du nivå 2 eller 3 nivåer databasprogram till Azure Virtual Machines genom att placera varje programnivå i en annan virtuell dator. Dessutom kan du distribuera application server-komponenter till flera virtuella datorer på grund av komplexiteten i ditt program.

Det här programmet mönstret är användbart om:

* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Azure Virtual Machines.
* Du vill distribuera programmet server-komponenter till flera virtuella datorer på grund av komplexiteten i ditt program.
* Du vill flytta business logic aktiverat lokala LOB (line of business) program till Azure Virtual Machines. LOB-program är en uppsättning program för kritiska datorer som är viktiga för att driva ett företag, till exempel redovisning, personalavdelningen (HR), löner, hantering av leverantörskedja och resursplanering program.
* Du snabbt publicera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer när som helst.
* Du äger en infrastruktursmiljö som kan skalas upp och ned på begäran.

Följande diagram visar ett scenario för lokala och dess aktiverat molnlösning. I det här scenariot kan placera du programskikt i flera virtuella datorer i Azure genom att skala ut business-nivå, som innehåller affärslogiknivå och komponenter för dataåtkomst. Azure Load Balancer är ansvarig för att distribuera trafik över flera virtuella datorer och även bestämma vilka webbserver för att ansluta till som visas i diagrammet. Att ha flera instanser av programservrar bakom en belastningsutjämnare säkerställer hög tillgänglighet till affärsnivå. Mer information finns i [Metodtips för n-nivå, nivå 2 eller 3 nivåer mönster som har flera virtuella datorer i en nivå](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Programmönster med företag nivån skala ut](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>nivå 2 och 3 nivåer med presentation och företag nivåerna skalbara och HADR
I det här programmönstret distribuerar du nivå 2 eller 3 nivåer databasprogram till Azure Virtual Machines genom att distribuera presentationsnivån (webbserver) och business-nivå (Programserver) komponenter till flera virtuella datorer. Dessutom kan implementera du lösningar för hög tillgänglighet och haveriberedskap för dina databaser i Azure virtual machines.

Det här programmet mönstret är användbart om:

* Du vill flytta företagsprogram från virtualiserade plattformar på lokala till Azure genom att implementera hög tillgänglighet för SQL Server och funktioner för katastrofåterställning.
* Du vill skala ut presentationsnivån och affärsnivån på grund av ökad mängden inkommande klientbegäranden och komplexiteten i ditt program.
* Du snabbt publicera utvecklings- och testmiljöer under korta tidsperioder.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer när som helst.
* Du äger en infrastruktursmiljö som kan skalas upp och ned på begäran.

Följande diagram visar ett scenario för lokala och dess aktiverat molnlösning. I det här scenariot kan skala du ut presentationsnivån och komponenter för business-nivå i flera virtuella datorer i Azure. Dessutom kan implementera du hög tillgänglighet och katastrofåterställning (HADR) återställningsfunktioner för SQL Server-databaser i Azure.

Köra flera kopior av ett program i olika Se virtuella datorer till att du är förfrågningar för belastningsutjämning mellan dem. När du har flera virtuella datorer kan behöva du se till att alla dina virtuella datorer är tillgänglig och körs vid en punkt i tiden. Om du konfigurerar belastningsutjämning, Azure Load Balancer spårar hälsotillståndet för virtuella datorer och dirigerar inkommande anrop till felfritt fungerande VM-noder korrekt. Information om hur du konfigurerar belastningsutjämning för de virtuella datorerna finns i [belastningsutjämning för Azures infrastrukturtjänster](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Att ha flera instanser av webb- och programservrar bakom en belastningsutjämnare säkerställer hög tillgänglighet för presentation- och företagsnivåerna.

![Skalbarhet och hög tillgänglighet](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Metodtips för mönster för att kräva SQL HADR
När du konfigurerar hög tillgänglighet för SQL Server och lösningar för haveriberedskap i Azure Virtual Machines, konfigurera ett virtuellt nätverk för dina virtuella datorer med [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) är obligatoriskt.  Virtuella datorer inom ett virtuellt nätverk har en stabil privata IP-adress även efter ett driftstopp för tjänsten, så du kan undvika uppdateringstiden krävs för DNS-namnmatchning. Dessutom kan det virtuella nätverket kan du utöka ditt lokala nätverk till Azure och skapar en betrodd säkerhetsgräns. Om ditt program har domänbegränsningar för företagets (till exempel Windows-autentisering, Active Directory), till exempel hur du konfigurerar [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) krävs.

De flesta kunder, som kör produktionskoden på Azure, hindrar både primära och sekundära repliker i Azure.

Omfattande information och självstudier om hög tillgänglighet och katastrofåterställning återställningsfunktioner finns i [hög tillgänglighet och katastrofåterställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>nivå 2 och 3 nivåer med hjälp av Azure virtuella datorer och molntjänster
I det här programmet mönstret distribuerar du nivå 2 eller 3 nivåer program till Azure med hjälp av båda [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webb- och worker-roller - plattform som en tjänst (PaaS)) och [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) () Infrastruktur som en tjänst (IaaS)). Med hjälp av [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) för presentationsnivå nivå/business och SQL Server i [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för data-nivån är fördelaktigt för de flesta program som körs på Azure. Anledningen är att ha en beräkningsinstans som körs på Cloud Services tillhandahåller en enkel hantering, distribution, övervakning och skala ut.

Med Cloud Services, Azure underhåller infrastrukturen åt dig, utför rutinunderhåll, korrigeringar operativsystemen och försöker återställa från fel på tjänsten och maskinvara. När ditt program behöver skala ut, är automatisk och manuell skalbar alternativ tillgängliga för ditt molntjänstprojekt genom att öka eller minska antalet instanser eller virtuella datorer som används av ditt program. Dessutom kan du använda en lokal Visual Studio till att distribuera programmet till ett molntjänstprojekt i Azure.

Sammanfattningsvis, om du inte vill att äga omfattande administrativa uppgifter för presentation/business-nivå och ditt program inte kräver någon komplexa konfigurering av programvaran eller operativsystemet, använda Azure Cloud Services. Om Azure SQL Database inte stöder alla funktioner som du letar efter, använder du SQL Server i en Azure virtuell dator för datanivån. Köra en App i Azure Cloud Services och lagra data i Azure Virtual Machines kombinerar fördelarna med båda tjänsterna. En detaljerad jämförelse finns i avsnittet i det här avsnittet på [jämföra utvecklingsstrategier i Azure](#comparing-web-development-strategies-in-azure).

I det här programmet mönstret innehåller presentationsnivån en webbroll som är en Cloud Services-komponent som körs i Azure-körningsmiljö och den är anpassade för web application programming som stöds av IIS och ASP.NET. Företag eller backend-nivån omfattar en arbetsroll, som är en Cloud Services-komponent som körs i Azure-körningsmiljö och det är användbart för generaliserad utveckling och kan utföra Bakgrundsbearbetning för en webbroll. Databasnivån finns i en SQL Server-dator i Azure. Kommunikationen mellan presentationsnivån och databasnivån sker direkt eller via affärsnivå – komponenter för worker-roll.

Det här programmet mönstret är användbart om:

* Du vill flytta företagsprogram från virtualiserade plattformar på lokala till Azure genom att implementera hög tillgänglighet för SQL Server och funktioner för katastrofåterställning.
* Du äger en infrastruktursmiljö som kan skalas upp och ned på begäran.
* Azure SQL Database stöder inte alla funktioner som krävs för ditt program eller en databas.
* Du vill utföra stresstester för olika arbetsbelastningsnivåer men samtidigt du vill inte äger och underhålla många fysiska datorer när som helst.

Följande diagram visar ett scenario för lokala och dess aktiverat molnlösning. I det här scenariot kan placera du presentationsnivå i webbroller affärsnivå i worker-roller men datanivå i virtuella datorer i Azure. Det ser till att köra flera kopior av presentationsnivån i olika web-roller för att belastningsutjämna förfrågningar mellan dem. När du kombinerar Azure Cloud Services med Azure Virtual Machines rekommenderar vi att du ställer in [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) samt. Med [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), du kan ha stabil och beständiga privata IP-adresser inom samma molntjänst i molnet. När du definierar ett virtuellt nätverk för virtuella datorer och molntjänster kan börja de kommunicera med varandra via privata IP-adress. Dessutom har virtuella datorer och Azure web/worker-roller i samma [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) ger mindre fördröjning och mer säker anslutning. Mer information finns i [vad är en molnbaserad tjänst](../../../cloud-services/cloud-services-choose-me.md).

Som visas i diagrammet, Azure Load Balancer distribuerar trafiken mellan flera virtuella datorer och avgör också vilken webbserver eller programserver ansluter till. Att ha flera instanser av webb- och programservrar bakom en belastningsutjämnare säkerställer hög tillgänglighet för presentationsnivån och affärsnivån. Mer information finns i [bästa praxis för programmet mönster som kräver SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Programmönster med Cloud Services](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

En annan metod för att implementera den här programmönster är att använda en konsoliderad webbroll som innehåller både presentationsnivån och business-nivå komponenter som du ser i följande diagram. Det här programmet mönstret är användbart för program som kräver tillståndskänsliga design. Eftersom Azure tillhandahåller tillståndslösa compute-noder på webb-och arbetsroller, rekommenderar vi att du implementera en logik för att lagra sessionstillstånd med någon av följande tekniker: [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) eller [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).

![Programmönster med Cloud Services](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Mönstret med virtuella Azure-datorer, Azure SQL Database och Azure App Service (Webbappar)
Det främsta målet med den här programmönster är att visa dig hur du kombinerar Azure-infrastruktur som en tjänst (IaaS)-komponenter med Azure platform as a service-komponenter (PaaS) i din lösning. Det här mönstret fokuserar på Azure SQL-databas för lagring av relationsdata. Den omfattar inte SQL Server i en Azure-dator som ingår i Azure-infrastrukturen som ett tjänsterbjudande.

I det här programmönstret distribuerar du ett databasprogram till Azure genom att placera presentation och nivåer i samma virtuella dator och få åtkomst till en databas i Azure SQL Database (SQL Database)-servrar. Du kan implementera presentationsnivån med hjälp av traditionella IIS-baserade webblösningar. Eller, du kan implementera en kombinerad presentation och affärsnivån med hjälp av [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Det här programmet mönstret är användbart om:

* Du har redan en befintlig SQL Database-server som konfigurerats i Azure och du vill testa ditt program snabbt.
* Du vill testa funktionerna i Azure-miljön.
* Du snabbt publicera utvecklings- och testmiljöer under korta tidsperioder.
* Komponenter för åtkomst av logik och information som ditt företag kan vara fristående i ett webbprogram.

Följande diagram visar ett scenario för lokala och dess aktiverat molnlösning. I det här scenariot kan placera du programskikt i en virtuell dator i Azure och åtkomst i Azure SQL Database.

![Blandat programmönster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Om du väljer att implementera en kombinerad webb- och programnivå med hjälp av Azure Web Apps, rekommenderar vi att du behåller nivån mellannivå eller ett program som dynamiska länkbibliotek (dll) i kontexten för ett webbprogram.

Dessutom Granska rekommendationerna som ges i den [jämföra web utvecklingsstrategier i Azure](#comparing-web-development-strategies-in-azure) i slutet av den här artikeln om du vill veta mer om hur du programmerar tekniker.

## <a name="n-tier-hybrid-application-pattern"></a>N-nivå hybrid-mönster
I n-nivå hybrid-mönster, kan du implementera ditt program i flera nivåer som distribueras mellan lokala och Azure. Därför kan du skapa en flexibel och återanvändbara hybridsystem som du kan ändra eller lägga till en specifik nivå utan att ändra de andra nivåerna. Om du vill utöka företagets nätverk till molnet, använder du [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) service.

Den här hybrid programmet mönstret är användbart om:

* Du vill bygga program som kör delvis i molnet och delvis lokalt.
* Du vill migrera vissa eller alla element i ett befintligt lokalt program till molnet.
* Du vill flytta företagsprogram från lokala virtualiserade plattformar till Azure.
* Du äger en infrastruktursmiljö som kan skalas upp och ned på begäran.
* Du snabbt publicera utvecklings- och testmiljöer under korta tidsperioder.
* Vill du ett kostnadseffektivt sätt att göra säkerhetskopior för enterprise-databasprogram.

Följande diagram visar en n-nivå hybrid-mönster som omfattar både lokalt och Azure. I diagrammet visas den lokala infrastrukturen innehåller [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) domänkontrollant för användarautentisering och auktorisering. Observera att diagrammet visar ett scenario, där vissa delar av datanivå live i ett lokalt Datacenter medan vissa delar av datanivå live i Azure. Du kan implementera flera andra hybridscenarier beroende på programmets behov. Exempelvis kan du behålla presentationsnivån och affärsnivån i en lokal miljö men datanivå i Azure.

![Mönster för N-nivåprogram](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

I Azure, kan du använda Active Directory som en fristående molnkatalog för din organisation eller du kan också integrera befintliga lokala Active Directory med [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Som visas i diagrammet, komponenter för business-nivå har åtkomst till flera datakällor, till exempel att [SQL Server i Azure](virtual-machines-windows-sql-server-iaas-overview.md) via en privat intern IP-adress till en lokal SQL Server via [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), eller till [SQL Database](../../../sql-database/sql-database-technical-overview.md) med hjälp av .NET Framework data provider-tekniker. I det här diagrammet är ett valfritt storage tjänst i Azure SQL Database.

Du kan implementera följande arbetsflöde i den ordning som anges i n-nivå hybrid-programmönster:

1. Identifiera databasprogram för företag som behöver flyttas till molnet med hjälp av den [Microsoft Assessment and Planning (MAP) Toolkit](https://microsoft.com/map). MAP Toolkit samlar in inventerings-och prestanda från datorer som du planerar för virtualisering och innehåller rekommendationer om kapacitet och planera för utvärdering.
2. Planera resurser och konfigurering som krävs i Azure-plattformen, till exempel lagringskonton och virtuella datorer.
3. Konfigurera nätverksanslutningen mellan företagsnätverket lokala platser och [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Använd någon av följande två metoder för att konfigurera anslutningen mellan företagsnätverket lokala platser och en virtuell dator i Azure:
   
   1. Upprätta en anslutning mellan lokala och Azure via offentliga slutpunkter på en virtuell dator i Azure. Den här metoden ger en enkel installation och kan du använda SQL Server-autentisering i den virtuella datorn. Konfigurera dessutom dina regler för nätverkssäkerhetsgrupper för att styra trafiken till den virtuella datorn. Mer information finns i [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Upprätta en anslutning mellan lokala och Azure via Azure virtuella privata nätverk (VPN)-tunnel. Den här metoden kan du utöka domänprinciper till en virtuell dator i Azure. Du kan dessutom konfigurera brandväggsregler och använder Windows-autentisering i den virtuella datorn. Azure stöder för närvarande säker plats-till-plats-VPN och punkt-till-plats VPN-anslutningar:
      
      * Med säker plats-till-plats-anslutning, kan du upprätta nätverksanslutning mellan ditt lokala nätverk och ditt virtuella nätverk i Azure. Vi rekommenderar för att ansluta ditt lokala Datacenter-miljö till Azure.
      * Du kan använda säker punkt-till-plats-anslutning för att upprätta nätverksanslutning mellan ditt virtuella nätverk i Azure och din enskilda datorer som kör var som helst. Huvudsakligen rekommenderas för utveckling och testning.
      
      Information om hur du ansluter till SQL Server i Azure finns i [Anslut till en SQL Server-dator på Azure](virtual-machines-windows-sql-connect.md).
4. Ställ in schemalagda jobb och aviseringar som säkerhetskopiera lokala data i en disk för virtuell dator i Azure. Mer information finns i [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx) och [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).
5. Beroende på programmets behov kan du implementera en av följande tre vanliga scenarier:
   
   1. Du kan behålla din webbserver och programserver okänsliga data i en databasserver i Azure medan du behålla de känsliga data lokalt.
   2. Du kan behålla din webbserver och programserver lokalt medan databasserver på en virtuell dator i Azure.
   3. Du kan behålla din databasserver, webbservern och på en lokal programserver medan du behåller replikeringarna i virtuella datorer i Azure. Den här inställningen på den lokala webbservrar eller reporting program få åtkomst till databasrepliker i Azure. Därför kan du uppnå om du vill sänka arbetsbelastningen i en lokal databas. Vi rekommenderar att du implementerar det här scenariot för tunga läsa arbetsbelastningar och utvecklingsarbetet syften. Information om hur du skapar databasrepliker i Azure finns i AlwaysOn-Tillgänglighetsgrupper på [hög tillgänglighet och katastrofåterställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Jämföra web utvecklingsstrategier i Azure
Du kan använda något av följande två metoder för programmering för att implementera och distribuera en SQL Server-baserade program i Azure med flera nivåer:

* Konfigurera en traditionell webbserver (IIS - Internet Information Services) i Azure och access-databaser i SQL Server i Azure Virtual Machines.
* Implementera och distribuera en tjänst i molnet till Azure. Se sedan till att den här Molntjänsten kan komma åt databaser i SQL Server på virtuella Azure-datorer. En molnbaserad tjänst kan innehålla flera webb- och worker-roller.

Följande tabell innehåller en jämförelse av traditionella webbutveckling med Azure Cloud Services och Azure Web Apps med avseende på SQL Server i Azure Virtual Machines. Tabellen innehåller Azure Web Apps som det är möjligt att använda SQL Server på virtuella Azure-datorn som en datakälla för Azure Web Apps via dess offentliga virtuella IP-adressen eller DNS-namn.

|  | Traditionella webbutveckling i Azure Virtual Machines | Cloud Services i Azure | Webbvärdtjänster med Azure Web Apps |
| --- | --- | --- | --- |
| **Program-migreringen från en lokal plats** |Befintliga program som – är. |Programmen behöver webb-och arbetsroller. |Befintliga program som – men lämpar sig för fristående webbprogram och webbtjänster som kräver snabb skalbarhet. |
| **Utveckling och distribution** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Manager, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Varje molntjänst har två miljöer som du kan distribuera dina service-paketet och konfiguration: mellanlagring och produktion. Du kan distribuera en molntjänst till mellanlagringsmiljön att testa det innan du höjer upp den till produktion. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web distribuera PowerShell. |
| **Administration och konfiguration** |Du är ansvarig för administrativa uppgifter på program, data, brandväggsregler, virtuellt nätverk och operativsystem. |Du är ansvarig för administrativa uppgifter på program, data, brandväggsregler och virtuellt nätverk. |Du är ansvarig för administrativa uppgifter på program och data endast. |
| **Hög tillgänglighet och katastrofåterställning (HADR)** |Vi rekommenderar att du placerar virtuella datorer i samma tillgänglighetsuppsättning och i samma molntjänst. Att hålla dina virtuella datorer i samma kan tillgänglighetsuppsättning Azure för att försätta noder med hög tillgänglighet i separata feldomäner och uppgraderingsdomäner. På samma sätt att hålla dina virtuella datorer i samma molntjänst kan Utjämning av nätverksbelastning och virtuella datorer kan kommunicera direkt med varandra via det lokala nätverket inom ett Azure-datacenter.<br/><br/>Du ansvarar för att implementera en hög tillgänglighet och en haveriberedskapslösning för SQL Server i Azure Virtual Machines för att undvika eventuella driftstopp. HADR teknik som stöds, se [hög tillgänglighet och katastrofåterställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Du är ansvarig för säkerhetskopiering av dina egna data och program.<br/><br/>Azure kan flytta dina virtuella datorer om värddatorn i datacentret misslyckas på grund av maskinvaruproblem. Dessutom kan det uppstå planerade driftstopp för den virtuella datorn när värddatorn har uppdaterats för säkerhets- eller uppdateringar. Därför rekommenderar vi att du har minst två datorer i varje programnivå för att säkerställa kontinuerlig tillgänglighet. Azure ger inte serviceavtal (SLA) för en enskild virtuell dator. |Azure hanterar fel som härrör från den underliggande maskinvara eller programvaran. Vi rekommenderar att du använder flera instanser av en web- eller worker-roll för att garantera hög tillgänglighet för ditt program. Mer information finns i [molntjänster, virtuella datorer och virtuella nätverk serviceavtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Du är ansvarig för säkerhetskopiering av dina egna data och program.<br/><br/>För databaser som finns i en SQL Server-databas i en Azure VM, är du ansvarig för att implementera en hög tillgänglighet och katastrofåterställning lösning för att undvika driftavbrott. HDAR teknik som stöds, finns i hög tillgänglighet och katastrofåterställning för SQL Server i Azure Virtual Machines.<br/><br/>**SQL Server-databas spegling**: Använd med Azure Cloud Services (web/worker-roller). SQL Server-datorer och ett molntjänstprojekt kan finnas i samma Azure-nätverk. Om SQL Server-VM inte är i samma virtuella nätverk, måste du skapa ett SQL Server-Alias för att dirigera kommunikation till SQL Server-instansen. Dessutom måste aliasnamnet matcha namnet på SQL Server. |Hög tillgänglighet har ärvts från arbetsroller i Azure, Azure blob storage och Azure SQL Database. Till exempel underhålls Azure-lagring tre repliker alla blob-, tabell-och ködata. När som helst en Azure SQL Database från tre repliker av data som körs – en primär replik och två sekundära repliker. Mer information finns i [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) och [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>När du använder SQL Server i Azure VM som en datakälla för Azure Web Apps, Kom ihåg att Azure Web Apps inte har stöd för Azure Virtual Network. Med andra ord måste alla anslutningar från Azure-Webbappar till SQL Server-datorer i Azure gå igenom offentliga slutpunkterna för virtuella datorer. Detta kan orsaka vissa begränsningar för hög tillgänglighet och katastrofåterställning. Klientprogrammet på Azure Web Apps som ansluter till SQL Server-dator med databasspegling skulle till exempel inte att kunna ansluta till den nya primära servern eftersom databasspegling kräver att du ställer in Azure-nätverk mellan SQL Server-värden virtuella datorer i Azure. Därför använder **databasspegling i SQL Server** med Azure Web Apps stöds inte för tillfället.<br/><br/>**SQL Server AlwaysOn-Tillgänglighetsgrupper**: Du kan konfigurera AlwaysOn-Tillgänglighetsgrupper när du använder Azure Web Apps med SQL Server-datorer i Azure. Men du måste konfigurera AlwaysOn tillgänglighetsgruppens lyssnare för att dirigera kommunikation till den primära repliken via offentliga slutpunkter för Utjämning av nätverksbelastning. |
| **Anslutning på flera platser** |Du kan använda Azure Virtual Network för att ansluta till den lokala. |Du kan använda Azure Virtual Network för att ansluta till den lokala. |Azure-nätverk stöds. Mer information finns i [Web Apps virtuell nätverksintegrering](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Skalbarhet** |Skala upp är tillgänglig genom att öka storlekar för virtuella datorer eller att lägga till fler diskar. Mer information om storlekar för virtuella datorer finns i [storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**För databasserver**: Skala ut är tillgänglig via tekniker för partitionering av databas och SQL Server AlwaysOn-Tillgänglighetsgrupper.<br/><br/>Du kan använda för tung skrivskyddade arbetsbelastningar [AlwaysOn Availability Groups](https://msdn.microsoft.com/library/hh510230.aspx) på flera sekundära noder samt SQL Server-replikering.<br/><br/>Du kan implementera horisontell partitionering data över flera fysiska servrar för att ge programmet skalbara för tunga arbetsbelastningar.<br/><br/>Dessutom kan du implementera en skalbar med hjälp av [SQL Server med databeroende routning](https://technet.microsoft.com/library/cc966448.aspx). Med Data beroende routning (DDR), måste du implementera partitionering mekanism i klientprogrammet, vanligtvis i lager för business-nivå, dirigera databasförfrågningar till flera SQL Server-noder. Affärsnivån innehåller mappningar till hur data är partitionerad och vilken nod som innehåller data.<br/><br/>Du kan skala program som de virtuella datorerna körs. Mer information finns i [hur du skalar en applikation](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Viktigt meddelande**: Den **Autoskala** funktionen i Azure kan du automatiskt öka eller minska de virtuella datorer som används av ditt program. Den här funktionen garanterar att slutanvändarens upplevelse inte påverkas negativt under perioder hög och virtuella datorer är avstängda när efterfrågan är låg. Vi rekommenderar att du inte har angett alternativet automatisk skalning för din molntjänst om den innehåller SQL Server-datorer. Det beror på att funktionen för automatisk skalning kan Azure att aktivera en virtuell dator när CPU-användning i den virtuella datorn är högre än tröskelvärdet för vissa och för att stänga av en virtuell dator när CPU-användningen blir lägre än den. Funktionen för automatisk skalning är användbart för tillståndslösa program, till exempel webbservrar, där alla virtuella datorer kan använda för att hantera arbetsbelastning utan några referenser till alla tidigare tillstånd. Funktionen för automatisk skalning är inte användbart för tillståndskänsliga program, till exempel SQL Server, där endast en instans kan skriva till databasen. |Skala upp är tillgänglig genom att använda flera webb- och worker-roller. Mer information om storlekar för virtuella datorer för web-roller och worker-roller finns i [konfigurera storlekar för Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>När du använder **molntjänster**, du kan definiera flera roller för att distribuera bearbetning och även få flexibel skalning av ditt program. Varje molntjänst innehåller en eller flera web-roller och/eller worker-roller, var och en med sin egen programfiler och konfiguration. Du kan skala upp en tjänst i molnet genom att öka antalet rollinstanser (virtuella datorer) distribueras för en roll och skala ned en tjänst i molnet genom att minska antalet rollinstanser. Detaljerad information finns i [Azure-Utförandemodeller](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Skala ut är tillgängligt via inbyggd hög tillgänglighet för Azure-support via [molntjänster, virtuella datorer och virtuella nätverk serviceavtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) och belastningsutjämnare.<br/><br/>För ett flernivåprogram rekommenderar vi att du ansluta web/worker-roller programmet till databasserver virtuella datorer via Azure-nätverk. Azure tillhandahåller dessutom belastningsutjämning för virtuella datorer i samma molntjänst, sprida användarbegäranden över dem. Virtuella datorer är anslutna på så sätt kan kommunicera direkt med varandra via det lokala nätverket inom ett Azure-datacenter.<br/><br/>Du kan ställa in **Autoskala** på Azure portal samt schema gånger. Mer information finns i [så här konfigurerar du automatisk skalning för en molntjänst i portalen](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skala upp och ned**: Du kan öka/minska storleken på den instansen (VM) som är reserverade för din webbplats.<br/><br/>Skala ut: Du kan lägga till fler reserverade instanser (VM) för webbplatsen.<br/><br/>Du kan ställa in **Autoskala** på portalen samt schema gånger. Mer information finns i [så skala Web Apps](../../../app-service/web-sites-scale.md). |

Läs mer om att välja mellan metoderna programming [Azure Web Apps, Cloud Services och virtuella datorer: När du ska använda som](../../../app-service/overview-compare.md).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [SQL Server på Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md).

