---
title: Duplicera program med SQL Server och Azure Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hur du replikerar SQL Server med Azure Site Recovery för SQL Server-funktioner för katastrofåterställning."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: e53f60979e01a0eabe118d3ae6457a61bd4b0ded
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Skydda SQL Server med SQL Server-katastrofåterställning och Azure Site Recovery

Den här artikeln beskriver hur du skyddar SQL Server-serverdelen av ett program som använder en kombination av SQL Server-affärskontinuitet och disaster recovery (BCDR)-teknik och [Azure Site Recovery](site-recovery-overview.md).

Innan du börjar, kontrollera att du förstår SQL Server funktioner för katastrofåterställning, inklusive redundanskluster, Always On-Tillgänglighetsgrupper, databasspegling och loggöverföring.


## <a name="sql-server-deployments"></a>SQL Server-distributioner

Många arbetsbelastningar använder SQL Server som grund och den kan integreras med appar som SharePoint, Dynamics och SAP, för att implementera datatjänster.  SQL Server kan distribueras i flera olika sätt:

* **Fristående SQL Server**: SQL Server och alla databaser som finns på en enskild dator (fysisk eller virtuell). När en virtualiserad, används värden klustring för lokal hög tillgänglighet. Gästnivå hög tillgänglighet är inte implementerad.
* **SQL Server Failover Clustering instanser (alltid på FCI)**: två eller flera noder som kör SQL Server instanced med delade diskar som är konfigurerade i ett redundanskluster i Windows. Om en nod är nere kan kan klustret redundansväxla SQL Server till en annan instans. Den här inställningen används vanligtvis för att implementera hög tillgänglighet på en primär plats. Den här distributionen skyddar inte mot fel eller avbrott i lagret för delad lagring. En delad disk kan implementeras med hjälp av iSCSI, fibre channel eller delad vhdx.
* **SQL Always On-Tillgänglighetsgrupper**: två eller flera noder som har ställts in i en delad inget kluster med SQL Server-databaser som konfigurerats i en tillgänglighetsgrupp med synkron replikering och automatisk redundans.

 Den här artikeln använder följande interna SQL disaster recovery tekniker för att återställa databaser till en fjärrplats:

* SQL Always On-Tillgänglighetsgrupper för katastrofåterställning för att SQL Server 2012 eller 2014 Enterprise-utgåvor.
* SQL databasspegling i läget för hög säkerhet för SQL Server Standard edition (alla versioner) eller för SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Site Recovery-stöd

### <a name="supported-scenarios"></a>Scenarier som stöds
Site Recovery kan skydda SQL Server som sammanfattas i tabellen.

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Ja | Ja

### <a name="supported-sql-server-versions"></a>SQL Server-versioner som stöds
De här SQL Server-versioner som stöds för scenarierna som stöds:

* SQL Server 2016 Enterprise och Standard
* SQL Server 2014 Enterprise och Standard
* SQL Server 2012 Enterprise och Standard
* SQL Server 2008 R2 Enterprise och Standard

### <a name="supported-sql-server-integration"></a>Stöds SQL Server-integration

Site Recovery kan integreras med SQL Server BCDR-teknik som sammanfattas i tabellen för att ge en lösning för katastrofåterställning.

**Funktion** | **Detaljer** | **SQL Server** |
--- | --- | ---
**Always On-tillgänglighetsgrupp** | Flera fristående instanser av SQL Server körs i ett redundanskluster med flera noder.<br/><br/>Databaser kan grupperas i failover-grupper som kan kopieras (speglad) på SQL Server-instanser så att ingen delad lagring krävs.<br/><br/>Ger katastrofåterställning mellan en primär plats och en eller flera sekundära platser. Två noder kan ställas in i en delad inget kluster med SQL Server-databaser som konfigurerats i en tillgänglighetsgrupp med synkron replikering och automatisk redundans. | SQL Server 2014 & 2012 Enterprise edition
**Failover-kluster (alltid på FCI)** | SQL Server använder Windows-redundanskluster för hög tillgänglighet för lokala SQL Server-arbetsbelastningar.<br/><br/>Noder som kör instanser av SQL Server med delade diskar har konfigurerats i ett redundanskluster. Om en instans som är igång flyttas klustret över till annan.<br/><br/>Klustret skyddar inte mot fel eller avbrott i den delade lagringen. Den delade disken kan implementeras med iSCSI, fibre channel eller delade vhdx-diskar. | SQL Server Enterprise Edition<br/><br/>SQL Server Standard edition (begränsat till två noder)
**Databasspegling (hög säkerhet läge)** | Skyddar en databas på en sekundär kopia. I båda hög säkerhet (synkron) och hög prestanda (asynkron) replikering lägen. Kräver inte ett failover-kluster. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise alla versioner
**Fristående SQL Server** | SQL-servern och databasen finns på en enskild server (fysisk eller virtuell). Om servern är virtuell används värden kluster för hög tillgänglighet. Inga gästnivå hög tillgänglighet. | Enterprise eller Standard edition

