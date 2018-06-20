---
title: Rehost Contoso Linux service supportavdelningen app till Azure och Azure MySQL | Microsoft Docs
description: Lär dig hur Contoso ändrar värd en lokal Linux-app genom att migrera den virtuella Azure-datorer och MySQL på Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 15a429c033cfd1598dd01b5c8cd2743c397dacdb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225525"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Contoso-migrering: Rehost en lokal Linux-app på Azure Virtual Machines och MySQL på Azure

Den här artikeln visar hur Contoso rehosting sin lokala två nivåer Linux service supportavdelningen app (osTicket) genom att migrera den till Azure och MySQL på Azure.

Detta dokument är åttonde i en serie med artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och scenarier som illustrerar hur du ställer in en infrastruktur för migrering och kör olika typer av migreringar. Scenarier växa i komplexitet och vi ska lägga till ytterligare artiklar över tid.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | En översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder dess lokalt och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla Contoso-Migreringsscenarier. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av sina lokala två nivåer SmartHotel app som körs på VMware. De utvärdera app virtuella datorer med den [Azure migrera](migrate-overview.md) tjänsten och SQL Server-databasen app med den [Azure databasen Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Rehost virtuella Azure-datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso migrerar SmartHotel appen till Azure. Migrerar appen web VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app databasen med hjälp av [Azure Databasmigrering](https://docs.microsoft.com/azure/dms/dms-overview) tjänsten för att migrera till en hanterad SQL-instans. | Tillgängligt
[Artikel 5: Rehost till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrera sina SmartHotel till virtuella Azure IaaS-datorer med Site Recovery-tjänsten.
[Artikel 6: Rehost virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera appen virtuella datorer och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Rehost en Linux-app på virtuella Azure-datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso migrerar osTicket Linux appen till Azure IaaS-VM med hjälp av Azure Site Recovery.
Artikel 8: Rehost en Linux-app på Azure Virtual Machines och Azure MySQL-Server (den här artikeln) | Visar hur Contoso migrerar osTicket Linux appen. De använder Site Recovery för VM-migrering och MySQL-arbetsstationen för att migrera till en Azure MySQL Server-instans. | Tillgängligt

I den här artikeln migrerar Contoso en två-lagers Linux Apache MySQL PHP (LYKTA) tjänsten supportavdelningen app (osTicket) till Azure. Om du vill använda den här appen med öppen källkod, kan du hämta det från [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå de vill uppnå:

- **Åtgärda tillväxt**: Contoso växer och det finns därför trycket på den lokala system och infrastruktur.
- **Begränsa risken**: service desk-appen är viktigt för företaget Contoso. De vill flytta den till Azure med noll risk.
- **Utöka**: Contoso inte vill ändra appen just nu. De vill se till att den är stabil.


## <a name="migration-goals"></a>Mål för migrering

Contoso molnet team har fäst ned mål för den här migreringen för att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestanda som idag i sina lokala VMWare-miljön.  Appen kommer att finnas kvar som kritiskt i molnet som lokalt. 
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form de vill flytta på ett säkert sätt till molnet.
- Efter att ha utfört några migrering av Windows-app, vill Contoso lära dig hur du använder en Linux-baserade infrastrukturen i Azure.
- Contoso vill minimera databasen administrativa uppgifter när programmet flyttas till molnet.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Appen nivåer över två virtuella datorer (OSTICKETWEB och OSTICKETMYSQL).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Nivån webbprogrammet på OSTICKETWEB kommer att migreras till en Azure IaaS-VM.
- App-databasen kommer att migreras till Azure-databasen för MySQL PaaS-tjänsten.
- Eftersom de migrerar en arbetsbelastning för produktion, resurser kommer att finnas i resursgruppen produktion **ContosoRG**.
- Resurser kommer att replikeras till den primära regionen (östra USA 2) och placeras i produktionsnätverket (VNET-PRODUKTPRENUMERATION-EUS2):
    - Web VM kommer att finnas i undernätet frontend (PROD-FE-EUS2).
    - Databasinstansen kommer att finnas i undernätet (PROD-DB-EUS2) för databasen.
- App-databasen kommer att migreras till Azure MySQL med hjälp av MySQL-verktyg.
- Lokala virtuella datorer i Contoso-datacenter ska inaktiveras när migreringen är klar.


![Scenariots arkitektur](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>migreringsprocessen

Contoso kommer att slutföra migreringen på följande sätt:

För att migrera web VM.

1. Som ett första steg Contoso ställer in i Azure och lokal infrastruktur som krävs för att distribuera Site Recovery.
2. När du har förberett Azure och lokala komponenter, de konfigurera och aktivera replikering för virtuell dator.
3. När replikeringen är upp och körs, migrera de den virtuella datorn genom att växla över till Azure.

För att migrera databasen.

1. Contoso etablerar en MySQL-instans i Azure.
2. De konfigurera MySQL-arbetsstationen och säkerhetskopiera databasen lokalt.
3. De sedan återställa databasen från den lokala säkerhetskopieringen till Azure.

![migreringsprocessen](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten samordnar hanterar migrering och katastrofåterställning för virtuella datorer i Azure och lokala virtuella datorer och fysiska servrar.  | Azure Storage avgifter tas ut vid replikering till Azure.  Virtuella Azure-datorer skapas och debiteras när växling vid fel. [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Databasen är baserad på öppen källkod MySQL Server-databasmotorn. Det ger en fullständigt hanterad, enterprise-redo community MySQL-databas som en tjänst för apputveckling och distribution. 

 
## <a name="prerequisites"></a>Förutsättningar

Om du (och Contoso) du vill köra det här scenariot, är här vad du bör ha.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration under tidig artiklarna i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du är inte administratör, måste du arbeta med administratören att tilldela behörigheter för ägare eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | Ställ in sina Azure-infrastrukturen enligt beskrivningen i Contoso [Azure-infrastrukturen för migrering](contoso-migration-infrastructure.md).<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [lagring](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) krav för Site Recovery.
**Lokala servrar** | Din lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värd.
**Lokala virtuella datorer** | [Granska kraven för Linux VM](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) som stöds för migrering med Site Recovery.<br/><br/> Kontrollera stöds [Linux-system för fil- och lagringstjänster](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Azure kommer att slutföra migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbered Azure Site Recovery**: du skapar ett Azure storage-konto för att lagra replikerade data och skapa ett Recovery Services-valv.
> * **Steg 2: Förbered lokal VMware för Site Recovery**: du förbereda konton för VM identifieringen och agentinstallationen på nytt och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
 * **Steg 3: tillhandahålla databasen]**: I Azure, etablera en instans av Azure MySQL-databas.
> * **Steg 4: Replikera VMs**: Konfigurera Site Recovery-miljö för källa och mål, ställa in en replikeringsprincip och starta replikera virtuella datorer till Azure storage.
> * **Steg 5: Migrera databasen**: du konfigurerar migrering med MySQL-verktyg.
> * **Steg 6: Migrera de virtuella datorerna med Site Recovery**: kör ett redundanstest för att kontrollera att allt fungerar och kör sedan en fullständig växling för att migrera de virtuella datorerna till Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Steg 1: Förbered Azure för Site Recovery-tjänsten

Contoso behöver några Azure komponenter för Site Recovery:

- Ett virtuellt nätverk som växlar över resurserna finns (Contoso använder produktion virtuella nätverk som de redan har distribuerats).
- Ett nytt Azure storage-konto att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

Contoso har redan skapat virtuellt nätverk under [Azure-infrastrukturen distribution](contoso-migration-infrastructure.md), så att de behöver skapa ett lagringskonto och valvet.


1. Contoso skapar ett Azure storage-konto (**contosovmsacc20180528**) i östra USA 2.

    - Lagringskontot måste finnas i samma region som Recovery Services-valvet.
    - Contoso använder ett generella-konto med standardlagring och LRS replikering.

    ![Site Recovery-lagring](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Med nätverks- och storage-konto på plats Contoso skapar ett valv (ContosoMigrationVault) och placerar den i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2.

    ![Recovery Services-valv](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Steg 2: Förbered lokal VMware för Site Recovery

Contoso förbereds lokalt VMware infrastructure på följande sätt:

- Skapar ett konto på vCenter-servern att automatisera VM-identifiering.
- Skapar ett konto som ger automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Förbereder lokala virtuella datorer, så att de kan ansluta till virtuella Azure-datorer när de skapas efter migreringen.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Automatiskt identifiera virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso har konfigurerat kontot på följande sätt:

1. Contoso skapar en roll på vCenter-nivå.
2. Contoso sedan tilldelar rollen behörigheterna som krävs.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje virtuell dator som Contoso vill migrera.

- Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för virtuella datorer.
- För automatisk installation. Site Recovery måste ha ett konto med behörighet att komma åt den virtuella datorn. 
- Kontoinformation ange under installationen av replikering. 
- Kontot kan vara domän- eller lokala konto så länge den har installationsbehörigheter.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till virtuella Azure-datorer efter redundans till Azure. Om du vill göra detta, som de behöver göra några saker: 

- För att få åtkomst via internet, kan SSH på lokala Linux VM före migreringen.  För Ubuntu kan detta utföras med hjälp av följande kommando: **lgh Sudo-get ssh installera -y**.
- De ska kontrollera efter växling vid fel, **starta diagnostik** att visa en skärmbild av den virtuella datorn.
- Om det inte fungerar, de behöver för att kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-3-provision-azure-database-for-mysql"></a>Steg 3: Konfigurera Azure-databas för MySQL

Contoso tillhandahåller en MySQL-databas instans i den primära regionen östra USA 2.

1. De skapar en Azure-databas för MySQL-resurs i Azure-portalen. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. De lägger till namnet **contosoosticket** för Azure-databasen. De lägga till databasen i resursgruppen produktion **ContosoRG**, och ange autentiseringsuppgifter för den.
3. Lokala MySQL-databas är version 5.7, så de väljer den här versionen för kompatibilitet. De använder standardstorlekar som matchar deras databaskrav.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. För **redundansalternativ**, Contoso väljer för att använda **Geo-Redundant**. Det här alternativet innebär att de kan återställa databasen i centrala USA sekundära regionen om ett avbrott inträffar. De kan bara konfigurera det här alternativet när de konfigurerar databasen.

     ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. I den **VNET-PRODUKTPRENUMERATION-EUS2** nätverket > **tjänstens slutpunkter**, de lägger till en tjänstslutpunkt (databasen undernät) för SQL-tjänsten.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. När du lägger till undernätet skapar de en regel för virtuella nätverk som tillåter åtkomst från undernätet som databasen i produktionsmiljön.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Steg 4: Replikera lokala virtuella datorer

Innan de kan migrera web VM till Azure, ställer in Contoso och aktiverar replikering.

### <a name="set-a-protection-goal"></a>Ange en skyddsmål

1. I valvet, under valvnamnet (ContosoVMVault) de ange ett mål för replikering (**komma igång** > **Site Recovery** > **Förbered infrastruktur**.
2. De kan ange att deras datorer är lokalt, att de är virtuella VMware-datorer och att de vill replikera till Azure.

    ![Replikeringsmål](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta att planera distribution

Om du vill fortsätta, de bekräftar att de har slutfört planera distribution genom att välja **Ja, jag har gjort det**. Contoso är bara migrera en enda virtuell dator i det här scenariot och behöver inte distributionsplanering.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso måste konfigurera sina källmiljön. Om du vill göra detta med hjälp av ett OVF-mall de distribuerar en Site Recovery konfigurationsservern med en hög tillgänglighet, lokal VMware VM. När konfigurationsservern är igång kan registrera den i valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfiguration av serverkomponent som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processerver som fungerar som en replikerings-gateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.

Contoso utför de här stegen på följande sätt:


1. De hämtar OVF-mall från **Förbered infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Hämta OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. De importera mallen till VMware för att skapa den virtuella datorn och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar kan logga de in på den virtuella datorn som administratör. Vid första inloggningen körs konfigurationsverktyget för Azure Site Recovery som standard.
5. I verktyget Ange de ett namn som ska användas för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure.
7. När anslutningen har etablerats kan de logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till valvet där de ska registrera konfigurationsservern.

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
9. De loggar in på datorn igen och guiden Konfigurera Server Management startar automatiskt.
10. I guiden Välj de nätverkskort för att ta emot replikeringstrafik. Den här inställningen kan inte ändras när den har konfigurerats.
11. De Välj prenumerationen, resursgruppen och valvet att registrera konfigurationsservern.

    ![Valvet](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Nu kan de hämta och installera MySQL-Server och VMWare PowerCLI. 
13. När valideringen ange de FQDN eller IP-adressen för vCenter-server eller vSphere-värd. De lämnar standardporten och anger ett eget namn för vCenter-servern.
14. De kan ange det konto som de har skapats för automatisk upptäckt och autentiseringsuppgifterna som Site Recovery för att automatiskt installera Mobilitetstjänsten. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. När registreringen är klar i Azure-portalen Contoso kontrollerar att konfigurationsservern och VMware-server visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
15. Site Recovery med allt på plats, ansluter till VMware-servrar och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso indata rikta nu replikeringsinställningarna.

1. I **Förbered infrastruktur** > **mål**, de Välj inställningar för målet.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.


### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Contoso är redo att skapa en replikeringsprincip med käll- och ställa in.

1. I **Förbered infrastruktur** > **replikeringsinställningarna** > **Replikeringsprincipen** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Återställningspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållningstid för återställningspunkten**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperiod är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens av programkonsekventa ögonblicksbilder**. Standardvärdet för en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincipen](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Behöver du mer hjälp?**

- Du kan läsa en fullständig genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurerar replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Lär dig mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) om Azure-gästagenten för Linux.

### <a name="enable-replication-for-the-web-vm"></a>Aktivera replikering för den virtuella datorn Web

Nu Contoso kan starta replikerar den **OSTICKETWEB** VM.

1. I **replikera program** > **källa** > **+ replikera** de välja inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer och välja inställningar för datakälla, inklusive vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Nu ange de inställningar för target. Dessa inkluderar resursgruppen och nätverk där den virtuella Azure-datorn kommer att finnas efter en redundansväxling och lagringskontot som replikerade data ska sparas. 

     ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Contoso väljer **OSTICKETWEB** för replikering. 

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. I VM-egenskaper kan de välja det konto som ska användas för att automatiskt installera Mobilitetstjänsten på den virtuella datorn.

     ![Mobilitetstjänsten](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. i **replikeringsinställningarna** > **konfigurerar replikeringsinställningar**, de kontrollera att rätt replikeringsprinciper tillämpade och välj **Aktivera replikering**. Mobilitetstjänsten installeras automatiskt.
6.  De spåra replikeringsförloppet på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.


**Behöver du mer hjälp?**

Du kan läsa en fullständig genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Steg 5: Migrera databasen

Contoso migreras databasen med säkerhetskopiering och återställning, med MySQL-verktyg. De installerar MySQL arbetsstationen, säkerhetskopiera databasen från OSTICKETMYSQL och återställa den till Azure-databas för MySQL-servern.

### <a name="install-mysql-workbench"></a>Installera MySQL Workbench

1. Contoso kontrollerar den [krav och hämtningsbara filer MySQL arbetsstationen](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. De installerar MySQL-arbetsstationen för Windows i enlighet med den [Installationsinstruktioner](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. De skapar en MySQL-anslutning till OSTICKETMYSQL i MySQL-arbetsstationen. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. De export av databas som **osticket**, till en lokal fristående fil.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. När databasen har säkerhetskopierats lokalt, kan de skapa en anslutning till Azure-databas för MySQL-instansen.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Contoso kan nu importera (återställa) databasen i Azure MySQL-instansen från det fristående fil. Ett nytt schema (osticket) skapas för instansen.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Steg 6: Migrera de virtuella datorerna med Site Recovery

Kör en snabb Contoso testa redundans och sedan migrera den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Kör ett redundanstest Kontrollera hjälper dig att att allt fungerar som förväntat innan migreringen. 

1. Contoso kör ett redundanstest till den senaste punkten i tid (**senaste bearbetas**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs se till att alla villkor krävs för migrering på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

3. När redundansväxlingen är klar visas repliken Azure VM i Azure-portalen. Contoso kontrollerar att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs. 
4. När du har verifierat, rensa växling vid fel, de och registrera och spara synpunkter.

### <a name="migrate-the-vm"></a>Migrera den virtuella datorn

Om du vill migrera den virtuella datorn Contoso skapar en återställningsplan som innehåller den virtuella datorn och misslyckas planen över till Azure.

1. Contoso skapar en plan och lägger till **OSTICKETWEB** till den.

    ![Återställningsplan](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. De kör en växling vid fel på planen. De markerar du den senaste återställningspunkten och ange Site Recovery ska försöka stänga av den lokala virtuella datorn innan växling vid fel. De kan följa förloppet för växling vid fel på den **jobb** sidan.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Under växling vid fel utfärdar vCenter-servern kommandon för att stoppa de två virtuella datorerna körs på ESXi-värd.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Efter växling vid fel verifierar Contoso att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. När du har kontrollerat VM slutföra de migreringen. Detta stoppar replikering för den virtuella datorn och stoppar Site Recovery-faktureringen för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) kör ett redundanstest. 
- [Läs](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxlar till Azure.


### <a name="connect-the-vm-to-the-database"></a>Anslut VM till databasen

Contoso uppdatera anslutningssträngen för appen så att den pekar till Azure-databasen för MySQL som det sista steget i migreringen. 

1. De gör en SSH-anslutning för OSTICKETWEB VM med hjälp av Putty eller en annan SSH-klient. Den virtuella datorn är privat så att de ansluter med privata IP-adress.

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. De uppdatera inställningar så att den **OSTICKETWEB** VM kan kommunicera med den **OSTICKETMYSQL** databas. Konfigurationen är för närvarande hårdkodad med lokala IP-adressen 172.16.0.43.

    **Innan uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Efter uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. De starta om tjänsten med **systemctl starta om apache2**.

    ![Starta om](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Slutligen uppdaterar de DNS-posterna för **OSTICKETWEB**, på en av domänkontrollanterna Contoso.

    ![Uppdatera DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Rensa efter migreringen

Migreringen är klar körs osTicket app nivåer på virtuella Azure-datorer.

Nu behöver Contoso gör du följande: 
- Tar bort den virtuella VMware-datorer från vCenter-lager
- Ta bort de lokala virtuella datorerna från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentationen visar nya platser och IP-adresser. 
- Granska alla resurser som interagerar med de lokala virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.
- Contoso används tjänsten Azure migrera med beroende mappning för att utvärdera den **OSTICKETWEB** VM för migrering. De bör nu ta bort agenter (Microsoft Monitoring Agent/beroende Agent) de installerade för detta ändamål, från den virtuella datorn.

## <a name="review-the-deployment"></a>Granska distributionen

Med appen nu körs, måste Contoso helt operationalisera och skydda den nya infrastrukturen.

### <a name="security"></a>Säkerhet

Contoso-säkerhetsteam granska VM och databasen för att fastställa eventuella säkerhetsproblem.

- De granska Nätverkssäkerhetsgrupper (NSG: er) för den virtuella datorn för åtkomstkontroll. NSG: er används för att se till att endast trafik som tillåts att programmet kan passera.
- De anser att skydda data på de Virtuella diskar med hjälp av kryptering och Azure KeyVault.
- Kommunikation mellan den virtuella dator och databasen instansen har inte konfigurerats för SSL. De måste göra detta för att säkerställa att databastrafik inte kan vara över.

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsåtgärder för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior

- Contoso att säkerhetskopiera data på den virtuella datorn med hjälp av Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- De behöver inte konfigurera säkerhetskopiering av databasen. Azure-databas för MySQL skapar automatiskt säkerhetskopior och lagrar. De markerade använder geo-redundans för databasen, så att den är flexibel och klar för produktion.

### <a name="licensing-and-cost-optimization"></a>Licens- och optimering

- När du har distribuerat resurser Contoso tilldelar Azure taggar, i enlighet med beslut som de har gjorts under den [Azure-infrastrukturen](contoso-migration-infrastructure.md#set-up-tagging) distribution.
- Det finns inga licensproblem för Contoso Ubuntu-servrar.
- Contoso aktiverar Azure kostnaden Management licensierade av Cloudyn, ett Microsoft-kontor. Det är en lösning för flera molnkostnad som hjälper dig att använda och hantera Azure och andra molnresurser.  [Lär dig mer](https://docs.microsoft.com/azure/cost-management/overview) om hantering av Azure kostnaden.


## <a name="next-steps"></a>Nästa steg

I det här scenariot visade vi slutliga rehost-situation att Contoso försökt. De migrerade klientdel VM lokala Linux osTicket appen till en Azure VM och migrera app-databas till en Azure MySQL-instans.

I nästa uppsättning självstudiekurser i serien migrering, är det dags att visa hur Contoso utförs en mer komplex uppsättning migreringar, som rör eftersom app i stället för enkel lift och SKIFT-migreringar.
