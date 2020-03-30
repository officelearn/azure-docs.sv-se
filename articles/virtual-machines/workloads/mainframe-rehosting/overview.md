---
title: Stordatorsåterhosting på virtuella Azure-datorer
description: Rehost din stordator arbetsbelastningar såsom IBM Z-baserade system med hjälp av virtuella datorer (VMs) på Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289806"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Stordatorsåterhosting på virtuella Azure-datorer

Genom att migrera arbetsbelastningar från stordatormiljöer till molnet kan du modernisera infrastrukturen och ofta spara på kostnaderna. Många arbetsbelastningar kan överföras till Azure med bara mindre kodändringar, till exempel uppdatering av namn på databaser.

Generellt sett betyder termen *stordator* ett stort datorsystem. De allra flesta som för närvarande används är IBM System Z-servrar eller IBM plug-kompatibla system som kör MVS, DOS, VSE, OS/390 eller z/OS.

En virtuell Azure-dator (VM) används för att isolera och hantera resurserna för ett visst program på en enda instans. Stordatorer som IBM z/OS använder logiska partitioner (LPARS) för detta ändamål. En stordator kan använda en LPAR för en CICS-region med associerade COBOL-program och en separat LPAR för IBM Db2-databas. Ett typiskt [n-nivåprogram på Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuerar virtuella Azure-datorer till ett virtuellt nätverk som kan segmenteras till undernät för varje nivå.

Virtuella Azure-datorer kan köra stordatoremuleringsmiljöer och kompilatorer som stöder scenarier för lyft och skift. Utveckling och testning är ofta bland de första arbetsbelastningarna som migrerar från en stordator till en Azure-utvecklings-/testmiljö. Vanliga serverkomponenter som du kan emulera inkluderar onlinetransaktionsprocessen (OLTP), batch och datainmatningssystem som följande bild visar.

![Emuleringsmiljöer på Azure gör att du kan köra z/OS-baserade system.](media/01-overview.png)

Vissa stordatorarbetsbelastningar kan migreras till Azure med relativ lätthet, medan andra kan vara värd på Azure med hjälp av en partnerlösning. Detaljerad vägledning om hur du väljer en partnerlösning kan [Azure Storframe Migration Center](https://azure.microsoft.com/migration/mainframe/) hjälpa dig.

## <a name="mainframe-migration"></a>Stordatormigrering

Rehost, bygga om, ersätta eller gå i pension? IaaS eller PaaS? Information om hur du tar reda på rätt migreringsstrategi för stordatorprogrammet finns i [migratorns migreringsguide för stordator](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) i Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>Micro Focus rehosting plattform

Micro Focus Enterprise Server är en av de största stordatorrehosting plattformar tillgängliga. Du kan använda den köra dina z /OS-arbetsbelastningar på en billigare x86-plattform på Azure.

Så här kommer du igång:

- [Installera Enterprise Server och Enterprise Developer på Azure](./microfocus/set-up-micro-focus-azure.md)
- [Konfigurera CICS BankDemo för Enterprise Developer på Azure](./microfocus/demo.md)
- [Köra Enterprise Server i en Docker-behållare på Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame på Azure

TmaxSoft OpenFrame är en populär stordator rehosting lösning som används i lift-and-shift scenarier. En OpenFrame-miljö på Azure är lämplig för utvecklings-, demos-, testnings- eller produktionsarbetsbelastningar.

Så här kommer du igång:

- [Komma igång med TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Ladda ner e-boken](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z Development and Test Environment (IBM zD&T) konfigurerar en icke-produktionsmiljö på Azure som du kan använda för utveckling, testning och demonstrationer av z/OS-baserade program.

Emuleringsmiljön på Azure kan vara värd för olika typer av Z-instanser via ADCD(Application Developers Controlled Distributions). Du kan köra zD&T Personal Edition, zD&T Parallel Sysplex och zD&T Enterprise Edition på Azure och Azure Stack.

Så här kommer du igång:

- [Konfigurera IBM zD&T 12.0 på Azure](./ibm/install-ibm-z-environment.md)
- [Konfigurera ADCD på zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale på Azure

IBM DB2 pureScale-miljön tillhandahåller ett databaskluster för Azure. Det är inte identiskt med den ursprungliga miljön, men det ger liknande tillgänglighet och skala som IBM DB2 för z / OS som körs i en parallell Sysplex setup.

Information om hur du kommer igång finns i [IBM DB2 pureScale på Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Överväganden

När du migrerar stordatorarbetsbelastningar till Azure-infrastruktur som en tjänst (IaaS) kan du välja mellan flera typer av skalbara datorresurser på begäran, inklusive virtuella Azure-datorer. Azure erbjuder en rad [virtuella Linux-](/azure/virtual-machines/linux/overview) och [Windows-datorer.](/azure/virtual-machines/windows/overview)

### <a name="compute"></a>Compute

Azure-beräkningskraft kan jämföras positivt med stordatorns kapacitet. Om du funderar på att flytta en stordatorarbetsbelastning till Azure kan du jämföra stordatormåttet på en miljon instruktioner per sekund (MIPS) med virtuella processorer. 

Lär dig hur du [flyttar stordatorberäkningen till Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Hög tillgänglighet och redundans

Azure erbjuder åtagandebaserade servicenivåavtal (SLA). Tillgänglighet för flera nior är standard och SLA-system kan optimeras med lokal eller geobaserad replikering av tjänster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

Med Azure IaaS, till exempel en virtuell dator, ger specifika systemfunktioner stöd för redundans, till exempel växlingsklustringsinstanser och tillgänglighetsuppsättningar. När du använder Azure-plattformen som en tjänst (PaaS) resurser hanterar plattformen redundans automatiskt. Exempel är [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) och Azure [Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Skalbarhet

Stordatorer skalas vanligtvis upp, medan molnmiljöer skalas ut. Azure erbjuder en rad [Linux-](/azure/virtual-machines/linux/sizes) och [Windows-storlekar](/azure/virtual-machines/windows/sizes) som uppfyller dina behov. Molnet skalas också upp eller ned för att matcha exakta användarspecifikationer. Beräkningskraft, lagring och tjänster [skalas](/azure/architecture/best-practices/auto-scaling) på begäran under en användningsbaserad faktureringsmodell.

### <a name="storage"></a>Lagring

I molnet har du en rad flexibla, skalbara lagringsalternativ och du betalar bara för det du behöver. [Azure Storage](/azure/storage/common/storage-introduction) erbjuder ett enormt skalbart objektlager för dataobjekt, en filsystemtjänst för molnet, ett tillförlitligt meddelandearkiv och ett NoSQL-arkiv. För virtuella datorer tillhandahåller hanterade och ohanterade diskar beständig, säker disklagring.

Lär dig hur du [flyttar stordatorlagring till Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

Att underhålla din egen katastrofåterställning webbplats kan vara ett dyrt förslag. Azure har enkla att implementera och kostnadseffektiva alternativ för [säkerhetskopiering,](/azure/backup/backup-introduction-to-azure-backup) [återställning](/azure/site-recovery/site-recovery-overview)och [redundans](/azure/storage/common/storage-redundancy) på lokal eller regional nivå, eller via geo-redundans.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government för stordatormigreringar

Många offentliga enheter skulle älska att flytta sina stordator applikationer till en modernare, mer flexibel plattform. Microsoft Azure Government är en fysiskt separerad instans av den globala Microsoft Azure-plattformen – paketerad för federala, statliga och lokala myndighetssystem. Det ger säkerhets-, skydds- och efterlevnadstjänster i världsklass specifikt för amerikanska myndigheter och deras partner.

Azure Government fick en provisorisk myndighet att driva (P-ATO) för FedRAMP High Impact för system som behöver den här typen av miljö.

För att komma igång, ladda ner [Microsoft Azure Government moln för stordatorprogram](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Nästa steg

Be våra [partners](partner-workloads.md) att hjälpa dig att migrera eller rehost dina stordatorprogram. 

Se även:

- [Vitböcker om stordatorer](mainframe-white-papers.md)
- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Troubleshooting](/azure/virtual-machines/troubleshooting/) (Felsökning)
- [Avmystifiera stordator till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
