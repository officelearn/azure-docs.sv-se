---
title: Konfigurera haveriberedskap för distribution av flera nivåer SAP NetWeaver med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar haveriberedskap för distribution av SAP NetWeaver-program med Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9399f9d47d89215080b1f633423843f501fefb7b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850439"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurera haveriberedskap för en app-distribution för flera nivåer av SAP NetWeaver

De flesta stora och medelstora SAP-distributioner använder någon form av lösning för haveriberedskap. Vikten av stabila och kan testas haveriberedskapslösningar har ökat när mer kärnprocesser flyttas till program som SAP. Azure Site Recovery har testats och integrerats med SAP-program. Site Recovery överskrider kapaciteterna för de flesta lokala lösningar för haveriberedskap, och på en lägre total ägandekostnad (TCO) än konkurrerande lösningar.

Med Site Recovery kan du:
* **Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som körs lokalt** genom att replikera komponenter till Azure.
* **Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som körs på Azure** genom att replikera komponenter till en annan Azure-datacenter.
* **Förenkla molnmigreringen** med Site Recovery för att migrera din SAP-distribution till Azure.
* **Förenkla SAP-projektuppgradering, testning och prototyper** klona på begäran för att testa SAP-program genom att skapa en produktion.

Den här artikeln beskriver hur du skyddar SAP NetWeaver programdistributioner med [Azure Site Recovery](site-recovery-overview.md). Artikeln beskriver Metodtips för att skydda en distribution med tre nivåer SAP NetWeaver på Azure genom att replikera till en annan Azure-datacenter med Site Recovery. Beskriver scenarier som stöds och konfigurationer och hur du utför redundanstestning (tester av haveriberedskap) och faktiska redundans.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar måste du se till att du vet hur du utföra följande uppgifter:

