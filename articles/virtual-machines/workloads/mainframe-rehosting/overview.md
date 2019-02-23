---
title: Stordatorprogram rehosting på Azure virtual machines | Microsoft Docs
description: Ange ny värd för din stordatorprogram arbetsbelastningar som till exempel IBM Z-baserade system använder virtuella datorer (VM) i Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 067ab7538924f4aef7c48731d10fa7e68855214a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739768"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Stordatorprogram rehosting på Azure virtual machines

Migrera arbetsbelastningar från stordatorprogram miljöer till molnet kan du modernisera din infrastruktur och ofta sänka kostnaderna. Många arbetsbelastningar kan överföras till Azure med bara mindre ändringar i koden, till exempel att uppdatera namnen på databaser.

Termen *stordatorprogram* avser vanligen en stor dator, men de flesta som för närvarande har distribuerats finns IBM System Z-servrar eller IBM plug-compatible system som kör MVS, DOS, VSE, OS/390 eller z/OS.

En Azure-dator (VM) används för att isolera och hantera resurser för ett visst program på en enda instans. Stordatorer, till exempel IBM z/OS använder logiska partitioner (LPARS) för detta ändamål. En stordatorprogram kan använda en LPAR för CICS region med associerade COBOL program och en separat LPAR för IBM Db2-databas. En typisk [program på n-nivå på Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuerar virtuella Azure-datorer till ett virtuellt nätverk som kan segmenteras i undernät för varje nivå.

Virtuella Azure-datorer kan köra stordatorprogram emulering miljöer och kompilatorer som har stöd för lift and shift-scenarier. Utveckling och testning är ofta bland de första arbetsbelastningarna du migrerar från en stordatorprogram till ett Azure dev/test-miljö. Serverkomponenter som du kan emulera innehålla online transaction-processen (OLTP), batch och system för inmatning av data som visas i följande bild.

![](media/01-overview.png)

Vissa stordatorprogram arbetsbelastningar kan migreras till Azure utan större besvär, medan andra kan rehosted på Azure med hjälp av en partnerlösning. Detaljerade anvisningar om hur du väljer en partnerlösning i [Azure Stordatormigrering center](https://azure.microsoft.com/migration/mainframe/) kan hjälpa.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Konfigurera dev/test-miljö med en Micro fokus rehosting plattform

Micro fokus Enterprise Server är en av de största stordatorprogram rehosting finns tillgängliga. Du kan använda den kör z/OS-arbetsbelastningar på en billigare x86 platform på Azure.

För att komma igång, finns i följande artiklar:

- [Installera Micro fokus Enterprise Server 4.0 och företagsutvecklare 4.0 på Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Konfigurera Micro fokus CICS BankDemo för Micro fokus Enterprise Developer 4.0 i Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame på Azure

TmaxSoft OpenFrame är en populär stordatorprogram rehosting lösning som gör det enkelt att flytta dina befintliga tillgångar stordatorprogram över dem till Azure. En OpenFrame miljö på Azure är lämplig för utveckling, demonstrationer, testning eller produktionsarbetsbelastningar.

Kom igång genom att ladda ned den [installera TmaxSoft OpenFrame på Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) e-boken.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Konfigurera en miljö för utveckling/testning med IBM Z Dev/Test 12.0

IBM Z Development och Test-miljö (IBM zD & T) ställer du in en icke-produktionsmiljö på Azure som du kan använda för utveckling, testning och demonstrationer av z/OS-baserade program.

Emulering miljön i Azure kan vara värd för en mängd olika Z-instanser via programmet utvecklare kontrolleras distributioner (ADCDs). Du kan köra zD & T Personal Edition, zD & T parallella Sysplex, och zD & T Enterprise Edition på Azure och Azure Stack.

För att komma igång, finns i följande artiklar:

-   [Konfigurera IBM zD & T 12.0 på Azure](./ibm/install-ibm-z-environment.md)
-   [Konfigurera ADCD på zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>Migrera IBM DB2 pureScale till Azure

IBM DB2 pureScale miljön innehåller ett databas-kluster för Azure med hög tillgänglighet och skalbarhet på Linux-operativsystem. Även om det är inte identiska med den ursprungliga miljön, ger IBM DB2 pureScale på Linux liknande hög tillgänglighet och skalbarhetsfunktioner som IBM DB2 för z/OS som körs i en parallell Sysplex konfiguration på stordatorprogram.

Kom igång genom att se [IBM DB2 pureScale på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Överväganden

När du migrerar stordatorprogram arbetsbelastningar till Azure-infrastrukturen som en tjänst (IaaS), kan du välja från flera typer av behovsbaserade och skalbara datorresurser, inklusive Azure virtuella datorer. Azure erbjuder en mängd [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) virtuella datorer.

### <a name="high-availability-and-failover"></a>Hög tillgänglighet och redundans

Azure erbjuder åtagande-baserade servicenivåavtal (SLA), där flera 9s tillgänglighet är standard optimerad med lokal eller platsbaserade replikering av tjänster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

För Azure IaaS till exempel virtuella datorer, växling vid fel är beroende av specifika systemfunktioner, till exempel instanser för redundanskluster och [tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). När du använder Azure-plattformen som en tjänst (PaaS)-resurser, till exempel [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) och [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), plattformen hanterar redundans automatiskt.

### <a name="scalability"></a>Skalbarhet

Stordatorer skala vanligtvis upp, samtidigt som molnet miljö skala ut. Azure erbjuder en mängd [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) storlekar som uppfyller dina behov. Molnet kan också skalas upp eller ned matchar exakt användaren specifikationer. Beräkningskraft, lagring och tjänster, [skala](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) på begäran under en användningsbaserad faktureringsmodell.

### <a name="storage"></a>Storage

Du har en mängd alternativ för flexibel och skalbar lagring i molnet, och du betalar bara för vad du behöver. [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) erbjuder en mycket skalbar objektlagring för dataobjekt, en filsystemtjänst för molnet, meddelandelagring tillförlitliga och ett NoSQL-Arkiv. Hanterade och ohanterade diskar ger beständig och säker disklagring för virtuella datorer.

### <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

Underhålla en egen plats för katastrofåterställning kan det vara en dyr lösning. Azure har lätt att implementera och kostnadseffektivt alternativ för [säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och [redundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy) på lokala eller regionala nivåer eller via geo-redundans.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government för migrering av stordatorprogram

Många offentliga entiteter vill gärna att flytta sina stordatorprogram till en modernare flexibel plattform. Microsoft Azure Government är en fysiskt avgränsade instans av molntjänster baserat på den globala Microsoft Azure-plattformen men paketeras för federala, statliga och lokala myndighetssystem. Det ger säkerhet i världsklass, skydd och tjänster för efterlevnad specifikt för USA: s myndigheter och deras partner.

Azure Government tjänade tillfällig behörighet to Operate (P-ATO) för FedRAMP hög påverkan för system som behöver den här typen av miljö. 

Kom igång genom att ladda ned [Microsoft Azure Government-molnet för stordatorprogram](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Läs mer

Om du funderar på att en stordatormigrering vår omfattande [partner](partner-workloads.md) -ekosystem är tillgängliga för att hjälpa dig. För detaljerad information om hur du väljer en partnerlösning som avser den [plattform modernisering Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

Se även:

-   [Stordatormigrering](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Felsökning](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Avmystifiera stordatorprogram till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
