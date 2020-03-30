---
title: Konfigurera SAP NetWeaver-haveriberedskap med Azure Site Recovery
description: Lär dig hur du konfigurerar haveriberedskap för SAP NetWeaver med Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190786"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Konfigurera haveriberedskap för en distribution av SAP NetWeaver-appar på flera nivåer

De flesta stora och medelstora SAP-distributioner använder någon form av lösning för katastrofåterställning. Vikten av robusta och testbara katastrofåterställningslösningar har ökat i takt med att fler kärnprocesser flyttas till program som SAP. Azure Site Recovery har testats och integrerats med SAP-program. Site Recovery överskrider funktionerna i de flesta lokala lösningar för katastrofåterställning och till en lägre total ägandekostnad än konkurrerande lösningar.

Med Site Recovery kan du:
* Aktivera skydd av SAP NetWeaver- och icke-NetWeaver-produktionsprogram som körs lokalt genom att replikera komponenter till Azure.
* Aktivera skydd av SAP NetWeaver- och icke-NetWeaver-produktionsprogram som körs på Azure genom att replikera komponenter till ett annat Azure-datacenter.
* Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
* Förenkla SAP-projektuppgraderingar, testning och prototyper genom att skapa en produktionsklon på begäran för testning av SAP-program.

Du kan skydda SAP NetWeaver-programdistributioner med hjälp av [Azure Site Recovery](site-recovery-overview.md). Den här artikeln innehåller metodtips för att skydda en SAP NetWeaver-distribution på tre nivåer på Azure när du replikerar till ett annat Azure-datacenter med hjälp av Site Recovery. Artikeln beskriver scenarier och konfigurationer som stöds och hur du testar redundans (haveriberedskapsövningar) och faktiska redundansväxlingar.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du se till att du vet hur du utför följande uppgifter:

