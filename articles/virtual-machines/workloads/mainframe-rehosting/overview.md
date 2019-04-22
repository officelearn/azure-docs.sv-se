---
title: Stordatorprogram rehosting på Azure virtual machines
description: Ange ny värd för din stordatorprogram arbetsbelastningar som till exempel IBM Z-baserade system använder virtuella datorer (VM) i Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8b7c2a088dc917c319acf6cad251ad53367a14b6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895045"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Stordatorprogram rehosting på Azure virtual machines

Migrera arbetsbelastningar från stordatorprogram miljöer till molnet kan du modernisera din infrastruktur och ofta sänka kostnaderna. Många arbetsbelastningar kan överföras till Azure med bara mindre ändringar i koden, till exempel att uppdatera namnen på databaser.

Normalt sett, termen *stordatorprogram* innebär en stor datorsystem. Flesta används för tillfället är mer specifikt IBM System Z-servrar eller IBM plug-compatible system som kör MVS, DOS, VSE, OS/390 eller z/OS.

En Azure-dator (VM) används för att isolera och hantera resurser för ett visst program på en enda instans. Stordatorer, till exempel IBM z/OS använder logiska partitioner (LPARS) för detta ändamål. En stordatorprogram kan använda en LPAR för CICS region med associerade COBOL program och en separat LPAR för IBM Db2-databas. En typisk [program på n-nivå på Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuerar virtuella Azure-datorer till ett virtuellt nätverk som kan segmenteras i undernät för varje nivå.

Virtuella Azure-datorer kan köra stordatorprogram emulering miljöer och kompilatorer som har stöd för lift and shift-scenarier. Utveckling och testning är ofta bland de första arbetsbelastningarna du migrerar från en stordatorprogram till ett Azure dev/test-miljö. Serverkomponenter som du kan emulera innehålla online transaction-processen (OLTP), batch och system för inmatning av data som visas i följande bild.

![Emulering miljöer i Azure kan du köra z/OS-baserade system.](media/01-overview.png)

Vissa stordatorprogram arbetsbelastningar kan migreras till Azure utan större besvär, medan andra kan rehosted på Azure med hjälp av en partnerlösning. Detaljerade anvisningar om hur du väljer en partnerlösning i [Azure Stordatormigrering center](https://azure.microsoft.com/migration/mainframe/) kan hjälpa.

## <a name="mainframe-migration"></a>Stordatormigrering

Rehost, återskapa, ersätta eller ta ur bruk? IaaS och PaaS? För att fastställa rätt migreringsstrategi för programmets stordatorprogram se den [stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) guide i Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>Micro fokus rehosting plattform

Micro fokus Enterprise Server är en av de största stordatorprogram rehosting finns tillgängliga. Du kan använda den kör z/OS-arbetsbelastningar på en billigare x86 platform på Azure.

Så här kommer du igång:

- [Installera Enterprise Server och företagsutvecklare på Azure](./microfocus/set-up-micro-focus-azure.md)
- [Konfigurera CICS BankDemo för företagsutvecklare på Azure](./microfocus/demo.md)
- [Kör företagsservern i en Docker-behållare i Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame på Azure

TmaxSoft OpenFrame är en populär stordatorprogram rehosting lösning som används i lift and shift-scenarier. En OpenFrame miljö på Azure är lämplig för utveckling, demonstrationer, testning eller produktionsarbetsbelastningar.

Så här kommer du igång:

- [Kom igång med TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Ladda ned e-boken](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z Development och Test-miljö (IBM zD & T) ställer du in en icke-produktionsmiljö på Azure som du kan använda för utveckling, testning och demonstrationer av z/OS-baserade program.

Emulering miljön i Azure kan vara värd för olika typer av Z-instanser via programmet utvecklare kontrolleras distributioner (ADCDs). Du kan köra zD & T Personal Edition, zD & T parallella Sysplex, och zD & T Enterprise Edition på Azure och Azure Stack.

Så här kommer du igång:

- [Konfigurera IBM zD & T 12.0 på Azure](./ibm/install-ibm-z-environment.md)
- [Konfigurera ADCD på zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale på Azure

IBM DB2 pureScale miljön innehåller ett databas-kluster för Azure. Det är inte identiska med den ursprungliga miljön, men det ger liknande tillgänglighet och skalbarhet som IBM DB2 för z/OS som körs i en parallell Sysplex-installationsprogrammet.

Kom igång genom att se [IBM DB2 pureScale på Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Överväganden

När du migrerar stordatorprogram arbetsbelastningar till Azure-infrastrukturen som en tjänst (IaaS), kan du välja från flera typer av behovsbaserade och skalbara datorresurser, inklusive Azure virtuella datorer. Azure erbjuder en mängd [Linux](/azure/virtual-machines/linux/overview) och [Windows](/azure/virtual-machines/windows/overview) virtuella datorer.

### <a name="compute"></a>Databearbetning

Azure beräkningskraft jämför positivt till en stordatorprogram kapacitet. Om du funderar på att flytta en stordatorprogram arbetsbelastning till Azure, jämför stordatorprogram måttet en miljon instruktioner per sekund (MIPS) till virtuella processorer. 

Lär dig hur du [flytta beräkning av stordatorprogram till Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Hög tillgänglighet och redundans

Azure erbjuder åtagande-baserade servicenivåavtal (SLA). Flera nines tillgänglighet är standard och serviceavtal kan optimeras med lokal eller platsbaserade replikering av tjänster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

Med Azure IaaS, till exempel en virtuell dator, specifika systemfunktioner tillhandahålla stöd för redundans, till exempel redundanskluster instanser och [tillgänglighetsuppsättningar](/azure/virtual-machines/windows/regions-and-availability#availability-sets). När du använder Azure-plattformen som en tjänst (PaaS) resurser hanterar plattformen redundans automatiskt. Exempel är [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) och [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Skalbarhet

Stordatorer vanligtvis kan skalas upp, när molnet miljöer skala ut. Azure erbjuder en mängd [Linux](/azure/virtual-machines/linux/sizes) och [Windows](/azure/virtual-machines/windows/sizes) storlekar som uppfyller dina behov. Molnet kan också skalas upp eller ned matchar exakt användaren specifikationer. Beräkningskraft, lagring och tjänster, [skala](/azure/architecture/best-practices/auto-scaling) på begäran under en användningsbaserad faktureringsmodell.

### <a name="storage"></a>Storage

Du har en mängd alternativ för flexibel och skalbar lagring i molnet, och du betalar bara för vad du behöver. [Azure Storage](/azure/storage/common/storage-introduction) erbjuder en mycket skalbar objektlagring för dataobjekt, en filsystemtjänst för molnet, meddelandelagring tillförlitliga och ett NoSQL-Arkiv. Hanterade och ohanterade diskar ger beständig och säker disklagring för virtuella datorer.

Lär dig hur du [flytta lagringen för stordatorprogram till Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

Underhålla en egen plats för katastrofåterställning kan det vara en dyr lösning. Azure har lätt att implementera och kostnadseffektivt alternativ för [säkerhetskopiering](/azure/backup/backup-introduction-to-azure-backup), [recovery](/azure/site-recovery/site-recovery-overview), och [redundans](/azure/storage/common/storage-redundancy) på lokala eller regionala nivåer eller via geo-redundans.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government för migrering av stordatorprogram

Många offentliga entiteter vill gärna att flytta sina stordatorprogram till en modernare flexibel plattform. Microsoft Azure Government är en fysiskt avgränsade instans av globala Microsoft Azure-plattformen – paketeras för federala, statliga och lokala myndighetssystem. Det ger säkerhet i världsklass, skydd och tjänster för efterlevnad specifikt för USA: s myndigheter och deras partner.

Azure Government tjänade tillfällig behörighet to Operate (P-ATO) för FedRAMP hög påverkan för system som behöver den här typen av miljö.

Kom igång genom att ladda ned [Microsoft Azure Government-molnet för stordatorprogram](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Nästa steg

Fråga vår [partner](partner-workloads.md) att hjälpa dig att migrera eller rehost stordatorprogram. Detaljerad vägledning om hur du väljer en partnerlösning finns i den [plattform modernisering Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) webbplats.

Se även:

- [White Paper-faktablad om stordatorprogram ämnen](mainframe-white-papers.md)
- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Felsökning](/azure/virtual-machines/troubleshooting/)
- [Avmystifiera stordatorprogram till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
