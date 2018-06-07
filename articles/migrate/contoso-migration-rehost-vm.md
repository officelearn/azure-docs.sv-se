---
title: Rehost en Contoso-app med migrering till virtuella datorer i Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig hur rehost appen lokalt och med en lift och SKIFT-migrering till Azure för migrering av lokala datorer med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2018
ms.author: raynew
ms.openlocfilehash: 11b5e2a408d3ba514753f3510b36fce02470c6e9
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825265"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso-migrering: Rehost en lokal app på virtuella Azure-datorer


Den här artikeln visar hur Contoso rehost SmartHotel appen i Azure, migrera appen virtuella datorer till virtuella Azure-datorer.


Det här dokumentet är i en serie artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, bedömning av lokala resurser för migrering och kör olika typer av migreringar. Scenarier växa i komplexitet och vi lägger till ytterligare artiklar över tid.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
Artikel 1: översikt | En översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
Artikel 2: Distribuera en Azure-infrastruktur | Beskriver hur Contoso förbereder dess lokalt och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla Contoso-Migreringsscenarier. | Tillgängligt
Artikel 3: Utvärdera lokala resurser  | Visar hur Contoso körs en bedömning av sina lokala två nivåer SmartHotel app som körs på VMware. De utvärdera app virtuella datorer med den [Azure migrera](migrate-overview.md) tjänsten och SQL Server-databasen app med den [Azure databasen Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
Artikel 4: Flytta (lift och SKIFT) till virtuella Azure-datorer och en SQL hanteras-instans (den här artikeln) | Visar hur Contoso migrerar SmartHotel appen till Azure. Migrerar appen klientdel VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app databasen med hjälp av [Azure Databasmigrering](https://docs.microsoft.com/azure/dms/dms-overview) tjänsten för att migrera till en hanterad SQL-instans. | Tillgängligt
Artikel 5: Flytta (lift och SKIFT) till virtuella Azure-datorer (den här artikeln) | Visar hur Contoso migrera SmartHotel appen VMs endast med Site Recovery.
Artikel 6: Flytta (lift och SKIFT) till virtuella datorer i Azure och SQL Server-Tillgänglighetsgrupper | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera appen virtuella datorer och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
Artikel 7: Flytta (lift och SKIFT) till virtuella Azure-datorer och Azure MySQL-Server | Visar hur Contoso migrerar SmartHotel app virtuella datorer med hjälp av Site Recovery och MySQL-arbetsstationen för att migrera (säkerhetskopiering och återställning) till en Azure MySQL Server-instans. | Tillgängligt

I den här artikeln kommer Contoso att migrera Windows två nivåer. NET SmartHotel app som körs på virtuella VMware-datorer, till Azure. Om du vill använda den här appen, den är öppen källkod och du kan ladda ned det från [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad de vill nå med migreringen:

- **Åtgärda tillväxt**: Contoso växer och det finns därför trycket på sina lokala datorer och infrastruktur.
- **Begränsa risken**: det SmartHotel appen är viktiga för företaget Contoso. De vill flytta den till Azure med noll risk.
- **Utöka**: Contoso vill inte att ändra appen. De vill kontrollera att det är stabil.


## <a name="migration-goals"></a>Mål för migrering

Contoso molnet team har fäst ned mål för den här migreringen. Dessa mål används för att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestanda som idag i VMware.  Appen kommer att finnas kvar som kritiskt i molnet som lokalt. 
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form de vill flytta på ett säkert sätt till molnet.
- Contoso vill inte ändra ops-modell för den här appen. De vill interagera med det i molnet på samma sätt som de gör det nu.
- Contoso vill inte ändra några funktioner i appen. Endast på platsen kommer att ändras.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

Här är den aktuella miljön

- Appen nivåer över två virtuella datorer (**WEBVM** och **SQLVM**).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter ska inaktiveras när migreringen är klar.

![Scenariots arkitektur](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>migreringsprocessen

Contoso migreras app klientdel och databasen virtuella datorer till virtuella Azure-datorer med Site Recovery:

- Som ett första steg bör ska de förbereda konfigurera komponenter som Azure Site Recovery och förbereda den lokala VMware-infrastrukturen.
- De redan har sina [Azure-infrastrukturen](contoso-migration-infrastructure.md) på plats, så de behöver bara lägga till några Azure komponenter för Site Recovery.
- De kan starta replikering av virtuella datorer med allt förberedd.
-När replikeringen är aktiverad och fungerar de migrera den virtuella datorn genom att växla över till Azure.

![migreringsprocessen](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten samordnar hanterar migrering och katastrofåterställning för virtuella datorer i Azure och lokala virtuella datorer och fysiska servrar.  | Azure Storage avgifter tas ut vid replikering till Azure.  Virtuella Azure-datorer skapas och debiteras när växling vid fel. [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.


## <a name="prerequisites"></a>Förutsättningar

Det här är vad du (och Contoso) behöver för att köra det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration under tidig artiklarna i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du är inte administratör, måste du arbeta med administratören att tilldela behörigheter för ägare eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [lagring](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) krav för Site Recovery.
**Lokala servrar** | Lokala vCenter-servrar bör köra version 5.5, 6.0 eller 6.5<br/><br/> ESXi-värdar ska köras version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer ska köras på ESXi-värd.
**Lokala virtuella datorer** | Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbered Azure Site Recovery**: de skapar ett Azure storage-konto för att lagra replikerade data och Recovery Services-valvet.
> * **Steg 2: Förbered lokal VMware för Site Recovery**: de förbereda konton för VM identifieringen och agentinstallationen på nytt och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 3: Replikera VMs**: de konfigurerar replikering och starta replikera virtuella datorer till Azure storage.
> * **Steg 4: Migrera de virtuella datorerna med Site Recovery**: de kör ett redundanstest för att kontrollera att allt fungerar och kör sedan en fullständig växling för att migrera de virtuella datorerna till Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Steg 1: Förbered Azure för Site Recovery-tjänsten

Här är Contoso behöver migrera de virtuella datorerna till Azure på Azure komponenter:

- Ett virtuellt nätverk som virtuella Azure-datorer kommer att finnas när de skapas under växling vid fel.
- Ett Azure storage-konto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

De har konfigurerat dessa på följande sätt:

1. Contoso som redan har konfigurerat ett nätverk som de kan använda Site Recovery när de [distribuerat Azure-infrastrukturen](contoso-migration-infrastructure.md)

    - SmartHotel appen är en produktionsapp och de virtuella datorerna kommer att migreras till Azure-produktionsnätverket (VNET-PRODUKTPRENUMERATION-EUS2) i den primära regionen östra USA 2.
    - Båda VM: ar placeras i resursgruppen ContosoRG som används för produktionsresurser.
    - App-klientdel VM (WEBVM) migreras till undernätet frontend (PROD-FE-EUS2) i produktionsmiljön.
    - Appen databasen VM (SQLVM) kommer att migrera till undernätet för databasen (PROD-DB-EUS2) i produktionsmiljön.

2. Contoso skapar ett Azure storage-konto (contosovmsacc20180528) i den primära regionen.
    - Lagringskontot måste finnas i samma region som Recovery Services-valvet.
    - De använder ett allmänt konto med standardlagring och LRS replikering. 

    ![Site Recovery-lagring](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Med nätverks- och storage-konto på plats Contoso nu skapar Recovery Services-valvet (ContosoMigrationVault) och placerar den i ContosoFailoverRG resursgrupp i den primära regionen östra USA 2.

    ![Recovery Services-valv](./media/contoso-migration-rehost-vm/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Steg 2: Förbered lokal VMware för Site Recovery

Här är vad Contoso förbereds lokalt:

- Ett konto på vCenter server eller vSphere ESXi värden, att automatisera VM-identifiering.
- Ett konto som ger automatisk installation av mobilitetstjänsten på den virtuella VMware-datorer. 
- Lokalt för Virtuella datorer, så att Contoso kan ansluta till den replikerade virtuella Azure-datorer efter redundans.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Automatiskt identifiera virtuella datorer. 
- Samordna replikering, redundans och återställning för virtuella datorer.
- Minst ett skrivskyddat konto krävs. Kontot ska kunna köra åtgärder som till exempel att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso har konfigurerat kontot på följande sätt:

1. De skapar en roll på vCenter-nivå.
2. De tilldela rollen behörigheterna som krävs.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje virtuell dator.

- Site Recovery kan göra en automatisk push-installation av mobilitetstjänsten när VM-replikering är aktiverat.
- Ett konto krävs för att Site Recovery kan komma åt de virtuella datorerna för push-installation. Du kan ange det här kontot när du konfigurerar replikering.
- Kontot kan vara domän eller lokalt med behörigheter för att installera på virtuella datorer.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill ansluta till virtuella Azure-datorer efter redundans. Om du vill göra detta måste göra de följande innan migreringen:

1. För åtkomst via internet de:

 - Aktiverar du RDP på den lokala virtuella datorn innan redundans
 - Kontrollera att TCP och UDP-regler har lagts till för den **offentliga** profil.
 - Kontrollera att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
 
2. För åtkomst via plats-till-plats-VPN, de:

 - Aktiverar du RDP på den lokala datorn.
 - Tillåt RDP i den **Windows-brandväggen** -> **tillåtna appar och funktioner**, för **domän och privat** nätverk.
 - Ange operativsystemets SAN-principen på den lokala virtuella datorn till **OnlineAll**.

Dessutom, när de kör en redundansväxling behöver de för att kontrollera följande:

- Det ska vara ingen Windows-uppdateringar som väntar på den virtuella datorn när utlösa en växling vid fel. Om det finns, kan de inte logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter växling vid fel, kan de kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn. Om det inte fungerar de bör kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-3-replicate-the-on-premises-vms"></a>Steg 3: Replikera lokala virtuella datorer

Innan de kan köra en migrering till Azure, måste Contoso att konfigurera och aktivera replikering.

### <a name="set-a-replication-goal"></a>Ange ett mål för replikering

1. I valvet, under valvnamnet (ContosoVMVault) de väljer ett mål för replikering (**komma igång** > **Site Recovery** > **Förbered infrastruktur** .
2. De ange som finns lokalt, körs på VMware och replikerar till Azure på sina datorer.

    ![Replikeringsmål](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta att planera distribution

Om du vill fortsätta, de bekräftar att de har slutfört planera distribution genom att välja **Ja, jag har gjort det**. I det här scenariot Contoso migrerar bara två virtuella datorer och behöver inte distributionsplanering.


### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso måste konfigurera sina källmiljön. Om du vill göra detta måste de hämtar ett OVF-mall och distribuera Site Recovery konfigurationsservern med hög tillgänglighet, lokal VMware VM. När konfigurationsservern är igång kan registrera den i den här valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfiguration av serverkomponent som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processerver som fungerar som en replikerings-gateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.



Contoso utför de här stegen på följande sätt:

1. De hämtar OVF mallen från i valvet, **Förbered infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Hämta OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. De importera mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar kan logga de in på den virtuella datorn som administratör. Vid första inloggningen körs konfigurationsverktyget för Azure Site Recovery som standard.
5. I verktyget Ange de ett namn för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har etablerats kan de logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till valvet där de ska registrera konfigurationsservern.

    [Registrera konfigurationsservern](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. De loggar in på datorn igen och guiden Konfigurera Server Management startar automatiskt.
9. I guiden Välj de nätverkskort för att ta emot replikeringstrafik. Den här inställningen kan inte ändras när den har konfigurerats.
10. De Välj prenumerationen, resursgruppen och valvet att registrera konfigurationsservern.
        ![Valvet](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. De kan hämta och installera MySQL-Server och VMWare PowerCLI. 
11. När valideringen ange de FQDN eller IP-adressen för vCenter-server eller vSphere-värd. De lämnar standardporten och ange ett eget namn för servern i Azure.
12. De kan ange det konto som de har skapats för automatisk upptäckt och de autentiseringsuppgifter som används för att automatiskt installera Mobilitetstjänsten. För Windows-datorer måste kontot behörighet som lokal administratör på virtuella datorer.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. När registreringen är klar i Azure-portalen Contoso dubbla kontrollerar att konfigurationsservern och VMware-server visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso anger nu Målinställningar för replikering.

1. I **Förbered infrastruktur** > **mål**, de Välj inställningar för målet.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i den angivna målplatsen.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Contoso kan nu skapa en replikeringsprincip.

1. I **Förbered infrastruktur** > **replikeringsinställningarna** > **Replikeringsprincipen** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Återställningspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållningstid för återställningspunkten**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperiod är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens av programkonsekventa ögonblicksbilder**. Standardvärdet för en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.

        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincipen](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Aktivera replikering för WEBVM

Med allt på plats aktivera Contoso replikering för virtuella datorer. De startar med WebVM.

1. I **replikera program** > **källa** > **+ replikera** de välja inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. De Välj målinställningar, inklusive resursgruppen och Azure-nätverk och storage-konto.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Contoso väljer **WebVM** för replikering, kontrollerar du replikeringsprinciper och aktiverar replikering.

    - I det här skedet väljer Contoso endast WEBVM eftersom VNet och undernät måste väljas och Contosos är att placera appen virtuella datorer i olika undernät.
    - Site Recovery installerar automatiskt mobilitetstjänsten på den virtuella datorn när replikeringen är aktiverad.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. De spåra replikeringsförloppet på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
6. I **Essentials** i Azure-portalen Contoso kan se strukturen för de virtuella datorerna som replikerar till Azure.


### <a name="enable-replication-for-sqlvm"></a>Aktivera replikering för SQLVM

Contoso kan nu starta replikerar den SQLVM datorn, med samma process som ovan.

1. De Välj inställningar för datakälla.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. De kan sedan ange Målinställningar.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. De Välj SQLVM för replikering. 

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. De gäller samma replikeringsprincip som användes för WEBVM och aktivera replikering.

    ![Visa infrastruktur](./media/contoso-migration-rehost-vm/essentials.png)

**Behöver du mer hjälp?**

- Du kan läsa en fullständig genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurerar replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Du kan lära dig mer om [replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Steg 4: Migrera de virtuella datorerna 

Contoso kör snabbt testa redundans och en fullständig växling för att migrera de virtuella datorerna.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Testa redundans ser till att allt fungerar som förväntat. 

1. Contoso kör ett redundanstest till den senaste punkten i tid (**senaste bearbetas**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs se till att alla villkor krävs för migrering på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.
    
3. När redundansväxlingen är klar visas repliken Azure VM i Azure-portalen. Contoso kontrollerar att den virtuella datorn har rätt storlek, ansluten till rätt nätverk och körs. 
4. När du har verifierat redundanstestningen Rensa växling vid fel, de och registrera och spara synpunkter. 

### <a name="create-and-customize-a-recovery-plan"></a>Skapa och anpassa en återställningsplan

 När du har verifierat att du testar redundansen fungerade som förväntat, skapar en återställningsplan för migrering i Contoso. 

- En återställningsplan anger ordningen i vilken redundans inträffar och visar hur virtuella Azure-datorer ska anslutas i Azure.
- Eftersom appen är två nivåer, anpassa de återställningsplanen så att data VM (SQLVM) startar före klientdel (WEBVM).

1. I **Återställningsplaner (Site Recovery)** > **+ återställningsplan**, de skapar en plan och lägger till de virtuella datorerna.

    ![Återställningsplan](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. När du har skapat planen de anpassa den (**Återställningsplaner** > **SmartHotelMigrationPlan** > **anpassa**).
2.  De tar bort WEBVM från **grupp 1: starta**.  Detta säkerställer att den första startåtgärden påverkar SQLVM endast.
3.  I **+ grupp** > **Lägg till skyddade objekt**, de lägger till WEBVM Grupp2: starta.  De virtuella datorerna måste vara i två olika grupper.


### <a name="migrate-the-vms"></a>Migrera de virtuella datorerna


Contoso kan nu köra en fullständig växling för att slutföra migreringen.

1. De väljer återställningsplanen > **redundans**.
2. De välja för att växla över till den senaste återställningspunkten och den Site Recovery bör försök att stänga av den lokala virtuella datorn innan växling vid fel. De kan följa förloppet för växling vid fel på den **jobb** sidan.

    ![Redundans](./media/contoso-migration-rehost-vm/failover1.png)


3. Efter växling vid fel kontrollera de att Azure VM visas som förväntat i Azure-portalen.

    ![Redundans](./media/contoso-migration-rehost-vm/failover2.png)  

3. När kontrollen är klar slutför de migrering för varje virtuell dator. Detta stoppar replikering för den virtuella datorn och stoppar Site Recovery-faktureringen för den.

    ![Redundans](./media/contoso-migration-rehost-vm/failover3.png)

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) kör ett redundanstest. 
- [Läs](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxlar till Azure.

## <a name="clean-up-after-migration"></a>Rensa efter migreringen

Migreringen är klar körs SmartHotel app nivåer nu på virtuella Azure-datorer.

Nu behöver Contoso Rensa stegen:  

- Ta bort den WEBVM datorn från vCenter-lagret.
- Ta bort den SQLVM datorn från vCenter-lagret.
- Ta bort WEBVM och SQLVM från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentation om du vill visa den nya platsen och IP-adresser för de virtuella datorerna.
- Granska alla resurser som interagerar med de virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.

## <a name="review-the-deployment"></a>Granska distributionen

Med appen är nu kör måste Contoso helt operationalisera och skydda den i Azure.

### <a name="security"></a>Säkerhet

Contoso security-teamet har granskat den virtuella Azure-datorer för att fastställa eventuella säkerhetsproblem.

- Om du vill styra åtkomst, granska de Nätverkssäkerhetsgrupper (NSG: er) för de virtuella datorerna. NSG: er används för att se till att endast trafik som tillåts i appen kan nå.
- De kan också överväga att skydda data på disken med hjälp av Azure Disk Encryption och KeyVault.

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsåtgärder för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior

Contoso kommer att säkerhetskopiera data på virtuella datorer med hjälp av Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licens- och optimering

1. Contoso har befintliga licens för sina virtuella datorer och använder förmån för Azure-Hybrid.  De kommer att konverteras den befintliga virtuella Azure-datorer för att dra nytta av den här prisnivån.
2. Contoso aktiverar Azure kostnaden Management licensierade av Cloudyn, ett Microsoft-kontor. Det är en lösning för flera molnkostnad som hjälper dig att använda och hantera Azure och andra molnresurser. [Lär dig mer](https://docs.microsoft.com/azure/cost-management/overview) om hantering av Azure kostnaden. 

## <a name="conclusion"></a>Sammanfattning

I den här artikeln rehosted Contoso SmartHotel app i Azure genom att migrera appen virtuella datorer till virtuella Azure-datorer med Site Recovery-tjänsten. 


## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien ska vi visa hur Contoso rehost SmartHotel app klientdel VM i Azure med hjälp av Site Recovery-tjänsten och migrera databasen till en Azure SQL AlwaysOn-tillgänglighetsgrupp.

