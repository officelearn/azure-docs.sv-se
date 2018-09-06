---
title: Omstrukturera en Contoso Linux service desk-app till Azure App Service och Azure MySQL | Microsoft Docs
description: Lär dig hur Contoso omstrukturerar lokala Linux-app genom att migrera den till Azure App Service med hjälp av GitHub för Webbnivån och Azure SQL Database.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: raynew
ms.openlocfilehash: 7deb22a0fd552ee697f6b39988d5f3ac01a29702
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782200"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Contoso-migrering: omstrukturera en Contoso Linux service desk-app till flera regioner med Azure App Service, Traffic Manager och Azure MySQL

Den här artikeln visar hur Contoso omstrukturerar sina lokala Linux service desk app på två nivåer (osTicket) genom att migrera den till Azure App Service med GitHub-integreringen och Azure MySQL.

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migreringen och kör olika typer av migreringar. Scenarier växer i komplexitet. Vi lägger till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera Azure-infrastrukturen](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt   
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel360-app. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt 
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso har slutförts en lift and shift-migrering av Linux osTicket app på virtuella Azure-datorer med Azure Site Recovery | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Azure Site Recovery och migrerar app-databasen till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar SmartHotel360-app till ett Azure Web Apps och app-databasen har migrerats till en Azure SQL Server-instans med Database Migration Assistant | Tillgängligt
Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Den här artikeln
[Artikel 11: Omstrukturera TFS på VSTS](contoso-migration-tfs-vsts.md) | Contoso migrerar sin lokala Team Foundation Server-distributionen till Visual Studio Team Services i Azure. | Tillgängligt
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel360-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt

I den här artikeln migrerar Contoso en tvålagers-Linux Apache MySQL PHP (LAMP) service desk-app (osTicket) till Azure. Om du vill använda den här appen med öppen källkod kan du hämta det från [GitHub](https://github.com/osTicket/osTicket).


## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå:

- **Åtgärda tillväxten**: Contoso växer och flytta till nya marknader. Den behöver ytterligare customer service agenter. 
- **Skala**: lösningen bör skapas så att Contoso kan lägga till fler customer service agenter som skalas för företag.
- **Öka skyddet**: I de tidigare problem med systemet interna användare som påverkas. Med den nya affärsmodellen externa användare kommer att påverkas och Contoso behöver appen igång hela tiden.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för den här migreringen för att avgöra den bästa migreringsmetoden:

- Programmet bör skala utöver den aktuella lokala kapacitet och prestanda.  Contoso flyttar programmet för att dra nytta av Azures på begäran skalning.
- Contoso vill flytta appen kodbas för en pipeline för kontinuerlig leverans.  Appändringar skickas till GitHub, Contoso vill distribuera dessa ändringar utan uppgifter för personalstyrka.
- Programmet måste vara elastisk med funktioner för tillväxt och redundans. Contoso vill distribuera appen i två olika Azure-regioner och konfigurera den för att skala automatiskt.
- Contoso vill minimera databasen administrativa uppgifter när appen har flyttats till molnet.

## <a name="solution-design"></a>Lösningsdesign
Efter att fästa ned sina mål och krav, Contoso utformar går igenom en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som ska användas för migreringen.


## <a name="current-architecture"></a>Aktuella arkitektur

- Appen är nivåindelad över två virtuella datorer (OSTICKETWEB och OSTICKETMYSQL).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).

![Aktuella arkitektur](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>Föreslagna arkitektur

Här är den föreslagna arkitekturen:

- Nivå-webbapp i OSTICKETWEB kommer att migreras genom att skapa en Azure App Service i två Azure-regioner. Azure App Service för Linux implementeras med hjälp av PHP 7.0 Docker-behållaren.
- Kod som kommer att flyttas till GitHub och Azure Web App kommer att konfigureras för kontinuerlig leverans med GitHub.
- Azure App-servrar ska distribueras i både den primära servern (östra USA 2) och en sekundär region för (USA, centrala).
- Traffic Manager ska ställas in framför de två Azure Web Apps i båda regionerna.
- Traffic Manager konfigureras i prioritet läge för att tvinga trafik via östra USA 2.
- Om Azure App-servern i östra USA 2 kopplas från kan kan användare nå den via app i centrala USA.
- App-databasen kommer att migreras till Azure MySQL PaaS-tjänsten med hjälp av verktyg för MySQL Workbench. En lokal databas ska säkerhetskopieras lokalt och återställs direkt till Azure MySQL.
- Databasen kommer att finnas i den primära regionen i östra USA 2, i databasen undernät (PROD-DB-EUS2) i företagets nätverk (VNET-PROD-EUS2):
- Eftersom de migrerar en produktionsarbetsbelastning, Azure-resurser för appen kommer att finnas i resursgruppen produktion **ContosoRG**.
- Traffic Manager-resurs som ska distribueras i Contosos infrastrukturresursgrupp **ContosoInfraRG**.
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.


![Scenariots arkitektur](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>Migreringsprocessen

Contoso kommer att slutföra migreringen på följande sätt:

1. Som ett första steg Contoso administratörer konfigurera Azure-infrastrukturen, inklusive etablera Azure App Services, för att installera Traffic Manager och etablera en Azure MySQL-instans.
2. När du har förberett Azure migrera de databasen med MySQL Workbench. 
3. När databasen körs i Azure, de upp ett privat GitHub-lagringsplatsen för Azure App Service med kontinuerlig leverans och läsa in den med osTicket appen.
4. I Azure-portalen kan de läsa in appen från GitHub till Docker-behållare som kör Azure App Service. 
5. De justera DNS-inställningarna och konfigurera automatisk skalning för appen.

![Migreringsprocessen](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Azure App Service](https://azure.microsoft.com/services/app-service/) | Tjänsten körs och skalar program med hjälp av Azure PaaS-tjänst för webbplatser.  | Priserna baseras på storleken på instanserna och de funktioner som krävs. [Läs mer](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | En belastningsutjämnare som använder DNS för att dirigera användarna till Azure, eller externa webbplatser och tjänster. | Priserna baseras på antalet mottagna DNS-frågor och antalet övervakade slutpunkter. | [Läs mer](https://azure.microsoft.com/pricing/details/traffic-manager/).
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Databasen är baserad på öppen källkod MySQL Server-motorn. Det ger en fullständigt hanterad, företagsfärdig community MySQL-databas som en tjänst för apputveckling och distribution. | Priser som baseras på beräkning, lagring och säkerhetskopiering. [Läs mer](https://azure.microsoft.com/pricing/details/mysql/).

 
## <a name="prerequisites"></a>Förutsättningar

Här är vad som behöver Contoso att köra det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Contoso skapat prenumerationer tidigare i den här artikeln. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare. 
**Azure-infrastrukturen** | Ställa in sina Azure-infrastrukturen enligt beskrivningen i Contoso [Azure-infrastrukturen för migrering](contoso-migration-infrastructure.md).



## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att slutföra migreringen:

> [!div class="checklist"]
> * **Steg 1: Etablera Azure-Apptjänster**: Contoso administratörer kommer att etablera Web Apps i de primära och sekundära regionerna.
> * **Steg 2: Konfigurera Traffic Manager**: de konfigurera Traffic Manager framför webbprogrammen för Routning och belastningsutjämning trafik.
> * **Steg 3: Etablera MySQL**: I Azure, de etablerar en instans av Azure MySQL-databas.
> * **Steg 4: Migrera databasen**: de migrera databasen med MySQL Workbench. 
> * **Steg 5: Konfigurera GitHub**: de har konfigurerat en lokal GitHub-lagringsplats för webbplatser/Appkod.
> * **Steg 6: Distribuera web apps**: de distribuera webbappar från GitHub.




## <a name="step-1-provision-azure-app-services"></a>Steg 1: Etablera Azure App Services

Contoso-administratörer etablera två webbappar (en i varje region) med hjälp av Azure App Services.

1. De skapar en Web App-resurs i den primära regionen östra USA 2 (**osticket eus2**) från Azure Marketplace.
2. De lägger till resursen i resursgruppen produktion **ContosoRG**.

    ![Azure-App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. De skapar en ny App Service-plan i den primära regionen (**APP-SVP-EUS2**), med hjälp av standardstorleken.

     ![Azure-App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. De väljer en Linux-operativsystem med PHP 7.0 körningsstack, vilket är en Docker-behållare.

    ![Azure-App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. De skapar en andra webbapp (**osticket Cu: er**), och App service-plan för regionen USA, centrala.

    ![Azure-App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**Behöver du mer hjälp?**

- Lär dig mer om [Azure App Service Web apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview).
- Lär dig mer om [Azure App Service i Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).


## <a name="step-2-set-up-traffic-manager"></a>Steg 2: Konfigurera Traffic Manager

Contoso administratörer konfigurera Traffic Manager att dirigera inkommande webbegäranden till Web Apps som körs på webbnivån osTicket.

1. De skapar en Traffic Manager-resurs (**osticket.trafficmanager.net**) från Azure Marketplace. De använder prioritet routning så att östra USA 2 är den primära platsen. De placera resursen i sina infrastrukturresursgrupp (**ContosoInfraRG**). Observera att Traffic Manager är global och inte knuten till en specifik plats

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. Nu kan konfigurera de Traffic Manager med slutpunkter. De lägger till östra USA 2 Web app som den primära platsen (**osticket eus2**), och den centrala USA-app som sekundär (**osticket Cu: er**).

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. De kan övervaka dem när du lägger till slutpunkterna.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**Behöver du mer hjälp?**

- Lär dig mer om [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview).
- Lär dig mer om [dirigera trafiken till en slutpunkt för prioritet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method).
 
## <a name="step-3-provision-azure-database-for-mysql"></a>Steg 3: Etablera Azure Database för MySQL

Contoso-administratörer etablera en MySQL-databasinstans i den primära regionen östra USA 2.

1. I Azure-portalen kan skapa de en Azure Database for MySQL-resurs. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. De lägger till namnet **contosoosticket** för Azure-databasen. De lägga till databasen i resursgruppen produktion **ContosoRG**, och ange autentiseringsuppgifter för den.
3. Den lokala MySQL-databas är version 5.7, så de väljer den här versionen för kompatibilitet. De använder standardstorlekar som matchar deras databaskrav.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. För **Redundansalternativen för säkerhetskopiering**, de väljer för att använda **Geo-Redundant**. Det här alternativet gör att de kan återställa databasen i sina sekundära regionen USA, centrala vid ett eventuellt strömavbrott. De kan bara konfigurera det här alternativet när de konfigurerar databasen.

    ![Redundans](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. De har konfigurerat anslutningssäkerhet. I databasen > **anslutningssäkerhet**, de har konfigurerat brandväggsregler så att databasen att ansluta till Azure-tjänster.
5. De lägger till lokala arbetsstationen klientens IP-adress till start- och IP-adresser. På så sätt kan de Web apps att använda MySQL-databasen, tillsammans med databasklient som utför migreringen.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>Steg 4: Migrera databasen

Contoso administratörer migrera databasen med säkerhetskopiering och återställning, med MySQL-verktyg. De installera MySQL Workbench, säkerhetskopiera databasen från OSTICKETMYSQL och sedan återställa det till Azure Database for MySQL-Server.

### <a name="install-mysql-workbench"></a>Installera MySQL Workbench

1. De kontrollerar den [krav och nedladdningar MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. De installera MySQL Workbench för Windows i enlighet med den [Installationsinstruktioner](https://dev.mysql.com/doc/workbench/en/wb-installing.html). Datorn där de installerar måste vara tillgänglig för OSTICKETMYSQL VM och Azure via internet.
3. De skapar en MySQL-anslutning till OSTICKETMYSQL i MySQL Workbench. 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. De exportera databasen som **osticket**, som en fristående lokal fil.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. När databasen har säkerhetskopierats lokalt, kan de skapa en anslutning till Azure Database for MySQL-instans.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. De kan nu importera (återställning) databasen i Azure MySQL-instans från den fristående fil. Ett nytt schema (osticket) skapas för instansen.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. När data har återställts kan efterfrågas med Workbench och visas i Azure-portalen.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. Slutligen kan behöva de uppdatera databasinformation på web apps. De öppna på MySQL-instans **anslutningssträngar**. 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. I listan över strängar de hitta inställningarna för Web App och klicka för att kopiera dem.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. De öppna ett fönster i anteckningar och klistra in strängen i en ny fil och uppdatera den så att den matchar osticket databasen, MySQL-instans och inställningar för autentiseringsuppgifter.

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Tney kan kontrollera servernamnet och inloggning från **översikt** i MySQL-instans i Azure-portalen.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>Steg 5: Konfigurera GitHub

Contoso-administratörer skapa en ny privat GitHub-lagringsplats och ställer in en anslutning till osTicket-databas i Azure MySQL. Sedan kan de läsa in Azure-Webbapp med appen.  

1.  De Bläddra till OsTicket programvara offentliga GitHub-lagringsplatsen och Förgrena på Contoso GitHub-konto.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. När förgreningen, har de navigerar till den **inkluderar** mapp och hitta den **ost config.php** fil.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. Filen öppnas i webbläsaren och de redigera den.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. I redigeraren, de uppdatera databasinformation, särskilt **DBHOST** och **DBUSER**. 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. Sedan ändringarna de.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. För varje webbapp (**osticket eus2** och **osticket Cu: er**), de modifiera den **programinställningar** i Azure-portalen.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. De anger anslutningssträngen med namnet **osticket**, och kopiera strängen från anteckningar till den **värde området**. De väljer **MySQL** i listrutan bredvid strängen och spara ändringarna.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>Steg 6: Konfigurera Web Apps

Contoso-administratörer konfigurera web apps med osTicket web sites som det sista steget i migreringen.



1. I den primära webbapp (**osticket eus2**) de öppnar **distributionsalternativet** och ange källan till **GitHub**.

    ![Konfigurera app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. De väljer distributionsalternativen.

    ![Konfigurera app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. När du har angett alternativen i konfigurationen visas som väntar på Azure-portalen.

    ![Konfigurera app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. När konfigurationen uppdateras och osTicket webbappen har lästs in från GitHub till Toysmart.com behållaren som körs i Azure App Service, visar platsen som aktiv.

    ![Konfigurera app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. De Upprepa stegen ovan för sekundära webbappen (**osticket Cu: er**).
6. När platsen har konfigurerats kan är den tillgänglig via Traffic Manager-profilen. DNS-namnet är den nya platsen för osTicket appen. [Läs mer](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

    ![Konfigurera app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Contoso vill ett DNS-namn som är lätt att komma ihåg. De skapar en aliaspost (CNAME) **osticket.contoso.com** som pekar på Traffic Manager-namnet i DNS på sina domänkontrollanter.

    ![Konfigurera app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. Hon konfigurerar både den **osticket eus2** och **osticket Cu: er** webbappar så att de anpassade värddatornamnen.

    ![Konfigurera app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>Konfigurera automatisk skalning

Slutligen, de ställa in automatisk skalning för appen. Detta säkerställer att som agenter använder appen, app-instanserna öka och minska enligt behov. 

1. I App Service **APP-SRV-EUS2**, de öppnar **Skalningsenhet**.
2. De kan konfigurera en ny autoskalningsinställning med en enda regel som ökar instansantalet av en när CPU-procent för den aktuella instansen är över 70% i 10 minuter.

    ![Automatisk skalning](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. De konfigurera samma inställning på **APP-SRV-CUS** så att samma beteende gäller om appen inte över till den sekundära regionen. Den enda skillnaden är att de angetts instans gränsen till 1 eftersom det är för redundans endast.

   ![Automatisk skalning](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Med migreringen har slutförts omstrukturerade osTicket appen till som körs i en Azure-webbapp med kontinuerlig leverans med en privat GitHub-lagringsplats. App som körs i två regioner för ökad flexibilitet. OsTicket databas körs i Azure database för MySQL efter migrering till PaaS-plattform.

För behöver rensa, Contoso du göra följande: 
- Ta bort den virtuella VMware-datorer från vCenter-lagret.
- Ta bort de lokala virtuella datorerna från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentationen visar nya platser och IP-adresser. 
- Granska eventuella resurser som kan interagera med lokala virtuella datorer och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.
- Konfigurera övervakning för att peka på osticket trafficmanager.net URL, för att spåra att appen är igång och körs.

## <a name="review-the-deployment"></a>Granska distributionen

Med den app som körs nu måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

Det Contoso security teamet har granskat appen för att fastställa eventuella säkerhetsproblem. De har identifierat att kommunikationen mellan appen osTicket och MySQL-databasinstans har inte konfigurerats för SSL. Användaren uppmanas att göra detta för att säkerställa att databastrafik inte kan vara hackad. [Läs mer](https://docs.microsoft.com/azure/mysql/howto-configure-ssl).

### <a name="backups"></a>Säkerhetskopior

- Webbprogram osTicket innehåller inte Systemtillstånd och därför behöver inte säkerhetskopieras.
- De behöver inte konfigurera säkerhetskopiering för databasen. Azure Database för MySQL skapar automatiskt säkerhetskopiering av servrar och lagrar. De har valt för att använda geo-redundans för databasen, så att den är flexibel och vara färdigt för produktionsanvändning. Säkerhetskopieringar kan användas för att återställa servern till point-in-time. [Läs mer](https://docs.microsoft.com/azure/mysql/concepts-backup).


### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- Det finns inga licensproblem för PaaS-distribution.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.