* [Replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Utforma återställningsnätverk](site-recovery-azure-to-azure-networking-guidance.md)
* [Gör ett redundanstest till Azure](azure-to-azure-walkthrough-test-failover.md)
* [Gör en redundansväxling till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan använda Site Recovery för att implementera en lösning för haveriberedskap i följande scenarier:
* SAP-system som körs i en Azure-datacenter som replikeras till en annan Azure-datacenter (haveriberedskap för Azure till Azure). Mer information finns i [arkitektur för Azure till Azure-replikering](https://aka.ms/asr-a2a-architecture).
* SAP-system som körs på VMware (eller fysisk) lokalt som replikerar till en plats för katastrofåterställning i ett Azure-datacenter (VMware till Azure-haveriberedskap). Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [arkitektur för VMware till Azure-replikering](https://aka.ms/asr-v2a-architecture).
* SAP-system som körs på Hyper-V på plats som replikeras till en plats för katastrofåterställning i ett Azure-datacenter (Hyper-V till Azure-haveriberedskap). Det här scenariot kräver vissa ytterligare komponenter. Mer information finns i [arkitektur för Hyper-V till Azure-replikering](https://aka.ms/asr-h2a-architecture).

I den här artikeln använder vi en **Azure till Azure** katastrofåterställning för att demonstrera SAP disaster recovery-funktionerna i Site Recovery. Eftersom Site Recovery-replikering inte är programspecifika kan förväntas processen som beskrivs gäller även för andra scenarier.

### <a name="required-foundation-services"></a>Nödvändiga foundation-tjänster
I scenario som beskrivs i den här artikeln distribueras följande foundation-tjänster:
* Med ExpressRoute i Azure eller Azure VPN-Gateway
* Minst en domänkontrollant för Active Directory och DNS-server som körs i Azure

Vi rekommenderar att du etablerar den här infrastrukturen innan du distribuerar Site Recovery.

## <a name="reference-sap-application-deployment"></a>Referens för SAP-programdistribution

Den här referensarkitekturen visar information om hur du kör SAP NetWeaver i en Windows-miljö på Azure med hög tillgänglighet.  Den här arkitekturen distribueras med storlekar för specifika virtuella datorer (VM) som kan ändras för att tillgodose organisationens behov.

![Diagram över ett typiskt mönster för SAP-distribution](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Överväganden kring haveriberedskap

För haveriberedskap (DR), måste du kunna växla över till en sekundär region. Varje nivå har en egen strategi för haveriberedskap.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuella datorer som kör SAP Web Dispatcher-pool 
Komponenten Web Dispatcher används som en belastningsutjämnare för SAP-trafik på SAP-programservrarna. För att uppnå hög tillgänglighet för Web Dispatcher-komponent, används Azure Load Balancer för att implementera parallella Web Dispatcher-installation i en konfiguration för resursallokering för HTTP (S) fördelning av trafik mellan de tillgängliga Web avsändare i poolen för belastningsutjämnaren. Detta kommer att replikeras med hjälp av Azure Site Recovery och automatiserade skript som används för att konfigurera en belastningsutjämnare disaster recovery-region. 

#### <a name="vms-running-application-servers-pool"></a>Virtuella datorer som körs programpool för servrar
SMLG transaktionen används för att hantera inloggningsgrupper för ABAP-programservrar. Den använder funktionen på meddelande-servern för Central tjänster för belastningsutjämning för att distribuera arbetsbelastningen mellan servrar för SAP programpool för SAPGUIs och RFC trafik. Detta kommer att replikeras med Azure Site Recovery 

#### <a name="vms-running-sap-central-services-cluster"></a>Virtuella datorer som kör SAP Central Services-kluster
Denna Referensarkitektur kör centrala tjänster på virtuella datorer i appnivån. Central Services är en potentiell felpunkt (SPOF) när de distribueras till en enda virtuell dator – typisk distribution när hög tillgänglighet inte är ett krav.<br>

Ett kluster med delad disk eller en resurs filklustret kan användas för att implementera en lösning med hög tillgänglighet. För att konfigurera virtuella datorer i ett kluster med delad disk, Använd Windows Server-redundanskluster. Molnvittne rekommenderas som ett kvorumvittne. 
 > [!NOTE]
 > Azure Site Recovery replikerar inte molnvittnet därför rekommenderas det att distribuera molnvittne i regionen disaster recovery.

Stöd för failover-klustermiljö [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) utför klusterdelade volym funktion genom att replikera oberoende diskar som ägs av klusternoderna. Azure har inte har inbyggt stöd för delade diskar och därför kräver lösningar som tillhandahålls av SIOS. 

Ett annat sätt att hantera kluster är att implementera en filklustret för resursen. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ändrades Central Services distribution mönstret för att komma åt de globala katalogerna /sapmnt via en UNC-sökväg. Det rekommenderas dock fortfarande att se till att /sapmnt UNC-resurs med hög tillgänglighet. Detta kan göras på Central Services-instans med hjälp av Windows Server-redundanskluster med skala ut filserver (SOFS) och Lagringsdirigering (S2D) i Windows Server 2016. 
 > [!NOTE]
 > För närvarande stöd för Azure Site Recovery endast krascha programkonsekventa replikering av virtuella datorer med hjälp av lagringsdirigering 


## <a name="disaster-recovery-considerations"></a>Överväganden kring haveriberedskap

Du kan använda Azure Site Recovery för att dirigera inte över för fullständig SAP-distributionen i Azure-regioner.
Nedan visas stegen för att konfigurera den för haveriberedskap 

1. Replikera virtuella datorer 
2. Utforma återställningsnätverk
3.  Replikera en domänkontrollant
4.  Replikera data basnivån 
5.  Utföra ett redundanstest 
6.  Göra en redundansväxling 

Nedan visas i rekommendationen för haveriberedskap för varje nivå som används i det här exemplet. 

 **SAP-nivåer** | **Rekommendationen**
 --- | ---
**SAP Web Dispatcher-pool** |  Replikera med Site recovery 
**Serverpoolen för SAP-program** |  Replikera med Site recovery 
**SAP Central Services-kluster** |  Replikera med Site recovery 
**Active directory-datorer** |  Active directory-replikering 
**SQL database-servrar** |  SQL always on-replikering

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill börja replikera alla SAP-program virtuella datorer till Azure disaster recovery datacenter, följer du riktlinjerna i [replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md).


* Anvisningar om hur du skyddar Active Directory och DNS avser [skydda Active Directory och DNS](site-recovery-active-directory.md) dokumentet.

* Vägledning om hur du skyddar databasnivån som körs på SQLServer finns i [skydda SQL Server](site-recovery-active-directory.md) dokumentet.

## <a name="networking-configuration"></a>Nätverkskonfigurationen

Om du använder en statisk IP-adress kan ange du IP-adressen som du vill att den virtuella datorn ska börja. Ange IP-adressen genom att gå till **beräkning och nätverk inställningar** > **nätverkskortet**.

![Skärmbild som visar hur du ställer in en privat IP-adress i fönstret Site Recovery Network interface-kort](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder Sekvenseringen av olika nivåer i ett flerskiktat program under en redundansväxling. Sekvensering hjälper till att upprätthålla programkonsekvens. När du skapar en återställningsplan för ett webbprogram med flera nivåer, fullständig stegen som beskrivs i [skapa en återställningsplan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för växling vid fel

1.  Skapa en återställningsplan genom att lägga till application server, web dispatcher och SAP Central services virtuella datorer.
2.  Klicka på Anpassa om du vill gruppera de virtuella datorerna. Som standard har alla virtuella datorer ingår i ”grupp 1”.



### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i en återställningsplan
För dina program ska fungera korrekt, kan du behöva göra vissa åtgärder på Azure virtuella datorer efter redundans eller under ett redundanstest. Du kan automatisera vissa åtgärder efter en redundansväxling. Du kan till exempel uppdatera DNS-posten och ändra bindningar och anslutningar genom att lägga till motsvarande skript till i återställningsplanen.


Du kan distribuera de mest använda Azure Site Recovery-skript i ditt Automation-konto att klicka på knappen ”distribuera till Azure” nedan. När du använder alla publicerade skript, se till att du följer riktlinjerna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till en åtgärd skript till ”grupp 1” till redundans SQL-tillgänglighetsgrupp. Använd ”ASR-SQL-FailoverAG'-skript som publiceras i exempelskripten. Se till att du följer riktlinjerna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.
2. Lägg till en post åtgärd skript om du vill koppla en belastningsutjämnare på den redundansväxlade virtuella datorerna på webbnivå (grupp 1). Använd ”ASR-AddSingleLoadBalancer'-skript som publiceras i exempelskripten. Se till att du följer riktlinjerna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.

![Återställningsplan för SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Köra ett redundanstest

1.  Välj Recovery Services-valvet i Azure-portalen.
2.  Välj återställningsplan som du skapade för SAP-program.
3.  Välj **Testa redundans**.
4.  Välj återställningspunkten och Azure-nätverket om du vill börja testa redundansprocessen.
5.  När den sekundära miljön är igång, utföra verifieringar.
6.  När verifieringar har slutförts, för att rensa redundans-miljö, Välj **Rensa redundanstestning**.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1.  Välj Recovery Services-valvet i Azure-portalen.
2.  Välj återställningsplan som du skapade för SAP-program.
3.  Välj **Redundans**.
4.  Välj återställningspunkten för att starta redundansprocessen.

Mer information finns i [redundans i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du skapar en lösning för haveriberedskap för SAP NetWeaver-distributioner genom att använda Site Recovery finns nedladdningsbara dokumentet [SAP NetWeaver: skapa en lösning för Haveriberedskap med Azure Site Recovery](https://aka.ms/asr-sap). Dokumentet beskriver rekommendationer för olika SAP-arkitekturer, visar en lista över vilka program och VM-typer för SAP på Azure och beskriver testning alternativ för din haveriberedskapslösning.
* Läs mer om [replikera andra arbetsbelastningar](site-recovery-workload.md) med hjälp av Site Recovery.
