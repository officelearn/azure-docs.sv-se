---
title: Omstrukturera en Contoso-app genom att migrera den till Azure Web App och Azure SQL Database | Microsoft Docs
description: Lär dig hur Contoso namnkonflikt en lokal app genom att migrera den till en Azure Web App och Azure SQL Server-databas.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 923a1ab9020925c17b3b19d24c7524ab2f8f3356
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092770"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Contoso-migrering: omstrukturera en lokal app till en Azure Web App och Azure SQL-databas

Den här artikeln visar hur Contoso omstrukturerar sina SmartHotel360-app i Azure. De migrera app-klientdelens VM till ett Azure Web Apps och app-databas till en Azure SQL-databas.

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, utvärdera lokala resurser för migrering och som kör olika typer av migreringar. Scenarier växer i komplexitet. Vi lägger till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder sina lokala och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av en lokal tvålagers-SmartHotel-app som körs på VMware. Contoso utvärderar app virtuella datorer med den [Azure Migrate](migrate-overview.md) -tjänsten och SQL Server-databas för appen med den [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Ange ny värd för en app på Azure virtuella datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso körs en lift and shift-migrering till Azure för SmartHotel appen. Contoso migrerar appen klientdelens VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app-databasen till en SQL Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrerar appen SmartHotel virtuella datorer med Site Recovery endast. | Tillgängligt
[Artikel 6: Ange ny värd för en app på Azure virtuella datorer och SQLServer Always On-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen och tjänsten Databasmigrering att migrera app-databas till en SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso gör en lift and shift-migrering av Linux osTicket-app till virtuella Azure-datorer med Site Recovery | Tillgängligt
[Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Site Recovery och migrerar app-databasen till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt
Artikel 9: Omstrukturera en app till en Azure Web App och Azure SQL-databas | Visar hur Contoso migrerar SmartHotel-app till ett Azure Web Apps och app-databasen har migrerats till Azure SQL Server-instans | Den här artikeln
[Artikel 10: Omstrukturera en Linux-app till Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure Web Apps på flera platser, integrerad med GitHub för kontinuerlig leverans. De migrera app-databasen till en Azure MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera TFS på Azure DevOps-tjänsterna](contoso-migration-tfs-vsts.md) | Visar hur Contoso migrerar sina lokala Team Foundation Server (TFS)-distributionen genom att migrera den till Azure DevOps-tjänsterna i Azure. | Tillgängligt
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Visar hur Contoso migrerar och rearchitects SmartHotel appen till Azure. De Omforma webbnivån appen som en Windows-behållare och app-databasen i en Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Visar hur Contoso återskapa SmartHotel appen med hjälp av en mängd Azure-funktioner och tjänster, inklusive App Services, Azure Kubernetes, Azure Functions, Cognitive services och Cosmos DB. | Tillgängligt
[Artikel 14: Skala en migrering till Azure](contoso-migration-scale.md) | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. | Tillgängligt

I den här artikeln migrerar Contoso tvålagers-Windows. NET SmartHotel360-app som körs på virtuella VMware-datorer till Azure. Om du vill använda den här appen tillhandahålls som öppen källkod och du kan ladda ned det från [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer och har Press på lokala system och infrastruktur.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och förenkla processer för utvecklare och användare.  Affärsbehov IT ska vara snabbt och inte spill tid eller pengar och därför leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**: Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi.  Det får inte vara i vägen eller bli en blockerare för företag.
- **Skala**: när verksamheten växer har, Contoso IT måste tillhandahålla system som kan växa i samma takt.
- **Kostnaderna**: Contoso vill minimera licensieringskostnaderna.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för den här migreringen. Dessa mål har använts för att avgöra den bästa migreringsmetoden.

**Krav** | **Detaljer**
--- | --- 
**App** | Appen i Azure finns kvar som kritiskt eftersom den är i dag.<br/><br/> Den bör ha samma prestandafunktioner som för närvarande i VMWare.<br/><br/> Teamet vill inte investera i appen. För tillfället kommer administratörer helt enkelt flytta appen på ett säkert sätt till molnet.<br/><br/> Teamet vill stoppa stöd för Windows Server 2008 R2, som appen körs för närvarande.<br/><br/> Teamet vill också att flytta från SQL Server 2008 R2 till en modern plattform för PaaS-databas, vilket minimerar behovet för hantering.<br/><br/> Contoso vill utnyttja dess investeringar i SQL Server-licensiering och Software Assurance där det är möjligt.<br/><br/> Dessutom kan vill Contoso minska felpunkt på webbnivån.
**Begränsningar** | Appen består av en ASP.NET-app och en WCF-tjänst som körs på samma virtuella dator. De vill dela upp detta på två webbappar med Azure App Service. 
**Azure** | Contoso vill flytta appen till Azure, men vill inte köra på virtuella datorer. Contoso vill utnyttja Azure PaaS-tjänster för både webb- och data-nivåerna. 
**DevOps** | Contoso vill flytta till en DevOps-modell, med Azure DevOps för deras versioner och release-pipelines.

## <a name="solution-design"></a>Lösningsdesign

Efter att fästa ned mål och krav, Contoso utformar och granska en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som ska användas för migrering.

### <a name="current-app"></a>Aktuell app

- SmartHotel360 lokal app är nivåindelad över två virtuella datorer (WEBVM och SQLVM).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.


### <a name="proposed-solution"></a>Föreslagen lösning

- För databasnivån appens Contoso jämfört med Azure SQL Database med SQL Server med hjälp av [i den här artikeln](https://docs.microsoft.com/azure/sql-database/sql-database-features). Contoso bestämde sig för att gå med Azure SQL Database olika orsaker:
    - Azure SQL Database är en relationsdatabas hanterad tjänst. Den levererar förutsägbar prestanda på flera servicenivåer med nästan obefintlig administration. Fördelarna är dynamisk skalbarhet utan driftstopp, inbyggd intelligent optimering, och global skalbarhet och tillgänglighet.
    - Contoso kan utnyttja lightweight Data Migration Assistant (DMA) för att utvärdera och migrera en lokal databas till Azure SQL.
    - Contoso kan utbyta befintliga rabatterade priser på en SQL-databas med Azure Hybrid-förmånen för SQL Server-licenser med Software Assurance. Detta kan ge besparingar på upp till 30%.
    - SQL Database tillhandahåller ett antal funktioner för säkerhet, inklusive alltid krypterad, dynamisk datamaskning och identifiering av säkerhet på radnivå/hot.
- Contoso har beslutat att använda Azure App Service för webbnivån app. Det här PaaS-tjänst gör det möjligt för att för att distribuera appen med bara några konfigurationsändringar. Contoso använder Visual Studio för att genomföra ändringen och distribuera två web apps. En för webbplatsen, och en för WCF-tjänst.
- Contoso har valt för att använda Azure DevOps för Source Code Management (SCM) med Git-databaser för att uppfylla kraven för en DevOps-pipeline. Automatiserade versioner och versionen används för att skapa koden och distribuera den till Azure Web Apps.
  
### <a name="solution-review"></a>Granska lösningen
Contoso utvärderar sina föreslagna design genom att sätta ihop en lista med för- och nackdelar.

**Beräkningen** | **Detaljer**
--- | ---
**Experter** | SmartHotel360-Appkod behöver inte ändras för migrering till Azure.<br/><br/> Contoso kan utnyttja deras investering i Software Assurance som använder Azure Hybrid-förmånen för både SQL Server och Windows Server.<br/><br/> Efter migreringen kommer inte Windows Server 2008 R2 måste stödjas. [Läs mer](https://support.microsoft.com/lifecycle).<br/><br/> Contoso kan konfigurera datanivån för appen med flera instanser, så att den inte längre en felpunkt.<br/><br/> Databasen kommer inte längre att vara beroende av föråldras SQL Server 2008 R2.<br/><br/> SQL Database stöder de tekniska kraven. Contoso bedömas i den lokala databasen med Database Migration Assistant och hitta att den är kompatibel.<br/><br/> SQL Database har inbyggda feltolerans som Contoso inte behöver konfigurera. Detta säkerställer att datanivå inte längre en enda åtkomstpunkt för redundans.
**Nackdelar** | Azure App Services har endast stöd för en app-distribution för varje Webbapp. Det innebär att två Webbappar måste vara etablerade (en för webbplatsen) och en WCF-tjänst.<br/><br/> Om Contoso använder Data Migration Assistant i stället för Data Migration Service för att migrera sin databas kan den inte har infrastrukturen som är redo för att migrera databaser i stor skala. Contoso behöver du skapa en annan region för att säkerställa att redundans om den primära regionen inte är tillgänglig.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

![Scenariots arkitektur](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Migreringsprocessen

1. Contoso etablerar en Azure SQL-instans och migrerar SmartHotel360-databasen till den.
2. Contoso etablerar och konfigurerar Webbappar och distribuerar SmartHotel360-appen till dem.

    ![Migreringsprocessen](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso använder DMA för att utvärdera och identifiera kompatibilitetsproblem som kan påverka deras databasfunktioner i Azure. DMA utvärderar funktionsparitet mellan SQL-källor och mål och rekommenderar förbättringar av prestanda och tillförlitlighet. | Det här verktyget kan laddas ned utan kostnad.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | En intelligent, hanterad fullständigt databastjänst för relationsdatabaser i molnet. | Kostnaden baserat på funktioner, dataflöde och storlek. [Läs mer](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure App Services – Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Skapa kraftfulla molnappar med en helt hanterad plattform | Kostnader beroende på storleken, plats och användning varaktighet. [Läs mer](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Innehåller en kontinuerlig integrering och kontinuerlig distribution (CI/CD) pipeline för utveckling av appar. Pipelinen börjar med en Git-lagringsplats för att hantera kod, ett versionssystem för att producera paket och andra byggartefakter och ett Release Management-system för att distribuera ändringar i utveckling, testning och produktionsmiljöer. 

## <a name="prerequisites"></a>Förutsättningar

Här är Contoso måste köra det här scenariot:

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Contoso skapades en tidig artikel prenumerationer. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Etablera en SQL-databasinstans i Azure**: Contoso etablerar en SQL-instans i Azure. När appwebbplatsen har migrerat till Azure, pekar WCF service-webbapp till den här instansen.
> * **Steg 2: Migrera databas med DMA**: Contoso migrerar app-databasen med Database Migration Assistant.
> * **Steg 3: Etablera Web Apps**: Contoso etablerar de två webbappar.
> * **Steg 4: Konfigurera Azure DevOps**: Contoso skapas ett nytt Azure DevOps-projekt och importerar Git-lagringsplats.
> * **Steg 5: Konfigurera anslutningssträngar**: Contoso konfigurerar anslutningssträngar så att webbapp för nivån, WCF service-webbapp och SQL-instansen kan kommunicera.
> * **Steg 6: Konfigurera build och släppa pipelines**: som ett sista steg Contoso ställer in version och versionen pipelines för att skapa appen och distribuerar dem till två separata Azure Web Apps.


## <a name="step-1-provision-an-azure-sql-database"></a>Steg 1: Etablera en Azure SQL-databas

1. Contoso-administratörer välja för att skapa en SQL-databas i Azure. 

    ![Etablera SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. De ange ett databasnamn så att den matchar den databas som körs på en lokal virtuell dator (**SmartHotel.Registration**). De placera databasen i resursgruppen ContosoRG. Det här är den resursgrupp som de använder för produktionsresurser i Azure.

    ![Etablera SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. De har konfigurerat en ny SQL Server-instans (**sql-smarthotel-eus2**) i den primära regionen.

    ![Etablera SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. De ställer in prisnivån så att de matchar deras server och databasbehov. Och de väljer för att spara pengar med Azure Hybrid-förmånen eftersom de redan har en SQL Server-licens.
5. De använder v-Core-baserade köpa för storleksändringar och ange begränsningarna för sina förväntade krav.

    ![Etablera SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Sedan skapar de databasinstansen.

    ![Etablera SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. När instansen har skapats kan de öppna databasen och Observera information de behöver när de använder Database Migration Assistant för migrering.

    ![Etablera SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**Behöver du mer hjälp?**

- [Få hjälp](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) etablera en SQL-databas.
- [Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) v-kärnor resursbegränsningar.


## <a name="step-2-migrate-the-database-with-dma"></a>Steg 2: Migrera databas med DMA

Contoso-administratörer kommer att migrera SmartHotel360-databasen med hjälp av DMA.

### <a name="install-dma"></a>Installera DMA

1. De ladda ned verktyget från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) till en lokal SQL Server-VM (**SQLVM**).
2. De kör installationsprogrammet (DownloadMigrationAssistant.msi) på den virtuella datorn.
3. På den **Slutför** kan de välja **starta Microsoft Data Migration Assistant** innan du avslutar guiden.

### <a name="migrate-the-database-with-dma"></a>Migrera databas med DMA

1. I DMA, de skapar ett nytt projekt (**SmartHotelDB**) och välj **migrering** 
2. De väljer typ av källserver som **SQL Server**, och mål som **Azure SQL Database**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Information om migrering de Lägg till i **SQLVM** som källservern och **SmartHotel.Registration** databas. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. De får ett felmeddelande som verkar vara associerad med autentisering. När du har letat, är men problemet punkt (.) i namnet på databasen. Som en lösning kan de bestämde sig att etablera en ny SQL-databas med namnet **SmartHotel registrering**, för att lösa problemet. När de kör DMA igen, de är kan du välja **SmartHotel registrering**, och fortsätta med guiden.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. I **Markera objekt**, de välja databastabellerna och generera ett SQL-skript.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. När skriptet skapas i DMS de klickar på **distribuera schemat**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA bekräftar att distributionen har utförts.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. Nu kan de starta migreringen.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. När migreringen är klar kan Contoso-administratörer kontrollera att databasen körs på Azure SQL-instansen.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. De ta bort extra SQL-databasen **SmartHotel.Registration** i Azure-portalen.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Steg 3: Etablera Web Apps

Med databasen migreras, Contoso-administratörer kan nu etablera de två web apps.

1. De väljer **Webbapp** i portalen.

    ![Webbapp](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. De tillhandahåller ett appnamn (**SHWEB EUS2**), köra den på Windows och placera den avregistrera hanteringsgruppen för produktion-resurser **ContosoRG**. De skapar en ny app service och planen.

    ![Webbapp](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. När webbappen har etablerats visas de upprepar du proceduren för att skapa en webbapp för WCF-tjänst (**SHWCF EUS2**)

    ![Webbapp](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. När de är klar kan gå de till adressen för att kontrollera att de har skapats.


## <a name="step-4-set-up-azure-devops"></a>Steg 4: Konfigurera Azure DevOps


Contoso behöver skapa DevOps-infrastrukturen och pipelines för programmet.  Detta gör Contoso administratörer skapa ett nytt DevOps-projekt, importera koden, konfigurera build och release-pipelines.

1.   De skapa ett nytt projekt i Contoso Azure DevOps-konto (**ContosoSmartHotelRefactor**), och välj **Git** för versionskontroll.

    ![Nytt projekt](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. De importerar Git-lagringsplats som för närvarande innehåller sin kod. Det är en [offentlig repo](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) och du kan hämta den.

    ![Hämta kod](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. När koden har importerats kan de ansluta Visual Studio till lagringsplatsen och klona den kod med hjälp av Team Explorer.

    ![Anslut till project](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. När lagringsplatsen klonas till developer-dator, kan de öppna lösningsfilen för appen. I web app och wcf-tjänsten har separata projektet i filen.

    ![Lösningsfil](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>Steg 5: Konfigurera anslutningssträngar

Contoso-administratörer måste du kontrollera att web apps och databasen kan kommunicera. Om du vill göra detta måste konfigurera de anslutningssträngar i koden och i web apps.

1. I webbapp för WCF-tjänst (**SHWCF EUS2**) > **inställningar** > **programinställningar**, de lägger till en ny anslutningssträng med namnet  **DefaultConnection**.
2. Anslutningssträngen hämtas från den **SmartHotel registrering** databasen och ska uppdateras med rätt autentiseringsuppgifter.

    ![Anslutningssträng](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Med Visual Studio, som de öppnar den **SmartHotel.Registration.wcf** projektet från lösningsfilen. Den **connectionStrings** i web.config-filen för WCF-tjänst som SmartHotel.Registration.Wcf ska uppdateras med anslutningssträngen.

     ![Anslutningssträng](media/contoso-migration-refactor-web-app-sql/string2.png)

4. Den **klienten** i web.config-filen för SmartHotel.Registration.Web ska ändras för att peka på den nya platsen för WCF-tjänst. Det här är URL: en för WCF-webbapp som är värd för tjänsteslutpunkt.

    ![Anslutningssträng](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. När ändringarna är i koden, måste administratörer att genomföra ändringarna. Med Team Explorer i Visual Studio, de commmit och synkronisering.


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>Steg 6: Konfigurera build och släppa pipelines i Azure DevOps

Contoso-administratörer kan nu konfigurera Azure DevOps för att skapa build-version och utgivningen.

1. I Azure DevOps de klickar på **skapa och släpp** > **ny pipeline**.

    ![Ny pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. De väljer **Azure lagringsplatser Git** och relevanta lagringsplatsen.

    ![Git och lagringsplats](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. I **Välj en mall**, de Välj ASP.NET-mall för deras version.

     ![ASP.NET-mall](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. Namnet **ContosoSmartHotelRefactor-ASP.NET-CI** används för kompilering. De klickar på **spara och köa**.

     ![Spara och kö](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. Detta startar den första versionen. De klickar på build-nummer kan du titta på processen. När den har slutförts de kan se processen feedback och klicka på **artefakter** att granska resultatet.

    ![Granska](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. Mappen **släppa** innehåller resultatet.

    - Två zip-filer är de paket som innehåller apparna.
    - De här filerna används i versionspipelinen för distribution till Azure Web Apps

     ![Artefakt](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. De klickar på **versioner** > **+ ny pipeline**.

    ![Ny pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. De väljer mallen för distribution av Azure App Service.

    ![Azure App Service-mall](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. De namnge versionspipelinen **ContosoSmartHotel360Refactor**, och ange namnet på WCF-webbapp (SHWCF EUS2) för den **scenen** namn.

    ![Miljö](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. Under stegvis, de klickar på **1 jobb, 1 aktivitet** att konfigurera distribution av WCF-tjänst.

    ![Distribuera WCF](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. De kontrollerar prenumerationen som är markerade och behöriga och välj den **App service-namn**.

     ![Välj app service](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. På pipelinen > **artefakter**, de väljer **+ Lägg till en artefakt**, och välja att skapa med den **ContosoSmarthotel360Refactor** pipeline.

     ![Utveckla](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. De klickar på blixten på artefakten kontrolleras. att aktivera utlösare av kontinuerlig distribution.

     ![Blixt](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. Utlösare av kontinuerlig distribution ska vara inställd på **aktiverad**.

   ![Kontinuerlig distribution aktiverad](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. Nu kan de gå tillbaka till steg 1-jobbet jag uppgifter och klicka på **distribuera Azure App Service**.

    ![Distribuera app service](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. I **Välj en fil eller mapp**, de lokalisera den **SmartHotel.Registration.Wcf.zip** -filen som skapades under build och clilck **spara**.-sql

    ![Spara WCF](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. De klickar på **Pipeline** > **faser** **+ Lägg till**, för att lägga till en miljö för **SHWEB EUS2**. De välja en annan distribution i Azure App Service.

    ![Lägg till miljön](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. De Upprepa processen för att publicera webbappen (**SmartHotel.Registration.Web.zip**) filen till rätt webbappen.

    ![Publicera webbapp](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. När det sparats visas versionspipelinen på följande sätt.

     ![Versionen pipeline-sammanfattning](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. De flytta tillbaka till **skapa**, och klicka på **utlösare** > **möjliggöra kontinuerlig integration**. På så sätt kan pipelinen så att när ändringar sparas i koden och fullständig skapa och släpp inträffar.

    ![Aktivera kontinuerlig integrering](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. De klickar på **spara och köa** att köra en fullständig pipeline. En ny version utlöses som i sin tur skapar den första versionen av appen till Azure App Service.

    ![Spara pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Contoso-administratörer kan följa versionen och släpp från Azure DevOps-processen. När bygget har slutförts startar versionen.

    ![Skapa och publicera app](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. När pipelinen har slutförts båda platserna som har distribuerats och appen är igång och körs online.

    ![Slut versionen](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

Appen har nu har migrerats till Azure.



## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Contoso måste slutföra stegen rensning efter migreringen:  

- Ta bort de lokala virtuella datorerna från vCenter-lagret.
- Ta bort de virtuella datorerna från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentation för att visa de nya platserna för SmartHotel360-appen. Visa databasen som körs i Azure SQL-databas och klientdelen som körs i två webbappar.
- Granska eventuella resurser som interagerar med de inaktiverade virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.


## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

- Contoso behöver att säkerställa att deras nya **SmartHotel registrering** databasen är skyddad. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Contoso bör i synnerhet uppdatera web apps för att använda SSL med certifikat.

### <a name="backups"></a>Säkerhetskopior

- Contoso behöver granska säkerhetskopieringskrav för Azure SQL Database. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso också måste veta mer om SQL Database-säkerhetskopior och återställer. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) om automatiska säkerhetskopior.
- Contoso bör överväga att implementera redundansgrupper för att tillhandahålla regional redundans för databasen. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso behöver överväga att distribuera Webbappen i den huvudsakliga östra USA 2 och centrala USA för återhämtning. Contoso kan konfigurera Traffic Manager för att säkerställa att redundans vid regionala avbrott.

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- När alla resurser har distribuerats, Contoso ska tilldela Azure taggar baserat på deras [infrastrukturplanering](contoso-migration-infrastructure.md#set-up-tagging).
- Alla licensiering ingår i kostnaden för PaaS-tjänster som använder Contoso. Detta dras från EA.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.

## <a name="conclusion"></a>Sammanfattning

I den här artikeln omstrukturerade Contoso SmartHotel360-app i Azure genom att migrera app-klientdelens VM till två Azure Web Apps. App-databasen har migrerats till en Azure SQL database.