## <a name="deployment-recommendations"></a>Distributionsrekommendationer

Den här tabellen sammanfattas våra rekommendationer för integrering av SQL Server BCDR-teknik med Site Recovery.

| **Version** | **Utgåva** | **Distribution** | **Lokal till lokal** | **Lokal till Azure** |
| --- | --- | --- | --- | --- |
| SQLServer 2014 eller 2012 |Enterprise |Redundansklusterinstans |Always On-Tillgänglighetsgrupper |Always On-Tillgänglighetsgrupper |
|| Enterprise |Always On-Tillgänglighetsgrupper för hög tillgänglighet |Always On-Tillgänglighetsgrupper |Always On-Tillgänglighetsgrupper | |
|| Standard |Redundansklusterinstans (FCI) |Site Recovery replikering med lokala spegling |Site Recovery replikering med lokala spegling | |
|| Enterprise eller Standard |Fristående |Site Recovery replikering |Site Recovery replikering | |
| SQL Server 2008 R2 eller 2008 |Enterprise eller Standard |Redundansklusterinstans (FCI) |Site Recovery replikering med lokala spegling |Site Recovery replikering med lokala spegling |
|| Enterprise eller Standard |Fristående |Site Recovery replikering |Site Recovery replikering | |
| SQL Server (alla versioner) |Enterprise eller Standard |Redundansklusterinstans - DTC-program |Site Recovery replikering |Stöds inte |

## <a name="deployment-prerequisites"></a>Distributionskrav

* En lokal SQL Server-distributionen, en SQL Server-version som stöds. Vanligtvis behöver du också Active Directory för SQLServer.
* Kraven för scenariot som du vill distribuera. Mer information om kraven för stöd för [replikering till Azure](site-recovery-support-matrix-to-azure.md) och [lokalt](site-recovery-support-matrix.md), och [kraven för distribution av](site-recovery-prereq.md).
* Ställ in återställning i Azure genom att köra den [Azure virtuella Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) verktyget på SQL Server virtuella datorer, se till att de är kompatibla med Azure och Site Recovery.

## <a name="set-up-active-directory"></a>Konfigurera Active Directory

Konfigurera Active Directory i den sekundära återställningsplatsen för SQL Server ska fungera korrekt.

* **Litet företag**– med ett litet antal program och en enda domänkontrollant för den lokala platsen, om du vill växla över hela platsen, rekommenderar vi du använder Site Recovery replikering för att replikera domänkontrollanten till sekundärt datacenter eller till Azure.
* **Medelstora till stora företag**– om du har ett stort antal program, en Active Directory-skog och du vill växla över program eller arbetsbelastning, vi rekommenderar att du konfigurerar ytterligare en domänkontrollant i sekundärt datacenter eller i Azure. Om du använder Always On-Tillgänglighetsgrupper för att återställa till en fjärrplats rekommenderar vi att du ställer in en annan ytterligare en domänkontrollant på den sekundära platsen eller i Azure, ska användas för den återställda SQL Server-instansen.

Anvisningarna i den här artikeln förutsätter att en domänkontrollant är tillgänglig på den sekundära platsen. [Läs mer](site-recovery-active-directory.md) om att skydda Active Directory med Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integreras med SQL Server alltid aktiverad för replikering till Azure

Här är vad du behöver göra:

