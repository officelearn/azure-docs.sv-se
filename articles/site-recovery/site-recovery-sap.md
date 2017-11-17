---
title: "Skydda en flera nivåer SAP NetWeaver programdistribution med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du kan skydda SAP NetWeaver programdistributioner med hjälp av Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 5a47acab598e113ef7ed968dd3a6429ac3bc1ec3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Skydda en flera nivåer SAP NetWeaver programdistribution med hjälp av Azure Site Recovery

De flesta distributioner för små och medelstora SAP har någon form av Disaster Recovery-lösning.  Vikten av robust och kan testas Disaster Recovery-lösningar har ökat när flera kärnprocesser flyttas till program, till exempel SAP.  Azure Site Recovery har testats och är integrerad med SAP-program och överskrider kapaciteterna för de flesta lokala Disaster Recovery-lösningar, på en lägre ägandekostnader (TCO) än konkurrerande lösningar.
Med Azure Site Recovery kan du:
* Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som körs lokalt genom att replikera komponenter till Azure.
* Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som kör Azure, genom att replikera komponenter till ett annat Azure-datacenter.
* Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
* Förenkla SAP-projektuppgradering, testning och prototyper genom att skapa en produktionsklon på begäran för att testa SAP-program.

Den här artikeln beskriver hur du kan skydda SAP NetWeaver programdistributioner med [Azure Site Recovery](site-recovery-overview.md). Den här artikeln beskrivs bästa praxis för att skydda en distribution med tre nivåer SAP NetWeaver i Azure genom att replikera till en annan Azure-datacenter med hjälp av Azure Site Recovery scenarier som stöds och konfigurationer och hur du utför redundans, både test växling vid fel (haveriberedskap) och faktiska växling vid fel.


## <a name="prerequisites"></a>Krav
Innan du börjar bör du kontrollera att du känna till följande:

