---
title: Återskapa en Contoso-app lokalt till Azure | Microsoft Docs
description: Lär dig hur Contoso återskapar en app till Azure med Azure App Services, Kubernetes-tjänsten, CosmosDB, Azure Functions och Cognitive services.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: raynew
ms.openlocfilehash: ff195093f97f00fca54e4e1c5800b6b0ecf7605a
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054469"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso-migrering: återskapa en lokal app till Azure

Den här artikeln visar hur Contoso migrerar och återskapar SmartHotel360-app i Azure. Contoso migrerar appens klientdel virtuell dator till Azure App Services Web apps. App-serverdel har skapats med mikrotjänster distribueras till behållare som hanteras av Azure Kubernetes Service (AKS). Platsen interagerar med Azure Functions för att tillhandahålla Husdjurets foto-funktioner. 

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, utvärdera lokala resurser för migrering och som kör olika typer av migreringar. Scenarier växer i komplexitet. Vi lägger till ytterligare artiklar med tiden.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera Azure-infrastrukturen](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt   
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till virtuella Azure-datorer med hjälp av Site Recovery-tjänsten. | Tillgängliga [artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel360-app. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängliga [artikel 7: Appvärd Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso Slutför en lift and shift-migrering av dess osTicket app i Linux på Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar dess osTicket Linux-app till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar app-databas till Azure Database för MySQL med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app i en Azure webbapp och Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar dess SmartHotel360-app till en Azure webbapp och migrerar app-databasen till en Azure SQL Server-instans med Database Migration Assistant. | Tillgängligt    
[Artikel 10: Omstrukturera en Linux-app i en Azure-webbapp och en Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera Team Foundation Server i Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso migrerar sin lokala Team Foundation Server-distributionen till Visual Studio Team Services i Azure. | Tillgängligt
[Artikel 12: Omforma en app i Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel360-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Tillgängligt 
Artikel 13: Återskapa en app i Azure | Contoso återskapas dess SmartHotel360-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Den här artikeln


## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer och vill tillhandahålla differentierade upplevelser för kunder på Contoso-webbplatser.
- **Flexibilitet**: Contoso måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi. 
- **Skala**: när verksamheten växer till Contoso IT-avdelningen måste ange system som kan växa i samma takt.
- **Kostnaderna**: Contoso vill minimera licensieringskostnaderna.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned appars krav för den här migreringen. Dessa krav som användes för att avgöra den bästa migreringsmetoden:
 - Appen i Azure finns kvar som kritiskt eftersom den är i dag. Den bör god och skala enkelt.
 - Appen bör inte använda IaaS-komponenter. Allt ska byggas om du vill använda PaaS och serverlös tjänster.
 - App-versioner som ska köras i cloud services och behållare ska placeras i ett privat företagsomfattande behållarregister i molnet.
 - API-tjänst som används för Husdjurets foton ska vara korrekta och tillförlitliga i verkligheten, eftersom beslut som görs av appen som måste hanteras i sina hotell. Alla husdjur beviljas åtkomst tillåts att hålla sig i hotell.
 - För att uppfylla kraven för en pipeline för DevOps, använder Contoso Visual Studio Team Services (VSTS) för Source Code Management (SCM) för med Git-lagringsplatser.  Automatiserade versioner och versioner används för att skapa kod och distribuera den till Azure Web Apps, Azure Functions och AKS.
 - Olika CI/CD-pipelines behövs för mikrotjänster på serverdelen och för webbplatsen på klientdelen.
 - Backend-tjänster har en annan version växla från frontend-webbapp.  För att uppfylla detta krav kan distribuerar de två olika DevOps-pipelines.
 - Contoso behöver management godkännande för alla webbplatsdistribution för klientdelen och CI/CD-pipeline måste ange den här.


## <a name="solution-design"></a>Lösningsdesign

Efter att fästa ned mål och krav, Contoso utformar och granska en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som ska användas för migreringen.

### <a name="current-app"></a>Aktuell app

- SmartHotel360 lokal app är nivåindelad över två virtuella datorer (WEBVM och SQLVM).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.


### <a name="proposed-architecture"></a>Föreslagna arkitektur

- Klientdelen av appen distribueras som en Azure App Services Web-app i den primära Azure-regionen.
- En Azure-funktion innehåller uppladdningar av Husdjurets foton och platsen interagerar med den här funktionen.
- Funktionen Husdjurets foto använder Cognitive Services API för visuellt innehåll och CosmosDB.
- Serverdelen av webbplatsen använder mikrotjänster. Dessa distribueras till behållare som hanteras i Azure Kubernetes service (AKS).
- Behållare har skapats med VSTS och push-överfört till Azure Container Registry (ACR).
- För tillfället distribuerar Contoso manuellt Web-app och funktionen kod med Visual Studio.
- Mikrotjänster distribueras med hjälp av ett PowerShell-skript som anropar Kubernetes kommandoradsverktyg.

    ![Scenariots arkitektur](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Granska lösningen

Contoso utvärderar den föreslagna designen genom att sätta ihop en lista med för- och nackdelar.

**Beräkningen** | **Detaljer**
--- | ---
**Experter** | Med PaaS och serverlösa lösningar för slutpunkt till slutpunkt-distributionen avsevärt minskar hanteringstiden för som Contoso måste ange.<br/><br/> Flytta till en mikrotjänstarkitektur kan Contoso till bygg ut lösningen över tid.<br/><br/> Nya funktioner kan anslutas utan att störa någon av de befintliga kodbaser lösningar.<br/><br/> Webbappen konfigureras med flera instanser med ingen enskild felpunkt.<br/><br/> Automatisk skalning aktiveras så att appen kan hantera olika trafikvolymer.<br/><br/> Contoso kan dra tillbaka gamla lösningar som körs på operativsystemet Windows Server 2008 R2 med flytten till PaaS-tjänster.<br/><br/> Cosmos DB har inbyggd feltolerans som kräver ingen konfiguration av Contoso. Det innebär att datanivån inte längre en enda åtkomstpunkt för redundans.
**Nackdelar** | Behållare är mer komplexa än andra migreringsalternativ. Inlärningskurvan bero på ett problem för Contoso.  En ny nivå av komplexitet som erbjuder en mängd olika värdet trots kurvan medför de.<br/><br/> Driftsteamet på Contoso behöver snabbt igång med att förstå och stöd för Azure, behållare och mikrotjänster för appen.<br/><br/> Contoso har inte fullt ut DevOps för hela lösningen. Contoso behöver tänka som för distribution av tjänster till AKS, functions och App Services.



### <a name="migration-process"></a>Migreringsprocessen

1. Contoso förbereder och etablerar tjänsterna och infrastrukturen som de behöver:

 - En hanterad Kubernetes-kluster som med AKS och ACR.
 - En Cosmos DB för Husdjurets foton.
 - En Azure-funktion för att tillhandahålla överför Husdjurets foton.
 - Ett storage-konto för att rymma behållare för Husdjurets foton och inställningar för klientdelsappen.
 - API för visuellt innehåll att utvärdera Husdjurets foton för appen.
 - En Azure-webbapp.
 
2. När infrastrukturen är på plats, Contoso ställer in en DevOps skapa och släpp pipeline för att skapa, skicka och integrera kontinuerligt behållarna för att i ACR.  
3. Slutligen Contoso distribuerar VSTS-projekt för klientdelen av webbplatsen och ställer in en pipeline för att skicka och integrera kontinuerligt koden i Azure.

    ![Migreringsprocessen](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Förenklar hantering, distribution och åtgärder för Kubernetes. Ger en fullständigt hanterad Kubernetes-behållare orchestration-tjänst.  | AKS är en kostnadsfri tjänst.  Betala för endast virtuella datorer och associerade lagringsmedia och nätverksresurser som förbrukas. [Läs mer](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Azure Functions](https://azure.microsoft.com/services/functions/) | Går det snabbare utveckling med en händelsedriven, serverlös beräkningsmiljö. Skala på begäran.  | Betala bara för förbrukade resurser. Plan faktureras utifrån sekundanvändning resursförbrukning och körningar. [Läs mer](https://azure.microsoft.com/pricing/details/functions/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Lagrar avbildningar av alla typer av behållardistributioner. | Kostnaden baserat på funktioner, lagring och varaktighet för användning. [Läs mer](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Skapa, distribuera och skala snabbt webb-, mobil- och API-appar i företagsklass som körs på valfri plattform. | App Service-planer faktureras per sekund. [Läs mer](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Förutsättningar

Här är vad Contoso behöver för det här scenariot:

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Contoso skapades prenumerationer under en tidigare artikel. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.
**Developer-krav** | Contoso behöver följande verktyg på en utvecklararbetsstation:<br/><br/> - [Visual Studio 2017 Community Edition: Version 15.5](https://www.visualstudio.com/)<br/><br/> .NET-arbetsbelastning är aktiverat.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) eller Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) inställd på att använda Windows-behållare.



## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Etablera serverdelsresurserna**: Contoso etablerar den hanterade Kubernetes-kluster med AKS och Azure container registry.
> * **Steg 2: Konfigurera en pipeline för backend-**: Contoso ställer in ett DevOps-arbetsflöde. De importerar git-lagringsplatsen till VSTS och skapa en ny build-pipeline. De skapa Docker-behållare och skickar dem till i ACR. När du skapar visas nya lagringsplatser (fyllts i med behållarna) i i ACR. Contoso distribuerar sedan resten av serverinfrastrukturen att migrera serverdelen.
> * **Steg 3: Etablera klientdelsresurserna**: Contoso distribuerar frontend infrastrukturen, inklusive blob-lagring för Husdjurets foton Cosmos-databasen för att lagra dokument med Husdjurets information och API för visuellt innehåll för webbplatsen.
> * **Steg 4: Konfigurera en pipeline för klientdelen**: Contoso publicerar SmartHotel360-app till Azure App service och distribuerar PetCheck funktionsappen. Detta hanteras i två separata pipelines för olika utgivningscykler.



## <a name="step-1-provision-back-end-resources"></a>Steg 1: Etablera backend-resurser

Contoso-administratörer som kör ett distributionsskript för att skapa den hanterade Kubernetes-kluster med AKS och Azure Container Registry (ACR).

- Anvisningarna för det här avsnittet används den **SmartHotel360-Azure-backend** lagringsplats.
- Den **SmartHotel360-Azure-backend** GitHub-lagringsplatsen innehåller alla program som den här delen av distributionen.

### <a name="prerequisites"></a>Förutsättningar

1. Innan de börjar kontrollera Contoso-administratörer att all programvara som prerequisitie i installerad på en utvecklingsdator som de använder för distributionen.
2. De klona lagringsplatsen lokalt på datorn för utveckling med Git: **git-klonen https://github.com/Microsoft/SmartHotel360-Azure-backend.git**


### <a name="provision-aks-and-acr"></a>Etablera AKS och ACR

Contoso-administratörer etablera enligt följande:

1.  De öppna mappen med hjälp av Visual Studio Code och flyttar till den **/distribuera/k8s** directory, som innehåller skriptet som **gen-aks-env.ps1**.
2. De kör skriptet för att skapa den hanterade Kubernetes-kluster med AKS och ACR.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  Öppna filen och uppdatera de parametern $location att **usaöstra2**, och spara filen.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. De klickar på **visa** > **integrerad Terminal** att öppna den integrerade terminalen i kod.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. I PowerShell integrerade terminalen de loggar in på Azure med hjälp av kommandot Connect-AzureRmAccount. [Läs mer](https://docs.microsoft.com/powershell/azure/get-started-azureps) om att komma igång med PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. De autentiserar Azure CLI genom att köra den **az-inloggning** kommandot och följer instruktionerna för att autentisera med hjälp av webbläsaren. [Läs mer](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) håller loggar in med Azure CLI.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. De kör följande kommando, skicka resursgruppens namn av ContosoRG, namnet på den AKS-kluster smarthotel-aks-eus2 och det nya namnet för registret.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Azure skapar en annan resursgrupp som innehåller resurser för AKS-klustret.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. När distributionen är klar som de installerar den **kubectl** kommandoradsverktyget. Verktyget är redan installerad på Azure CloudShell.

    **az aks install-cli**

10. De kontrollerar anslutningen till klustret genom att köra den **kubectl få noder** kommando. Noden är samma namn som den virtuella datorn automatiskt skapade resursgruppen.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. De kör följande kommando för att starta Kubernetes-instrumentpanelen: 

    **AZ-aks Bläddra--resource-group ContosoRG--name smarthotelakseus2**

12. En webbläsarflik öppnas på instrumentpanelen. Det här är en tunnel anslutning med Azure CLI. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>Steg 2: Konfigurera backend-pipelinen

### <a name="create-a-vsts-project-and-build-the-pipeline"></a>Skapa ett VSTS-projekt och skapa pipelinen

Contoso-administratörer skapa ett VSTS-projekt och konfigurera en CI skapa för att skapa behållaren och skickar den till i ACR. Anvisningarna i det här avsnittet används den [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) lagringsplats.

1. De ska skapa ett nytt konto från visualstudio.com (**contosodevops360.visualstudio.com**), och konfigurera den att använda Git.

2. De skapar ett nytt projekt (**SmartHotelBackend**) med hjälp av Git för versionskontroll och Agile för arbetsflödet.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. De importerar GitHub-lagringsplatsen.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. I **Build and Release**, de skapar en ny build-pipeline med VSTS Git som en källa från importerade **SmartHotelBackend** lagringsplats. 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. De välja för att starta med en tom pipeline.

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. De väljer **finns Linux-förhandsvisningen** för build-definition.

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. I **fas 1**, de lägger till en **Docker Compose** uppgift. Den här uppgiften versioner Docker compose.

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. De upprepar och Lägg till en annan **Docker Compose** uppgift. Den här skickar behållarna till ACR.

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. De väljer den första aktiviteten (för att skapa) och konfigurera versionen med Azure-prenumeration, auktorisering och i ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. De ange sökvägen till den **Toysmart.com compose.yaml** filen den **src** mappen på lagringsplatsen. De väljer att skapa underhålla avbildningar och inkludera den senaste taggen. När åtgärden ändras till **skapa underhålla avbildningar**, ändras namnet på VSTS-uppgiften till **skapa tjänster automatiskt**

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. Nu kan konfigurera de den andra aktiviteten Docker (för att skicka). De väljer du prenumerationen och **smarthotelacreus2** ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Igen, de ange filen till docker-compose.yaml-filen och välj **Push underhålla avbildningar** och inkludera den senaste taggen. När åtgärden ändras till **Push underhålla avbildningar**, ändras namnet på VSTS-uppgiften till **Push-tjänster automatiskt**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Contoso-administratörer med VSTS-åtgärder som konfigurerats, spara byggesdefinition och starta skapandeprocessen.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. De klickar på build-jobbet för att kontrollera förloppet.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. När bygget har slutförts visas i ACR nya lagringsplatser, som fylls i med de behållare som används av mikrotjänster.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>Distribuera backend-infrastruktur

Med AKS-kluster skapas och de inbyggda Docker-avbildningar, distribuera Contoso administratörer nu resten av den infrastruktur som ska användas av backend-mikrotjänster.

- Anvisningarna i avsnittet använder de [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) lagringsplatsen.
- I den **/deploy/k8s/arm** mapp, det finns ett enda skript för att skapa alla objekt. 

De distribuerar på följande sätt:

1. De öppna en kommandotolk för utvecklare och använda kommandot az-inloggning för Azure-prenumerationen.
2. De kan använda filen deploy.cmd för att distribuera Azure-resurser i resursgruppen för ContosoRG och EUS2 region genom att skriva följande kommando:

    **.\deploy.cmd azuredeploy ContosoRG - c usaöstra2**

    ![Distribuera serverdel](./media/contoso-migration-rebuild/backend1.png)

2. De fångar in anslutningssträngen för varje databas som ska användas senare i Azure-portalen.

    ![Distribuera serverdel](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>Skapa backend-releasepipeline

Nu kan göra Contoso administratörer följande:

- Distribuera NGINX ingress-kontrollanten för att tillåta inkommande trafik till tjänsterna.
- Distribuera mikrotjänster till AKS-klustret.
- Som ett första steg uppdatera de anslutningssträngar till mikrotjänster med VSTS. De kan sedan konfigurera en ny version av VSTS-pipeline för att distribuera mikrotjänster.
- Anvisningarna i det här avsnittet används den [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) lagringsplatsen.
- Observera att några av konfigurationsinställningarna (till exempel Active Directory B2C) inte tas upp i den här artikeln. Läs mer om de här inställningarna i lagringsplatsen.

De skapar du en pipeline:

1. Med Visual Studio som de uppdatera den **/deploy/k8s/config_local.yml** fil med anslutningsinformationen de antecknade tidigare.

    ![DB-anslutningar](./media/contoso-migration-rebuild/back-pipe1.png)

2. De öppnas VSTS och i SmartHotel360 projektet i **versioner**, de klickar på **+ ny Pipeline**.

    ![Ny pipeline](./media/contoso-migration-rebuild/back-pipe2.png)

3. De klickar på **tom jobbet** att starta pipelinen utan en mall.

    ![Tom jobb](./media/contoso-migration-rebuild/back-pipe3.png)

4. De tillhandahåller namnen på miljö och pipeline.

      ![Miljönamn](./media/contoso-migration-rebuild/back-pipe4.png)

      ![Namn på pipeline](./media/contoso-migration-rebuild/back-pipe5.png)

5. De lägger till en artefakt.

     ![Lägg till artefakt](./media/contoso-migration-rebuild/back-pipe6.png)

6. De väljer **Git** som källa och ange projekt-, käll- och huvudgrenen för SmartHotel360-appen.

    ![Artefakten inställningar](./media/contoso-migration-rebuild/back-pipe7.png)

7. De klickar på länken för uppgiften.

    ![Aktivitetslänk](./media/contoso-migration-rebuild/back-pipe8.png)

8. De lägger till en ny uppgift Auzre PowerShell så att de kan köra ett PowerShell-skript i en Azure-miljö.

    ![PowerShell i Azure](./media/contoso-migration-rebuild/back-pipe9.png)

9. De Välj Azure-prenumerationen för aktiviteten och välj den **deploy.ps1** skriptet från Git-lagringsplats.

    ![Kör skript](./media/contoso-migration-rebuild/back-pipe10.png)


10. De lägga till argument i skriptet. skriptet tar bort allt innehåll som klustret (utom **ingångshändelser** och **ingress-kontrollant**), och distribuera mikrotjänster.

    ![Skriptargument](./media/contoso-migration-rebuild/back-pipe11.png)

11. De ange den önskade Azure PowerShell-versionen till senast och spara pipelinen.
12. De flytta tillbaka till den **viktig** sidan och försök skapa en ny version.

    ![Ny version](./media/contoso-migration-rebuild/back-pipe12.png)

13. De klickar på versionen när du har skapat den, och i **åtgärder**, de klickar på **distribuera**.

      ![Distribuera version](./media/contoso-migration-rebuild/back-pipe13.png)  

14. När distributionen är klar, de kör följande kommando för att kontrollera status för tjänster, med hjälp av Azure Cloud Shell: **kubectl få tjänster**.


## <a name="step-3-provision-front-end-services"></a>Steg 3: Etablera frontend-tjänster

Contoso-administratörer måste du distribuera den infrastruktur som används av klientdelen-appar. De skapar en blob storage-behållare för att lagra de Husdjurets bilderna. Cosmos-databasen för att lagra dokument med Husdjurets information. och API för visuellt innehåll för webbplatsen. 

Anvisningar för det här avsnittet används den [SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web) lagringsplatsen.

### <a name="create-blob-storage-containers"></a>Skapa bloblagringscontainrar

1.  I Azure-portalen kan de öppna storage-konto som har skapats och klickar på **Blobar**.
2.  De skapar en ny behållare (**husdjur**) med offentlig åtkomstnivå som har angetts till behållaren. Användare kommer att överföra sina Husdjurets foton till den här behållaren.

    ![Lagringsblob](./media/contoso-migration-rebuild/blob1.png)

3. De skapar en andra ny behållare med namnet **inställningar**. En fil med alla inställningar för klient-appen kommer att placeras i den här behållaren.

    ![Lagringsblob](./media/contoso-migration-rebuild/blob2.png)

4. De fångar in åtkomstinformation för storage-konto i en textfil för framtida bruk.

    ![Lagringsblob](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Etablera en Cosmos-databas

Contoso-administratörer etablera en Cosmos-databas som ska användas för Husdjurets information.

1. De skapar en **Azure Cosmos DB** på Azure Marketplace.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. De ange ett namn (**contosomarthotel**), Välj SQL-API och placera den i produktion resursgruppen ContosoRG i regionen USA, östra 2 main.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. De lägger till en ny samling för databasen med standardkapacitet och dataflöde.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. De Observera anslutningsinformationen för databasen för framtida bruk. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Etablera för visuellt innehåll

Contoso-administratörer etablera den API för visuellt innehåll. API: et kommer att anropas av funktionen för att utvärdera bilder som laddats upp av användare.

1. De skapar en **visuellt** instans i Azure Marketplace.

     ![Visuellt innehåll](./media/contoso-migration-rebuild/vision1.png)

2. De etablera API: et (**smarthotelpets**) i produktionsresursen grupperar ContosoRG i regionen USA, östra 2 main.

    ![Visuellt innehåll](./media/contoso-migration-rebuild/vision2.png)

3. De kan spara anslutningsinställningar för API: et till en textfil för senare referens.

     ![Visuellt innehåll](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Etablera Azure-Webbappen

Contoso-administratörer etablera webbappen i Azure Portal.


1. De väljer **Webbapp** i portalen.

    ![Webbapp](media/contoso-migration-rebuild/web-app1.png)

2. De tillhandahåller ett appnamn (**smarthotelcontoso**), köra den på Windows och placera den i hanteringsgruppen för produktion resurser **ContosoRG**. De skapar en ny Application Insights-instans för appövervakning...

    ![Namn på webbapp](media/contoso-migration-rebuild/web-app2.png)

3. När de är klar kan gå de till adressen av appen för att kontrollera att den har skapats.

4. De kan nu skapa en mellanlagringsplats för koden i Azure-portalen. pipelinen ska distribuera till den här platsen. Detta säkerställer att koden inte använda i produktion tills administratörer utför en version.

    ![Web app mellanlagringsplats](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Etablera Azure Functions-app

Contoso-administratörer etablera Funktionsappen i Azure-portalen.

1. De väljer **Funktionsapp**.

    ![Skapa funktionsapp](./media/contoso-migration-rebuild/function-app1.png)

2. De tillhandahåller ett appnamn (**smarthotelpetchecker**). De placera appen i resursgruppen produktion **ContosoRG**. De inställd som värd plats **Standardförbrukningsplanen**, och placera appen i regionen östra USA 2. Ett nytt lagringskonto skapas tillsammans med en Application Insights-instans för övervakning.

    ![Funktionsappinställningar](./media/contoso-migration-rebuild/function-app2.png)


3. När appen har distribuerats kan gå de till app-adress för att kontrollera att den har skapats.


## <a name="step-4-set-up-the-front-end-pipeline"></a>Steg 4: Konfigurera frontend pipelinen

Contoso-administratörer skapa två olika projekt för frontend-platsen. 

1. De skapa ett projekt i VSTS, **SmartHotelFrontend**.

    ![Frontend-projekt](./media/contoso-migration-rebuild/function-app1.png)

2. De importerar den [SmartHotel360 frontend](https://github.com/Microsoft/SmartHotel360-public-web.git) Git-lagringsplats till det nya projektet.
3. De skapar en annan VSTS-projekt (SmartHotelPetChecker) för Funktionsappen, och importera den [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) Git-lagringsplats i det här projektet.

### <a name="configure-the-web-app"></a>Konfigurera Webbappen

Contoso-administratörer kan nu konfigurera Webbappen för att använda Contoso-resurser.

1. De ansluter till VSTS-projekt och klona lagringsplatsen lokalt för att utvecklingsdator.
2. I Visual Studio, kan de öppna mapp för att visa alla filer i lagringsplatsen.

    ![Lagringsplats-filer](./media/contoso-migration-rebuild/configure-webapp1.png)

3. De uppdatera konfigurationsändringar efter behov.

    - När appen startar den letar efter den **SettingsUrl** appinställningen.
    - Den här variabeln måste innehålla en URL som pekar till en konfigurationsfil.
    - Som standard är inställningen används en offentlig slutpunkt.

4.  De uppdatera filen /config-sample.json/sample.json.

    - Det här är konfigurationsfilen för webben när du använder den offentliga slutpunkten.
    - De redigera den **URL: er** och **pets_config** avsnitt med värdena för AKS-API-slutpunkter, lagringskonton och Cosmos-databasen.
    - URL: er ska matcha DNS-namnet på den nya webbappen Contoso vill skapa.
    - Detta är för Contoso, **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![JSON-inställningar](./media/contoso-migration-rebuild/configure-webapp2.png)

5. När filen har uppdaterats, de Byt namn på den **smarthotelsettingsurl**, och överföra den till storage-blogg som de skapade tidigare.

    ![Byt namn på och ladda upp](./media/contoso-migration-rebuild/configure-webapp3.png)

6. De klickar på filen för att hämta URL: en. URL: en används av appen när den hämtar ned konfigurationsfilerna.

    ![App-URL](./media/contoso-migration-rebuild/configure-webapp4.png)

7. I den **appsettings. Production.JSON** filen, de uppdateras den **SettingsURL** till Webbadressen för den nya filen.

    ![Uppdatera URL: en](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Distribuera webbplatsen till Azure App Service

Contoso-administratörer kan nu publicera webbplatsen.


1. De öppnar VSTS, och i den **SmartHotelFrontend** projektet i **byggen och publiceringar**, de klickar på **+ ny Pipeline**.
2. De väljer **VSTS Git** som källa.

    ![Ny pipeline](./media/contoso-migration-rebuild/vsts-publishfront1.png)

3. De väljer den **ASP.NET Core** mall.
4. De granska pipelinen och kontrollera att **publicera webbprojekt** och **Zip publicerade projekt** har valts.

    ![Pipelineinställningarna](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. I **utlösare**, de aktiverar du kontinuerlig integrering och lägga till huvudgrenen. Detta säkerställer att varje tim lösningen har ny kod som allokerats till huvudgrenen, build-pipelinen startar.

    ![Kontinuerlig integrering](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. De klickar på **spara och köa** att starta en version.
7. När bygget har slutförts, de konfigurerar en produktionspipeline med den **Azure App Service-distribution**.
8. De tillhandahåller en miljönamn **mellanlagring**.

    ![Miljönamn](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. De lägga till en artefakt och välj den version som de precis har konfigurerat.

     ![Lägg till artefakt](./media/contoso-migration-rebuild/vsts-publishfront5.png)

6. De klickar du på blixtikonen på artifcat och aktivera kontinuerlig distribution.

    ![Kontinuerlig distribution](./media/contoso-migration-rebuild/vsts-publishfront6.png)

7. I **miljö**, de klickar på **fas 1, 1 aktivitet** under **mellanlagring**.
8. När du har valt prenumerationen och appnamn, de öppnar den **distribuera Azure App Service** uppgift. Distributionen är konfigurerad för att använda den **mellanlagring** distributionsplats. Detta skapar automatiskt kod för granskning och godkännande i den här platsen.

     ![Plats](./media/contoso-migration-rebuild/vsts-publishfront7.png)

9. I den **New viktig pipeline**, de lägger till en ny miljö.

    ![Ny miljö](./media/contoso-migration-rebuild/vsts-publishfront8.png)

10. De väljer **Azure App Service-distributionen med platsen**, och namnge miljö **Prod**.

    ![Miljönamn](./media/contoso-migration-rebuild/vsts-publishfront9.png)

11. De klickar på **fas 1, 2 uppgifter**, och välj den prenumeration, app service-namn och **mellanlagring** fack.

    ![Miljönamn](./media/contoso-migration-rebuild/vsts-publishfront10.png)

12. De tar bort den **distribuera Azure App Service till facket** från pipeline. Det har placerats där av föregående steg.

    ![Ta bort från pipelinen](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. De sparar pipelinen. I pipelinen, de klickar du på **efter distributionen villkor**.

    ![Efter distributionen](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. De gör att **efter distributionen godkännanden**, och lägger till en dev-lead som godkännaren.

    ![Efter distributionen godkännande](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. I Build-pipelinen startar de manuellt en version. Detta utlöser nya versionspipelinen, som distribuerar den till mellanlagringsplatsen. För Contoso, URL-Adressen för facket är **https://smarthotelcontoso-staging.azurewebsites.net/**.
16. När bygget har slutförts och versionen distribuerar till facket, VSTS e-postmeddelanden dev lead för godkännande.
17. Dev lead klick **visa godkännande**, och kan godkänna eller Avvisa begäran i VSTS-portal.

    ![E-post för godkännande](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. Ditt lead gör en kommentar och godkänner. Detta startar växling av den **mellanlagring** och **prod** platser och flyttar versionen till produktionen.

    ![Godkänna och växla](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. Pipelinen har slutförts växlingen.

    ![Slutför växling](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. Team-kontroller i **prod** fack för att verifiera att webbappen är i produktion på **https://smarthotelcontoso.azurewebsites.net/**.


### <a name="deploy-the-petchecker-function-app"></a>Distribuera PetChecker Funktionsappen

Contoso-administratörer distribuera appen på följande sätt.

1. De klona lagringsplatsen lokalt till den dev-datorn genom att ansluta till VSTS-projekt.
2. I Visual Studio, kan de öppna mapp för att visa alla filer i lagringsplatsen.
3. De öppnar den **src/PetCheckerFunction/local.settings.json** filen och Lägg till appinställningar för lagring, Cosmos-databasen och den API för visuellt innehåll.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function5.png)

4. De kan koppla koden och synkronisera den VSTS, push-överföra ändringarna.
5. De Lägg till en ny Build-pipeline och välj **VSTS Git** för källan.
6. De väljer den **ASP.NET Core (.NET Framework)** mall.
7. De accepterar standardinställningarna för mallen.
8. I **utlösare**och välj sedan att **möjliggöra kontinuerlig integration**, och klicka på **spara och köa** att starta en version.
9. När den har byggts de skapar en releasepipeline att lägga till den **Azure App Service-distributionen med platsen**.
10. De namnge miljön **Prod**, och välj prenumerationen. De har den **apptyp** till **funktionen Ap**, och app service-namn som **smarthotelpetchecker**.

    ![Funktionsapp](./media/contoso-migration-rebuild/petchecker2.png)

11. De lägger till en artefakt **skapa**.

    ![Artefakt](./media/contoso-migration-rebuild/petchecker3.png)

12. De gör att **utlösare av kontinuerlig distribution**, och klicka på **spara**.
13. De klickar på **ny version i kö** att köra en fullständig CI/CD-pipeline.
14. När funktionen har distribuerats, visas den i Azure-portalen med den **kör** status.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function6.png)


7. De Bläddra till appen att testa att husdjur layout appen fungerar som förväntat på [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. De klickar på avatar att ladda upp en bild.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function7.png)

9. Det första foto som de vill kontrollera är av en liten hund.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function8.png)

10. Appen returnerar ett meddelande om godkännande.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso nu fullständigt operationalisera och skydda den nya infrastrukturen.

### <a name="security"></a>Säkerhet

- Contoso måste se till att de nya databaserna är säkra. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Appen måste uppdateras för att använda SSL med certifikat. Behållarinstansen ska distribueras för att besvara på 443.
- Contoso bör överväga att använda KeyVault för att skydda hemligheter för sina Service Fabric-appar. [Läs mer](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Säkerhetskopiering och katastrofåterställning

- Contoso behöver granska säkerhetskopieringskrav för Azure SQL Database. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso bör överväga att implementera SQL redundansgrupper för att tillhandahålla regional redundans för databasen. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso kan utnyttja geo-replikering för ACR-premium-SKU. [Läs mer](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Cosmos DB säkerhetskopierar automatiskt. Contoso kan [mer](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) om den här processen.

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- När alla resurser har distribuerats, Contoso ska tilldela Azure taggar baserat på deras [infrastrukturplanering](contoso-migration-infrastructure.md#set-up-tagging).
- Alla licensiering ingår i kostnaden för PaaS-tjänster som använder Contoso. Detta dras från EA.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.

## <a name="conclusion"></a>Sammanfattning

I den här artikeln återskapar Contoso SmartHotel360-app i Azure. Den lokala appen klientdelens VM återskapas till Azure App Services Web Apps. App-serverdel har skapats med mikrotjänster distribueras till behållare som hanteras av Azure Kubernetes Service (AKS). Contoso utökad funktionalitet med en Husdjurets foto-app.