1. Importera skript till Azure Automation-konto. Innehåller skripten till SQL-tillgänglighetsgrupp-redundans i en [Resource Manager virtuella](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) och en [klassiska virtuella](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Lägg till ASR-SQL-FailoverAG som en pre-åtgärd i den första gruppen i återställningsplanen.

1. Följ instruktionerna i skriptet för att skapa ett automation-variabel för att ange namnet på tillgänglighetsgrupperna.

### <a name="steps-to-do-a-test-failover"></a>Gör ett redundanstest

SQL Always On inte har inbyggt stöd testa redundans. Därför rekommenderar vi följande:

1. Ställ in [Azure Backup](../backup/backup-azure-vms.md) på den virtuella datorn som är värd för tillgänglighetsgruppsrepliker i Azure.

1. Innan du utlöser test av redundansväxling återställningsplanen kan du återställa den virtuella datorn från säkerhetskopior som gjorts i föregående steg.

    ![Återställa från Azure-säkerhetskopiering ](./media/site-recovery-sql/restore-from-backup.png)

1. [Framtvinga ett kvorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) på den virtuella datorn som återställts från säkerhetskopian.

1. Uppdatera IP för lyssnare till en IP-adress som är tillgängliga i nätverket.

    ![Uppdatera lyssnare IP](./media/site-recovery-sql/update-listener-ip.png)

1. Skapa lyssnare online.

    ![Anslut lyssnare](./media/site-recovery-sql/bring-listener-online.png)

1. Skapa en belastningsutjämnare med en IP skapade under klientdelens IP-poolen som motsvarar varje tillgänglighetsgruppens lyssnare och SQL-dator som lagts till i serverdelspoolen.

     ![Skapa belastningsutjämnaren - Frontend-IP-pool ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Skapa belastningsutjämnaren - serverdelspool ](./media/site-recovery-sql/create-load-balancer2.png)

1. Göra ett redundanstest för återställningsplanen.

### <a name="steps-to-do-a-failover"></a>Hur du gör en växling vid fel

När du har lagt till skriptet i återställningsplanen och verifiera återställningsplanen genom att göra ett redundanstest, kan du göra växling vid fel i återställningsplanen.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integreras med SQL Server alltid aktiverad för replikering till en sekundär lokal plats

Om SQL Server använder Tillgänglighetsgrupper för hög tillgänglighet (eller en FCI), bör du använda Tillgänglighetsgrupper på webbplatsen återställning. Observera att detta gäller för appar som inte använder distribuerade transaktioner.

1. [Konfigurera databaserna för](https://msdn.microsoft.com/library/hh213078.aspx) i Tillgänglighetsgrupper.
1. Skapa ett virtuellt nätverk på den sekundära platsen.
1. Konfigurera en plats-till-plats VPN-anslutning mellan det virtuella nätverket och den primära platsen.
1. Skapa en virtuell dator på återställningsplatsen och installera SQL Server på den.
1. Utöka de befintliga Always On-tillgänglighetsgrupperna till den nya SQL Server-VM. Konfigurera SQL Server-instansen som en asynkron replikering kopia.
1. Skapa en tillgänglighetsgruppslyssnare eller uppdatera den befintliga lyssnaren för att inkludera den asynkrona replikerade virtuella datorn.
1. Kontrollera att programmet servergruppen har ställts in i lyssnaren. Om den har konfigurerats med Databasservernamnet, uppdatera den om du vill använda lyssnare, så du inte behöver konfigurera om den efter växling vid fel.

För program som använder distribuerade transaktioner, rekommenderar vi du distribuerar Site Recovery med [VMware/fysisk server plats-till-plats-replikering](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Planera överväganden
1. Lägg till det här exempelskriptet till VMM-biblioteket på primära och sekundära platser.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. När du skapar en återställningsplan för programmet, kan du lägga till en pre-åtgärd i grupp 1 skripta steg som anropar skriptet för att växla över Tillgänglighetsgrupper.

## <a name="protect-a-standalone-sql-server"></a>Skydda en fristående SQL Server

I det här scenariot rekommenderar vi att du använder Site Recovery replikering för att skydda SQL Server-datorn. Åtgärder som krävs beror på om SQL Server är en virtuell dator eller en fysisk server, och om du vill replikera till Azure eller en sekundär lokal plats. Lär dig mer om [Site Recovery scenarier](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Skydda SQL Server-kluster (standard edition och Windows Server 2008 R2)

För ett kluster som kör SQL Server Standard edition eller SQL Server 2008 R2, rekommenderar vi att du använder Site Recovery replikering för att skydda SQL Server.

### <a name="on-premises-to-on-premises"></a>Lokal till lokal

* Om appen använder distribuerade transaktioner vi rekommenderar att du distribuerar [Site Recovery med SAN-replikering](site-recovery-vmm-san.md) för en Hyper-V-miljö eller [VMware/fysisk server till VMware](site-recovery-vmware-to-vmware.md) för en VMware-miljön.
* För icke-DTC-program kan du använda detta tillvägagångssätt kan återställa klustret som en fristående server genom att använda en lokal hög säkerhet DB speglingen.

### <a name="on-premises-to-azure"></a>Lokalt till Azure

Site Recovery inte ger gäst klusterstöd när du replikerar till Azure. SQL Server ge inte också en lösning för katastrofåterställning för låg kostnad för Standard edition. I det här scenariot rekommenderar vi du skydda lokala SQL Server-kluster till fristående SQL Server och återställa den i Azure.

1. Konfigurera en ytterligare fristående SQL Server-instansen på den lokala platsen.
1. Konfigurera instansen som ska fungera som en spegling för databaser som du vill skydda. Konfigurera spegling i läget för hög säkerhet.
1. Konfigurera Site Recovery på den lokala platsen för ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) eller [VMware virtuella datorer/fysiska servrar)](site-recovery-vmware-to-azure-classic.md).
1. Använd Site Recovery replikering för att replikera den nya SQL Server-instansen till Azure. Eftersom det är en hög säkerhet speglingskopia kommer att synkroniseras med det primära klustret, men kommer att replikeras till Azure med Site Recovery replikering.


![Standard-kluster](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Överväganden för återställning efter fel

För SQL Server Standard kluster återställning efter en oplanerad redundans kräver en SQL server-säkerhetskopiering och återställning, från den speglade-instansen till det ursprungliga klustret med reestablishment för speglingen.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer](site-recovery-components.md) om Site Recovery-arkitekturen.
