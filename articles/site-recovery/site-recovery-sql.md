---
title: Konfigurera haveriberedskap för SQL Server med SQL Server och Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar haveriberedskap för SQL Server med hjälp av SQL Server och Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491784"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Konfigurera haveriberedskap för SQL Server

Den här artikeln beskriver hur du skyddar SQL Server-serverdelen för ett program med hjälp av en kombination av SQL Server-affärskontinuitet och disaster recovery (BCDR)-teknik och [Azure Site Recovery](site-recovery-overview.md).

Innan du börjar bör du kontrollera att du förstår disaster recovery funktioner i SQL Server, inklusive redundanskluster, Always On-Tillgänglighetsgrupper, databasspegling, logga leverans, aktiv geo-replikering och automatisk redundans grupper.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>DR-rekommendation för integrering av SQL Server BCDR-teknik med Site Recovery

Val av en BCDR-teknik till recovery SQL-servrar ska baseras på dina RTO och RPO behov enligt i tabellen nedan. När val görs kan Site Recovery integreras med redundansväxlingen av den tekniken du organiserar återställning av hela programmet.

**Distributionstyp** | **BCDR-teknik** | **Förväntade RTO för SQL** | **Förväntade Återställningspunktmålet för SQL** |
--- | --- | --- | ---
SQLServer på Azure IaaS-VM eller på en lokal| **[Always On-tillgänglighetsgrupp](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | Motsvarar den tid det tar att göra en sekundär replik som primär | Replikering är asynkrona till den sekundära repliken, det är därför vissa dataförluster.
SQLServer på Azure IaaS-VM eller på en lokal| **[Failover-kluster (alltid på FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | Motsvarar den tid det tar för redundansväxling mellan noderna | Den använder delad lagring, därför samma vy av storage-instansen är tillgängliga vid redundansväxling.
SQLServer på Azure IaaS-VM eller på en lokal| **[Databasspegling (högpresterande läge)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | Motsvarar den tid det tar att tvinga-tjänsten, som använder den speglade servern som en varm standby-server. | Replikering är asynkrona. Spegeldatabasen kan ligga något bakom huvuddatabas. Luckan är normalt små howvever, kan bli avsevärd om huvudnamn eller spegling-serverns systemet är hårt belastat.<br></br>Loggöverföring kan vara ett tillägg till databasspegling och är en ett alternativ till asynkron databasspegling
SQL som PaaS på Azure<br></br>(Elastiska pooler, SQL database-servrar) | **Aktiv Geo-replikering** | 30 sekunder när den utlöses<br></br>När redundans har aktiverats för en av de sekundära databaserna kan länkas automatiskt alla sekundära till den nya primärt. | Återställningspunktmål på 5 sekunder<br></br>Aktiv geo-replikering använder teknik för Always On av SQL Server för att replikera asynkront genomförda transaktioner på den primära databasen till en sekundär databas med ögonblicksbildisolering. <br></br>Sekundär data är säkert att aldrig har delvis transaktioner.
SQL som PaaS som konfigurerats med aktiv geo-replikering i Azure<br></br>(SQL Database Managed Instance, elastiska pooler SQL database-servrar) | **Automatiska redundansgrupper** | RTO 1 timme | Återställningspunktmål på 5 sekunder<br></br>Automatisk redundans grupper ger gruppen semantik ovanpå aktiv geo-replikering, men samma asynkron replikeringsmekanism används.
SQLServer på Azure IaaS-VM eller på en lokal| **Replikeringen med Azure Site Recovery** | Vanligtvis mindre än 15 minuter. [Läs mer](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) vill veta mer om RTO serviceavtalet tillhandahålls av Azure Site Recovery. | 1 timme för programkonsekvens och 5 minuter för kraschkonsekvens. 

> [!NOTE]
> Några viktiga överväganden när du skyddar SQL-arbetsbelastningar med Azure Site Recovery:
> * Azure Site Recovery är oberoende av program och därför en version av SQLServer som har distribuerats på ett operativsystem kan skyddas av Azure Site Recovery. [Läs mer](vmware-physical-azure-support-matrix.md#replicated-machines).
> * Du kan välja att använda Site Recovery för alla distributioner på Azure, Hyper-V, VMware eller fysiska infrastrukturen. Följ den [vägledning](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) i slutet av dokumentet om att skydda SQL Server-kluster med Azure Site Recovery.
> * Se till att de dataändringshastighet (skrivna byte per sekund) observerats på datorn ligger inom [Site Recovery-begränsningarna](vmware-physical-azure-support-matrix.md#churn-limits). Du kan visa det under fliken prestanda på Aktivitetshanteraren för windows-datorer. Notera skrivning hastighet för varje disk.
> * Azure Site Recovery har stöd för replikering av instanser för redundanskluster på Storage Spaces Direct. [Läs mer](azure-to-azure-how-to-enable-replication-s2d-vms.md).
 

## <a name="disaster-recovery-of-application"></a>Haveriberedskap för program

**Azure Site Recovery dirigerar testa redundans och redundans för hela programmet med hjälp av Återställningsplaner.** 

Det finns vissa förutsättningar för att se till att Återställningsplanen är helt anpassad enligt dina behov. Alla SQL Server-distribution måste vanligtvis en Active Directory. Dessutom behövs anslutning för programnivån.

### <a name="step-1-set-up-active-directory"></a>Steg 1: Konfigurera Active Directory

Konfigurera Active Directory i den sekundära platsen, för SQL Server ska fungera korrekt.

* **Små företag**– med ett litet antal program och en enda domänkontrollant för den lokala platsen, om du vill växla över hela platsen, rekommenderar vi du använder Site Recovery-replikering för att replikera domänkontrollanten till sekundärt datacenter, eller till Azure.
* **Mellanstora till stora företag**– om du har ett stort antal program, en Active Directory-skog och du vill växla över av program eller en arbetsbelastning, vi rekommenderar att du ställer in ytterligare en domänkontrollant i det sekundära datacentret eller i Azure. Om du använder Always On-Tillgänglighetsgrupper för att återställa till en fjärrplats, rekommenderar vi att du ställer in en annan ytterligare en domänkontrollant på den sekundära platsen eller i Azure, för den återställda SQL Server-instansen.

Anvisningarna i den här artikeln förutsätter att en domänkontrollant är tillgänglig på den sekundära platsen. [Läs mer](site-recovery-active-directory.md) om hur du skyddar Active Directory med Site Recovery.

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>Steg 2: Se till att anslutningar till andra program tier(s) och webbnivå

Kontrollera att du är ansluten med programmet och webbnivån när databasnivån är igång och körs i Azure-målregion. Nödvändiga åtgärder bör vidtas i förväg för att verifiera anslutningarna med redundanstest.

Förstå hur du kan utforma program för anslutning överväganden med ett par exempel:
* [Utforma ett program för katastrofåterställning i molnet](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Elastisk pool Disaster Recovery strategies](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>Steg 3: Integrera med Always On, aktiv Geo-replikering eller automatisk redundans grupper för växling vid fel i programmet

BCDR-teknik alltid på aktiv Geo-replikering och automatisk redundans grupper ha sekundära repliker av SQLServer som körs i Azure-målregion. Därför är det första steget för ditt program redundans att göra den här repliken som primär (förutsatt att du redan har en domänkontrollant i sekundära). Det här steget kan inte vara nödvändigt om du väljer att göra en automatisk redundans. Endast när databasen redundansen är klar bör du redundans dina webb- eller nivåer.

> [!NOTE] 
> Om du har skyddat SQL-datorer med Azure Site Recovery, behöver du bara skapa en grupp för dataåterställning för dessa datorer och lägga till sina redundans i återställningsplanen.

[Skapa en Återställningsplanen](site-recovery-create-recovery-plans.md) med program och virtuella datorer med web-nivån. Följ de stegen nedan för att lägga till redundans för databasnivån:

1. Importera skript till ditt Azure Automation-konto. Innehåller skript att redundansväxla SQL-tillgänglighetsgrupp i en [Resource Manager-dator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) och en [klassisk virtuell dator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Lägg till ASR-SQL-FailoverAG som en pre-åtgärd i den första gruppen av återställningsplanen.

1. Följ anvisningarna i skriptet för att skapa ett automation-variabel för att ange namnet på tillgänglighetsgrupperna.

### <a name="step-4-conduct-a-test-failover"></a>Steg 4: Testa redundans

Vissa BCDR-teknik som SQL Always On stöd inte har inbyggt för testning av redundans. Därför rekommenderar vi följande metod **endast när integrering med sådan teknik**:

1. Konfigurera [Azure Backup](../backup/backup-azure-arm-vms.md) på den virtuella datorn som är värd för tillgänglighetsgruppsrepliker i Azure.

1. Återställa den virtuella datorn från säkerhetskopior som gjorts i föregående steg innan du aktiverar redundanstestning av återställningsplanen.

    ![Återställa från Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Framtvinga ett kvorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) på den virtuella datorn återställs från en säkerhetskopia.

1. Uppdatera IP-Adressen för lyssnaren till en IP-adress som är tillgängliga i nätverket för redundanstestet.

    ![Uppdatera lyssnare IP](./media/site-recovery-sql/update-listener-ip.png)

1. Ta med lyssnaren online.

    ![Anslut lyssnare](./media/site-recovery-sql/bring-listener-online.png)

1. Skapa en belastningsutjämnare med en IP som skapats under frontend IP-poolen som motsvarar varje tillgänglighetsgruppens lyssnare och med SQL-dator som har lagts till i serverdelspoolen.

     ![Skapa belastningsutjämnare – Frontend IP-pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Skapa belastningsutjämnare – serverdelspool](./media/site-recovery-sql/create-load-balancer2.png)

1. Lägga till redundans för programnivån, följt av webbnivån i den här återställningsplanen i efterföljande recovery grupper. 
1. Göra en redundanstestning av återställningsplan för att testa redundans för slutpunkt till slutpunkt av programmet.

## <a name="steps-to-do-a-failover"></a>Hur du gör en redundansväxling

När du har lagt till skriptet i återställningsplanen i steg3 och verifieras genom att göra ett redundanstest med en särskild metod i steg 4, kan du göra redundans för återställningsplan som skapades i steg3.

Observera att redundans stegen för att program och web nivåerna ska vara samma i både redundanstest och återställningsplaner för redundans.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Hur du skyddar en SQL Server-kluster (standard edition/SQL Server 2008 R2)

För ett kluster som kör SQL Server Standard edition eller SQL Server 2008 R2, rekommenderar vi du använder Site Recovery-replikering för att skydda SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure till Azure och lokalt till Azure

Site Recovery ger inte gäst klusterstöd när du replikerar till en Azure-region. SQL Server även ger inte en haveriberedskapslösning med låg kostnad för Standard edition. I det här scenariot rekommenderar vi du skydda SQL Server-klustret till en fristående SQL Server på primär plats och återställa den i sekundärt.

1. Konfigurera en ytterligare fristående SQL Server-instansen på den primära Azure-regionen eller på den lokala platsen.
1. Konfigurera en instans som fungerar som en spegling för databaserna som du vill skydda. Konfigurera spegling i läget för hög säkerhet.
1. Konfigurera Site Recovery på den primära platsen ([Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md) eller [VMware-datorer/fysiska servrar)](site-recovery-vmware-to-azure-classic.md).
1. Använd Site Recovery-replikering för att replikera den nya SQL Server-instansen till sekundär plats. Eftersom det är en hög säkerhet speglingskopia kommer att synkroniseras med det primära klustret, men det kommer att replikeras med hjälp av Site Recovery-replikering.


![Standard-kluster](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Överväganden för återställning efter fel

SQL Server Standard-kluster kan återställning efter fel efter en oplanerad redundans kräver en SQL server-säkerhetskopiering och återställning, från spegling-instans till det ursprungliga klustret med reestablishment av spegeln.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>Hur SQL hämta licensieras när skyddade med Azure Site Recovery?
Azure Site Recovery-replikering för SQL Server ingår i Disaster Recovery Software Assurance-förmånen, för alla Azure Site Recovery-scenarier (lokalt till Azure-haveriberedskap eller Azure IaaS-haveriberedskap mellan regioner). [Läs mer](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Azure Site Recovery stöder min SQL-version?
Azure Site Recovery är oberoende av programmet. Alla versioner av SQLServer som har distribuerats på ett operativsystem kan därför skyddas av Azure Site Recovery. [Läs mer](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>Nästa steg
* [Läs mer](site-recovery-components.md) om Site Recovery-arkitekturen.
* För SQL-servrar i Azure, Lär dig mer om [lösningar för hög tillgänglighet](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) för återställning i sekundär Azure-region.
* För SQL-databas i Azure, Lär dig mer om den [affärskontinuitet](../sql-database/sql-database-business-continuity.md) och [hög tillgänglighet](../sql-database/sql-database-high-availability.md) alternativ för återställning i sekundär Azure-region.
* För datorer med SQL server på lokalt, [mer](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) om alternativ för hög tillgänglighet för återställning i Azure Virtual Machines.
