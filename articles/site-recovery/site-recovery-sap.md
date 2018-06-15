---
title: Skydda en flera nivåer SAP NetWeaver programdistribution med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du kan skydda SAP NetWeaver distribution av program med hjälp av Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: asgang
ms.openlocfilehash: 27dfdec4e833a2f30963157ba2f4d95232e21270
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267340"
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

I den här artikeln använder vi en **Azure till Azure** katastrofåterställning för att demonstrera SAP återställningskapacitet av Site Recovery. Eftersom Site Recovery replikering inte programspecifika, förväntas processen som beskrivs gäller även för andra scenarier.

### <a name="required-foundation-services"></a>Nödvändiga foundation-tjänster
Följande foundation-tjänster har distribuerats i det scenariot diskuterar vi i den här artikeln:
* Azure ExpressRoute eller Azure VPN-Gateway
* Minst en Active Directory-domänkontrollant och DNS-server som körs i Azure

Vi rekommenderar att du upprättar infrastrukturen innan du distribuerar Site Recovery.

## <a name="reference-sap-application-deployment"></a>Referens för SAP-programdistribution

Denna Referensarkitektur visar kör SAP NetWeaver i en Windows-miljö på Azure med hög tillgänglighet.  Den här arkitekturen distribueras med storlekar för specifika virtuella datorer (VM) som kan ändras för att passa organisationens behov.

![Diagram över ett typiskt mönster för SAP-distribution](./media/site-recovery-sap/reference_sap.png)

## <a name="disaster-recovery-considerations"></a>Överväganden för Disaster Recovery

För katastrofåterställning (DR), måste du kunna växla över till en sekundär region. Varje nivå har en egen strategi för haveriberedskap.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuella datorer som kör SAP Web dispatcherpoolen 
Komponenten Web Dispatcher används som en belastningsutjämnare för SAP-trafik mellan SAP-programservrar. För att uppnå hög tillgänglighet för komponenten Web Dispatcher används Azure belastningsutjämnare för att implementera parallella Web Dispatcher inställningarna i en konfiguration för resursallokering för HTTP (S) trafikfördelning bland de tillgängliga Web avsändare i pool för belastningsutjämnare. Detta kommer att replikeras med hjälp av Azure Site Recovery(ASR) och automatiserade skript används för att konfigurera belastningsutjämnare på disaster recovery-region. 

####<a name="vms-running-application-servers-pool"></a>Virtuella datorer som kör servrar programpool
Om du vill hantera inloggning grupper för ABAP programservrar används SMLG transaktionen. Belastningsutjämning fungerar inom meddelandet servern centrala tjänster används för att distribuera arbetsbelastningen mellan servrar för SAP programpool för SAPGUIs och RFC trafik. Detta kommer att replikeras med hjälp av Azure Site Recovery 

####<a name="vms-running-sap-central-services-cluster"></a>Virtuella datorer som kör SAP centrala tjänster kluster
Denna Referensarkitektur körs centrala tjänster på virtuella datorer på nivån för programmet. Central Services är en potentiell felpunkt (SPOF) vid distribution till en enda virtuell dator – typisk distribution när hög tillgänglighet inte är ett krav.<br>

Delade diskkluster eller ett kluster för resursen kan användas för att implementera en lösning för hög tillgänglighet. Konfigurera virtuella datorer i ett kluster med delad disk med redundanskluster i Windows Server. Molnet vittne rekommenderas som ett kvorumvittne. 
 > [!NOTE]
 > Azure Site Recovery replikerar inte molnet vittne därför rekommenderas det att distribuera moln vittne i disaster recovery region.

Som stöd för failover-klustermiljö [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) utför klusterdelade volym funktion genom att replikera oberoende diskar som ägs av klusternoderna. Azure stöder inte delade diskar internt och därför kräver lösningar som tillhandahålls av SIOS. 

