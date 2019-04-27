---
title: Ange ny värd för en Contoso-app med migrering till Azure virtuella datorer med Azure Site Recovery | Microsoft Docs
description: Lär dig hur rehost en lokal app med en lift and shift-migrering av lokala datorer till Azure med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4a6ed900753747c1d5bf394aced54da11177320f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60668413"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso-migrering: Byt värd för en lokal app till virtuella Azure-datorer


Den här artikeln visar hur Contoso namnkonflikt lokal SmartHotel360-app i Azure, genom att migrera appen virtuella datorer till virtuella Azure-datorer.


Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, utvärdera lokala resurser för migrering och som kör olika typer av migreringar. Scenarier växer i komplexitet. Vi lägger till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: Översikt över](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera Azure-infrastrukturen](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt
Artikel 5: Byta värd för en app på virtuella Azure-datorer | Contoso migrerar dess SmartHotel360 app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Den här artikeln
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel360-app. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Ange ny värd för en app för Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso har slutförts en lift and shift-migrering av Linux osTicket app på virtuella Azure-datorer med Azure Site Recovery | Tillgängligt
[Artikel 8: Ange ny värd för en app för Linux på Azure virtuella datorer och Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Azure Site Recovery och migrerar app-databasen till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar SmartHotel360-app till ett Azure Web Apps och app-databasen har migrerats till en Azure SQL Server-instans med Database Migration Assistant | Tillgängligt
[Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera TFS på Azure DevOps-tjänsterna](contoso-migration-tfs-vsts.md) | Contoso migrerar dess lokal Team Foundation Server-distribution till Azure DevOps-tjänsterna i Azure. | Tillgängligt
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt
[Artikel 14: Skala en migrering till Azure](contoso-migration-scale.md) | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. | Tillgängligt


I den här artikeln kommer Contoso att migrera tvålagers-Windows. NET SmartHotel360-app som körs på virtuella VMware-datorer till Azure. Om du vill använda den här appen tillhandahålls som öppen källkod och du kan ladda ned det från [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer och det finns därför trycket på sina lokala system och infrastrukturer.
- **Begränsa risken**: SmartHotel360-appen är viktigt för företaget Contoso. Företaget vill flytta appen till Azure med noll risk.
- **Utöka**: Contoso vill inte ändra appen, men vill säkerställa att den är stabil.


## <a name="migration-goals"></a>Migreringsmål

Contoso cloud-teamet har fästs ned mål för den här migreringen. Dessa mål används till att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestandafunktioner eftersom det gör idag i VMware. Appen finns kvar som kritiskt i molnet eftersom det är på plats.
- Contoso vill inte investera i den här appen. Det är viktigt att verksamheten, men i sin nuvarande form Contoso helt enkelt vill flytta på ett säkert sätt till molnet.
- Contoso vill inte ändra ops-modellen för den här appen. Contoso vill interagera med den i molnet på samma sätt som de gör nu.
- Contoso vill inte ändra någon app-funktioner. App plats kommer att ändras.


## <a name="solution-design"></a>Lösningsdesign

Efter att fästa ned mål och krav, Contoso utformar och granska en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som använder Contoso för migreringen.

### <a name="current-app"></a>Aktuell app

- Appen är nivåindelad över två virtuella datorer (**WEBVM** och **SQLVM**).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).

### <a name="proposed-architecture"></a>Föreslagna arkitektur

- Eftersom appen är en produktionsarbetsbelastning, kommer att finnas i produktion resursgruppen ContosoRG appen virtuella datorer i Azure.
- Appen virtuella datorer ska migreras till den primära Azure-regionen (USA, östra 2) och placeras i företagets nätverk (VNET-PROD-EUS2).
- Webbservergrupp VM kommer att finnas i undernätet på klientsidan (PROD-FE-EUS2) i produktionsmiljön.
- Databasen VM kommer att finnas i databas-undernät (PROD-DB-EUS2) i produktionsmiljön.
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.

![Scenariots arkitektur](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>Överväganden för databasen

Som en del av designprocessen gjorde Contoso en funktionsjämförelse mellan Azure SQL Database och SQL Server. Följande överväganden hjälpte dem att bestämma att gå med SQL Server som körs på en Azure IaaS-VM:

- Med hjälp av en Azure-dator som kör SQL Server verkar vara en optimal lösning om Contoso behöver anpassa operativsystemet eller databasservern, eller om det kanske vill samordna och köra appar från tredje part på samma virtuella dator.
- Med Software Assurance kan i framtiden Contoso utbyta befintliga licenser för rabatterade priser på en SQL Database Managed Instance med Azure Hybrid-förmånen för SQL Server. Detta kan spara upp till 30 procent på hanterad instans.



### <a name="solution-review"></a>Granska lösningen

Contoso utvärderar den föreslagna designen genom att sätta ihop en lista med för- och nackdelar.

**Beräkningen** | **Detaljer**
--- | ---
**Experter** | Båda de virtuella app-datorerna kommer att flyttas till Azure utan ändringar, kan du förenkla migreringen.<br/><br/> Eftersom Contoso använder lift and shift för både virtuella datorer i appen, behövs ingen särskild konfiguration eller migrering verktyg för app-databasen.<br/><br/> Contoso kan utnyttja deras investering i Software Assurance, med hjälp av Azure Hybrid-förmånen.<br/><br/> Contoso behåller fullständig kontroll över appen virtuella datorer i Azure.
**Nackdelar** | WEBVM och SQLVM kör Windows Server 2008 R2. Operativsystemet stöds av Azure för specifika roller (juli 2018). [Läs mer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> Nivåerna web och data för appen förblir en enda åtkomstpunkt för redundans.</br><br/> SQLVM körs på SQL Server 2008 R2, vilket inte är i mainstream-support. Men det finns stöd för virtuella Azure-datorer (juli 2018). [Läs mer](https://support.microsoft.com/en-us/help/956893).<br/><br/> Contoso behöver att fortsätta stödja appen som virtuella Azure-datorer i stället för att flytta till en hanterad tjänst, till exempel Azure App Service och Azure SQL Database.



### <a name="migration-process"></a>Migreringsprocessen

Contoso migrerar app klient- och databasen virtuella datorer till virtuella datorer i Azure med Site Recovery:

- Som ett första steg Contoso förbereder och konfigurerar Azure-komponenter för Site Recovery och förbereder den lokala VMware-infrastrukturen.
- De redan har den [Azure-infrastrukturen](contoso-migration-infrastructure.md) på plats, så Contoso behöver bara lägga till några Azure-komponenter specifikt för Site Recovery.
- Med allt förberett börja Contoso replikera de virtuella datorerna.
- När replikering har aktiverats och arbetar, Contoso kommer att migrera den virtuella datorn genom att växla över till Azure.

![Migreringsprocessen](./media/contoso-migration-rehost-vm/migraton-process.png)



### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure. Virtuella Azure-datorer skapas och betala, vid redundans. [Läs mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.


## <a name="prerequisites"></a>Nödvändiga komponenter

Här är vad som behöver Contoso att köra det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Contoso skapa prenumerationer i en tidigare artikel i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md).
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) kraven för Site Recovery.
**Lokala servrar** | Lokala vCenter-servrar bör kör version 5.5, 6.0 eller 6.5<br/><br/> ESXi-värdar som ska köras version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer ska köras på ESXi-värden.
**Lokala virtuella datorer** | Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso administratörer kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbereda Azure Site Recovery**: De skapar ett Azure storage-konto för att lagra replikerade data och Recovery Services-valvet.
> * **Steg 2: Förbereda lokala VMware för Site Recovery**: De förbereda konton för installation av VM-identifiering och agentinstallation och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 3: Replikera virtuella datorer**: De Konfigurera replikering och börja replikera virtuella datorer till Azure storage.
> * **Steg 4: Migrera de virtuella datorerna med Site Recovery**: De kör ett redundanstest för att kontrollera att allt fungerar och sedan köra en fullständig redundans om du vill migrera de virtuella datorerna till Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Steg 1: Förbered Azure för Site Recovery-tjänsten

Här är Azure-komponenterna Contoso behöver för att migrera de virtuella datorerna till Azure:

- Ett virtuellt nätverk som virtuella Azure-datorer kommer att finnas när de skapas under en redundansväxling.
- Ett Azure storage-konto för att lagra replikerade data.
- Ett Recovery Services-valv i Azure.

De har konfigurerat dessa på följande sätt:

1. Konfigurera en nätverket Contoso redan har konfigurerat ett nätverk som kan vara för Site Recovery när de [distribuerade Azure-infrastrukturen](contoso-migration-infrastructure.md)

    - SmartHotel360-app är en produktionsapp och de virtuella datorerna kommer att migreras till Azure-produktionsnätverket (VNET-PROD-EUS2) i den primära regionen östra USA 2.
    - Båda virtuella datorerna kommer att placeras i resursgruppen ContosoRG som används för produktionsresurser.
    - App-klientdelens VM (WEBVM) kommer att migrera frontend-undernätet (PROD-FE-EUS2), i produktionsmiljön.
    - App-databasen VM (SQLVM) kommer att migrera till undernätet för databasen (PROD-DB-EUS2), i produktionsnätverket.

2. Konfigurera en storage-konto-Contoso skapar ett Azure storage-konto (contosovmsacc20180528) i den primära regionen.
   - Lagringskontot måste finnas i samma region som Recovery Services-valvet.
   - De använder ett allmänt konto med standardlagring och LRS-replikering.

     ![Site Recovery-lagring](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Skapa ett valv med nätverks- och storage-konto på plats, Contoso nu skapar ett Recovery Services-valv (ContosoMigrationVault) och placerar den i ContosoFailoverRG resursgrupp i den primära regionen östra USA 2.

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

Contoso-administratörer som konfigurerar kontot enligt följande:

1. De kan skapa en roll på vCenter-nivå.
2. De tilldela rollen behörigheterna som krävs.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje virtuell dator.

- Site Recovery kan göra en automatisk push-installation av mobilitetstjänsten när replikering av virtuella datorer är aktiverad.
- Ett konto krävs, så att Site Recovery kan få åtkomst till de virtuella datorerna för push-installationen. Du anger det här kontot när du konfigurerar replikering.
- Kontot kan vara domän eller lokal, med behörigheter för att installera på virtuella datorer.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill ansluta till virtuella Azure-datorer efter redundansväxling. Gör detta genom göra Contoso administratörer följande innan migreringen:

1. För åtkomst via internet de:

   - Aktiverar du RDP på den lokala virtuella datorn före redundans.
   - Se till att TCP och UDP-regler lagts till för den **offentliga** profil.
   - Kontrollera att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.

2. För åtkomst via plats-till-plats-VPN, de:

   - Aktiverar du RDP på den lokala datorn.
   - Tillåt RDP i den **Windows-brandväggen** -> **tillåtna appar och funktioner**, för **domän och privat** nätverk.
   - Ange operativsystemets SAN-princip på den lokala virtuella datorn till **OnlineAll**.

Dessutom när de kör en redundans måste de du kontrollera följande:

- Det bör finnas inga Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Om det finns kan de inte kunna logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter en redundansväxling, kan de kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om det inte fungerar de bör kontrollera att den virtuella datorn körs, och dessa [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-3-replicate-the-on-premises-vms"></a>Steg 3: Replikera lokala virtuella datorer

Innan Contoso-administratörer kan köra en migrering till Azure, som de måste du konfigurera och aktivera replikering.

### <a name="set-a-replication-goal"></a>Ange ett replikeringsmål

1. I valvet under valvnamnet (ContosoVMVault) de väljer ett replikeringsmål (**komma igång** > **Site Recovery** > **Förbered infrastruktur** .
2. De ange som finns lokalt, som körs på VMware och replikeras till Azure på sina datorer.

    ![Replikeringsmål](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Om du vill fortsätta, de bekräftar att de har slutfört distributionsplanering genom att välja **Ja, jag har gjort det**. I det här scenariot Contoso migrerar bara två virtuella datorer och behöver inte distributionsplanering.


### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso-administratörer måste du konfigurera källmiljön. Detta gör de ladda ned en OVF-mall och använda den för att distribuera Site Recovery configuration server med hög tillgänglighet, en lokal VMware VM. När configuration server är igång, registrera den i valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfigurationsserverkomponenten som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.



Contoso-administratörer utför dessa steg på följande sätt:

1. I valvet, de ladda ner OVF-mall från **förbereda infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Ladda ner OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. De importerar mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
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

7. När registreringen är klar i Azure portal, de dubbla kontrollerar du att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer.
8. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso-administratörer kan nu ange Målinställningar för replikering.

1. I **Förbered infrastruktur** > **Target**, de väljer målinställningarna.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i den angivna målplatsen.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Contoso-administratörer kan nu skapa en replikeringsprincip.

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Replikeringspunktmål**: Standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens för appkonsekvent ögonblicksbild**. Som standard på en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.

        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern.

    ![Koppla replikeringsprincip](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Aktivera replikering för WEBVM

Med allt på plats, kan Contoso-administratörer nu aktivera replikering för de virtuella datorerna. De börjar med WebVM.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. De Välj målinställningar, inklusive resursgruppen och Azure-nätverk och storage-konto.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. De väljer **WebVM** Kontrollera replikeringsprinciper för replikering och aktivera replikering.

   - I det här skedet de endast väljer WEBVM eftersom du måste välja VNet och undernät och virtuella datorerna för appen kommer att placeras i olika undernät.
   - Site Recovery installerar automatiskt mobilitetstjänsten på den virtuella datorn när replikering har aktiverats.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. De spåra Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
6. I **Essentials** i Azure-portalen kan de se strukturen för de virtuella datorerna replikeras till Azure.


### <a name="enable-replication-for-sqlvm"></a>Aktivera replikering för SQLVM

Contoso-administratörer kan nu börja replikera SQLVM-datorn, med samma process som ovan.

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

Contoso administratörer kör en snabb testredundans och en fullständig växling vid fel att migrera de virtuella datorerna.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Ett redundanstest ser till att allt fungerar som förväntat.

1. Köra ett redundanstest till den senaste tillgängliga tidpunkt (**senaste bearbetade**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan redundansen. Redundansväxlingen fortsätter även om avstängningen misslyckas.
3. Testa redundans körs:

    - En kravkontroll körs för att kontrollera att alla de villkor som krävs för migrering är på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

3. När redundansen är klar visas repliken virtuell Azure-dator i Azure-portalen. De kontrollera att den virtuella datorn har rätt storlek, ansluten till rätt nätverk och körs.
4. När du har verifierat redundanstestningen Rensa växling vid fel, de och registrera och sparar eventuella observationer.

### <a name="create-and-customize-a-recovery-plan"></a>Skapa och anpassa en återställningsplan

 Efter att ha kontrollerat att redundanstestningen fungerar som förväntat, Contoso-administratörer för att skapa en återställningsplan för migrering.

- En återställningsplan anger ordningen i vilken redundans inträffar och visar hur virtuella Azure-datorer ska anslutas i Azure.
- Eftersom appen är två lager kan anpassa de återställningsplanen så att data VM (SQLVM) startas före klientdelen (WEBVM).

1. I **Återställningsplaner (Site Recovery)** > **+ återställningsplan**, de skapa en plan och Lägg till de virtuella datorerna.

    ![Återställningsplan](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. När du har skapat planen, anpassa de den (**Återställningsplaner** > **SmartHotelMigrationPlan** > **anpassa**).
2.  De tar bort WEBVM från **grupp 1: Starta**. Detta säkerställer att den första startåtgärden påverkar SQLVM endast.
3.  I **+ grupp** > **Lägg till skyddade objekt**, de lägger till WEBVM Grupp2: Starta. De virtuella datorerna måste vara i två olika grupper.


### <a name="migrate-the-vms"></a>Migrera de virtuella datorerna


Contoso-administratörer kan nu köra en fullständig redundans för att slutföra migreringen.

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

Med migreringen har slutförts körs app-nivåerna SmartHotel360 nu på virtuella Azure-datorer.

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

- För att styra åtkomsten granskar till teamet Nätverkssäkerhetsgrupper (NSG) för de virtuella datorerna. NSG: er för att säkerställa att endast trafik som tillåts i appen kan nå.
- Teamet Överväg också att skydda data på disken med hjälp av Azure Disk Encryption och KeyVault.

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) om säkerhetsrutiner för virtuella datorer.

## <a name="bcdr"></a>BCDR

Contoso utförs följande åtgärder för affärskontinuitet och haveriberedskap (BCDR):

- Skydda data: Contoso säkerhetskopierar data på de virtuella datorerna med Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Håll appar igång: Contoso replikerar appen virtuella datorer i Azure till en sekundär region med hjälp av Site Recovery. [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).



### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

1. Contoso har befintliga licenser för sina virtuella datorer och att utnyttja Azure Hybrid-förmånen. Contoso konverterar befintliga virtuella Azure-datorer, om du vill dra nytta av den här prissättning.
2. Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser. [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.

## <a name="conclusion"></a>Sammanfattning

I den här artikeln rehosted Contoso SmartHotel360-app i Azure genom att migrera de virtuella datorerna för appen till Azure virtuella datorer med Site Recovery-tjänsten.


## <a name="next-steps"></a>Nästa steg

I den [nästa artikel](contoso-migration-rehost-vm-sql-ag.md) i serien, får du lära dig hur Contoso namnkonflikt SmartHotel360 app klientdelens VM på en Azure virtuell dator och migrerar databasen till en SQL Server AlwaysOn Availability Group i Azure.

