---
title: Skydda en flera nivåer SAP NetWeaver programdistribution med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du kan skydda SAP NetWeaver distribution av program med hjälp av Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: e2107177663163259d1f731717c4910bc986fc1f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Skydda en distribution av flera nivåer SAP NetWeaver med Site Recovery

De flesta stora och medelstora SAP-distributioner använder någon form av lösning för katastrofåterställning. Vikten av robust och kan testas katastrofåterställning har ökat när flera kärnprocesser flyttas till program som SAP. Azure Site Recovery har testats och integrera med SAP-program. Site Recovery överskrider kapaciteterna för de flesta lokala lösningar för katastrofåterställning, och på en lägre ägandekostnader (TCO) än konkurrerande lösningar.

Med Site Recovery kan du:
* **Aktivera skyddet för SAP NetWeaver och icke-NetWeaver produktions-program som körs lokalt** genom att replikera komponenter till Azure.
* **Aktivera skyddet för SAP NetWeaver och icke-NetWeaver produktionsprogram som körs på Azure** genom att replikera komponenter till en annan Azure-datacenter.
* **Förenkla molnmigreringen** med Site Recovery för att migrera din SAP-distribution till Azure.
* **Förenkla SAP projektet uppgraderingar, testning och prototyper** klona på begäran för att testa SAP-program genom att skapa en produktion.

Den här artikeln beskriver hur du skyddar SAP NetWeaver programdistributioner med [Azure Site Recovery](site-recovery-overview.md). Artikeln innehåller metodtips för att skydda en distribution med tre nivåer SAP NetWeaver i Azure genom att replikera till en annan Azure-datacentret genom att använda Site Recovery. Beskriver scenarier som stöds och konfigurationer och hur du utför redundanstestningar (haveriberedskap) och faktiska växling vid fel.

## <a name="prerequisites"></a>Förutsättningar
Se till att du vet hur du gör följande innan du börjar:

