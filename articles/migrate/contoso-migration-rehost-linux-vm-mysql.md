---
title: Ange ny värd för en Contoso Linux service desk-app till Azure och Azure MySQL | Microsoft Docs
description: Lär dig hur Contoso namnkonflikt en lokala Linux-app genom att migrera det virtuella Azure-datorer och Azure MySQL.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fbb70bd20b89bb1b711630ba54fe31806292385c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002236"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Contoso-migrering: Rehost en lokala Linux-app på Azure virtuella datorer och Azure MySQL

Den här artikeln visar hur Contoso namnkonflikt lokal tvålagers-Linux service desk app (osTicket) genom att migrera den till Azure och Azure MySQL.

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migreringen och kör olika typer av migreringar. Scenarier växer i komplexitet och vi ska lägga till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder sina lokala och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla scenarier för migrering av Contoso. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av sina lokala tvålagers-SmartHotel-app som körs på VMware. De utvärdera app virtuella datorer med den [Azure Migrate](migrate-overview.md) -tjänsten och SQL Server-databas för appen med den [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Rehost till Azure virtuella datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso migrerar SmartHotel appen till Azure. De migrera appen web VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och appen databasen med den [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) service för att migrera till en SQL-hanterad instans. | Tillgängligt
[Artikel 5: Byt värd till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrerar sina SmartHotel till virtuella Azure IaaS-datorer med hjälp av Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Byt värd till virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera de virtuella datorerna för appen och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso migrerar sina osTicket Linux-app till Azure IaaS-VM med Azure Site Recovery. | Tillgängligt
Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server | Visar hur Contoso migrerar osTicket Linux-app. De kan använda Site Recovery för VM-migrering och MySQL Workbench för att migrera till en Azure MySQL-Server-instans. | Den här artikeln.
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Visar hur Contoso migrerar SmartHotel-app till ett Azure Web Apps och app-databasen har migrerats till Azure SQL Server-instans | Tillgängligt
[Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure Web Apps på flera platser, integrerad med GitHub för kontinuerlig leverans. De migrera app-databasen till en Azure MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera TFS på VSTS](contoso-migration-tfs-vsts.md) | Visar hur Contoso migrerar sina lokala Team Foundation Server (TFS)-distributionen genom att migrera den till Visual Studio Team Services (VSTS) i Azure. | Tillgängligt
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Visar hur Contoso migrerar och rearchitects SmartHotel appen till Azure. De Omforma webbnivån appen som en Windows-behållare och app-databasen i en Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Visar hur Contoso återskapa SmartHotel appen med hjälp av en mängd Azure-funktioner och tjänster, inklusive App Services, Azure Kubernetes, Azure Functions, Cognitive services och Cosmos DB. | Tillgängligt


I den här artikeln migrerar Contoso en tvålagers-Linux Apache MySQL PHP (LAMP) service desk-app (osTicket) till Azure. Om du vill använda den här appen med öppen källkod kan du hämta det från [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad de vill uppnå:

- **Åtgärda tillväxten**: Contoso växer, och därför har Press på lokala system och infrastruktur.
- **Begränsa risken**: service desk-app är viktigt för företaget Contoso. De vill flytta den till Azure med noll risk.
- **Utöka**: Contoso inte vill ändra appen just nu. De vill hålla den säkra.


## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för den här migreringen för att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestandafunktioner som idag i sina lokala VMWare-miljön.  Appen finns kvar som kritiskt i molnet eftersom det är på plats. 
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form de helt enkelt vill flytta på ett säkert sätt till molnet.
- När du har utfört några Windows app migreringar, vill Contoso lära dig hur du använder en Linux-baserade infrastruktur i Azure.
- Contoso vill minimera databasen administrativa uppgifter när programmet flyttas till molnet.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Appen är nivåindelad över två virtuella datorer (OSTICKETWEB och OSTICKETMYSQL).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Nivå-webbapp i OSTICKETWEB kommer att migreras till en Azure IaaS-dator.
- App-databasen kommer att migreras till Azure Database for MySQL PaaS-tjänst.
- Eftersom de migrerar en produktionsarbetsbelastning resurserna kommer att finnas i resursgruppen produktion **ContosoRG**.
- Resurserna som ska replikeras till den primära regionen (östra USA 2) och placeras i företagets nätverk (VNET-PROD-EUS2):
    - Web VM kommer att finnas i undernätet på klientsidan (PROD-FE-EUS2).
    - Databasinstansen som kommer att finnas i undernätet (PROD-DB-EUS2) för databasen.
- App-databasen kommer att migreras till Azure MySQL med MySQL-verktyg.
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.


![Scenariots arkitektur](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Migreringsprocessen

Contoso kommer att slutföra migreringen på följande sätt:

Att migrera web VM:

1. Som ett första steg Contoso ställer in Azure och lokal infrastruktur som krävs för att distribuera Site Recovery.
2. När du har förberett Azure och lokala komponenter, de konfigurera och aktivera replikering för webb-VM.
3. När replikeringen har upp och som körs, migrera de den virtuella datorn genom att växla över till Azure.

Migrera databasen:

1. Contoso etablerar en MySQL-instans i Azure.
2. De konfigurera MySQL workbench och säkerhetskopiera databasen lokalt.
3. De sedan återställa databasen från en lokal säkerhetskopiering till Azure.

![Migreringsprocessen](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure.  Virtuella Azure-datorer skapas och betala, vid redundans. [Läs mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Databasen är baserad på öppen källkod MySQL Server-motorn. Det ger en fullständigt hanterad, företagsfärdig community MySQL-databas som en tjänst för apputveckling och distribution. 

 
## <a name="prerequisites"></a>Förutsättningar

Om du (och Contoso) du vill köra det här scenariot, är här vad du bör ha.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration under tidig artiklar i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | Ställa in sina Azure-infrastrukturen enligt beskrivningen i Contoso [Azure-infrastrukturen för migrering](contoso-migration-infrastructure.md).<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) kraven för Site Recovery.
**Lokala servrar** | Din lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värden.
**Lokala virtuella datorer** | [Granska kraven för Linux VM](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) som stöds för migrering med Site Recovery.<br/><br/> Kontrollera stöds [Linux-system för fil- och lagringstjänster](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Azure kommer att slutföra migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbereda Azure Site Recovery**: du skapar ett Azure storage-konto för att lagra replikerade data och skapa ett Recovery Services-valv.
> * **Steg 2: Förbereda lokala VMware för Site Recovery**: du förbereda konton för installation av VM-identifiering och agentinstallation och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
 * **Steg 3: etablera databasen]**: etablera en instans av Azure MySQL-databas i Azure.
> * **Steg 4: Replikera datorer**: Konfigurera Site Recovery-miljö för källa och mål, konfigurera en replikeringsprincip och börja replikera virtuella datorer till Azure storage.
> * **Steg 5: Migrera databasen**: du konfigurerar migrering med MySQL-verktyg.
> * **Steg 6: Migrera de virtuella datorerna med Site Recovery**: köra ett redundanstest för att kontrollera att allt fungerar och sedan köra en fullständig redundans om du vill migrera de virtuella datorerna till Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Steg 1: Förbereda Azure för Site Recovery-tjänsten

Contoso behöver några Azure-komponenter för Site Recovery:

- Ett virtuellt nätverk i vilket växlas över resurserna finns (Contoso använder produktion VNet som de redan har distribuerats).
- Ett nytt Azure storage-konto för replikerade data. 
- Ett Recovery Services-valv i Azure.

Contoso som redan har skapat det virtuella nätverket under [av Azure infrastructure](contoso-migration-infrastructure.md), så att de bara behöver för att skapa ett lagringskonto och valv.


1. Contoso skapar ett Azure storage-konto (**contosovmsacc20180528**) i regionen östra USA 2.

    - Lagringskontot måste finnas i samma region som Recovery Services-valvet.
    - Contoso använder ett konto för generell användning med standardlagring och LRS-replikering.

    ![Site Recovery-lagring](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Med nätverks- och storage-kontot på plats, Contoso skapar ett valv (ContosoMigrationVault) och placerar den i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2.

    ![Recovery Services-valv](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Steg 2: Förbereda lokala VMware för Site Recovery

Contoso förbereder den lokala VMware-infrastrukturen på följande sätt:

- Skapar ett konto på vCenter-servern att automatisera VM-identifiering.
- Skapar ett konto som tillåter automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Förbereder den lokala virtuella datorer, så att de kan ansluta till virtuella Azure-datorer när de skapas efter migreringen.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso ställer in kontot enligt följande:

1. Contoso skapar en roll på vCenter-nivå.
2. Contoso sedan tilldelar rollen behörigheterna som krävs.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje virtuell dator som Contoso vill migrera.

- Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för virtuella datorer.
- För automatisk installation. Site Recovery behöver ett konto med behörighet att komma åt den virtuella datorn. 
- Information om kontot för indata under installationen av replikering. 
- Kontot kan vara domän eller lokalt konto, så länge den har installationsbehörigheter.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till virtuella Azure-datorer efter redundansväxling till Azure. Detta gör som de behöver göra några saker: 

- För att komma åt via internet, gör SSH på den lokala Linux VM före migreringen.  För Ubuntu kan detta utföras med hjälp av följande kommando: **Sudo apt-get ssh installera -y**.
- Efter redundansen, ska de kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn.
- Om det inte fungerar kan de måste du kontrollera att den virtuella datorn körs, och dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-3-provision-azure-database-for-mysql"></a>Steg 3: Etablera Azure Database för MySQL

Contoso etablerar en MySQL-databas-instans i den primära regionen östra USA 2.

1. I Azure-portalen kan skapa de en Azure Database for MySQL-resurs. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. De lägger till namnet **contosoosticket** för Azure-databasen. De lägga till databasen i resursgruppen produktion **ContosoRG**, och ange autentiseringsuppgifter för den.
3. Den lokala MySQL-databas är version 5.7, så de väljer den här versionen för kompatibilitet. De använder standardstorlekar som matchar deras databaskrav.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. För **Redundansalternativen för säkerhetskopiering**, Contoso väljer för att använda **Geo-Redundant**. Det här alternativet gör att de kan återställa databasen i sina sekundära regionen USA, centrala vid ett eventuellt strömavbrott. De kan bara konfigurera det här alternativet när de konfigurerar databasen.

     ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. I den **VNET-PROD-EUS2** nätverk > **tjänstslutpunkter**, de lägga till tjänstens slutpunkt (database undernät) för SQL-tjänsten.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. När du lägger till undernätet kan skapa de en vnet-regel som tillåter åtkomst från undernätet på databasen i produktionsmiljön.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Steg 4: Replikera lokala virtuella datorer

Innan de kan migrera web virtuell dator till Azure, Contoso konfigurerar och aktiverar replikering.

### <a name="set-a-protection-goal"></a>Ange en skyddsmål

1. I valvet under valvnamnet (ContosoVMVault) de ange ett replikeringsmål (**komma igång** > **Site Recovery** > **Förbered infrastruktur**.
2. De ange som finns lokalt på sina datorer, att de är virtuella VMware-datorer och att de vill replikera till Azure.

    ![Replikeringsmål](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Om du vill fortsätta, de bekräftar att de har slutfört distributionsplanering genom att välja **Ja, jag har gjort det**. Contoso är bara migrera en enskild virtuell dator i det här scenariot och behöver inte distributionsplanering.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso behöver konfigurera sina källmiljö. Om du vill göra detta med hjälp av en OVF-mall som de distribuerar en konfigurationsserver för Site Recovery som en högtillgänglig, en lokal VMware VM. När configuration server är igång, registrera den i valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfigurationsserverkomponenten som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.

Contoso utför de här stegen på följande sätt:


1. De laddar ned OVF-mall från **förbereda infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Ladda ner OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. De importerar mallen till VMware för att skapa den virtuella datorn och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar, de loggar in på den virtuella datorn som administratör. Vid första inloggning körs Azure Site Recovery-konfigurationsverktyget som standard.
5. I verktyget Ange de ett namn som ska användas för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure.
7. När anslutningen har upprättats kan de logga in på Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv som de ska registrera konfigurationsservern.

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
9. De loggar in på datorn igen och i guiden Konfigurera serverhantering startar automatiskt.
10. I guiden Välj de nätverkskort för att ta emot replikeringstrafiken. Den här inställningen kan inte ändras när den har konfigurerats.
11. De Välj prenumeration, resursgrupp och valv där du vill registrera konfigurationsservern.

    ![Valvet](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Nu kan de ladda ned och installera MySQL-Server och VMWare PowerCLI. 
13. Efter valideringen kan ange de FQDN eller IP-adress för vCenter-servern eller vSphere-värden. De lämnar standardporten och anger ett eget namn för vCenter-servern.
14. De ange det konto som de har skapat för automatisk identifiering och de autentiseringsuppgifter som Site Recovery använder för att automatiskt installera Mobilitetstjänsten. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. När registreringen är klar, i Azure-portalen, Contoso kontrollerar att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
15. Med allt på plats, Site Recovery ansluter till VMware-servrar och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso indata rikta nu replikeringsinställningar.

1. I **Förbered infrastruktur** > **Target**, de väljer målinställningarna.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.


### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Contoso är redo att skapa en replikeringsprincip med käll- och ställa in.

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Replikeringspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens för appkonsekvent ögonblicksbild**. Som standard på en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincip](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Behöver du mer hjälp?**

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Läs mer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) om Azure-gästagenten för Linux.

### <a name="enable-replication-for-the-web-vm"></a>Aktivera replikering för Web-VM

Nu Contoso kan börja replikera den **OSTICKETWEB** VM.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer och välj inställningar för datakälla, inklusive vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Nu ange de målinställningarna. Dessa inkluderar resursgruppen och nätverk där den virtuella Azure-datorn kommer att finnas efter redundansväxling och lagringskontot som replikerade data ska lagras. 

     ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Contoso väljer **OSTICKETWEB** för replikering. 

    ![Aktivera replikering](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. Välj det konto som ska användas för att automatiskt installera Mobilitetstjänsten på den virtuella datorn i VM-egenskaperna de.

     ![Mobilitetstjänsten](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. i **replikeringsinställningar** > **konfigurera replikeringsinställningar**, de kontrollera att rätt replikeringsprincip är tillämpad och välj **Aktivera replikering**. Mobilitetstjänsten installeras automatiskt.
6.  De spåra Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.


**Behöver du mer hjälp?**

Du kan läsa en genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Steg 5: Migrera databasen

Contoso kommer att migrera databasen med säkerhetskopiering och återställning, med MySQL-verktyg. De installera MySQL Workbench, säkerhetskopiera databasen från OSTICKETMYSQL och sedan återställa det till Azure Database for MySQL-Server.

### <a name="install-mysql-workbench"></a>Installera MySQL Workbench

1. Contoso kontrollerar den [krav och nedladdningar MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. De installera MySQL Workbench för Windows i enlighet med den [Installationsinstruktioner](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. De skapar en MySQL-anslutning till OSTICKETMYSQL i MySQL Workbench. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. De exportera databasen som **osticket**, som en fristående lokal fil.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. När databasen har säkerhetskopierats lokalt, kan de skapa en anslutning till Azure Database for MySQL-instans.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Contoso kan nu importera (återställning) databasen i Azure MySQL-instans från den fristående fil. Ett nytt schema (osticket) skapas för instansen.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Steg 6: Migrera de virtuella datorerna med Site Recovery

Contoso som kör ett snabbt redundanstest och sedan migrera den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Kör ett redundanstest Kontrollera hjälper till att att allt fungerar som förväntat innan migreringen. 

1. Contoso kör ett redundanstest till den senaste tillgängliga tidpunkt (**senaste bearbetade**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan redundansen. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs för att kontrollera att alla de villkor som krävs för migrering är på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

3. När redundansen är klar visas repliken virtuell Azure-dator i Azure-portalen. Contoso kontrollerar att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs. 
4. När du har verifierat, rensa växling vid fel, de och registrera och sparar eventuella observationer.

### <a name="migrate-the-vm"></a>Migrera den virtuella datorn

Om du vill migrera den virtuella datorn, Contoso skapar en återställningsplan som innehåller den virtuella datorn och misslyckas planen över till Azure.

1. Contoso skapar en plan och lägger till **OSTICKETWEB** till den.

    ![Återställningsplan](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. De kör en redundansväxling med planen. De väljer du den senaste återställningspunkten och ange att Site Recovery bör försöka att stänga av den lokala virtuella datorn innan du utlöser redundansväxlingen. De kan följa redundansförloppet på den **jobb** sidan.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Under redundansen utfärdar kommandon för att stoppa de två virtuella datorer som körs på ESXi-värden i vCenter-servern.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Efter redundansväxlingen verifierar Contoso att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. När du har kontrollerat den virtuella datorn, slutföra de migreringen. Detta stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) som kör ett redundanstest. 
- [Lär dig](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxla till Azure.


### <a name="connect-the-vm-to-the-database"></a>Ansluta den virtuella datorn till databasen

Contoso uppdatera anslutningssträngen för appen så att den pekar till Azure Database för MySQL som det sista steget i migreringen. 

1. De gör en SSH-anslutning för OSTICKETWEB VM med hjälp av Putty eller någon annan SSH-klient. Den virtuella datorn är privat så att de ansluter via privata IP-adress.

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![Ansluta till databasen](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. De uppdatera inställningarna så att den **OSTICKETWEB** VM kan kommunicera med den **OSTICKETMYSQL** databas. Konfigurationen är för närvarande hårdkodad med den lokala IP-adressen 172.16.0.43.

    **Innan uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Efter uppdateringen**
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Uppdatera IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. De starta om tjänsten med **systemctl starta om apache2**.

    ![Starta om](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Slutligen de uppdaterar du DNS-posterna för **OSTICKETWEB**, på en av domänkontrollanterna Contoso.

    ![Uppdatera DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Med migreringen har slutförts körs app-nivåerna osTicket på virtuella Azure-datorer.

Contoso behöver nu kan du göra följande: 
- Ta bort den virtuella VMware-datorer från vCenter-lagret
- Ta bort de lokala virtuella datorerna från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentationen visar nya platser och IP-adresser. 
- Granska eventuella resurser som kan interagera med lokala virtuella datorer och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.
- Contoso för tjänsten Azure Migrate med beroendemappning för att bedöma den **OSTICKETWEB** VM för migrering. De ska nu ta bort agenter (Microsoft Monitoring Agent/beroende Agent) som de är installerade för detta ändamål, från den virtuella datorn.

## <a name="review-the-deployment"></a>Granska distributionen

Med den app som körs nu måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

Contoso security team granska den virtuella datorn och databasen för att fastställa eventuella säkerhetsproblem.

- De granska Nätverkssäkerhetsgrupper (NSG) för den virtuella datorn för åtkomstkontroll. NSG: er för att säkerställa att endast trafik som tillåts att programmet kan skicka.
- De anser att skydda data på VM-diskar med hjälp av diskkryptering och Azure KeyVault.
- Kommunikation mellan den virtuella dator och databasen instansen har inte konfigurerats för SSL. Användaren uppmanas att göra detta för att säkerställa att databastrafik inte kan vara hackad.

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsrutiner för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior

- Contoso säkerhetskopieras data på den virtuella datorn med hjälp av Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- De behöver inte konfigurera säkerhetskopiering för databasen. Azure Database för MySQL skapar automatiskt säkerhetskopiering av servrar och lagrar. De har valt för att använda geo-redundans för databasen, så att den är flexibel och vara färdigt för produktionsanvändning.

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- När du har distribuerat resurser, Contoso tilldelar Azure taggar, i enlighet med beslut som de har gjorts under den [Azure-infrastrukturen](contoso-migration-infrastructure.md#set-up-tagging) distribution.
- Det finns inga licensproblem för Contoso Ubuntu-servrar.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.


## <a name="next-steps"></a>Nästa steg

I det här scenariot visade vi sista rehost-situation Contoso provat. De migrerade klientdelens VM av lokala Linux osTicket appen till en Azure-dator och migrera app-databasen till en Azure MySQL-instans.

I nästa uppsättning självstudiekurser i serien migrering vi att visa hur Contoso utförs en mer komplex uppsättning migreringar, som rör app omstrukturering i stället för enkel lift and shift-migreringar.
