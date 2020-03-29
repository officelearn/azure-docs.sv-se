---
title: Flytta stordatorberäkning till virtuella Azure-datorer
description: Azure-beräkningsresurser jämför positivt med stordatorkapacitet så att du kan migrera och modernisera IBM z14-program.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288939"
---
# <a name="move-mainframe-compute-to-azure"></a>Flytta stordatorberäkning till Azure

Stordatorer har ett rykte om hög tillförlitlighet och tillgänglighet och fortsätter att vara den betrodda ryggraden i många företag. De är ofta tros ha nästan obegränsad skalbarhet och datorkraft också. Vissa företag har dock vuxit ur kapaciteten hos de största tillgängliga stordatorerna. Om detta låter som du erbjuder Azure flexibilitet, räckvidd och infrastrukturbesparingar.

Om du vill köra stordatorarbetsbelastningar på Microsoft Azure måste du veta hur stordatorns beräkningsfunktioner står sig i jämförelse med Azure. Baserat på en IBM z14 stordator (den senaste modellen i den här skriften), berättar den här artikeln hur du får jämförbara resultat på Azure.

För att komma igång, överväga miljöer sida vid sida. Följande bild jämför en stordatormiljö för att köra program med en Azure-värdmiljö.

![Azure-tjänster och emuleringsmiljöer erbjuder jämförbar support och effektiviserar migreringen](media/mainframe-compute-azure.png)

Kraften i stordatorer används ofta för online-transaktionsbearbetning (OLTP) system som hanterar miljontals uppdateringar för tusentals användare. Dessa program använder ofta programvara för transaktionsbearbetning, skärmhantering och formulärpost. De kan använda ett CICS(Customer Information Control System), INFORMATION Management System (IMS) eller Transaction Interface Package (TIP).

Som bilden visar kan en TPM-emulator på Azure hantera CICS- och IMS-arbetsbelastningar. En batchsystememulator på Azure utför rollen som JCL (Job Control Language). Stordatordata migreras till Azure-databaser, till exempel Azure SQL Database. Azure-tjänster eller annan programvara som finns i Virtuella Azure-datorer kan användas för systemhantering.

## <a name="mainframe-compute-at-a-glance"></a>Stordator beräkning i korthet

I z14 stordatorn, processorer är ordnade i upp till fyra *lådor*. En *låda* är helt enkelt ett kluster av processorer och chipset. Varje låda kan ha sex aktiva cp-chips (Central Processor) och varje CP har 10 SC-chips (System Controller). I Intel x86-terminologin finns det sex uttag per låda, 10 kärnor per uttag och fyra lådor. Denna arkitektur ger grov motsvarighet till 24 uttag och 240 kärnor, maximalt, för en z14.

Den snabba z14 CP har en 5,2 GHz klockfrekvens. Vanligtvis levereras en z14 med alla CD-skivor i lådan. De aktiveras efter behov. En kund debiteras ofta för minst fyra timmars beräkningstid per månad trots faktisk användning.

En stordatorprocessor kan konfigureras som en av följande typer:

- GP-processor (General Purpose)
- System z Integrerad informationsprocessor (zIIP)
- Integrerad facility för Linux-processor (IFL)
- Systemassistansprocessor (SAP)
- Integrerad kopplingsanordning (ICF) processor

## <a name="scaling-mainframe-compute-up-and-out"></a>Skala stordatorberäkning upp och ut

IBM stordatorer erbjuder möjligheten att skala upp till 240 kärnor (den nuvarande z14 storlek för ett enda system). Dessutom kan IBM stordatorer skala ut genom en funktion som kallas kopplingsanordningen (CF). CF tillåter flera stordatorsystem att komma åt samma data samtidigt. Med hjälp av CF grupperar stordatorn Parallel Sysplex-teknik stordatorprocessorer i kluster. När den här guiden skrevs stödde funktionen Parallel Sysplex 32 grupperingar med 64 processorer vardera. Upp till 2 048 processorer kan grupperas på det här sättet för att skala ut beräkningskapaciteten.

