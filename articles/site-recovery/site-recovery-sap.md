---
title: Konfigurera haveri beredskap för SAP NetWeaver med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för SAP NetWeaver med Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: eeb85e97d653b0faac171e2986cb933fc41e6606
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940674"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurera haveri beredskap för en distribution med flera nivåer av SAP NetWeaver-appar

I de flesta stora och medel stora SAP-distributioner används någon form av katastrof återställnings lösning. Vikten av robusta och testable katastrof återställnings lösningar har ökat allteftersom fler viktiga affärs processer flyttas till program som SAP. Azure Site Recovery har testats och integrerats med SAP-program. Site Recovery överstiger funktionerna i de flesta lokala katastrof återställnings lösningar och till en lägre totalkostnad (TCO) än konkurrerande lösningar.

Med Site Recovery kan du:
* **Aktivera skydd av SAP-NetWeaver och icke-NetWeaver produktions program som körs lokalt** genom att replikera komponenter till Azure.
* **Aktivera skydd av SAP-NetWeaver och icke-NetWeaver produktions program som körs på Azure** genom att replikera komponenter till ett annat Azure-datacenter.
* **Förenkla migreringen av molnet** genom att använda Site Recovery för att MIGRERA din SAP-distribution till Azure.
* **Förenkla SAP-projektets uppgraderingar, testning och prototyp** genom att skapa en produktions klon på begäran för att testa SAP-program.

Den här artikeln beskriver hur du skyddar distributioner av SAP NetWeaver-program med hjälp av [Azure Site Recovery](site-recovery-overview.md). Artikeln beskriver metod tips för att skydda en SAP NetWeaver-distribution på tre nivåer i Azure genom att replikera till ett annat Azure-datacenter med hjälp av Site Recovery. Den beskriver scenarier och konfigurationer som stöds och hur du utför redundanstest (haveri beredskap) och faktiska redundanser.

## <a name="prerequisites"></a>Krav
Innan du börjar ska du se till att du vet hur du gör följande uppgifter:

