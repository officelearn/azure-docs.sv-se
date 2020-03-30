---
title: Konfigurera haveriberedskap för SQL Server med Azure Site Recovery
description: I den hÃ¤r artikeln beskrivs hur du konfigurerar haveriã¤ndeering för SQL Server med hjälp av SQL Server och Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084742"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Konfigurera haveriberedskap för SQL Server

I den hÃ¤r artikeln beskrivs hur du skyddar SQL Server-server-server-server-baksidan av ett program. Du gör det genom att använda en kombination av BCDR-tekniker (SQL Server business continuity and disaster recovery) och [Azure Site Recovery](site-recovery-overview.md).

Innan du börjar, se till att du förstår SQL Server katastrofåterställning kapacitet. Dessa funktioner är:

* Redundansklustring
* Alltid på tillgänglighetsgrupper
* Databasspegling
* Loggöverföring
* Aktiv geo-replikering
* Automatiska redundansgrupper

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Kombinera BCDR-teknik med site recovery

Ditt val av en BCDR-teknik för att återställa SQL Server-instanser bör baseras på dina återställningstidsmål (RTO) och återställningspunktsmål (RPO) som beskrivs i följande tabell. Kombinera Site Recovery med redundans drift av din valda teknik för att orkestrera återställning av hela ditt program.