En CF gör det möjligt för beräkningskluster att dela data med direkt åtkomst. Den används för att låsa information, cacheinformation och listan över delade dataresurser. En parallell Sysplex med en eller flera CFs kan ses som ett "delat allt" skalningsbara beräkningskluster. Mer information om dessa funktioner finns i [Parallel Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) på IBM:s webbplats.

Program kan använda dessa funktioner för att ge både skalningsprestanda och hög tillgänglighet. För information om hur CICS kan använda Parallel Sysplex med CF, ladda ner [IBM CICS och kopplingsanordningen: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Azure-beräkning i korthet

Vissa människor tror felaktigt att Intel-baserade servrar inte är lika kraftfulla som stordatorer. De nya kärntäta, Intel-baserade systemen har dock lika mycket beräkningskapacitet som stordatorer. I det här avsnittet beskrivs Azure Infrastructure-as-a-service (IaaS) alternativ för beräkning och lagring. Azure tillhandahåller paaS-alternativ (Platform-as-a-service) men den här artikeln fokuserar på IaaS-alternativen som ger jämförbar stordatorkapacitet.

Virtuella Azure-datorer tillhandahåller beräkningskraft i en rad storlekar och typer. I Azure motsvarar en virtuell PROCESSOR (vCPU) ungefär en kärna på en stordator.

För närvarande ger utbudet av Azure Virtual Machine storlekar från 1 till 128 virtuella processorer. Virtuella datortyper (VM) är optimerade för särskilda arbetsbelastningar. I följande lista visas till exempel de virtuella typerna (aktuella per den här skrivningen) och deras rekommenderade användningsområden:

| Storlek     | Typ och beskrivning                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-serien | Allmänt syfte med 64 vCPU och upp till 3,5 GHz klockfrekvens                           |
| E-serien | Minne optimerat med upp till 64 virtuella processorer                                                 |
| F-serien | Beräkna optimerad med upp till 64 vCPUs och 3,7 GHz klockfrekvens                       |
| H-serien | Optimerad för högpresterande datorprogram (HPC)                          |
| L-serien | Lagring optimerad för program med hög genomströmning som backas upp av databaser som NoSQL |
| M-serien | Största beräknings- och minnesoptimerade virtuella datorer med upp till 128 virtuella processorer                        |

Mer information om tillgängliga virtuella datorer finns i [Virtual Machine-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

En z14 stordator kan ha upp till 240 kärnor. Z14 stordatorer använder dock nästan aldrig alla kärnor för ett enda program eller en enda arbetsbelastning. I stället avskilrar en stordator arbetsbelastningar till logiska partitioner (LPARs) och LPARs har betyg–MIPS (Miljontals instruktioner per sekund) eller MSU (Million Service Unit). När du bestämmer den jämförbara vm-storleken som krävs för att köra en stordatorarbetsbelastning på Azure, faktor i MIPS (eller MSU) betyg.

Följande är allmänna uppskattningar:

-   150 MIPS per vCPU

-   1 000 MIPS per processor

För att fastställa rätt VM-storlek för en viss arbetsbelastning i en LPAR optimerar du först den virtuella datorn för arbetsbelastningen. Bestäm sedan antalet virtuella processorer som behövs. En konservativ uppskattning är 150 MIPS per vCPU. Baserat på den här uppskattningen, till exempel, en F-serien VM med 16 vCPUs kan enkelt stödja en IBM Db2 arbetsbelastning som kommer från en LPAR med 2.400 MIPS.

## <a name="azure-compute-scale-up"></a>Azure-beräkningsuppskalning

Virtuella datorer i M-serien kan skala upp till 128 virtuella processorer (när den här artikeln skrevs). Med hjälp av den konservativa uppskattningen av 150 MIPS per vCPU motsvarar M-serien VM cirka 19 000 MIPS. Den allmänna regeln för att uppskatta MIPS för en stordator är 1 000 MIPS per processor. En z14 stordator kan ha upp till 24 processorer och ge ca 24.000 MIPS för en enda stordator system.

Den största enskilda z14 stordatorn har cirka 5 000 MIPS mer än den största virtuella datorn som är tillgänglig i Azure. Ändå är det viktigt att jämföra hur arbetsbelastningar distribueras. Om ett stordatorsystem har både ett program och en relationsdatabas distribueras de vanligtvis på samma fysiska stordator – var och en i sin egen LPAR. Samma lösning på Azure distribueras ofta med en virtuell dator för programmet och en separat, lämplig storlek VIRTUELL för databasen.

Om till exempel ett M64 vCPU-system stöder programmet och en M96-vCPU används för databasen, behövs ungefär 150 vCPU:er – eller cirka 24 000 MIPS som följande bild visar.

![Jämföra arbetsbelastningsdistributioner av 24 000 MIPS](media/mainframe-compute-mips.png)

Metoden är att migrera LPARs till enskilda virtuella datorer. Sedan skalas Azure enkelt upp till den storlek som behövs för de flesta program som distribueras på ett enda stordatorsystem.

## <a name="azure-compute-scale-out"></a>Azure-beräkningsskalning

En av fördelarna med en Azure-baserad lösning är möjligheten att skala ut. Skalning gör nästan obegränsad beräkningskapacitet tillgänglig för ett program. Azure stöder flera metoder för att skala ut beräkningskraft:

- **Belastningsutjämning över ett kluster.** I det här fallet kan ett program använda en [belastningsutjämnare](/azure/load-balancer/load-balancer-overview) eller resurshanterare för att sprida ut arbetsbelastningen mellan flera virtuella datorer i ett kluster. Om mer beräkningskapacitet behövs läggs ytterligare virtuella datorer till i klustret.

- **Skala uppsättningar för virtuella datorer.** I det här burst-scenariot kan ett program skalas till ytterligare [beräkningsresurser](/azure/virtual-machine-scale-sets/overview) baserat på vm-användning. När efterfrågan sjunker kan antalet virtuella datorer i en skalningsuppsättning också minska, vilket säkerställer en effektiv användning av beräkningskraft.

- **PaaS skalning.** Azure PaaS erbjuder skalningsberäkningsresurser. [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) allokerar till exempel beräkningsresurser för att möta ökningar av mängden begäranden.

- **Kubernetes kluster.** Program på Azure kan använda [Kubernetes-kluster](/azure/aks/concepts-clusters-workloads) för beräkningstjänster för angivna resurser. Azure Kubernetes Service (AKS) är en hanterad tjänst som dirigerar Kubernetes-noder, pooler och kluster på Azure.

För att välja rätt metod för att skala ut beräkningsresurser är det viktigt att förstå hur Azure och stordatorer skiljer sig åt. Nyckeln är hur eller om data delas av beräkningsresurser. I Azure delas data (som standard) vanligtvis inte av flera virtuella datorer. Om datadelning krävs av flera virtuella datorer i ett utskalningsberäkningskluster måste delade data finnas i en resurs som stöder den här funktionen. På Azure innebär datadelning lagring som följande avsnitt diskuterar.

## <a name="azure-compute-optimization"></a>Azure-beräkningsoptimering

Du kan optimera varje nivå av bearbetning i en Azure-arkitektur. Använd den lämpligaste typen av virtuella datorer och funktioner för varje miljö. Följande bild visar ett potentiellt mönster för distribution av virtuella datorer i Azure för att stödja ett CICS-program som använder Db2. På den primära platsen distribueras virtuella datorer för produktion, förproduktion och testning med hög tillgänglighet. Den sekundära platsen är för säkerhetskopiering och haveriberedskap.

Varje nivå kan också tillhandahålla lämpliga tjänster för haveriberedskap. Till exempel kan produktions- och databas-virtuella datorer kräva en varm eller varm återställning, medan utvecklings- och testnings virtuella datorer stöder en kall återställning.

![Högtillgänge med distribution som stöder haveriberedskap](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Nästa steg

- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Stordatorsåterhosting på virtuella Azure-datorer](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Flytta stordatorlagring till Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM-resurser

- [Parallell Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS och kopplingsmöjligheten: Bortom grunderna](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Skapa nödvändiga användare för en Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Skapa förekomst, kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale klustrade databaslösning](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government-molnet för stordatorprogram](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft och FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Fler migreringsresurser

- [Azure Virtual Data Center Lyft och Skift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