* [Replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Utforma ett återställnings nätverk](site-recovery-azure-to-azure-networking-guidance.md)
* [Gör ett redundanstest till Azure](azure-to-azure-walkthrough-test-failover.md)
* [Gör en redundansväxling till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan använda Site Recovery för att implementera en katastrof återställnings lösning i följande scenarier:
* SAP-system som körs i ett Azure-datacenter som replikerar till ett annat Azure-datacenter (Azure-till-Azure haveri beredskap). Mer information finns i [arkitektur för Azure-till-Azure-replikering](https://aka.ms/asr-a2a-architecture).
* SAP-system som körs på VMware-servrar (eller fysiska) som replikeras till en katastrof återställnings plats i ett Azure-datacenter (VMware-till-Azure haveri beredskap). Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [arkitektur för VMware-till-Azure-replikering](https://aka.ms/asr-v2a-architecture).
* SAP-system som körs på Hyper-V lokalt och som replikeras till en katastrof återställnings plats i ett Azure-datacenter (haveri beredskap för Hyper-V-till-Azure). Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [arkitektur för Hyper-V-till-Azure-replikering](https://aka.ms/asr-h2a-architecture).

I den här artikeln använder vi ett scenario för haveri beredskap i **Azure-till-Azure** för att demonstrera inkassering av SAP-haveri beredskap i Site Recovery. Eftersom Site Recovery replikering inte är programspecifikt förväntas den process som beskrivs även gälla för andra scenarier.

### <a name="required-foundation-services"></a>Nödvändiga grund tjänster
I det scenario vi diskuterar i den här artikeln, är följande grundläggande tjänster distribuerade:
* Azure ExpressRoute eller Azure VPN Gateway
* Minst en Active Directory-domänkontrollant och DNS-server som körs i Azure

Vi rekommenderar att du upprättar den här infrastrukturen innan du distribuerar Site Recovery.

## <a name="reference-sap-application-deployment"></a>Referens för SAP-program

Den här referens arkitekturen visar att köra SAP-NetWeaver i en Windows-miljö i Azure med hög tillgänglighet.  Den här arkitekturen distribueras med en speciell storlek för virtuella datorer (VM) som kan ändras för att passa organisationens behov.

![Diagram över ett typiskt SAP-distributions mönster](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Överväganden vid haveri beredskap

För haveriberedskap (DR), måste du kunna växla över till en sekundär region. Varje nivå har en egen strategi för haveriberedskap.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuella datorer som kör SAP Web dispatcher-poolen 
Komponenten Web Dispatcher används som en belastningsutjämnare för SAP-trafik på SAP-programservrarna. För att uppnå hög tillgänglighet för webb dispatcher-komponenten används Azure Load Balancer för att implementera installations programmet för parallell webb dispatcher i en Round Robin-konfiguration för HTTP (S)-trafik distribution bland de tillgängliga webb dispatcharna i poolen för Utjämning av nätverks belastning. Detta kommer att replikeras med hjälp av Site Recovery och automation-skript används för att konfigurera belastningsutjämnaren i Disaster Recovery-regionen. 

#### <a name="vms-running-application-servers-pool"></a>Virtuella datorer som kör program servrar, pool
SMLG transaktionen används för att hantera inloggningsgrupper för ABAP-programservrar. Den använder funktionen på meddelande-servern för Central tjänster för belastningsutjämning för att distribuera arbetsbelastningen mellan servrar för SAP programpool för SAPGUIs och RFC trafik. Detta kommer att replikeras med hjälp av Site Recovery.

#### <a name="vms-running-sap-central-services-cluster"></a>Virtuella datorer som kör SAP Central Services-kluster
Denna Referensarkitektur kör centrala tjänster på virtuella datorer i appnivån. Central Services är en potentiell felpunkt (SPOF) när de distribueras till en enda virtuell dator – typisk distribution när hög tillgänglighet inte är ett krav.<br>

Om du vill implementera en lösning för hög tillgänglighet kan antingen ett delat disk kluster eller ett fil resurs kluster användas. Om du vill konfigurera virtuella datorer för ett delat disk kluster använder du Windows Server-redundanskluster. Moln vittne rekommenderas som ett kvorumlogg. 
 > [!NOTE]
 > Site Recovery replikerar inte moln vittnet, därför rekommenderas det att distribuera moln vittnet i Disaster Recovery-regionen.

Stöd för failover-klustermiljö [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) utför klusterdelade volym funktion genom att replikera oberoende diskar som ägs av klusternoderna. Azure har inte har inbyggt stöd för delade diskar och därför kräver lösningar som tillhandahålls av SIOS. 

Ett annat sätt att hantera klustring är att implementera ett fil resurs kluster. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ändrades Central Services distribution mönstret för att komma åt de globala katalogerna /sapmnt via en UNC-sökväg. Vi rekommenderar dock fortfarande att se till att/sapmnt-UNC-resursen är hög tillgänglig. Detta kan göras på den centrala Services-instansen med hjälp av Windows Server-redundanskluster med skalbar fil server (SOFS) och funktionen Lagringsdirigering (S2D) i Windows Server 2016. 
 > [!NOTE]
 > För närvarande Site Recovery stöd endast krasch konsekvens av virtuella datorer som använder lagrings dirigering och passiv nod för SIOS DataKeeper


## <a name="disaster-recovery-considerations"></a>Överväganden kring haveriberedskap

Du kan använda Site Recovery för att dirigera redundans över hela SAP-distributionen i Azure-regioner.
Nedan visas stegen för att konfigurera haveri beredskap 

1. Replikera virtuella datorer 
2. Utforma ett återställnings nätverk
3.  Replikera en domänkontrollant
4.  Replikera data bas nivån 
5.  Utföra ett redundanstest 
6.  Göra en redundansväxling 

Nedan visas en rekommendation för haveri beredskap för varje nivå som används i det här exemplet. 

 **SAP-nivåer** | **Rekommendationen**
 --- | ---
**SAP Web dispatcher-pool** |  Replikera med Site Recovery 
**SAP Application Server-pool** |  Replikera med Site Recovery 
**SAP-kluster för central tjänster** |  Replikera med Site Recovery 
**Active Directory-virtuella datorer** |  Active Directory-replikering 
**SQL Database-servrar** |  SQL Always on-replikering

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill börja replikera alla virtuella SAP-program till data centret för Azure haveri beredskap följer du rikt linjerna i [replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md).


* Anvisningar om hur du skyddar Active Directory och DNS finns i [skydda Active Directory och DNS-](site-recovery-active-directory.md) dokument.

* Information om hur du skyddar databas nivån som körs på SQL Server finns i [skydda SQL Server](site-recovery-sql.md) -dokument.

## <a name="networking-configuration"></a>Nätverks konfiguration

Om du använder en statisk IP-adress kan du ange den IP-adress som du vill att den virtuella datorn ska ta. Ange IP-adressen genom att gå till **Inställningar för beräkning och nätverk** > **nätverkskort**.

![Skärm bild som visar hur du ställer in en privat IP-adress i fönstret för nätverkskortet Site Recovery nätverkskort](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Skapa en återställnings plan
En återställnings plan stöder sekvenseringen av olika nivåer i ett program med flera nivåer under en redundansväxling. Sekvenseringen bidrar till att upprätthålla programmets konsekvens. När du skapar en återställnings plan för ett webb program med flera nivåer slutför du stegen som beskrivs i [skapa en återställnings plan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för redundans

1.  Skapa en återställnings plan genom att lägga till program servern, de virtuella datorerna för www-avsändare och SAP Central Services.
2.  Klicka på Anpassa för att gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i "grupp 1".



### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställnings planen
För att dina program ska fungera korrekt kan du behöva utföra några åtgärder på de virtuella Azure-datorerna efter redundansväxlingen eller under ett redundanstest. Du kan automatisera vissa åtgärder efter redundans. Du kan till exempel uppdatera DNS-posten och ändra bindningar och anslutningar genom att lägga till motsvarande skript i återställnings planen.


Du kan distribuera de vanligaste Site Recovery-skripten till ditt Automation-konto genom att klicka på knappen distribuera till Azure nedan. När du använder ett publicerat skript, se till att du följer anvisningarna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ett skript för för åtgärd i gruppen 1 i SQL-tillgänglighetsgruppen för redundans. Använd skriptet "ASR-SQL-FailoverAG" publicerat i exempel skripten. Se till att du följer anvisningarna i skriptet och gör de nödvändiga ändringarna i skriptet på lämpligt sätt.
2. Lägg till ett inläggs åtgärds skript för att koppla en belastningsutjämnare på den misslyckade över virtuella datorerna på webb nivån (grupp 1). Använd skriptet "ASR-AddSingleLoadBalancer" publicerat i exempel skripten. Se till att du följer anvisningarna i skriptet och gör de nödvändiga ändringarna i skriptet på lämpligt sätt.

![SAP-återställnings plan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Köra ett redundanstest

1.  I Azure Portal väljer du Recovery Services valvet.
2.  Välj den återställnings plan som du skapade för SAP-program.
3.  Välj **Testa redundans**.
4.  Starta processen för redundanstest genom att välja återställnings punkten och det virtuella Azure-nätverket.
5.  Utföra verifieringar när den sekundära miljön är igång.
6.  När verifieringen är klar kan du rensa redundansväxlingen genom att välja **rensning**av redundanstest.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1.  I Azure Portal väljer du Recovery Services valvet.
2.  Välj den återställnings plan som du skapade för SAP-program.
3.  Välj **Redundans**.
4.  Starta redundansväxlingen genom att välja återställnings punkten.

Mer information finns i [redundansväxlingen i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du skapar en katastrof återställnings lösning för SAP NetWeaver-distributioner med hjälp av Site Recovery finns i nedladdnings bara white paper [SAP-NetWeaver: skapa en lösning för haveri beredskap med Site Recovery](https://aka.ms/asr_sap). White paper diskuterar rekommendationer för olika SAP-arkitekturer, visar en lista över program som stöds och VM-typer för SAP på Azure och beskriver alternativ för test plan för din katastrof återställnings lösning.
* Lär dig mer om att [Replikera andra arbets belastningar](site-recovery-workload.md) med hjälp av Site Recovery.
