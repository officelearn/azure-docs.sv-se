---
title: Replikera program med SQL Server och Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du replikera SQL Server med Azure Site Recovery för SQL Server-funktioner för haveriberedskap.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/22/2018
ms.author: raynew
ms.openlocfilehash: a734999f0fa473b5f1ee8ed55c4b73950566490d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188781"
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Skydda SQL Server med hjälp av SQL Server-haveriberedskap och Azure Site Recovery

Den här artikeln beskriver hur du skyddar SQL Server-serverdelen för ett program med hjälp av en kombination av SQL Server-affärskontinuitet och disaster recovery (BCDR)-teknik och [Azure Site Recovery](site-recovery-overview.md).

Innan du börjar bör du kontrollera att du förstår SQL Server haveriberedskap, bland annat redundanskluster, Always On-Tillgänglighetsgrupper, databasspegling och loggöverföring.


## <a name="sql-server-deployments"></a>SQL Server-distributioner

Många arbetsbelastningar använder SQL Server som grund och den kan integreras med appar som SharePoint, Dynamics och SAP, för att implementera datatjänster.  SQL Server kan distribueras på flera olika sätt:

* **Fristående SQL Server**: SQL Server och alla databaser finns på en enskild dator (fysisk eller en virtuell). När virtualiserade, används värd klustring för hög tillgänglighet lokalt. På gästnivå hög tillgänglighet är inte implementerad.
* **SQL Server Failover Clustering-instanser (alltid på FCI)**: två eller flera noder som kör SQL Server instanced med delade diskar konfigureras i ett redundanskluster i Windows. Om en nod är nere kan kan klustret redundansväxla SQL Server till en annan instans. Den här konfigurationen används vanligtvis för att implementera hög tillgänglighet på en primär plats. Den här distributionen skydda inte mot strömavbrott eller avbrott i lagret delad lagring. En delad disk kan implementeras med hjälp av iSCSI, fibre channel eller delade vhdx.
* **SQL Always On-Tillgänglighetsgrupper**: två eller flera noder ställs in i en delad inget kluster med SQL Server-databaser som konfigurerats i en tillgänglighetsgrupp med synkron replikering och automatisk redundans.

 Den här artikeln använder följande inbyggda SQL disaster recovery tekniker för att återställa databaser till en fjärrplats:

* SQL Always On-Tillgänglighetsgrupper förse för katastrofåterställning för SQL Server 2012 eller 2014 Enterprise-versioner.
* SQL databasspegling i läget för hög säkerhet för SQL Server Standard edition (alla versioner) eller SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Site Recovery-stöd

### <a name="supported-scenarios"></a>Scenarier som stöds
Site Recovery kan skydda SQL Server som sammanfattas i tabellen.

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Ja | Ja
**Azure**|Ej tillämpligt| Ja

### <a name="supported-sql-server-versions"></a>SQL Server-versioner som stöds
De här SQL Server-versioner som stöds för scenarierna som stöds:

* SQL Server 2016 Enterprise och Standard
* SQL Server 2014 Enterprise och Standard
* SQL Server 2012 Enterprise och Standard
* SQL Server 2008 R2 Enterprise och Standard

### <a name="supported-sql-server-integration"></a>Stöds SQL Server-integration

Site Recovery kan integreras med SQL Server BCDR-teknik som sammanfattas i tabellen för att tillhandahålla en lösning för haveriberedskap.

**Funktion** | **Detaljer** | **SQL Server** |
--- | --- | ---
**Always On-tillgänglighetsgrupp** | Flera fristående instanser av SQL Server körs i ett redundanskluster med flera noder.<br/><br/>Databaser kan grupperas i redundansgrupper som kan kopieras (speglad) på SQL Server-instanser så att ingen delad lagring krävs.<br/><br/>Erbjuder haveriberedskap mellan en primär plats och en eller flera sekundära platser. Två noder kan ställas in i en delad inget kluster med SQL Server-databaser som konfigurerats i en tillgänglighetsgrupp med synkron replikering och automatisk redundans. | SQL Server 2016, SQL Server 2014 och SQL Server 2012 Enterprise edition
**Failover-kluster (alltid på FCI)** | SQL Server använder Windows-redundanskluster för hög tillgänglighet för en lokal SQL Server-arbetsbelastningar.<br/><br/>Noder som kör instanser av SQL Server med delade diskar konfigureras i ett failover-kluster. Om en instans inte är igång klustret som redundansväxlar till annan.<br/><br/>Klustret skydda inte mot strömavbrott eller avbrott i den delade lagringen. Den delade disken kan implementeras med iSCSI, fibre channel, eller delat vhdx-diskar. | SQL Server Enterprise-utgåvor<br/><br/>SQL Server Standard edition (begränsat till två noder)
**Databasspegling (hög säkerhet läge)** | Skyddar en enkel databas på en sekundär kopia. Tillgängliga i både hög säkerhet (synkron) och höga prestanda (asynkrona) replikeringslägen. Du behöver ett redundanskluster. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise alla utgåvor
**Fristående SQL Server** | SQL-servern och databasen finns på en enskild server (fysisk eller virtuell). Värden klustring används för hög tillgänglighet om servern är virtuell. Ingen hög tillgänglighet på gästnivå. | Enterprise eller Standard edition

