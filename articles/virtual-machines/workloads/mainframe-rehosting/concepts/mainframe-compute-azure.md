---
title: Flytta stordatorprogram beräkning till Azure Virtual Machines
description: Azure compute resurser jämfört med positivt stordatorprogram kapacitet så att du kan migrera och modernisera IBM z14 program.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896523"
---
# <a name="move-mainframe-compute-to-azure"></a>Flytta stordatorprogram beräkning till Azure

Stordatorer har kända för hög tillförlitlighet och tillgänglighet och fortsätter att vara betrodda ryggraden för många företag. De är ofta trodde att ha nästan obegränsade skalbarhet och även beräkningskraft. Vissa företag har dock outgrown möjligheterna för de största tillgängliga stordatorer. Om detta låter som du erbjuder Azure rörlighet, räckvidd och infrastruktur besparingar.

För att köra stordatorprogram arbetsbelastningar på Microsoft Azure, som du behöver veta hur dina stordatorprogram compute funktioner jämfört med Azure. Baserat på ett IBM z14 (senaste modellen när detta skrivs) kan visar den här artikeln hur du få jämförbara resultat på Azure.

Överväg att miljöer sida vid sida för att komma igång. Följande bild jämför en stordatorprogram miljö för att köra program till en Azure värdmiljö.

![Azure-tjänster och emulering miljöer erbjudandet jämförbara stöd och effektiviserar migreringen](media/mainframe-compute-azure.png)

Kraften hos stordatorer används ofta för online transaktionsbearbetning (OLTP) system som hanterar miljontals uppdateringar för tusentals användare. Dessa program använder ofta programvara för bearbetning av transaktioner, hantering av skärmen och formuläret post. De kan använda en kund Information kontroll System (CICS), Information Management System (IMS) eller Transaction gränssnittet paketet (TIP).

Som bilden visar kan en TPM-emulator på Azure hantera CICS och IMS arbetsbelastningar. En batch system emulator på Azure utför rollen av jobbet Control Language (JCL). Stordatorprogram data migreras till Azure-databaser, till exempel Azure SQL Database. Azure-tjänster eller annan programvara som finns i Azure virtuella datorer kan användas för administration av system.

## <a name="mainframe-compute-at-a-glance"></a>Stordatorprogram beräkning i korthet

I z14-stordatorprogram processorer är ordnade i upp till fyra *aktivitetslådorna*. En *lådan* är bara ett kluster av processorer och kretsuppsättningar. Varje lådan kan ha sex active Huvudprocessor (CP)-kretsar, och varje CP har 10 system controller (SC) kretsar. Det finns sex sockets per lådan, 10 kärnor per socket och fyra aktivitetslådorna i Intel x86 terminologi. Den här arkitekturen ger grov motsvarigheten 24 sockets och 240 kärnor, maximal för en z14.

Ett snabbt z14 CP har en 5.2 GHz klockfrekvens. Vanligtvis levereras en z14 med alla CPs i rutan. De visas efter behov. En kund är ofta debiteras för minst fyra timmar beräkningstid per månad trots faktisk användning.

En stordatorprogram processor kan konfigureras som en av följande typer:

- Allmänt syfte (GP)-processor
- System z integrerad Information-Processor (zIIP)
- Integrerad anläggning för Linux (IFL)-processor
- System Assist-Processor (SAP)
- Integrerad koppling anläggning för-processor

## <a name="scaling-mainframe-compute-up-and-out"></a>Stordatorprogram skalningsberäkning uppåt och utåt

IBM-stordatorer erbjuder möjligheten att skala upp till 240 kärnor (den aktuella z14 storleken för ett enda system). Dessutom kan IBM-stordatorer skala ut via en funktion som kallas koppling anläggning (CF). CF kan flera stordatorprogram system för att komma åt samma data samtidigt. Med hjälp av CF, stordatorprogram parallella Sysplex teknik grupper stordatorprogram processorerna i kluster. När den här guiden skrevs stöds funktionen parallella Sysplex 32 grupperingar av 64 processorer. Upp till 2 048 processorer kan grupperas i det här sättet att skala ut beräkningskapacitet.

