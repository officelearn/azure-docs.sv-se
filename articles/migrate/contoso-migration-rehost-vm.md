---
title: Ange ny värd för en Contoso-app med migrering till Azure virtuella datorer med Azure Site Recovery | Microsoft Docs
description: Lär dig hur appvärd både lokalt och med en lift and shift-migrering till Azure för migrering av lokala datorer med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: e152a95a392fd503e027591a4930fbeef744d6f4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009422"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso-migrering: Appvärd till virtuella Azure-datorer på plats


Den här artikeln visar hur Contoso namnkonflikt lokal SmartHotel app i Azure, genom att migrera appen virtuella datorer till virtuella Azure-datorer.


Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar n lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, utvärdera lokala resurser för migrering och som kör olika typer av migreringar. Scenarier växer i komplexitet och vi lägger till ytterligare artiklar med tiden.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder sina lokala och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av en lokal tvålagers-SmartHotel-app som körs på VMware. Contoso utvärderar app virtuella datorer med den [Azure Migrate](migrate-overview.md) -tjänsten och SQL Server-databas för appen med den [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Ange ny värd för en app på Azure virtuella datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso körs en lift and shift-migrering till Azure för lokala SmartHotel appen. Contoso migrerar appen klientdelens VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app-databasen till en SQL Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt
Artikel 5: Ange ny värd för en app på virtuella Azure-datorer | Visar hur Contoso migrerar appen SmartHotel virtuella datorer till virtuella Azure-datorer med hjälp av Site Recovery-tjänsten. | Den här artikeln.
[Artikel 6: Ange ny värd för en app på Azure virtuella datorer och SQLServer Always On-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen och tjänsten Databasmigrering att migrera app-databas till en SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso gör en lift and shift-migrering av Linux osTicket-app till virtuella Azure-datorer med Site Recovery | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Site Recovery och migrerar app-databasen till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Visar hur Contoso migrerar SmartHotel-app till ett Azure Web Apps och app-databasen har migrerats till Azure SQL Server-instans | Tillgängligt
[Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure Web Apps på flera platser, integrerad med GitHub för kontinuerlig leverans. De migrera app-databasen till en Azure MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera TFS på VSTS](contoso-migration-tfs-vsts.md) | Visar hur Contoso migrerar sina lokala Team Foundation Server (TFS)-distributionen genom att migrera den till Visual Studio Team Services (VSTS) i Azure. | Tillgängligt
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Visar hur Contoso migrerar och rearchitects SmartHotel appen till Azure. De Omforma webbnivån appen som en Windows-behållare och app-databasen i en Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Visar hur Contoso återskapa SmartHotel appen med hjälp av en mängd Azure-funktioner och tjänster, inklusive App Services, Azure Kubernetes, Azure Functions, Cognitive services och Cosmos DB. | Tillgängligt



I den här artikeln kommer Contoso att migrera tvålagers-Windows. NET SmartHotel app som körs på virtuella VMware-datorer till Azure. Om du vill använda den här appen tillhandahålls som öppen källkod och du kan ladda ned det från [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer, och därför har Press på sina lokala system och infrastruktur.
- **Begränsa risken**: The SmartHotel app är viktigt för företaget Contoso. De vill flytta den till Azure med noll risk.
- **Utöka**: Contoso inte vill ändra appen. De vill säkerställa att den är stabil.


## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för den här migreringen. Dessa mål används till att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestandafunktioner eftersom det gör idag i VMware.  Appen finns kvar som kritiskt i molnet eftersom det är på plats. 
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form de helt enkelt vill flytta på ett säkert sätt till molnet.
- Contoso vill inte ändra ops-modellen för den här appen. De vill interagera med den i molnet på samma sätt som de gör nu.
- Contoso vill inte ändra någon app-funktioner. App plats kommer att ändras.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

Här är den aktuella miljön

- Appen är nivåindelad över två virtuella datorer (**WEBVM** och **SQLVM**).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.

![Scenariots arkitektur](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>Migreringsprocessen

Contoso migrerar app klient- och databasen virtuella datorer till virtuella Azure-datorer med Site Recovery:

- Som ett första steg de Förbered och konfigurera Azure-komponenter för Site Recovery och förbereda lokala VMware-infrastruktur.
- De redan har sina [Azure-infrastrukturen](contoso-migration-infrastructure.md) på plats, så de behöver bara lägga till några Azure-komponenter specifikt för Site Recovery.
- Med allt förberett börjar de replikera de virtuella datorerna.
-När replikering har aktiverats och arbetar måste de migrera den virtuella datorn genom att växla över till Azure.

![Migreringsprocessen](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure.  Virtuella Azure-datorer skapas och betala, vid redundans. [Läs mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.


## <a name="prerequisites"></a>Förutsättningar

Här är vad du (och Contoso) behöver att köra det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration under tidig artiklar i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) kraven för Site Recovery.
**Lokala servrar** | Lokala vCenter-servrar bör kör version 5.5, 6.0 eller 6.5<br/><br/> ESXi-värdar som ska köras version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer ska köras på ESXi-värden.
**Lokala virtuella datorer** | Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbereda Azure Site Recovery**: de skapar ett Azure storage-konto för att lagra replikerade data och Recovery Services-valvet.
> * **Steg 2: Förbereda lokala VMware för Site Recovery**: de förbereda konton för installation av VM-identifiering och agentinstallation och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 3: Replikera datorer**: de konfigurerar replikering och börja replikera virtuella datorer till Azure storage.
> * **Steg 4: Migrera de virtuella datorerna med Site Recovery**: de kör ett redundanstest för att kontrollera att allt fungerar och sedan köra en fullständig redundans om du vill migrera de virtuella datorerna till Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Steg 1: Förbereda Azure för Site Recovery-tjänsten

Här är Azure-komponenterna Contoso behöver för att migrera de virtuella datorerna till Azure:

- Ett virtuellt nätverk som virtuella Azure-datorer kommer att finnas när de skapas under en redundansväxling.
- Ett Azure storage-konto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

De har konfigurerat dessa på följande sätt:

1. Contoso som redan har konfigurerat ett nätverk som de kan använda för Site Recovery när de [distribuerade Azure-infrastrukturen](contoso-migration-infrastructure.md)

    - SmartHotel-app är en produktionsapp och de virtuella datorerna kommer att migreras till Azure-produktionsnätverket (VNET-PROD-EUS2) i den primära regionen östra USA 2.
    - Båda virtuella datorerna kommer att placeras i resursgruppen ContosoRG som används för produktionsresurser.
    - App-klientdelens VM (WEBVM) kommer att migrera frontend-undernätet (PROD-FE-EUS2), i produktionsmiljön.
    - App-databasen VM (SQLVM) kommer att migrera till undernätet för databasen (PROD-DB-EUS2), i produktionsnätverket.

2. Contoso skapar ett Azure storage-konto (contosovmsacc20180528) i den primära regionen.
    - Lagringskontot måste finnas i samma region som Recovery Services-valvet.
    - De använder ett allmänt konto med standardlagring och LRS-replikering. 

    ![Site Recovery-lagring](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Med nätverks- och storage-kontot på plats, Contoso nu skapar ett Recovery Services-valv (ContosoMigrationVault) och placerar den i ContosoFailoverRG resursgrupp i den primära regionen östra USA 2.

    ![Recovery Services-valv](./media/contoso-migration-rehost-vm/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Steg 2: Förbereda lokala VMware för Site Recovery

Här är vad Contoso förbereder lokalt:

- Ett konto på vCenter-servern eller vSphere ESXi-värden, att automatisera VM-identifiering.
- Ett konto som tillåter automatisk installation av mobilitetstjänsten på virtuella VMware-datorer. 
- Lokala inställningarna för virtuella datorer så att Contoso kan ansluta till den replikerade virtuella Azure-datorer efter redundans.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. 
- Dirigera replikering, redundans och återställning efter fel för virtuella datorer.
- Minst ett skrivskyddat konto krävs. Konton som ska kunna köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso ställer in kontot enligt följande:

1. De kan skapa en roll på vCenter-nivå.
2. De tilldela rollen behörigheterna som krävs.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje virtuell dator.

- Site Recovery kan göra en automatisk push-installation av mobilitetstjänsten när replikering av virtuella datorer är aktiverad.
- Ett konto krävs, så att Site Recovery kan få åtkomst till de virtuella datorerna för push-installationen. Du anger det här kontot när du konfigurerar replikering.
- Kontot kan vara domän eller lokal, med behörigheter för att installera på virtuella datorer.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill ansluta till virtuella Azure-datorer efter redundansväxling. Om du vill göra detta måste göra de följande innan migreringen:

1. För åtkomst via internet de:

 - Aktiverar du RDP på den lokala virtuella datorn före redundans
 - Se till att TCP och UDP-regler lagts till för den **offentliga** profil.
 - Kontrollera att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
 
2. För åtkomst via plats-till-plats-VPN, de:

 - Aktiverar du RDP på den lokala datorn.
 - Tillåt RDP i den **Windows-brandväggen** -> **tillåtna appar och funktioner**, för **domän och privat** nätverk.
 - Ange operativsystemets SAN-princip på den lokala virtuella datorn till **OnlineAll**.

Dessutom när de kör en redundans måste de du kontrollera följande:

- Det bör finnas inga Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Om det finns kan de inte kunna logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter en redundansväxling, kan de kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om det inte fungerar de bör kontrollera att den virtuella datorn körs, och dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-3-replicate-the-on-premises-vms"></a>Steg 3: Replikera lokala virtuella datorer

Innan de kan köra en migrering till Azure, måste Contoso att konfigurera och aktivera replikering.

### <a name="set-a-replication-goal"></a>Ange ett replikeringsmål

1. I valvet under valvnamnet (ContosoVMVault) de väljer ett replikeringsmål (**komma igång** > **Site Recovery** > **Förbered infrastruktur** .
2. De ange som finns lokalt, som körs på VMware och replikeras till Azure på sina datorer.

    ![Replikeringsmål](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Om du vill fortsätta, de bekräftar att de har slutfört distributionsplanering genom att välja **Ja, jag har gjort det**. I det här scenariot Contoso migrerar bara två virtuella datorer och behöver inte distributionsplanering.


### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso behöver konfigurera sina källmiljö. Detta gör de ladda ned en OVF-mall och använda den för att distribuera Site Recovery configuration server med hög tillgänglighet, en lokal VMware VM. När configuration server är igång, registrera den i valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfigurationsserverkomponenten som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.



Contoso utför de här stegen på följande sätt:

1. I valvet, de ladda ner OVF-mall från **förbereda infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Ladda ner OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. De importerar mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar, de loggar in på den virtuella datorn som administratör. Vid första inloggning körs Azure Site Recovery-konfigurationsverktyget som standard.
5. De kan ange ett namn för att registrera konfigurationsservern i valvet i verktyget.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats kan de logga in på Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv som de ska registrera konfigurationsservern.

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. De loggar in på datorn igen och i guiden Konfigurera serverhantering startar automatiskt.
9. I guiden Välj de nätverkskort för att ta emot replikeringstrafiken. Den här inställningen kan inte ändras när den har konfigurerats.
10. De Välj prenumerationen, resursgrupp och valv där du vill registrera konfigurationsservern.
        ![Valvet](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. De ladda ned och installera MySQL-Server och VMWare PowerCLI. 
11. Efter valideringen kan ange de FQDN eller IP-adress för vCenter-servern eller vSphere-värden. De lämnar standardporten och anger ett eget namn för servern i Azure.
12. De ange det konto som de har skapat för automatisk identifiering och de autentiseringsuppgifter som används för att automatiskt installera Mobilitetstjänsten. Kontot måste ha lokal administratörsbehörighet på de virtuella datorerna för Windows-datorer.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. När registreringen är klar, i Azure-portalen, Contoso dubbla kontrollerar att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Nu anger Contoso Målinställningar för replikering.

1. I **Förbered infrastruktur** > **Target**, de väljer målinställningarna.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i den angivna målplatsen.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Contoso kan nu skapa en replikeringsprincip.

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Replikeringspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens för appkonsekvent ögonblicksbild**. Som standard på en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.

        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincip](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Aktivera replikering för WEBVM

Med allt på plats aktivera Contoso replikering för de virtuella datorerna. De börjar med WebVM.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. De Välj målinställningar, inklusive resursgruppen och Azure-nätverk och storage-konto.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Contoso väljer **WebVM** för replikering, kontrollerar du replikeringsprincipen och aktiverar replikering.

    - I det här skedet väljer Contoso endast WEBVM eftersom VNet och undernät måste väljas och Contosos är att placera de virtuella datorerna för appen i olika undernät.
    - Site Recovery installerar automatiskt mobilitetstjänsten på den virtuella datorn när replikering har aktiverats.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. De spåra Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
6. I **Essentials** i Azure-portalen, Contoso kan se strukturen för de virtuella datorerna replikeras till Azure.


### <a name="enable-replication-for-sqlvm"></a>Aktivera replikering för SQLVM

Contoso kan nu börja replikera SQLVM-datorn, med samma process som ovan.

1. De Välj inställningar för datakälla.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. De kan sedan ange målinställningarna.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. De väljer SQLVM för replikering. 

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. De gäller samma replikeringsprincip som användes för WEBVM och aktivera replikering.

    ![Infrastruktur-vy](./media/contoso-migration-rehost-vm/essentials.png)

**Behöver du mer hjälp?**

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Du kan läsa mer om [att aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Steg 4: Migrera de virtuella datorerna 

Contoso kör en snabb testredundans och en fullständig växling vid fel att migrera de virtuella datorerna.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Ett redundanstest ser till att allt fungerar som förväntat. 

1. Contoso kör ett redundanstest till den senaste tillgängliga tidpunkt (**senaste bearbetade**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan redundansen. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs för att kontrollera att alla de villkor som krävs för migrering är på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.
    
3. När redundansen är klar visas repliken virtuell Azure-dator i Azure-portalen. Contoso kontrollerar att den virtuella datorn har rätt storlek, ansluten till rätt nätverk och körs. 
4. När du har verifierat redundanstestningen Rensa växling vid fel, de och registrera och sparar eventuella observationer. 

### <a name="create-and-customize-a-recovery-plan"></a>Skapa och anpassa en återställningsplan

 Efter att ha kontrollerat att redundanstestningen fungerar som förväntat, skapar Contoso du en återställningsplan för migrering. 

- En återställningsplan anger ordningen i vilken redundans inträffar och visar hur virtuella Azure-datorer ska anslutas i Azure.
- Eftersom appen är två lager kan anpassa de återställningsplanen så att data VM (SQLVM) startas före klientdelen (WEBVM).

1. I **Återställningsplaner (Site Recovery)** > **+ återställningsplan**, de skapa en plan och Lägg till de virtuella datorerna.

    ![Återställningsplan](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. När du har skapat planen, anpassa de den (**Återställningsplaner** > **SmartHotelMigrationPlan** > **anpassa**).
2.  De tar bort WEBVM från **grupp 1: starta**.  Detta säkerställer att den första startåtgärden påverkar SQLVM endast.
3.  I **+ grupp** > **Lägg till skyddade objekt**, de lägger till WEBVM Grupp2: börja.  De virtuella datorerna måste vara i två olika grupper.


### <a name="migrate-the-vms"></a>Migrera de virtuella datorerna


Contoso kan nu köra en fullständig redundans för att slutföra migreringen.

1. De väljer återställningsplanen > **redundans**.
2. De väljer för att växla över till den senaste återställningspunkten och den Site Recovery bör försök att stänga av den lokala virtuella datorn innan du utlöser redundansväxlingen. De kan följa redundansförloppet på den **jobb** sidan.

    ![Redundans](./media/contoso-migration-rehost-vm/failover1.png)


3. Efter redundansen kan kontrollera de att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

    ![Redundans](./media/contoso-migration-rehost-vm/failover2.png)  

3. När kontrollen är klar slutför de migreringen för varje virtuell dator. Detta stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den.

    ![Redundans](./media/contoso-migration-rehost-vm/failover3.png)

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) som kör ett redundanstest. 
- [Lär dig](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxla till Azure.

## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Med migreringen har slutförts körs app-nivåerna SmartHotel nu på virtuella Azure-datorer.

Nu kan måste Contoso slutföra stegen rensning:  

- Ta bort den WEBVM datorn från vCenter-lagret.
- Ta bort den SQLVM datorn från vCenter-lagret.
- Ta bort WEBVM och SQLVM från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentation för att visa den nya platsen och IP-adresser för de virtuella datorerna.
- Granska eventuella resurser som interagerar med de virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.

## <a name="review-the-deployment"></a>Granska distributionen

Med den app som körs nu måste Contoso helt operationalisera och skydda den i Azure.

### <a name="security"></a>Säkerhet

Contoso security-teamet granskar den virtuella Azure-datorer för att fastställa eventuella säkerhetsproblem.

- För att styra åtkomsten, granska de Nätverkssäkerhetsgrupper (NSG) för de virtuella datorerna. NSG: er för att säkerställa att endast trafik som tillåts i appen kan nå.
- De också överväga att skydda data på disken med hjälp av Azure Disk Encryption och KeyVault.

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsrutiner för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior

Contoso kommer att säkerhetskopiera data på de virtuella datorerna med hjälp av Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

1. Contoso har befintliga licenser för sina virtuella datorer och att utnyttja Azure Hybrid-förmånen.  De konverterar befintliga virtuella Azure-datorer, om du vill dra nytta av den här prissättning.
2. Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser. [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management. 

## <a name="conclusion"></a>Sammanfattning

I den här artikeln rehosted Contoso SmartHotel-app i Azure genom att migrera de virtuella datorerna för appen till Azure virtuella datorer med Site Recovery-tjänsten. 


## <a name="next-steps"></a>Nästa steg

I den [nästa artikel](contoso-migration-rehost-vm-sql-ag.md) i serien, får du lära dig hur Contoso namnkonflikt SmartHotel app klientdelens VM på en Azure virtuell dator och migrerar databasen till en SQL Server AlwaysOn Availability Group i Azure.