* [Replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Utforma ett återställningsnätverk](site-recovery-azure-to-azure-networking-guidance.md)
* [Gör en testväxling till Azure](azure-to-azure-walkthrough-test-failover.md)
* [Gör en redundans till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera en SQL Server-instans](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan använda Site Recovery för att implementera en katastrofåterställningslösning i följande scenarier:
* Du har SAP-system som körs i ett Azure-datacenter och replikerar dem till ett annat Azure-datacenter (Azure-to-Azure-haveriberedskap). 
   Mer information finns i [Azure-to-Azure-replikeringsarkitektur](https://aka.ms/asr-a2a-architecture).
* Du har SAP-system som körs på VMware (eller fysiska) servrar lokalt. Du replikerar också SAP-systemen till en haveriberedskapsplats i ett Azure-datacenter (VMware-to-Azure-haveriberedskap). 
   Det här scenariot kräver ytterligare komponenter. Mer information finns i [VMware-to-Azure-replikeringsarkitektur](https://aka.ms/asr-v2a-architecture).
* Du har SAP-system som körs på Hyper-V lokalt. Du replikerar också SAP-systemen till en haveriberedskapsplats i ett Azure-datacenter (Hyper-V-to-Azure-haveriberedskap).
   Det här scenariot kräver ytterligare komponenter. Mer information finns i [Hyper-V-till-Azure-replikeringsarkitektur](https://aka.ms/asr-h2a-architecture).

I den här artikeln använder vi ett **Azure-to-Azure-katastrofåterställningsscenario.** Scenariot visar sap-haveriberedskapsfunktionerna för webbplatsåterställning. Eftersom site recovery-replikering inte är programspecifik förväntas den process som beskrivs även gälla för andra scenarier.

### <a name="required-foundation-services"></a>Nödvändiga grundtjänster
I det scenario vi diskuterar i den här artikeln distribueras följande grundläggande tjänster:
* Azure ExpressRoute eller Azure VPN Gateway
* Minst en Azure Active Directory-domänkontrollant och DNS-server som körs i Azure

Vi rekommenderar att du upprättar den här infrastrukturen innan du distribuerar Webbplatsåterställning.

## <a name="reference-sap-application-deployment"></a>Distribution av referera till SAP-program

Den här referensarkitekturen kör SAP NetWeaver i en Windows-miljö på Azure med hög tillgänglighet. Den här arkitekturen distribueras med specifika storlekar för virtuella datorer som du kan ändra för att tillgodose organisationens behov.

![Diagram över ett typiskt SAP-distributionsmönster](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Överväganden kring haveriberedskap

För haveriberedskap måste du kunna växla över till en sekundär region. Varje nivå använder en annan strategi för att tillhandahålla skydd för haveriberedskap.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Virtuella datorer som kör SAP Web Dispatcher-pooler

Web Dispatcher-komponenten fungerar som en belastningsutjämnare för SAP-trafik mellan SAP-programservrarna. Azure Load Balancer implementerar den parallella webbavsändarens konfiguration för att uppnå hög tillgänglighet för komponenten Webbavsändare. Webbavsändare använder en avrundningskonfiguration för HTTP(S) trafikdistribution mellan tillgängliga webbavsändare i balancers-poolen.

#### <a name="vms-running-application-servers-pools"></a>Virtuella datorer som kör programservrar pooler
SMLG-transaktionen hanterar inloggningsgrupper för ABAP-programservrar. Den använder belastningsutjämningsfunktionen i meddelandeservern för Central Services för att distribuera arbetsbelastning mellan SAP-programserverpooler för SAPGUIs och RFC-trafik. Du kan replikera den här hanteringen med hjälp av Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Virtuella datorer som kör SAP Central Services-kluster
Den här referensarkitekturen kör Centrala tjänster på virtuella datorer på programnivån. Central Services är en potentiell enskild felpunkt när i en enda virtuell dator. Typisk distribution och hög tillgänglighet är inte krav.

Om du vill implementera en lösning med hög tillgänglighet kan du använda antingen ett delat diskkluster eller ett filresurskluster. Om du vill konfigurera virtuella datorer för ett delat diskkluster använder du Windows Server Redundanskluster. Vi rekommenderar att du använder molnvittnet som kvorumvittne.

 > [!NOTE]
 > Eftersom Site Recovery inte replikerar molnvittnet rekommenderar vi att du distribuerar molnvittnet i katastrofåterställningsregionen.

SIOS [DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) stöder den delade volymfunktionen för att stödja redundanskluster. I funktionen replikerar SIOS DataKeeper Cluster oberoende diskar som ägs av klusternoderna. Eftersom Azure inte stöder delade diskar internt kräver det lösningar som tillhandahålls av SIOS.

Du kan också hantera klustring genom att implementera ett filresurskluster. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) har nyligen ändrat distributionsmönstret för centrala tjänster för att komma åt de globala /sapmnt-globala katalogerna via en UNC-sökväg. Vi rekommenderar fortfarande att du ser till att /sapmnt UNC-resursen är mycket tillgänglig. Du kan kontrollera din Central Services-instans. Använd Windows Server Redundanskluster med SOFS (Scale Out File Server) och funktionen Storage Spaces Direct (S2D) i Windows Server 2016.

 > [!NOTE]
 > Site Recovery stöder för närvarande endast kraschkonsekvent punktreplikering av virtuella datorer som använder lagringsutrymmen direkt och den passiva noden för SIOS-datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Fler överväganden om katastrofåterställning

Du kan använda Site Recovery för att dirigera redundansen för fullständig SAP-distribution över Azure-regioner.
Nedan följer stegen för att ställa in haveriberedskapen:

1. Replikera virtuella datorer
1. Utforma ett återställningsnätverk
1. Replikera en domänkontrollant
1. Replikera databasnivå
1. Utföra ett redundanstest
1. Göra en redundansväxling

Följande är rekommendationen för haveriberedskap för varje nivå som används i det här exemplet.

 **SAP-nivåer** | **Rekommendation**
 --- | ---
**SAP Webb dispatcher pool** |  Replikera med hjälp av site recovery 
**SERVERpool för SAP-program** |  Replikera med hjälp av site recovery 
**KLUSTRET SAP Central Services** |  Replikera med hjälp av site recovery 
**Virtuella datorer i Active Directory** |  Använda Active Directory-replikering 
**SQL Database-servrar** |  Använd SQL Server always on replication

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill börja replikera alla virtuella SAP-programdatorer till Azure disaster recovery-datacentret följer du anvisningarna i [Replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md).

* Mer information om hur du skyddar Active Directory och DNS finns i hur du [skyddar Active Directory och DNS](site-recovery-active-directory.md).

* Mer information om hur du skyddar databasnivån som körs på SQL Server kan du [läsa om hur du skyddar SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Nätverkskonfiguration

Om du använder en statisk IP-adress kan du ange den IP-adress som du vill att den virtuella datorn ska ta. Om du vill ange IP-adressen går du till**Gränssnittskort** **för beräknings- och nätverksinställningar** > .

![Skärmbild som visar hur du anger en privat IP-adress i gränssnittsfönstret för nätverk för webbplatsåterställning](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

En återställningsplan stöder sekvensering av olika nivåer i ett program på flera nivåer under en redundansväxling. Sekvensering hjälper till att upprätthålla programmets konsekvens. När du skapar en återställningsplan för ett webbprogram på flera nivåer slutför du stegen som beskrivs i [Skapa en återställningsplan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i redundansgrupper

1. Skapa en återställningsplan genom att lägga till virtuella programserver, webbavsändare och VIRTUELLA SAP-centraltjänster.
1. Välj **Anpassa** om du vill gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i grupp 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställningsplanen
För att dina program ska fungera korrekt kan du behöva göra vissa åtgärder på virtuella Azure-datorer. Gör dessa åtgärder efter redundansen eller under en test redundans. Du kan också automatisera vissa åtgärder efter redundans. Uppdatera till exempel DNS-posten och ändra bindningar och anslutningar genom att lägga till motsvarande skript i återställningsplanen.

Du kan distribuera de mest använda Site Recovery-skripten till ditt Azure Automation-konto genom att välja **Distribuera till Azure**. När du använder ett publicerat skript följer du anvisningarna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ett föråtgärdsskript i grupp 1 för att växla över sql server-tillgänglighetsgruppen. Använd ASR-SQL-FailoverAG-skriptet som publicerats i exempelskripten. Följ anvisningarna i skriptet och gör de ändringar som krävs i skriptet på rätt sätt.
1. Lägg till ett efteråtgärdsskript för att koppla en belastningsutjämningsfaktor till de virtuella datorerna för misslyckade datorer på webbnivån (grupp 1). Använd skriptet ASR-AddSingleLoadBalancer som publicerats i exempelskripten. Följ anvisningarna i skriptet och gör nödvändiga ändringar i skriptet efter behov.

![SAP återhämtningsplan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. Välj ditt Recovery Services-valv i Azure-portalen.
1. Välj den återställningsplan som du skapade för SAP-program.
1. Välj **Testa redundans**.
1. Om du vill starta testundanundansprocessen väljer du återställningspunkten och det virtuella Azure-nätverket.
1. När den sekundära miljön är uppe utför du valideringar.
1. När valideringarna är klara rensar du redundansmiljön genom att välja **Redundanstest redundans**.

Mer information finns i [Testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1. Välj ditt Recovery Services-valv i Azure-portalen.
1. Välj den återställningsplan som du skapade för SAP-program.
1. Välj **Redundans**.
1. Om du vill starta redundansprocessen väljer du återställningspunkten.

Mer information finns [i Redundans i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om att skapa en lösning för haveriberedskap för SAP NetWeaver-distributioner med hjälp av Site Recovery. Se det nedladdningsbara faktabladet [SAP NetWeaver: Bygga en katastrofåterställningslösning med site recovery](https://aka.ms/asr_sap). I vitboken beskrivs rekommendationer för olika SAP-arkitekturer. Du kan se program och VM-typer som stöds för SAP på Azure. Det finns också planalternativ för att testa din katastrofåterställningslösning.
* Läs mer om [att replikera andra arbetsbelastningar](site-recovery-workload.md) med hjälp av Site Recovery.