Distributionstyp | BCDR-teknik | Förväntad RTO för SQL Server | Förväntad RPO för SQL Server |
--- | --- | --- | ---
SQL Server på en Azure-infrastruktur som en virtuell tjänst (IaaS) virtuell dator (VM) eller lokalt.| [Always On-tillgänglighetsgrupp](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | Det tar att göra den sekundära repliken som primär. | Eftersom replikering till den sekundära repliken är asynkron, finns det en viss dataförlust.
SQL Server på en Virtuell Azure IaaS eller lokalt.| [Redundanskluster (alltid på FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Den tid det tar att växla över mellan noderna. | Eftersom Alltid på FCI använder delad lagring är samma vy av lagringsinstansen tillgänglig vid redundans.
SQL Server på en Virtuell Azure IaaS eller lokalt.| [Databasspegling (högpresterande läge)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Den tid det tar att tvinga tjänsten, som använder spegelservern som en varm standby-server. | Replikering är asynkron. Spegeldatabasen kan ligga något efter huvuddatabasen. Fördröjningen är vanligtvis liten. Men det kan bli stort om huvudmannen eller spegelserverns system är under en tung belastning.<br/><br/>Loggfrakt kan vara ett komplement till databasspegling. Det är ett gynnsamt alternativ till asynkron databas spegling.
SQL som plattform som en tjänst (PaaS) på Azure.<br/><br/>Den här distributionstypen innehåller elastiska pooler och Azure SQL Database-servrar. | Aktiv geo-replikering | 30 sekunder efter att redundansen har utlösts.<br/><br/>När redundans aktiveras för en av de sekundära databaserna länkas alla andra sekundärfiler automatiskt till den nya primär. | RPO på fem sekunder.<br/><br/>Aktiv georeplikering använder alltid på-tekniken för SQL Server. Det replikerar asynkront genomförda transaktioner på den primära databasen till en sekundär databas med hjälp av ögonblicksbildisolering.<br/><br/>De sekundära data är garanterat att aldrig ha partiella transaktioner.
SQL som PaaS konfigurerat med aktiv geo-replikering på Azure.<br/><br/>Den här distributionstypen innehåller en SQL Database-hanterad instans, elastiska pooler och SQL Database-servrar. | Automatiska redundansgrupper | RTO på en timme. | RPO på fem sekunder.<br/><br/>Grupper med automatisk redundans ger gruppsenmantiken ovanpå aktiv georeplikering. Men samma asynkrona replikeringsmekanism används.
SQL Server på en Virtuell Azure IaaS eller lokalt.| Replikering med Azure Site Recovery | RTO är vanligtvis mindre än 15 minuter. Mer information finns i [RTO SLA som tillhandahålls av Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | En timme för programkonsekvens och fem minuter för kraschkonsekvens. Om du letar efter lägre RPO, använd andra BCDR-tekniker.

> [!NOTE]
> Några viktiga saker när du hjälper till att skydda SQL-arbetsbelastningar med Site Recovery:
> * Site Recovery är programagnostiker. Site Recovery kan skydda alla versioner av SQL Server som distribueras på ett operativsystem som stöds. Mer information finns i [supportmatrisen för återställning](vmware-physical-azure-support-matrix.md#replicated-machines) av replikerade datorer.
> * Du kan välja att använda Site Recovery för distribution på Azure, Hyper-V, VMware eller fysisk infrastruktur. Följ vägledningen i slutet av den här artikeln om [hur du skyddar ett SQL Server-kluster](#how-to-help-protect-a-sql-server-cluster) med Site Recovery.
> * Kontrollera att den dataförändringshastighet som observerats på maskinen ligger inom [gränserna för platsåterställning](vmware-physical-azure-support-matrix.md#churn-limits). Förändringshastigheten mäts i skrivbyte per sekund. För datorer som kör Windows kan du visa den här ändringshastigheten genom att välja fliken **Prestanda** i Aktivitetshanteraren. Observera skrivhastigheten för varje disk.
> * Site Recovery stöder replikering av Redundansklusterinstanser på Storage Spaces Direct. Mer information finns i [hur du aktiverar Storage Spaces Direct-replikering](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Haveriberedskap för ett program

Site Recovery orkestrerar testet redundans och redundans av hela programmet med hjälp av återställningsplaner.

Det finns vissa förutsättningar för att säkerställa att din återhämtningsplan är helt anpassad efter dina behov. Alla SQL Server-distributioner behöver vanligtvis en Active Directory-distribution. Den behöver också anslutning för din programnivå.

### <a name="step-1-set-up-active-directory"></a>Steg 1: Konfigurera Active Directory

Konfigurera Active Directory på den sekundära återställningsplatsen för SQL Server så att den körs korrekt.

* **Småföretag:** Du har ett litet antal program och en enda domänkontrollant för den lokala platsen. Om du vill växla över hela platsen använder du Replikering av webbplatsåterställning. Den här tjänsten replikerar domänkontrollanten till det sekundära datacentret eller till Azure.
* **Medelstort till stort företag:** Du kan behöva konfigurera ytterligare domänkontrollanter.
  - Om du har ett stort antal program, har en Active Directory-skog och vill växla över av program eller arbetsbelastning konfigurerar du en annan domänkontrollant i det sekundära datacentret eller i Azure.
  -  Om du använder alltid på tillgänglighetsgrupper för att återställa till en fjärrplats konfigurerar du en annan domänkontrollant på den sekundära platsen eller i Azure. Den här domänkontrollanten används för den återställda SQL Server-instansen.

Instruktionerna i den här artikeln förutsätter att en domänkontrollant är tillgänglig på den sekundära platsen. Mer information finns i procedurerna för [att skydda Active Directory med Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Steg 2: Se till att anslutningen med andra nivåer

När databasnivån körs i Azure-regionen för mål, se till att du har anslutning till program- och webbnivåerna. Vidta nödvändiga åtgärder i förväg för att validera anslutningen med test redundans.

Information om hur du kan utforma program för anslutningsöverväganden finns i följande exempel:

* [Utforma ett program för molnkatastrofåterställning](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Elastisk pool Katastrofåterställning strategier](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Steg 3: Samspela med grupper med Alltid på, aktiv geo-replikering och automatisk redundansgrupper

BCDR-tekniker Alltid på, aktiva geo-replikering och automatisk redundansgrupper har sekundära repliker av SQL Server som körs i regionen Mål Azure. Det första steget för programmet redundans är att ange den här repliken som primär. Det här steget förutsätter att du redan har en domänkontrollant i den sekundära. Steget kanske inte är nödvändigt om du väljer att göra en automatisk redundans. Växla över webb- och programnivåerna först när databasens redundans har slutförts.

> [!NOTE]
> Om du har hjälpt till att skydda SQL-datorer med Site Recovery behöver du bara skapa en återställningsgrupp av dessa datorer och lägga till deras redundans i återställningsplanen.

[Skapa en återställningsplan](site-recovery-create-recovery-plans.md) med virtuella datorer på program- och webbnivå. Följande steg visar hur du lägger till redundans på databasnivån:

1. Importera skripten för att växla över SQL-tillgänglighetsgruppen i både en [virtuell resurshanteraredator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) och en [klassisk virtuell dator](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importera skripten till ditt Azure Automation-konto.

    [![Bild av logotypen "Distribuera till Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ASR-SQL-FailoverAG-skriptet som en föråtgärd för den första gruppen i återställningsplanen.

1. Följ instruktionerna i skriptet för att skapa en automatiseringsvariabel. Den här variabeln innehåller namnet på tillgänglighetsgrupperna.

### <a name="step-4-conduct-a-test-failover"></a>Steg 4: Genomföra en test redundans

Vissa BCDR-tekniker som SQL Always On stöder inte test redundans internt. Vi rekommenderar följande metod *endast när du använder sådan teknik*.

1. Konfigurera [Azure Backup](../backup/backup-azure-arm-vms.md) på den virtuella datorn som är värd för tillgänglighetsgrupprepliken i Azure.

1. Innan du utlöser test redundans för återställningsplanen återställer du den virtuella datorn från säkerhetskopian som togs i föregående steg.

    ![Skärmbild som visar fönster för att återställa en konfiguration från Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Tvinga kvorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) i den virtuella datorn som återställdes från säkerhetskopiering.

1. Uppdatera IP-adressen för lyssnaren så att den blir en adress som är tillgänglig i testundanundannätverket.

    ![Skärmbild av regelfönster och dialogrutan EGENSKAPER för IP-adress](./media/site-recovery-sql/update-listener-ip.png)

1. Ta med lyssnaren online.

    ![Skärmbild av fönster med etiketten Content_AG som visar servernamn och status](./media/site-recovery-sql/bring-listener-online.png)

1. Kontrollera att belastningsutjämnaren i redundansnätverket har en IP-adress, från ip-adresspoolen som motsvarar varje tillgänglighetsgruppavlyssnare och med SQL Server-datorn i backend-poolen.

     ![Skärmbild av fönstret med titeln "SQL-AlwaysOn-LB - Frontend IP Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Skärmbild av fönstret med titeln "SQL-AlwaysOn-LB - Backend IP Pool](./media/site-recovery-sql/create-load-balancer2.png)

1. I senare återställningsgrupper lägger du till redundans för programnivån följt av webbnivån för den här återställningsplanen.

1. Gör en testväxling av återställningsplanen för att testa end-to-end redundans av ditt program.

## <a name="steps-to-do-a-failover"></a>Steg för att göra en redundans

När du har lagt till skriptet i steg 3 och validerat det i steg 4 kan du göra en redundansväxling av återställningsplanen som skapats i steg 3.

Redundansstegen för program- och webbnivåer bör vara desamma i både test redundans- och redundansökningsplaner.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Skydda ett SQL Server-kluster

För ett kluster som kör SQL Server Standard edition eller SQL Server 2008 R2 rekommenderar vi att du använder Site Recovery Replication för att skydda SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure till Azure och lokalt till Azure

Site Recovery ger inte support för gästkluster när du replikerar till en Azure-region. SQL Server Standard Edition ger inte heller en billig katastrofåterställningslösning. I det här fallet rekommenderar vi att du skyddar SQL Server-klustret till en fristående SQL Server-instans på den primära platsen och återställa den i den sekundära.

1. Konfigurera ytterligare en fristående SQL Server-instans i den primära Azure-regionen eller på lokal plats.

1. Konfigurera instansen så att den fungerar som en spegel för de databaser som du vill skydda. Konfigurera spegling i högsäkerhetsläge.

1. Konfigurera platsåterställning på den primära platsen för virtuella [Azure-, Hyper-V-](site-recovery-hyper-v-site-to-azure.md)eller [VMware-datorer och fysiska servrar](site-recovery-vmware-to-azure-classic.md). [Azure](azure-to-azure-tutorial-enable-replication.md)

1. Använd Site Recovery Replication för att replikera den nya SQL Server-instansen till den sekundära platsen. Eftersom det är en spegelkopia med hög säkerhet synkroniseras den med det primära klustret men replikeras med hjälp av Site Recovery-replikering.

   ![Avbildning av ett standardkluster som visar relationen och flödet mellan en primär plats, Platsåterställning och Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Överväganden om återgång

För SQL Server Standard-kluster kräver återställning efter en oplanerad redundans en SQL Server-säkerhetskopiering och återställning. Den här åtgärden utförs från speglingsinstansen till det ursprungliga klustret med ometablering av spegeln.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Hur licensieras SQL Server när den används med Site Recovery?

Site Recovery replikering för SQL Server omfattas av Software Assurance katastrofåterställning fördel. Den här täckningen gäller alla scenarier för webbplatsåterställning: lokal för Azure-haveriberedskap och azure IaaS-haveriberedskap mellan regioner. Se [Azure Site Recovery priser](https://azure.microsoft.com/pricing/details/site-recovery/) för mer.

### <a name="will-site-recovery-support-my-sql-server-version"></a>Stöder Site Recovery min SQL Server-version?

Site Recovery är programagnostiker. Site Recovery kan skydda alla versioner av SQL Server som distribueras på ett operativsystem som stöds. Mer information finns i [supportmatrisen för återställning](vmware-physical-azure-support-matrix.md#replicated-machines) av replikerade datorer.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [arkitekturen Webbplatsåterställning](site-recovery-components.md).
* För SQL Server i Azure kan du läsa mer om lösningar med [hög tillgänglighet](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) för återställning i en sekundär Azure-region.
* För SQL Database kan du läsa mer om [affärskontinuitet](../sql-database/sql-database-business-continuity.md) och alternativ för [hög tillgänglighet](../sql-database/sql-database-high-availability.md) för återställning i en sekundär Azure-region.
* För SQL Server-datorer lokalt kan du läsa mer om de [högtillgängliga alternativen](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) för återställning i virtuella Azure-datorer.