Ett annat sätt att hantera kluster är att implementera ett kluster för resursen. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nyligen ändrade centrala tjänster distribution mönster för att komma åt /sapmnt globala kataloger via en UNC-sökväg. Den här ändringen tar bort behovet av SIOS eller andra lösningar för delad disk på centrala tjänster virtuella datorer. Det rekommenderas ändå att /sapmnt UNC-resurs är hög tillgänglighet. Detta kan göras på centrala Services-instans med hjälp av Windows Server Failover-kluster med skala ut filserver (SOFS) och Storage Spaces Direct (S2D)-funktionen i Windows Server 2016. 
 > [!NOTE]
 > För närvarande stöd för Azure Site Recovery endast krascher programkonsekventa replikering av virtuella datorer med hjälp av lagringsutrymmen direkt 


## <a name="disaster-recovery-considerations"></a>Överväganden kring haveriberedskap

Du kan använda Azure Site Recovery för att samordna misslyckas över hela SAP-distribution över Azure-regioner.
Nedan visas stegen för att ställa in disaster recovery 

1. Replikera virtuella datorer 
2. Utforma ett nätverk för återställning
3.  Replikera en domänkontrollant
4.  Replikera grundläggande datanivå 
5.  Gör ett redundanstest 
6.  Gör en redundansväxling 

Nedan visas rekommendation för katastrofåterställning för varje nivå som används i det här exemplet. 

 **SAP nivåer** | **Rekommendation**
 --- | ---
**SAP Web dispatcherpoolen** |  Replikera med Site recovery 
**SAP server programpool** |  Replikera med Site recovery 
**SAP centrala tjänster kluster** |  Replikera med Site recovery 
**Active directory virtuella datorer** |  Active directory-replikering 
**SQL database-servrar** |  SQL alltid på replikering

##<a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill starta replikera alla SAP programmet virtuella datorer till Azure disaster recovery datacenter, följer du anvisningarna i [replikera en virtuell dator till Azure](azure-to-azure-walkthrough-enable-replication.md).


* För anvisningar om hur du skyddar Active Directory och DNS Se [skydda Active Directory och DNS](site-recovery-active-directory.md) dokumentet.

* För anvisningar om hur du skyddar databasnivå som körs på SQLServer Se [skydda SQL Server](site-recovery-active-directory.md) dokumentet.

## <a name="networking-configuration"></a>Nätverkskonfiguration

Om du använder en statisk IP-adress kan ange du IP-adressen som du vill att den virtuella datorn ska börja. Om du vill ange IP-adress, gå till **beräknings- och nätverksinställningar inställningar** > **nätverkskort**.

![Skärmbild som visar hur du ställer in en privat IP-adress i rutan Site Recovery Network interface card](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder Sekvenseringen av olika nivåer i en flernivåapp under en växling vid fel. Sekvensering upprätthåller programkonsekvens. När du skapar en återställningsplan för ett webbprogram med flera nivåer fullständig steg som beskrivs i [skapa en återställningsplan med Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer till grupper för växling vid fel

1.  Skapa en återställningsplan genom att lägga till programservern, web dispatcher och SAP centrala tjänster virtuella datorer.
2.  Klicka på Anpassa om du vill gruppera de virtuella datorerna. Som standard är alla virtuella datorer som en del av grupp 1.



### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställningsplanen
Du kan behöva göra vissa åtgärder på de virtuella Azure-datorerna efter växling vid fel eller under ett redundanstest för dina program ska fungera korrekt. Du kan automatisera vissa åtgärder för postredundans. Du kan till exempel uppdatera DNS-post och ändra bindningar och anslutningar genom att lägga till motsvarande skript återställningsplanen.


Du kan distribuera de mest använda Azure Site Recovery-skript i ditt Automation-konto att klicka på knappen ”distribuera till Azure'. När du använder alla publicerade skript, se till att du följer riktlinjerna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ett skript före åtgärden till grupp 1 redundans SQL-tillgänglighetsgruppen. Använda 'ASR-SQL-FailoverAG'-skript som publiceras i skriptexemplen. Se till att du följer du anvisningarna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.
2. Lägg till en post åtgärd skript om du vill koppla en belastningsutjämnare på den över virtuella datorer i webbnivå (grupp 1). Använda 'ASR AddSingleLoadBalancer'-skript som publiceras i skriptexemplen. Se till att du följer du anvisningarna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.

![SAP återställningsplan](./media/site-recovery-sap/sap_recovery_plan.png)


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