En CF kan beräkningskluster att dela data med direktåtkomst. Den används för att låsa information, cacheinformationen och listan över delade dataresurser. En parallell Sysplex med hjälp av en eller flera CFs kan betraktas som en ”delad allt” skalbar beräkningskluster. Mer information om dessa funktioner finns i [parallella Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) på IBM-webbplatsen.

Program kan använda dessa funktioner för att både skalbara prestanda och hög tillgänglighet. Information om hur CICS kan använda parallella Sysplex med CF hämta den [IBM CICS och funktionen koppling: Bortom grunderna](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Azure-beräkning i korthet

Vissa av misstag tror att Intel-baserade servrar inte är lika kraftfulla som stordatorer. De nya core-kompakta, Intel-baserade system har dock som mycket beräkningskapacitet som stordatorer. Det här avsnittet beskrivs de Azure infrastructure as a service (IaaS)-alternativ för databehandling och lagring. Azure ger platform as a service (PaaS) samt alternativ, men den här artikeln fokuserar på de IaaS-alternativ som ger jämförbara stordatorprogram kapacitet.

Azure virtuella datorer ger beräkningskraft på många olika storlekar och -typer. I Azure motsvarar en virtuell processor (vCPU) ungefär en kärna på en stordatorprogram.

Storlekar för intervallet av Azure virtuella datorer ger för närvarande från 1 till 128 virtuella processorer. Typer av virtuella datorer (VM) är optimerade för specifika arbetsbelastningar. I följande lista visas till exempel VM-typer (aktuella när detta skrivs) och hur de rekommendera används:

| Storlek     | Typ och beskrivning                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-serien | Generell användning med 64 virtuella processorer och upp till 3,5 GHz klockfrekvens                           |
| E-serien | Minnesoptimerade med upp till 64 virtuella processorer                                                 |
| F-serien | Compute optimerade med upp till 64 virtuella processorer och 3..7 GHz klockfrekvens                       |
| H-serien | Optimerad för databehandling med höga prestanda (HPC) program                          |
| L-serien | Lagring för program med högt dataflöde backas upp av databaser, till exempel NoSQL |
| M-serien | Största beräknings- och minnesresurser optimerad virtuella datorer med upp till 128 virtuella processorer                        |

Mer information om tillgängliga virtuella datorer finns i [serie för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

En z14 stordatorprogram kan ha upp till 240 kärnor. Dock använda z14 stordatorer nästan aldrig alla kärnor för ett enda program eller en arbetsbelastning. I stället en stordatorprogram segregerar arbetsbelastningar i logiska partitioner (LPARs) och LPARs har klassificeringarna – MIPS (miljoner instruktioner Per sekund) eller MSU (miljoner Service Unit). När du fastställer jämförbara VM-storleken som behövs för att köra en arbetsbelastning stordatorprogram på Azure, faktor för MIPS (eller MSU)-klassificeringen.

Följande är allmänna beräkningar:

-   150 MIPS per vCPU

-   1 000 MIPS per processor

Fastställ rätt VM-storleken för en viss arbetsbelastning i en LPAR genom att optimera den virtuella datorn för arbetsbelastningen först. Sedan fastställa hur många virtuella processorer som behövs. En konservativ uppskattning är 150 MIPS per virtuell processor. Baserat på den här beräkningen, till exempel kan en virtuell F-serien med 16 vcpu: er enkelt stöd för en IBM Db2-arbetsbelastning som kommer från en LPAR med 2 400 MIPS.

## <a name="azure-compute-scale-up"></a>Azure-beräkning, skala upp

Virtuella datorer i M-serien kan skala upp till 128 virtuella processorer (vid den tidpunkt som den här artikeln skrevs). Med hjälp av konservativ uppskattning av 150 MIPS per vCPU, M-serien VM som motsvarar ungefär 19,000 MIPS. Regel för att uppskatta MIPS för en stordatorprogram är 1 000 MIPS per processor. En z14 stordatorprogram kan ha upp till 24 processorer och ger cirka 24 000 MIPS för ett enda stordatorprogram system.

Den största enda z14 stordatorprogram har cirka 5 000 MIPS mer än den största virtuella datorn finns i Azure. Ännu är det viktigt att jämföra hur arbetsbelastningar distribueras. Om ett stordatorprogram system har både ett program och en relationsdatabas, distribueras de vanligtvis på samma fysiska stordatorprogram – var och en i sin egen LPAR. Samma lösning på Azure med ofta hjälp av en virtuell dator för programmet och en separat, lämpligt storlekar virtuell dator för databasen.

Till exempel om programmet har stöd för ett system för M64 vCPU och en M96 vCPU används för databasen, ungefär 150 vcpu: er behövs – eller cirka 24 000 MIPS så som visas i följande bild.

![Jämföra arbetsbelastningen distributioner av 24 000 MIPS](media/mainframe-compute-mips.png)

Metoden är att migrera LPARs till enskilda virtuella datorer. Sedan Azure enkelt kan skalas till den storlek som krävs för de flesta program som distribueras på ett enda stordatorprogram system.

## <a name="azure-compute-scale-out"></a>Azure compute-utskalning

En av fördelarna med en Azure-baserade lösning är möjligheten att skala ut. Skalning gör nästan obegränsade beräkningskapacitet tillgängliga för ett program. Azure har stöd för flera metoder för att skala ut beräkningskraften:

- **Belastningsutjämning över ett kluster.** I det här scenariot, ett program kan använda en [belastningsutjämnare](/azure/load-balancer/load-balancer-overview) eller resource manager för att sprida ut arbetsbelastningen mellan flera virtuella datorer i ett kluster. Om mer databearbetning kapacitet, läggs ytterligare virtuella datorer i klustret.

- **VM-skalningsuppsättningar.** I det här burst-scenariot, ett program kan skalas till ytterligare [beräkningsresurser](/azure/virtual-machine-scale-sets/overview) baserat på VM-användning. När efterfrågan är, kan hur många virtuella datorer i en skalningsuppsättning också gå ned, att säkerställa effektiv användning av beräkningskraft.

- **PaaS skalning.** Azure PaaS-erbjudanden skala beräkningsresurser. Till exempel [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) allokerar beräkningsresurser för att möta volymen av begäranden.

- **Kubernetes-kluster.** Program i Azure kan använda [Kubernetes-kluster](/azure/aks/concepts-clusters-workloads) för Beräkningstjänster för angivna resurserna. Azure Kubernetes Service (AKS) är en hanterad tjänst som samordnar Kubernetes-noder, pooler och kluster i Azure.

Det är viktigt att förstå hur Azure och stordatorer skiljer sig åt för att välja rätt metod för att skala ut beräkningsresurserna. Nyckeln är hur – eller om – data som delas av beräkningsresurser. I Azure delas data (som standard) inte normalt av flera virtuella datorer. Om delning av data krävs av flera virtuella datorer i en skalbar beräkningskluster, delade data måste finnas i en resurs som har stöd för den här funktionen. På Azure innebär delning av data lagring som följer en beskrivning.

## <a name="azure-compute-optimization"></a>Optimering av Azure-beräkning

Du kan optimera varje nivå av bearbetning i en Azure-arkitektur. Använd den lämpligaste typen av virtuella datorer och funktioner för varje miljö. Följande bild visar en potentiell mönster för att distribuera virtuella datorer som stöder ett CICS program som använder Db2. I den primära platsen, produktion, distribueras förproduktionsmiljön och testa virtuella datorer med hög tillgänglighet. Den sekundära platsen är för säkerhetskopiering och haveriberedskap.

Varje nivå kan också ge lämpliga disaster recovery services. Till exempel kan produktions- och virtuella kräva en frekventa och den varma återställning, även om utveckling och testning virtuella datorer stöder en kall återställning.

![Distribution med hög tillgänglighet som har stöd för katastrofåterställning](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Nästa steg

- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Stordatorprogram rehosting på Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Flytta lagringen för stordatorprogram till Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM-resurser

- [Parallell Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS och funktionen koppling: Bortom grunderna](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Skapa nödvändiga användare för en Db2-pureScale funktionsinstallation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - skapa instans-kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale klustrade databaslösning](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government-molnet för stordatorprogram](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft och FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Fler migreringsresurser

- [Plattformen modernisering Alliance: IBM Db2 i Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Virtual Datacenter Lift and Shift-Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
