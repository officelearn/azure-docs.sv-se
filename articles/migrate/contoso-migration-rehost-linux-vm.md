---
title: Rehost-migrera och rehost en lokala Linux-app på virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur Contoso rehost en lokala Linux-app genom att migrera virtuella Azure-datorerna.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 6c96beee347a7a36a3dc04ecf8cd994484fd6bb7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007259"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Contoso-migrering: Rehost en lokala Linux-app på virtuella Azure-datorer

Den här artikeln visar hur Contoso rehosting en lokala Linux-baserat service desk-app (**osTicket**), till virtuella Azure IaaS-datorer.

Det här dokumentet är i en serie av artiklar som dokumenterar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och en uppsättning scenarier som illustrerar hur du konfigurerar en infrastruktur för migreringen och kör olika typer av migreringar. Scenarier växer i komplexitet och vi ska lägga till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder sina lokala och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla scenarier för migrering av Contoso. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av sina lokala tvålagers-SmartHotel-app som körs på VMware. De utvärdera app virtuella datorer med den [Azure Migrate](migrate-overview.md) -tjänsten och SQL Server-databas för appen med den [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Rehost till Azure virtuella datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso migrerar SmartHotel appen till Azure. De migrera appen klientdelens VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och appen databasen med den [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) service för att migrera till en SQL-hanterad instans. | Tillgängligt
[Artikel 5: Byt värd till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrerar SmartHotel appen virtuella datorer till virtuella Azure-datorer. | Tillgängligt
[Artikel 6: Byt värd till virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera de virtuella datorerna för appen och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer | Visar hur Contoso migrerar sina osService Linux-app med Azure Site Recovery. | Den här artikeln.
[Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar osService Linux-app med Site Recovery för VM-migrering och MySQL Workbench för att migrera (till en Azure MySQL-Server-instans. | Tillgängligt
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Visar hur Contoso migrerar SmartHotel-app till ett Azure Web Apps och app-databasen har migrerats till Azure SQL Server-instans | Tillgängligt
[Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure Web Apps på flera platser, integrerad med GitHub för kontinuerlig leverans. De migrera app-databasen till en Azure MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera TFS på VSTS](contoso-migration-tfs-vsts.md) | Visar hur Contoso migrerar sina lokala Team Foundation Server (TFS)-distributionen genom att migrera den till Visual Studio Team Services (VSTS) i Azure. | Tillgängligt
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Visar hur Contoso migrerar och rearchitects SmartHotel appen till Azure. De Omforma webbnivån appen som en Windows-behållare och app-databasen i en Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Visar hur Contoso återskapa SmartHotel appen med hjälp av en mängd Azure-funktioner och tjänster, inklusive App Services, Azure Kubernetes, Azure Functions, Cognitive services och Cosmos DB. | Tillgängligt

I den här artikeln kommer Contoso att migrera tvålagers- **osTicket** app, som körs på Linux Apache MySQL PHP (LAMP) till Azure. De virtuella datorerna för appen kommer att migreras med hjälp av Azure Site Recovery-tjänsten. Om du vill använda den här appen med öppen källkod kan du hämta det från [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer, och därför har Press på lokala system och infrastruktur.
- **Begränsa risken**: service desk-app är viktigt för företaget Contoso. De vill flytta den till Azure med noll risk.
- **Utöka**: de inte vill ändra appen just nu. De vill säkerställa att den är stabil.


## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för den här migreringen, att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestandafunktioner som idag i sina lokala VMWare-miljön.  Appen finns kvar som kritiskt i molnet eftersom det är på plats. 
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form de helt enkelt vill flytta på ett säkert sätt till molnet.
- Contoso vill inte ändra ops-modellen för den här appen. De vill interagera med den i molnet på samma sätt som de gör nu.
- Contoso vill inte ändra funktionalitet. App plats kommer att ändras.
- När du har utfört några Windows app migreringar, vill Contoso lära dig hur du använder en Linux-baserade infrastruktur i Azure.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Appen är nivåindelad över två virtuella datorer (OSTICKETWEB och OSTICKETMYSQL).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (**contoso-datacenter**), med en lokal domänkontrollant (**contosodc1**).
- Eftersom appen är en produktionsarbetsbelastning kan de virtuella datorerna i Azure kommer att finnas i resursgruppen produktion **ContosoRG**.
- De virtuella datorerna ska migreras till den primära regionen (östra USA 2) och placeras i företagets nätverk (VNET-PROD-EUS2):
    - Web VM kommer att finnas i undernätet på klientsidan (PROD-FE-EUS2).
    - Databasen VM kommer att finnas i undernätet (PROD-DB-EUS2) för databasen.
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.

![Scenariots arkitektur](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>Migreringsprocessen

Contoso migreras på följande sätt:

1. Som ett första steg Contoso ställer in Azure och lokal infrastruktur som krävs för att distribuera Site Recovery.
2. När du har förberett Azure och lokala komponenter, de konfigurera och aktivera replikering för de virtuella datorerna.
3. När replikeringen fungerar, migrera de virtuella datorer genom att föra över dem till Azure.

![Migreringsprocessen](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure.  Virtuella Azure-datorer skapas och betala, vid redundans. [Läs mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.

 
## <a name="prerequisites"></a>Förutsättningar

Här är vad du (och Contoso) behöver det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration under tidig artiklar i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | Ställa in sina Azure-infrastrukturen enligt beskrivningen i Contoso [Azure-infrastrukturen för migrering](contoso-migration-infrastructure.md).<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) kraven för Site Recovery.
**Lokala servrar** | Din lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värden.
**Lokala virtuella datorer** | [Granska Linux-datorer](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) som stöds för migrering med Site Recovery.<br/><br/> Kontrollera stöds [Linux-system för fil- och lagringstjänster](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Azure kommer att slutföra migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbereda Azure Site Recovery**: de skapa ett Azure storage-konto för att lagra replikerade data och skapa ett Recovery Services-valv.
> * **Steg 2: Förbereda lokala VMware för Site Recovery**: de förbereda konton som ska användas för installation av VM-identifiering och agentinstallation och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 3: Replikera datorer**: de ställa in miljön för käll- och migrering, skapa en replikeringsprincip och börja replikera virtuella datorer till Azure storage.
> * **Steg 4: Migrera de virtuella datorerna med Site Recovery**: de kör ett redundanstest för att kontrollera att allt fungerar och sedan köra en fullständig redundans om du vill migrera de virtuella datorerna till Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Steg 1: Förbereda Azure för Site Recovery-tjänsten

Contoso behöver några Azure-komponenter för Site Recovery:

- Ett virtuellt nätverk i vilket växlas över resurserna finns (Contoso använder produktion VNet som de redan har distribuerats)
- Ett nytt Azure storage-konto för replikerade data. 
- Ett Recovery Services-valv i Azure.

Contoso som redan har skapat det virtuella nätverket under [av Azure infrastructure](contoso-migration-infrastructure.md), så att de bara behöver för att skapa ett lagringskonto och valv.

1. Contoso skapar ett Azure storage-konto (contosovmsacc20180528) i regionen östra USA 2.

    - Lagringskontot måste finnas i samma region som Recovery Services-valvet.
    - De använder ett konto för generell användning med standardlagring och LRS-replikering.

    ![Site Recovery-lagring](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Med nätverks- och storage-kontot på plats, Contoso skapar ett valv (ContosoMigrationVault) och placera den i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2.

    ![Recovery Services-valv](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Steg 2: Förbereda lokala VMware för Site Recovery

Contoso förbereder den lokala VMware-infrastrukturen på följande sätt:

- Skapa ett konto på vCenter-servern eller vSphere ESXi-värden, att automatisera VM-identifiering.
- Skapa ett konto som tillåter automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Förbereda lokala virtuella datorer, så att de kan ansluta till virtuella Azure-datorer när de skapas efter migreringen.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso ställer in kontot enligt följande:

1. Contoso skapar en roll på vCenter-nivå.
2. Contoso sedan tilldelar rollen behörigheterna som krävs.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på den virtuella Linux-datorer som migrerar Contoso:

- Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för virtuella datorer.
- För automatisk push-installation måste du förbereda ett konto som Site Recovery använder för att få åtkomst till de virtuella datorerna.
- Information om konton ange under installationen av replikering. 
- Kontot kan vara domän eller lokalt konto med behörighet att installera på virtuella datorer.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till de replikerade virtuella datorerna i Azure efter redundansväxlingen till Azure. Detta gör att det finns några saker som de behöver göra:

- För att komma åt via internet, gör SSH på den lokala Linux VM före migreringen.  För Ubuntu kan detta utföras med hjälp av följande kommando: **Sudo apt-get ssh installera -y**.
- När de kör migreringen (redundans) bör de kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn.
- Om det inte fungerar de bör kontrollera att den virtuella datorn körs, och dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-3-replicate-the-on-premises-vms"></a>Steg 3: Replikera lokala virtuella datorer

Innan de kan migrera web virtuell dator till Azure, Contoso konfigurerar och aktiverar replikering.

### <a name="set-a-protection-goal"></a>Ange en skyddsmål

1. I valvet under valvnamnet (ContosoVMVault) de ange ett replikeringsmål (**komma igång** > **Site Recovery** > **Förbered infrastruktur**.
2. De ange som finns lokalt på sina datorer, att de är virtuella VMware-datorer och att de vill replikera till Azure.
    ![Replikeringsmål](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Om du vill fortsätta, de bekräftar att de har slutfört distributionsplanering genom att välja **Ja, jag har gjort det**. Contoso är bara migrera en enskild virtuell dator i det här scenariot och behöver inte distributionsplanering.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso behöver konfigurera sina källmiljö. Detta gör de ladda ned en OVF-mall och använda den för att distribuera Site Recovery configuration server med hög tillgänglighet, en lokal VMware VM. När configuration server är igång, registrera den i valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfigurationsserverkomponenten som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.

Contoso utför de här stegen på följande sätt:

1. De laddar ned OVF-mall från **förbereda infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Ladda ner OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. De importerar mallen till VMware för att skapa den virtuella datorn och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar, de loggar in på den virtuella datorn som administratör. Vid första inloggning körs Azure Site Recovery-konfigurationsverktyget som standard.
5. I verktyget Ange de ett namn som ska användas för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats kan de logga in på Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. De loggar in på datorn igen och i guiden Konfigurera serverhantering startar automatiskt.
9. I guiden Välj de nätverkskort för att ta emot replikeringstrafiken. Den här inställningen kan inte ändras när den har konfigurerats.
10. De Välj prenumeration, resursgrupp och valv där du vill registrera konfigurationsservern.

    ![Valvet](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. De och sedan hämta och installera MySQL-Server och VMWare PowerCLI. 
12. Efter valideringen kan ange de FQDN eller IP-adress för vCenter-servern eller vSphere-värden. De lämnar standardporten och anger ett eget namn för vCenter-servern.
13. De ange det konto som de har skapat för automatisk identifiering och de autentiseringsuppgifter som ska användas för att automatiskt installera Mobilitetstjänsten.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. När registreringen är klar, i Azure-portalen, Contoso kontrollerar att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
15. Site Recovery ansluter till VMware-servrar och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Nu konfigurerar Contoso Målinställningar för replikering.

1. I **Förbered infrastruktur** > **Target**, de väljer målinställningarna.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

När källan och målet har konfigurerats, är Contoso redo att skapa en replikeringsprincip.

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Replikeringspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens för appkonsekvent ögonblicksbild**. Som standard på en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincip](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Behöver du mer hjälp?**

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Läs mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) om Azure-gästagenten för Linux.

**Behöver du mer hjälp?**

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Läs mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) om Azure-gästagenten för Linux.



### <a name="enable-replication-for-osticketweb"></a>Aktivera replikering för OSTICKETWEB

Nu Contoso kan börja replikera den **OSTICKETWEB** VM.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för datakälla.
2. De väljer att de vill aktivera virtuella datorer, Välj inställningar för datakälla, inklusive vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. De ange Målinställningar, inklusive resursgruppen och virtuellt nätverk där den virtuella Azure-datorn kommer att finnas efter redundansväxling och lagringskontot som replikerade data ska lagras.

     ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Contoso väljer **OSTICKETWEB** för replikering. 

    - I det här skedet Contoso väljer endast **OSTICKETWEB** eftersom VNet och undernät måste väljas och de virtuella datorerna inte är i samma undernät.
    - Site Recovery installerar automatiskt mobilitetstjänsten när replikering har aktiverats för den virtuella datorn.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. I virtuella datorns egenskaper väljer Contoso du det konto som används av processervern för att automatiskt installera Mobilitetstjänsten på datorn.

     ![Mobilitetstjänsten](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. i **replikeringsinställningar** > **konfigurera replikeringsinställningar**, de kontrollera att rätt replikeringsprincip är tillämpad och välj **Aktivera replikering**.
6.  De spåra Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.



### <a name="enable-replication-for-osticketmysql"></a>Aktivera replikering för OSTICKETMYSQL

Nu Contoso kan påbörja replikeringen **OSTICKETMYSQL**.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för källa och mål.
2. Contoso väljer **OSTICKETMYSQL** för replikering, och väljer kontot som ska användas för installation av Mobilitetstjänsten.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Contoso gäller samma replikeringsprincip som användes för OSTICKETWEB och aktiverar replikering.  

**Behöver du mer hjälp?**

Du kan läsa en genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Steg 4: Migrera de virtuella datorerna 

Contoso som kör ett snabbt redundanstest och sedan migrera de virtuella datorerna.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Kör ett redundanstest hjälper dig att se till att allt fungerar som förväntat innan migreringen. 

1. Contoso kör ett redundanstest till den senaste tillgängliga tidpunkt (**senaste bearbetade**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan redundansen. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 
    - En kravkontroll körs för att kontrollera att alla de villkor som krävs för migrering är på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om den senaste återställningspunkten är vald, skapas en återställningspunkt från data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.
3. När redundansen är klar visas repliken virtuell Azure-dator i Azure-portalen. De kontrollera att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs. 
4. När du har verifierat, rensa växling vid fel, de och registrera och sparar eventuella observationer.

### <a name="create-and-customize-a-recovery-plan"></a>Skapa och anpassa en återställningsplan

 Efter att ha kontrollerat att redundanstestningen fungerar som förväntat, skapa en återställningsplan för migrering i Contoso. 

- En återställningsplan anger ordningen i vilken redundans hur virtuella Azure-datorer ska göras tillgänglig i Azure.
- Eftersom de vill migrera en app på två nivåer, ska de anpassa återställningsplanen så att data VM (SQLVM) startar innan klientdelen (WEBVM).


1. I **Återställningsplaner (Site Recovery)** > **+ återställningsplan**, de skapa en plan och Lägg till de virtuella datorerna.

    ![Återställningsplan](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. När du har skapat planen som de markeras för anpassning (**Återställningsplaner** > **OsTicketMigrationPlan** > **anpassa**.
3.  De tar bort **OSTICKETWEB** från **grupp 1: starta**.  Detta säkerställer att den första startåtgärden påverkar **OSTICKETMYSQL** endast.

    ![Grupp för dataåterställning](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  I **+ grupp** > **Lägg till skyddade objekt**, de lägger till **OSTICKETWEB** till **Grupp2: börja**.  Contoso behöver dessa i två olika grupper.

    ![Grupp för dataåterställning](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrera de virtuella datorerna


Contoso är redo att köra en redundans på återställningsplanen för att migrera de virtuella datorerna.

1. De väljer du planen > **redundans**.
2.  De väljer att växla över till den senaste återställningspunkten och ange att Site Recovery bör försöka att stänga av den lokala virtuella datorn innan du utlöser redundansväxlingen. De kan följa redundansförloppet på den **jobb** sidan.

    ![Redundans](./media/contoso-migration-rehost-vm/failover1.png)

3. Under redundansen utfärdar kommandon för att stoppa de två virtuella datorer som körs på ESXi-värden i vCenter-servern.

    ![Redundans](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Efter redundansen, Contoso kontrollerar du att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

    ![Redundans](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. När du har verifierat den virtuella datorn i Azure, slutföra de migreringen för att slutföra migreringen för varje virtuell dator. Detta stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Ansluta den virtuella datorn till databasen

Som det sista steget i migreringsprocessen Contoso uppdatera anslutningssträngen för programmet som ska peka på app-databas som körs på den **OSTICKETMYSQL** VM. 

1. De gör en SSH-anslutning till den **OSTICKETWEB** virtuell dator med Putty eller någon annan SSH-klient. Den virtuella datorn är privat så att de ansluter via privata IP-adress.

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. De behöver för att se till att den **OSTICKETWEB** VM kan kommunicera med den **OSTICKETMYSQL** VM. Konfigurationen är för närvarande hårdkodad med den lokala IP-adressen 172.16.0.43.

    **Innan uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Efter uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. De starta om tjänsten med **systemctl starta om apache2**.

    ![Starta om](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Slutligen de uppdaterar du DNS-posterna för **OSTICKETWEB** och **OSTICKETMYSQL**, på en av domänkontrollanterna Contoso.

    ![Uppdatera DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Uppdatera DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) som kör ett redundanstest. 
- [Lär dig](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxla till Azure.

## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Med migreringen har slutförts körs app-nivåerna osTicket nu på virtuella Azure-datorer.

Nu kan måste Contoso Rensa:  

- De tar bort lokala virtuella datorer från vCenter-lagret.
- De ta bort de lokala virtuella datorerna från lokala säkerhetskopieringsjobb.
- IP-adresser för OSTICKETWEB och OSTICKETMYSQL när de uppdaterar sin interna dokumentation för att visa den nya platsen.
- De granska eventuella resurser som interagerar med de virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.
- Contoso används tjänsten Azure Migrate med beroendemappning för att utvärdera de virtuella datorerna för migrering. De bör ta bort Microsoft Monitoring Agent och Beroendeagenten de installerade för detta ändamål, från den virtuella datorn.

## <a name="review-the-deployment"></a>Granska distributionen

Med den app som körs nu måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

Contoso security team granska OSTICKETWEB och OSTICKETMYSQLVMs för att fastställa eventuella säkerhetsproblem.

- De Läs igenom Nätverkssäkerhetsgrupper (NSG) för de virtuella datorerna för åtkomstkontroll. NSG: er för att säkerställa att endast trafik som tillåts att programmet kan skicka.
- De överväger också skydda data på VM-diskar med hjälp av diskkryptering och Azure KeyVault.

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsrutiner för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior

Contoso säkerhetskopiera data på de virtuella datorerna med Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- När du har distribuerat resurser, Contoso tilldelar Azure taggar som definierats under den [av Azure infrastructure](contoso-migration-infrastructure.md#set-up-tagging).
- Contoso har inga licensproblem med deras Ubuntu-servrar.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln visade vi hur Contoso migrerade nivåer en lokal service desk-app på två virtuella Linux-datorer till virtuella Azure IaaS-datorer med Azure Site Recovery.

I nästa artikel i serien, får du lära dig hur Contoso migrerar samma service desk-app till Azure. Den här gången Contoso använder Site Recovery migrera klientdelens VM för appen och migrera app-databasen med backup och återställa till Azure Database för MySQL med hjälp av verktyget MySQL workbench. [Kom igång](contoso-migration-rehost-linux-vm-mysql.md).