1. [Replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md)
2. Så här [utforma ett nätverk för återställning](site-recovery-azure-to-azure-networking-guidance.md)
3. [Gör ett redundanstest till Azure](azure-to-azure-walkthrough-test-failover.md)
4. [Gör en redundansväxling till Azure](site-recovery-failover.md)
5. Så här [replikera en domänkontrollant](site-recovery-active-directory.md)
6. Så här [replikera SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenarier som stöds
Med Azure Site Recovery kan du implementera en lösning för katastrofåterställning i följande scenarier:
* SAP-system som körs i en Azure-datacenter replikeras till en annan Azure-datacenter (Azure till Azure DR) som konstruerad [här](https://aka.ms/asr-a2a-architecture).
* SAP-system som kör VMWare (eller fysisk) servrar på plats replikeras till en DR-plats i ett Azure-datacenter (VMware till Azure DR) som kräver vissa ytterligare komponenter som konstruerad [här](https://aka.ms/asr-v2a-architecture).
* SAP-system som kör Hyper-V lokalt replikeras till en DR-plats i ett Azure-datacenter (Hyper-V-till-Azure DR) som kräver vissa ytterligare komponenter som konstruerad [här](https://aka.ms/asr-h2a-architecture).

Det här dokumentet använder det första fallet - Azure till Azure DR - för att demonstrera funktioner för Azure Site Recovery SAP katastrofåterställning. Azure Site Recovery replikering är oberoende av programmet, förväntas processen som beskrivs håller för andra scenarier samt.

### <a name="required-foundation-services"></a>Nödvändiga foundation-tjänster
Den här dokumentationen scenariot alla har distribuerats med följande foundation-tjänster som distribueras:
* ExpressRoute eller plats-till-plats virtuellt privat nätverk (VPN)
* Minst en Active Directory-domänkontrollant och DNS-server som körs i Azure

Du rekommenderas att infrastrukturen ovan upprättas innan du distribuerar Azure Site Recovery.


## <a name="typical-sap-application-deployment"></a>Vanliga SAP-programdistribution
Stora SAP-kunder distribuera vanligtvis mellan 6 och 20 enskilda SAP-program.  De flesta av dessa program är baserade på motorer SAP NetWeaver ABAP eller Java.  Stöd för programmen core NetWeaver är många mindre specifika icke - NetWeaver SAP fristående motorer och vanligtvis vissa icke-SAP-program.  

Det är viktigt att alla de SAP-program som körs i ett liggande och för att fastställa distributionsläget (nivå 2 eller 3-skikts), versioner, korrigeringsfiler, storlek, omsättningsuppdateringar frekvens och beständiga diskkrav inventering.

![Mönster för distribution](./media/site-recovery-sap/sap-typical-deployment.png)

SAP-databasen beständiga lagret borde vara skyddad via native DBMS-verktyg som SQL Server AlwaysOn, Oracle DataGuard eller HANA System replikering. Klienten lagret också skyddas av Azure Site Recovery, men det är viktigt att tänka på ämnen som påverkar detta skikt som DNS-spridningen fördröjning, säkerhet och fjärråtkomst till DR-datacenter.

Azure Site Recovery är den rekommenderade lösningen på programnivå, inklusive (A) SCS. Andra program, till exempel NetWeaver SAP-program och icke-SAP-program utgör en del av hela miljön för SAP-distribution och bör också skyddas med Azure Site Recovery.

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer
Följ [vägledningen](azure-to-azure-walkthrough-enable-replication.md) att starta replikering av alla SAP programmet virtuella datorer till Azure DR-datacenter.

Om du använder en statisk IP-adress kan ange du IP-adress som du vill att den virtuella datorn i avsnittet Network interface-kort i beräknings-och nätverksinställningar.

![Mål-IP](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan kan ordningsföljd för växling vid fel på olika nivåer i en flernivåapp, därför kan upprätthålla programkonsekvens. Följ stegen som beskrivs [här](site-recovery-create-recovery-plans.md) när du skapar en återställningsplan för ett webbprogram med flera nivåer.

### <a name="adding-scripts-to-the-recovery-plan"></a>Att lägga till skript återställningsplanen
Du kan behöva göra vissa åtgärder på virtuella Azure-datorer efter redundans/testa redundans för dina program ska fungera korrekt. Du kan automatisera efter redundansväxlingen, till exempel uppdatera DNS-post och ändra bindningar och anslutningar, genom att lägga till motsvarande skript i återställningsplanen som beskrivs i [i den här artikeln](site-recovery-create-recovery-plans.md#add-scripts).

### <a name="dns-update"></a>DNS-uppdatering
Om DNS-servern har konfigurerats för dynamisk DNS-uppdatering och virtuella datorer vanligtvis uppdatera DNS med den nya IP när de startar. Om du vill lägga till ett explicit steg för att uppdatera DNS med den nya IP-adresser för virtuella datorer och lägger till detta [skript för att uppdatera IP-Adressen i DNS](https://aka.ms/asr-dns-update) som en post-åtgärd på recovery planeringsgrupper.  

## <a name="example-azure-to-azure-deployment"></a>Exempel på Azure-Azure-distribution
I diagrammet nedan Azure Site Recovery Azure till Azure DR illustreras scenariot:
* Det primära Datacentrat finns i Singapore (Azure sydöstra Asien) och DR-datacenter Hongkong (Azure Östasien).  I det här scenariot som lokal hög tillgänglighet har två virtuella datorer som kör SQL Server AlwaysOn i synkront läge i Singapore.
* Resurs-ASCS-fil kan användas för att tillhandahålla hög tillgänglighet för de SAP enskilda felpunkter. File Share ASCS kräver inte en delad klusterdisk och program, till exempel SIOS krävs inte.
* DR-skydd för DBMS-lagret uppnås med hjälp av asynkron replikering.
* Det här scenariot visar ”symmetrisk DR” – en term som används för att beskriva en DR-lösning som är en exakt replik av produktion, därför DR SQL Server-lösning har lokal hög tillgänglighet. Användning av symmetrisk DR är inte obligatoriskt för databas-lagret och många kunder utnyttja flexibiliteten hos molndistributioner att snabbt skapa en lokal nod för hög tillgänglighet efter en DR-händelse.
* Diagrammet visar SAP NetWeaver ASCS och programmet server lagret replikeras av Azure Site Recovery.

![Scenario för replikering](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>Gör ett redundanstest
Följ [vägledningen](azure-to-azure-walkthrough-test-failover.md) att göra ett redundanstest.

1.  Gå till Azure-portalen och välj Recovery Services-valvet.
2.  Klicka på återställningsplan som skapats för SAP-program (s).
3.  Klicka på Testa redundans.
4.  Välj återställningspunkten och Azure-nätverk för att starta processen testa redundans.
5.  Du kan utföra dina verifieringar när den sekundära miljön är upp.
6.  När validering har slutförts klickar du på på Rensa test-redundans och för att rensa failover-miljö.

## <a name="doing-a-failover"></a>Genomför en redundansväxling enligt
Följ [vägledningen](site-recovery-failover.md) när du gör en redundansväxling.

1.  Gå till Azure-portalen och välj Recovery Services-valvet.
2.  Klicka på återställningsplan som skapats för SAP-program.
3.  Klicka på 'Redundans'.
4.  Välj återställningspunkt för att starta processen för växling vid fel.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur du skapar en lösning för katastrofåterställning för SAP NetWeaver distributioner med hjälp av Azure Site Recovery i [rapporten](http://aka.ms/asr-sap). Vitboken diskuteras också rekommendationer för olika arkitekturer för SAP, visar vilka program som stöds och VM-typer för SAP på Azure och beskriver möjliga testplaner för din lösning för katastrofåterställning.

Lär dig mer om [replikerar andra arbetsbelastningar](site-recovery-workload.md) med Site Recovery.
