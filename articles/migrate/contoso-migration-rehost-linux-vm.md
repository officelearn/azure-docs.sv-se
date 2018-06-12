---
title: Rehost-migrera och rehost en lokal Linux-app på Azure Virtual Machines | Microsoft Docs
description: Lär dig hur Contoso rehost en lokal Linux-app genom att migrera till Azure virtuella datorer.
services: site-recovery
author: rayne-wiselman
manager: ''
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 85c377c0fbca32a33cc68c9a1bedfa00ce0a366a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300382"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Contoso-migrering: Rehost en lokal Linux-app på virtuella Azure-datorer

Den här artikeln visar hur Contoso rehosting en lokal Linux-baserade tjänsten supportavdelningen app (**osTicket**), till Azure IaaS-VM.

Det här dokumentet är sjunde i en serie med artiklar i hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och en uppsättning scenarier som illustrerar hur du ställer in en infrastruktur för migrering och kör olika typer av migreringar. Scenarier växa i komplexitet och vi ska lägga till ytterligare artiklar över tid.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | En översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder dess lokalt och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla Contoso-Migreringsscenarier. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av sina lokala två nivåer SmartHotel app som körs på VMware. De utvärdera app virtuella datorer med den [Azure migrera](migrate-overview.md) tjänsten och SQL Server-databasen app med den [Azure databasen Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Rehost virtuella Azure-datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso migrerar SmartHotel appen till Azure. Migrerar appen klientdel VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app databasen med hjälp av [Azure Databasmigrering](https://docs.microsoft.com/azure/dms/dms-overview) tjänsten för att migrera till en hanterad SQL-instans. | Tillgängligt
[Artikel 5: Rehost till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrera SmartHotel appen VMs endast med Site Recovery.
[Artikel 6: Rehost virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera appen virtuella datorer och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
Artikel 7: Rehost en Linux-app på virtuella Azure-datorer (den här artikeln) | Visar hur Contoso migrerar sina osService Linux-app med Azure Site Recovery.
[Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar appen osService Linux med hjälp av Site Recovery för VM-migrering och MySQL-arbetsstationen för att migrera (till en Azure MySQL Server-instans. | Tillgängligt

I den här artikeln kommer Contoso att migrera tvålagers- **osTicket** app som körs på Linux Apache MySQL PHP (LYKTA) till Azure. Appen virtuella datorer migreras med hjälp av Azure Site Recovery-tjänsten. Om du vill använda den här appen med öppen källkod, kan du hämta det från [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad de vill nå med migreringen:

- **Åtgärda tillväxt**: Contoso växer och det finns därför trycket på den lokala system och infrastruktur.
- **Begränsa risken**: service desk-appen är viktigt för företaget Contoso. De vill flytta den till Azure med noll risk.
- **Utöka**: de inte vill ändra appen just nu. De vill kontrollera att det är stabil.


## <a name="migration-goals"></a>Mål för migrering

Contoso molnet team har fäst ned mål för den här migreringen till den bästa metoden för migrering:

- Efter migreringen ska app i Azure ha samma prestanda som idag i sina lokala VMWare-miljön.  Appen kommer att finnas kvar som kritiskt i molnet som lokalt. 
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form de vill flytta på ett säkert sätt till molnet.
- Contoso vill inte ändra ops-modell för den här appen. De vill interagera med det i molnet på samma sätt som de gör det nu.
- Contoso vill inte ändra funktionalitet. Endast på platsen kommer att ändras.
- Efter att ha utfört några migrering av Windows-app, vill Contoso lära dig hur du använder en Linux-baserade infrastrukturen i Azure.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Appen nivåer över två virtuella datorer (OSTICKETWEB och OSTICKETMYSQL).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), körs på en virtuell dator.
- Contoso har ett lokalt datacenter (**contoso datacenter**), med en lokal domänkontrollant (**contosodc1**).
- Eftersom appen är en arbetsbelastning för produktion, virtuella datorer i Azure kommer att finnas i resursgruppen produktion **ContosoRG**.
- De virtuella datorerna ska migreras till den primära regionen (östra USA 2) och placeras i produktionsnätverket (VNET-PRODUKTPRENUMERATION-EUS2):
    - Web VM kommer att finnas i undernätet frontend (PROD-FE-EUS2).
    - Databasen VM kommer att finnas i undernätet (PROD-DB-EUS2) för databasen.
- Lokala virtuella datorer i Contoso-datacenter ska inaktiveras när migreringen är klar.

![Scenariots arkitektur](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>migreringsprocessen

Contoso migreras på följande sätt:

1. Som ett första steg Contoso ställer in i Azure och lokal infrastruktur som krävs för att distribuera Site Recovery.
2. När du har förberett Azure och lokala komponenter, de konfigurera och aktivera replikering för virtuella datorer.
3. När replikeringen fungerar, migrera de virtuella datorer genom att inte dem via Azure.

![migreringsprocessen](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten samordnar hanterar migrering och katastrofåterställning för virtuella datorer i Azure och lokala virtuella datorer och fysiska servrar.  | Azure Storage avgifter tas ut vid replikering till Azure.  Virtuella Azure-datorer skapas och debiteras när växling vid fel. [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.

 
## <a name="prerequisites"></a>Förutsättningar

Det här är vad du (och Contoso) behöver för det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration under tidig artiklarna i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du är inte administratör, måste du arbeta med administratören att tilldela behörigheter för ägare eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | Ställ in sina Azure-infrastrukturen enligt beskrivningen i Contoso [Azure-infrastrukturen för migrering](contoso-migration-infrastructure.md).<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [lagring](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) krav för Site Recovery.
**Lokala servrar** | Din lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värd.
**Lokala virtuella datorer** | [Granska Linux-datorer](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) som stöds för migrering med Site Recovery.<br/><br/> Kontrollera stöds [Linux-system för fil- och lagringstjänster](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Azure kommer att slutföra migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbered Azure Site Recovery**: de skapa ett Azure storage-konto för att lagra replikerade data och skapa ett Recovery Services-valvet.
> * **Steg 2: Förbered lokal VMware för Site Recovery**: de förbereda konton som ska användas för VM identifieringen och agentinstallationen på nytt och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 3: Replikera VMs**: de konfigurerade miljön för käll- och migrering, skapa en replikeringsprincip och starta replikera virtuella datorer till Azure storage.
> * **Steg 4: Migrera de virtuella datorerna med Site Recovery**: de kör ett redundanstest för att kontrollera att allt fungerar och kör sedan en fullständig växling för att migrera de virtuella datorerna till Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Steg 1: Förbered Azure för Site Recovery-tjänsten

Contoso behöver några Azure komponenter för Site Recovery:

- Ett virtuellt nätverk som växlar över resurserna finns (Contoso använder produktion virtuella nätverk som de redan har distribuerats)
- Ett nytt Azure storage-konto att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

Contoso har redan skapat virtuellt nätverk under [Azure-infrastrukturen distribution](contoso-migration-infrastructure.md), så att de behöver skapa ett lagringskonto och valvet.

1. Contoso skapar ett Azure storage-konto (contosovmsacc20180528) i östra USA 2.

    - Lagringskontot måste finnas i samma region som Recovery Services-valvet.
    - De använder ett generella-konto med standardlagring och LRS replikering.

    ![Site Recovery-lagring](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Med nätverks- och storage-konto på plats Contoso skapar ett valv (ContosoMigrationVault) och placerar den i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2.

    ![Recovery Services-valv](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Steg 2: Förbered lokal VMware för Site Recovery

Contoso förbereds lokalt VMware infrastructure på följande sätt:

- Skapa ett konto på vCenter server eller vSphere ESXi värden, att automatisera VM-identifiering.
- Skapa ett konto som ger automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Förbereda lokala virtuella datorer, så att de kan ansluta till virtuella Azure-datorer när de skapas efter migreringen.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Automatiskt identifiera virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso har konfigurerat kontot på följande sätt:

1. Contoso skapar en roll på vCenter-nivå.
2. Contoso sedan tilldelar rollen behörigheterna som krävs.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på den virtuella Linux-datorer som migrerar Contoso:

- Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för virtuella datorer.
- För automatisk push-installation måste du förbereda ett konto som Site Recovery för att komma åt de virtuella datorerna.
- Information om konton ange under installationen av replikering. 
- Kontot kan vara domän eller lokalt konto med behörighet för att installera på virtuella datorer.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till de replikerade virtuella datorerna i Azure efter en redundansväxling till Azure. Detta gör finns det några saker som de behöver göra:

- För att få åtkomst via internet, kan SSH på lokala Linux VM innan migreringen.  För Ubuntu kan detta utföras med hjälp av följande kommando: **lgh Sudo-get ssh installera -y**.
- När de kör migrering (failover), de kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn.
- Om det inte fungerar de bör kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-3-replicate-the-on-premises-vms"></a>Steg 3: Replikera lokala virtuella datorer

Innan de kan migrera web VM till Azure, ställer in Contoso och aktiverar replikering.

### <a name="set-a-protection-goal"></a>Ange en skyddsmål

1. I valvet, under valvnamnet (ContosoVMVault) de ange ett mål för replikering (**komma igång** > **Site Recovery** > **Förbered infrastruktur**.
2. De kan ange att deras datorer är lokalt, att de är virtuella VMware-datorer och att de vill replikera till Azure.
    ![Målet för replikering](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta att planera distribution

Om du vill fortsätta, de bekräftar att de har slutfört planera distribution genom att välja **Ja, jag har gjort det**. Contoso är bara migrera en enda virtuell dator i det här scenariot och behöver inte distributionsplanering.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso måste konfigurera sina källmiljön. Om du vill göra detta måste de hämtar ett OVF-mall och distribuera Site Recovery konfigurationsservern med hög tillgänglighet, lokal VMware VM. När konfigurationsservern är igång kan registrera den i valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfiguration av serverkomponent som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processerver som fungerar som en replikerings-gateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.

Contoso utför de här stegen på följande sätt:

1. De hämtar OVF-mall från **Förbered infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Hämta OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. De importera mallen till VMware för att skapa den virtuella datorn och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar kan de logga in på den virtuella datorn som administratör. Vid första inloggningen körs konfigurationsverktyget för Azure Site Recovery som standard.
5. I verktyget Ange de ett namn som ska användas för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har etablerats kan de logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. De loggar in på datorn igen och guiden Konfigurera Server Management startar automatiskt.
9. I guiden Välj de nätverkskort för att ta emot replikeringstrafik. Den här inställningen kan inte ändras när den har konfigurerats.
10. De Välj prenumerationen, resursgruppen och valvet att registrera konfigurationsservern.

    ![Valvet](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. De och sedan ladda ned och installera MySQL-Server och VMWare PowerCLI. 
12. När valideringen ange de FQDN eller IP-adressen för vCenter-server eller vSphere-värd. De lämnar standardporten och anger ett eget namn för vCenter-servern.
13. De kan ange det konto som de har skapats för automatisk upptäckt och autentiseringsuppgifter som ska användas för att automatiskt installera Mobilitetstjänsten.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. När registreringen är klar i Azure-portalen Contoso kontrollerar att konfigurationsservern och VMware-server visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
15. Site Recovery ansluter till VMware-servrar och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso konfigurerar nu Målinställningar för replikering.

1. I **Förbered infrastruktur** > **mål**, de Välj inställningar för målet.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Contoso är redo att skapa en replikeringsprincip när käll- och ställs in.

1. I **Förbered infrastruktur** > **replikeringsinställningarna** > **Replikeringsprincipen** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Återställningspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållningstid för återställningspunkten**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperiod är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens av programkonsekventa ögonblicksbilder**. Standardvärdet för en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincipen](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Behöver du mer hjälp?**

- Du kan läsa en fullständig genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurerar replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Lär dig mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) om Azure-gästagenten för Linux.

**Behöver du mer hjälp?**

- Du kan läsa en fullständig genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurerar replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Lär dig mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) om Azure-gästagenten för Linux.



### <a name="enable-replication-for-osticketweb"></a>Aktivera replikering för OSTICKETWEB

Nu Contoso kan starta replikerar den **OSTICKETWEB** VM.

1. I **replikera program** > **källa** > **+ replikera** de välja inställningar för datakälla.
2. De Välj som de vill aktivera virtuella datorer, Välj inställningar för datakälla, inklusive vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. De ange Målinställningar, inklusive resursgruppen och virtuella nätverk som virtuella Azure-datorn kommer att finnas efter en redundansväxling och lagringskontot som replikerade data ska sparas.

     ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Contoso väljer **OSTICKETWEB** för replikering. 

    - I det här skedet Contoso väljer endast **OSTICKETWEB** eftersom VNet och undernät måste väljas och de virtuella datorerna inte finns i samma undernät.
    - Site Recovery installerar automatiskt mobilitetstjänsten när replikering har aktiverats för den virtuella datorn.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. I egenskaperna för VM väljer Contoso du det konto som används av processervern för att automatiskt installera Mobilitetstjänsten på datorn.

     ![Mobilitetstjänsten](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. i **replikeringsinställningarna** > **konfigurerar replikeringsinställningar**, de kontrollera att rätt replikeringsprinciper tillämpade och välj **Aktivera replikering**.
6.  De spåra replikeringsförloppet på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.



### <a name="enable-replication-for-osticketmysql"></a>Aktivera replikering för OSTICKETMYSQL

Nu Contoso kan starta replikering av **OSTICKETMYSQL**.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för källan och målet.
2. Contoso väljer **OSTICKETMYSQL** för replikering, och väljer sedan kontot som ska användas för installationen av Mobility.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Contoso gäller samma replikeringsprincip som användes för OSTICKETWEB och aktiverar replikering.  

**Behöver du mer hjälp?**

Du kan läsa en fullständig genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Steg 4: Migrera de virtuella datorerna 

Kör en snabb Contoso testa redundans och sedan migrera de virtuella datorerna.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Kör ett redundanstest säkerställer att allt fungerar som förväntat innan migreringen. 

1. Contoso kör ett redundanstest till den senaste punkten i tid (**senaste bearbetas**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 
    - En kravkontroll körs se till att alla villkor krävs för migrering på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om den senaste återställningspunkten väljs, skapa en återställningspunkt från data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.
3. När redundansväxlingen är klar visas repliken Azure VM i Azure-portalen. De kontrollera att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs. 
4. När du har verifierat, rensa växling vid fel, de och registrera och spara synpunkter.

### <a name="create-and-customize-a-recovery-plan"></a>Skapa och anpassa en återställningsplan

 När du har verifierat att du testar redundansen fungerade som förväntat, skapa en återställningsplan för migrering i Contoso. 

- En återställningsplan anger ordningen i vilken växling vid fel inträffar, hur virtuella Azure-datorer kommer i Azure.
- Eftersom de vill migrera en tvålagers-app, ska de anpassa återställningsplanen så att data VM (SQLVM) startar före klientdel (WEBVM).


1. I **Återställningsplaner (Site Recovery)** > **+ återställningsplan**, de skapar en plan och lägger till de virtuella datorerna.

    ![Återställningsplan](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Efter att planen kan de välja den för anpassning (**Återställningsplaner** > **OsTicketMigrationPlan** > **anpassa**.
3.  De tar bort **OSTICKETWEB** från **grupp 1: starta**.  Detta säkerställer att den första startåtgärden påverkar **OSTICKETMYSQL** endast.

    ![Grupp för dataåterställning](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  I **+ grupp** > **Lägg till skyddade objekt**, de lägger till **OSTICKETWEB** till **Grupp2: starta**.  Contoso måste dessa i två olika grupper.

    ![Grupp för dataåterställning](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrera de virtuella datorerna


Contoso är redo att köra en växling vid fel på återställningsplan går att migrera de virtuella datorerna.

1. De väljer du planen > **redundans**.
2.  De kan välja att växla över till den senaste återställningspunkten och ange Site Recovery ska försöka stänga av den lokala virtuella datorn innan växling vid fel. De kan följa förloppet för växling vid fel på den **jobb** sidan.

    ![Redundans](./media/contoso-migration-rehost-vm/failover1.png)

3. Under växling vid fel utfärdar vCenter-servern kommandon för att stoppa de två virtuella datorerna körs på ESXi-värd.

    ![Redundans](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Efter växling vid fel Kontrollera Contoso att Azure VM visas som förväntat i Azure-portalen.

    ![Redundans](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. När du har verifierat att den virtuella datorn i Azure, slutföra de migreringen för att slutföra migreringen för varje virtuell dator. Detta stoppar replikering för den virtuella datorn och stoppar Site Recovery-faktureringen för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Anslut VM till databasen

Som det sista steget i migreringen Contoso uppdatera anslutningssträngen för att peka på app-databas som körs på den **OSTICKETMYSQL** VM. 

1. De gör en SSH-anslutning till den **OSTICKETWEB** VM som använder Putty eller en annan SSH-klient. Den virtuella datorn är privat så att de ansluter med privata IP-adress.

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. De behöver för att se till att den **OSTICKETWEB** VM kan kommunicera med den **OSTICKETMYSQL** VM. Konfigurationen är för närvarande hårdkodad med lokala IP-adressen 172.16.0.43.

    **Innan uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Efter uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. De starta om tjänsten med **systemctl starta om apache2**.

    ![Starta om](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Slutligen uppdaterar de DNS-posterna för **OSTICKETWEB** och **OSTICKETMYSQL**, på en av domänkontrollanterna Contoso.

    ![Uppdatera DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Uppdatera DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) kör ett redundanstest. 
- [Läs](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxlar till Azure.

## <a name="clean-up-after-migration"></a>Rensa efter migreringen

Migreringen är klar körs osTicket app nivåer nu på virtuella Azure-datorer.

Nu behöver Contoso göra vissa Rensa:  

- De tar bort de lokala virtuella datorerna från vCenter-lagret.
- De tar bort de lokala virtuella datorerna från lokala säkerhetskopieringsjobb.
- IP-adresser för OSTICKETWEB och OSTICKETMYSQL när de uppdaterar sin interna dokumentation om du vill visa den nya platsen.
- De granska alla resurser som interagerar med de virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.
- Contoso används tjänsten Azure migrera med beroende mappning för att bedöma de virtuella datorerna för migrering. De bör ta bort Microsoft Monitoring Agent och Beroendeagent de installerade för detta ändamål, från den virtuella datorn.

## <a name="review-the-deployment"></a>Granska distributionen

Med appen nu körs, måste Contoso helt operationalisera och skydda den nya infrastrukturen.

### <a name="security"></a>Säkerhet

Contoso-säkerhetsteam granska OSTICKETWEB OSTICKETMYSQLVMs för att fastställa eventuella säkerhetsproblem.

- De granska Nätverkssäkerhetsgrupper (NSG: er) för de virtuella datorerna för åtkomstkontroll. NSG: er används för att se till att endast trafik som tillåts att programmet kan passera.
- De också med tanke på att skydda data på de Virtuella diskar med hjälp av kryptering och Azure KeyVault.

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsåtgärder för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior

Contoso säkerhetskopiera data på virtuella datorer med hjälp av Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licens- och optimering

- När du har distribuerat resurser Contoso tilldelar Azure taggar som definierats under den [Azure-infrastrukturen distribution](contoso-migration-infrastructure.md#set-up-tagging).
- Contoso har inga licenser problem med sina Ubuntu-servrar.
- Contoso aktiverar Azure kostnaden Management licensierade av Cloudyn, ett Microsoft-kontor. Det är en lösning för flera molnkostnad som hjälper dig att använda och hantera Azure och andra molnresurser.  [Lär dig mer](https://docs.microsoft.com/azure/cost-management/overview) om hantering av Azure kostnaden. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln som beskrevs hur Contoso migreras nivåer en lokal tjänst supportavdelningen app på två virtuella Linux-datorer till virtuella Azure IaaS-datorer med hjälp av Azure Site Recovery.

I nästa artikel i serien får du lära du hur Contoso migrera samma tjänst supportavdelningen app till Azure. Nu Contoso använder Site Recovery för att migrera klientdel VM för appen och de migrerar app-databasen med backup och restore till Azure-databas för MySQL, med hjälp av verktyget MySQL-arbetsstationen. [Kom igång](contoso-migration-rehost-linux-vm-mysql.md).