* [Replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Utforma ett nätverk för återställning](site-recovery-azure-to-azure-networking-guidance.md)
* [Gör ett redundanstest till Azure](azure-to-azure-walkthrough-test-failover.md)
* [Gör en redundansväxling till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan använda Site Recovery för att implementera en lösning för katastrofåterställning i följande scenarier:
* SAP-system körs i en Azure-datacenter som replikeras till en annan Azure-datacenter (Azure till Azure-katastrofåterställning). Mer information finns i [Azure till Azure replikeringsarkitektur](https://aka.ms/asr-a2a-architecture).
* SAP-system som körs på VMware (eller fysisk) servrar lokalt som replikerar till en disaster recovery-plats i ett Azure-datacenter (VMware till Azure-katastrofåterställning). Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [VMware till Azure replikeringsarkitektur](https://aka.ms/asr-v2a-architecture).
* SAP-system som kör Hyper-V på plats och som replikeras till en disaster recovery-plats i ett Azure-datacenter (Hyper-V-till-Azure katastrofåterställning). Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [Hyper-V-till-Azure replikeringsarkitektur](https://aka.ms/asr-h2a-architecture).

I den här artikeln använder vi en Azure-Azure-katastrofåterställning för att demonstrera SAP återställningskapacitet av Site Recovery. Eftersom Site Recovery replikering inte programspecifika, förväntas processen som beskrivs gäller även för andra scenarier.

### <a name="required-foundation-services"></a>Nödvändiga foundation-tjänster
Följande foundation-tjänster har distribuerats i det scenariot diskuterar vi i den här artikeln:
* Azure ExpressRoute eller Azure VPN-Gateway
* Minst en Active Directory-domänkontrollant och DNS-server som körs i Azure

Vi rekommenderar att du upprättar infrastrukturen innan du distribuerar Site Recovery.

## <a name="typical-sap-application-deployment"></a>Vanliga SAP-programdistribution
Stora SAP-kunder distribuera vanligtvis mellan 6 och 20 enskilda SAP-program. De flesta av dessa program är baserade på SAP NetWeaver ABAP eller Java. Många mindre och specifika icke - NetWeaver SAP fristående motorer och vanligtvis vissa icke-SAP-program, stöd för dessa core NetWeaver program.  

Det är viktigt att inventera alla SAP-program som körs i din miljö. Kontrollera sedan distributionsläget (antingen två eller tre nivåer), versioner, korrigeringsfiler, storlek, omsättning priser och beständiga diskkrav.

![Diagram över ett typiskt mönster för SAP-distribution](./media/site-recovery-sap/sap-typical-deployment.png)

Skydda SAP-databasen beständiga lagret med hjälp av inbyggda DBMS-verktyg som SQL Server AlwaysOn, Oracle Data Guard eller SAP HANA system replikering. Som SAP-databasen lagret, är inte klient-lagret skyddad av Site Recovery. Det är viktigt att beakta faktorer som påverkar detta skikt. Faktorer är DNS-spridningen fördröjning, säkerhet och fjärråtkomst till disaster recovery-datacenter.

Site Recovery är den rekommenderade lösningen på programnivå, inklusive för SAP SCS och ASCS. Andra program, till exempel NetWeaver SAP-program och icke-SAP-program, utgör en del av hela miljön för SAP-distribution. Du bör skydda dem med Site Recovery.

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer
Om du vill starta replikera alla SAP programmet virtuella datorer till Azure disaster recovery datacenter, följer du anvisningarna i [replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md).

Om du använder en statisk IP-adress kan ange du IP-adressen som du vill att den virtuella datorn ska börja. Om du vill ange IP-adress, gå till **beräknings- och nätverksinställningar inställningar** > **nätverkskort**.

![Skärmbild som visar hur du ställer in en privat IP-adress i rutan Site Recovery Network interface card](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder Sekvenseringen av olika nivåer i en flernivåapp under en växling vid fel. Sekvensering upprätthåller programkonsekvens. När du skapar en återställningsplan för ett webbprogram med flera nivåer fullständig steg som beskrivs i [skapa en återställningsplan med Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställningsplanen
Du kan behöva göra vissa åtgärder på de virtuella Azure-datorerna efter växling vid fel eller under ett redundanstest för dina program ska fungera korrekt. Du kan automatisera vissa åtgärder för postredundans. Du kan till exempel uppdatera DNS-post och ändra bindningar och anslutningar genom att lägga till motsvarande skript återställningsplanen.

### <a name="dns-update"></a>DNS-uppdatering
Om DNS är konfigurerat för dynamisk DNS-uppdatering, uppdatera virtuella datorer vanligtvis DNS med den nya IP-adressen när de startar. Om du vill lägga till ett explicit steg för att uppdatera DNS med de nya IP-adresserna för virtuella datorer, lägga till en [skript för att uppdatera IP-adressen i DNS-](https://aka.ms/asr-dns-update) som en postredundans-åtgärd på recovery planeringsgrupper.  

## <a name="example-azure-to-azure-deployment"></a>Exempel på Azure-Azure-distribution
Följande diagram visar Site Recovery Azure till Azure-katastrofåterställning:

![Diagram över ett scenario för Azure-Azure-replikering](./media/site-recovery-sap/sap-replication-scenario.png)

* Det primära datacentret är i Singapore (Azure sydöstra Asien). Disaster recovery datacenter är i Hongkong SAR (Azure Östasien). I det här scenariot tillhandahålls lokal hög tillgänglighet av två virtuella datorer som kör SQL Server AlwaysOn i synkront läge i Singapore.
* Filresursen SAP ASCS ger hög tillgänglighet för de SAP enskilda felpunkter. Filresursen ASCS kräver inte en delad klusterdisk. Program som SIOS inte behövs.
* Disaster recovery-skydd för DBMS-lagret uppnås med hjälp av asynkron replikering.
* Det här scenariot visar ”symmetrisk katastrofåterställning”. Den här termen beskriver en lösning för katastrofåterställning som är en exakt replik av produktion. SQL Server-lösning för katastrofåterställning har lokal hög tillgänglighet. Symmetrisk katastrofåterställning är inte obligatoriska för databas-lagret. Många kunder kan utnyttja flexibiliteten hos molndistributioner snabbt skapa en lokal hög tillgänglighet nod efter en disaster recovery-händelse.
* Diagrammet visar SAP NetWeaver ASCS och programnivå server som replikeras av Site Recovery.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1.  Välj Recovery Services-valvet i Azure-portalen.
2.  Välj återställningsplanen som du skapade för SAP-program.
3.  Välj **Redundanstestningen**.
4.  Om du vill börja testa redundans, Välj återställningspunkten och virtuella Azure-nätverket.
5.  När den sekundära miljön är in, utföra verifieringar.
6.  När verifieringar har slutförts kan välja om du vill rensa failover-miljön **Rensa redundanstestet**.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1.  Välj Recovery Services-valvet i Azure-portalen.
2.  Välj återställningsplanen som du skapade för SAP-program.
3.  Välj **redundans**.
4.  Välj återställningspunkt för att starta processen för växling vid fel.

Mer information finns i [redundans i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du skapar en lösning för katastrofåterställning för SAP NetWeaver distributioner med Site Recovery finns i nedladdningsbara faktabladet [SAP NetWeaver: skapa en lösning för katastrofåterställning med Azure Site Recovery](http://aka.ms/asr-sap). Vitboken beskriver rekommendationer för olika arkitekturer för SAP, visar vilka program som stöds och VM-typer för SAP på Azure och beskriver tester alternativ för din lösning för katastrofåterställning.
* Lär dig mer om [replikerar andra arbetsbelastningar](site-recovery-workload.md) genom att använda Site Recovery.