## <a name="deployment-recommendations"></a>Distributionsrekommendationer

Den här tabellen sammanfattas våra rekommendationer för integrering av SQL Server BCDR-teknik med Site Recovery.

| **Version** | **Utgåva** | **Distribution** | **Lokal till lokal** | **Lokal till Azure** |
| --- | --- | --- | --- | --- |
| SQLServer 2016, 2014 eller 2012 |Enterprise |Redundansklusterinstans |Always On-Tillgänglighetsgrupper |Always On-Tillgänglighetsgrupper |
|| Enterprise |Always On-Tillgänglighetsgrupper för hög tillgänglighet |Always On-Tillgänglighetsgrupper |Always On-Tillgänglighetsgrupper | |
|| Standard |Redundansklusterinstans (FCI) |Site Recovery-replikering med lokala spegling |Site Recovery-replikering med lokala spegling | |
|| Enterprise eller Standard |Fristående |Site Recovery-replikering |Site Recovery-replikering | |
| SQL Server 2008 R2 eller 2008 |Enterprise eller Standard |Redundansklusterinstans (FCI) |Site Recovery-replikering med lokala spegling |Site Recovery-replikering med lokala spegling |
|| Enterprise eller Standard |Fristående |Site Recovery-replikering |Site Recovery-replikering | |
| SQL Server (alla versioner) |Enterprise eller Standard |Redundansklusterinstans - DTC-program |Site Recovery-replikering |Stöds inte |

## <a name="deployment-prerequisites"></a>Distributionskrav

* En lokal SQL Server-distributionen, kör en SQL Server-version som stöds. Vanligtvis måste du också Active Directory för SQL-servern.
* Kraven för scenariot som du vill distribuera. Mer information om kraven för stöd för [replikering till Azure](site-recovery-support-matrix-to-azure.md) och [lokala](site-recovery-support-matrix.md), och [distributionskraven](site-recovery-prereq.md).

## <a name="set-up-active-directory"></a>Konfigurera Active Directory

Konfigurera Active Directory i den sekundära platsen, för SQL Server ska fungera korrekt.

* **Små företag**– med ett litet antal program och en enda domänkontrollant för den lokala platsen, om du vill växla över hela platsen, rekommenderar vi du använder Site Recovery-replikering för att replikera domänkontrollanten till sekundärt datacenter, eller till Azure.
* **Mellanstora till stora företag**– om du har ett stort antal program, en Active Directory-skog och du vill växla över av program eller en arbetsbelastning, vi rekommenderar att du ställer in ytterligare en domänkontrollant i det sekundära datacentret eller i Azure. Om du använder Always On-Tillgänglighetsgrupper för att återställa till en fjärrplats, rekommenderar vi att du ställer in en annan ytterligare en domänkontrollant på den sekundära platsen eller i Azure, för den återställda SQL Server-instansen.

Anvisningarna i den här artikeln förutsätter att en domänkontrollant är tillgänglig på den sekundära platsen. [Läs mer](site-recovery-active-directory.md) om hur du skyddar Active Directory med Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integrera med SQL Server Always On för replikering till Azure

Här är vad du behöver göra:

1. Importera skript till ditt Azure Automation-konto. Innehåller skript att redundansväxla SQL-tillgänglighetsgrupp i en [Resource Manager-dator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) och en [klassisk virtuell dator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Lägg till ASR-SQL-FailoverAG som en pre-åtgärd i den första gruppen av återställningsplanen.

1. Följ anvisningarna i skriptet för att skapa ett automation-variabel för att ange namnet på tillgänglighetsgrupperna.

### <a name="steps-to-do-a-test-failover"></a>Steg för att göra ett redundanstest

SQL Always On stöd inte har inbyggt för testning av redundans. Därför rekommenderar vi följande:

1. Konfigurera [Azure Backup](../backup/backup-azure-arm-vms.md) på den virtuella datorn som är värd för tillgänglighetsgruppsrepliker i Azure.

1. Återställa den virtuella datorn från säkerhetskopior som gjorts i föregående steg innan du aktiverar redundanstestning av återställningsplanen.

    ![Återställa från Azure Backup ](./media/site-recovery-sql/restore-from-backup.png)

1. [Framtvinga ett kvorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) på den virtuella datorn återställs från en säkerhetskopia.

1. Uppdatera IP-Adressen för lyssnaren till en IP-adress som är tillgängliga i nätverket för redundanstestet.

    ![Uppdatera lyssnare IP](./media/site-recovery-sql/update-listener-ip.png)

1. Ta med lyssnaren online.

    ![Anslut lyssnare](./media/site-recovery-sql/bring-listener-online.png)

1. Skapa en belastningsutjämnare med en IP som skapats under frontend IP-poolen som motsvarar varje tillgänglighetsgruppens lyssnare och med SQL-dator som har lagts till i serverdelspoolen.

     ![Skapa belastningsutjämnare – Frontend IP-pool ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Skapa belastningsutjämnare – serverdelspool ](./media/site-recovery-sql/create-load-balancer2.png)

1. Gör ett redundanstest av återställningsplanen.

### <a name="steps-to-do-a-failover"></a>Hur du gör en redundansväxling

När du har lagt till skriptet i återställningsplanen och verifierats återställningsplanen genom att göra ett redundanstest, kan du göra redundans för återställningsplanen.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integrera med SQL Server Always On för replikering till en sekundär lokal plats

Om SQL Server använder Tillgänglighetsgrupper för hög tillgänglighet (eller en FCI), bör du använda Tillgänglighetsgrupper på återställningsplatsen samt. Observera att detta gäller för appar som inte använder distribuerade transaktioner.

1. [Konfigurera databaserna](https://msdn.microsoft.com/library/hh213078.aspx) i Tillgänglighetsgrupper.
1. Skapa ett virtuellt nätverk på den sekundära platsen.
1. Konfigurera en plats-till-plats VPN-anslutning mellan det virtuella nätverket och den primära platsen.
1. Skapa en virtuell dator på återställningsplatsen och installera SQL Server på den.
1. Utöka de befintliga Always On-tillgänglighetsgrupperna till den nya SQL Server-dator. Konfigurera SQL Server-instansen som en asynkron replikering kopia.
1. Skapa en tillgänglighetsgruppslyssnare eller uppdatera den befintliga lyssnaren för att inkludera den asynkrona replikerade virtuella datorn.
1. Se till att programmet servergruppen är konfigurerad med lyssnaren. Om den har konfigurerats med Databasservernamnet, uppdatera den om du vill använda lyssnare, så du inte behöver konfigurera om den efter redundansen.

För program som använder distribuerade transaktioner, rekommenderar vi att du distribuerar Site Recovery med [VMware/fysisk serverreplikering för plats-till-plats](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Planera överväganden
1. Lägg till det här exempelskriptet till VMM-biblioteket på primära och sekundära platser.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. När du skapar en återställningsplan för programmet, kan du lägga till en pre-åtgärd till grupp-1 skriptade steg som anropar skriptet att växla över Tillgänglighetsgrupper.

## <a name="protect-a-standalone-sql-server"></a>Skydda en fristående SQL Server

I det här scenariot rekommenderar vi att du använder Site Recovery-replikering för att skydda SQL Server-datorn. De exakta stegen beror om SQL Server är en virtuell dator eller en fysisk server, och om du vill replikera till Azure eller en sekundär lokal plats. Lär dig mer om [Site Recovery-scenarier](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Skydda SQL Server-kluster (standard edition och Windows Server 2008 R2)

För ett kluster som kör SQL Server Standard edition eller SQL Server 2008 R2, rekommenderar vi du använder Site Recovery-replikering för att skydda SQL Server.

### <a name="on-premises-to-on-premises"></a>Lokal till lokal

* Om appen använder distribuerade transaktioner rekommenderar vi att du distribuerar [Site Recovery med SAN-replikering](site-recovery-vmm-san.md) för en Hyper-V-miljö eller [VMware/fysiska servern till VMware](site-recovery-vmware-to-vmware.md) för en VMware-miljö.
* För icke-DTC-program kan du använda den senare metoden för att återställa klustret som en fristående server genom att använda en lokal hög säkerhet DB spegling.

### <a name="on-premises-to-azure"></a>Lokalt till Azure

Site Recovery ger inte gäst klusterstöd vid replikering till Azure. SQL Server även ger inte en haveriberedskapslösning med låg kostnad för Standard edition. I det här scenariot rekommenderar vi du skyddar en lokal SQL Server-klustret till en fristående SQL Server och återställa den i Azure.

1. Konfigurera en ytterligare fristående SQL Server-instansen på den lokala platsen.
1. Konfigurera en instans som fungerar som en spegling för databaserna som du vill skydda. Konfigurera spegling i läget för hög säkerhet.
1. Konfigurera Site Recovery på den lokala platsen för ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) eller [VMware-datorer/fysiska servrar)](site-recovery-vmware-to-azure-classic.md).
1. Använd Site Recovery-replikering för att replikera den nya SQL Server-instansen till Azure. Eftersom det är en hög säkerhet speglingskopia kommer att synkroniseras med det primära klustret, men det kommer att replikeras till Azure med Site Recovery-replikering.


![Standard-kluster](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Överväganden för återställning efter fel

SQL Server Standard-kluster kan återställning efter fel efter en oplanerad redundans kräver en SQL server-säkerhetskopiering och återställning, från spegling-instans till det ursprungliga klustret med reestablishment av spegeln.

## <a name="next-steps"></a>Nästa steg
[Läs mer](site-recovery-components.md) om Site Recovery-arkitekturen.
