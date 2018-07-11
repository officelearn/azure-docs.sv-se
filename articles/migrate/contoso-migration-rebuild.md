---
title: Återskapa en Contoso-app lokalt till Azure | Microsoft Docs
description: Lär dig hur Contoso återskapar en app till Azure med Azure App Services, Kubernetes-tjänsten, CosmosDB, Azure Functions och Cognitive services.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: raynew
ms.openlocfilehash: b99e4c4062723dad2b5ef089160799623f9d906c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934698"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso-migrering: återskapa en lokal app till Azure

Den här artikeln visar hur Contoso migrerar och återskapar sin SmartHotel app i Azure. De migrera appens klientdel virtuell dator till Azure App Services Web apps. App-serverdel har skapats med mikrotjänster distribueras till behållare som hanteras av Azure Kubernetes Service (AKS). Platsen interagerar med Azure Functions Husdjurets foto-funktioner. 

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, utvärdera lokala resurser för migrering och som kör olika typer av migreringar. Scenarier växer i komplexitet och vi lägger till ytterligare artiklar med tiden.

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
[Artikel 9: Omstrukturera en app till en Azure Web App och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Visar hur Contoso migrerar SmartHotel-app till ett Azure Web Apps och app-databasen har migrerats till Azure SQL Server-instans | Tillgängligt
[Artikel 10: Omstrukturera en Linux-app till Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure Web Apps på flera platser, integrerad med GitHub för kontinuerlig leverans. De migrera app-databasen till en Azure MySQL-instans. | Tillgängligt
[Artikel 11: Omforma en app på Azure-behållare och SQL-databas](contoso-migration-rearchitect-container-sql.md) | Visar hur Contoso migrerar och rearchitects SmartHotel appen till Azure. De Omforma webbnivån appen som en Windows-behållare och app-databasen i en Azure SQL Database. | Tillgängligt
Artikel 12: Återskapa en app till Azure | Visar hur Contoso återskapa SmartHotel appen med hjälp av en mängd Azure-funktioner och tjänster, inklusive App Services, Azure Kubernetes, Azure Functions, Cognitive services och Cosmos DB. | Den här artikeln.

I den här artikeln migrerar Contoso tvålagers-Windows. NET SmartHotel app som körs på virtuella VMware-datorer till Azure. Om du vill använda den här appen tillhandahålls som öppen källkod och du kan ladda ned det från [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer. De vill tillhandahålla differentierade upplevelser för sina kunder på sina webbplatser.
- **Flexibilitet**: Contoso måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi. 
- **Skala**: när verksamheten växer har, Contoso IT måste tillhandahålla system som kan växa i samma takt.
- **Kostnaderna**: Contoso vill minimera licensieringskostnaderna.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned appars krav för den här migreringen. Dessa krav som användes för att avgöra den bästa migreringsmetoden:
 - Appen i Azure finns kvar som kritiskt eftersom den är i dag. Den bör god och skala enkelt.
 - Appen bör inte använda IaaS-komponenter. Allt ska byggas om du vill använda PaaS och serverlös tjänster.
 - App-versioner som ska köras i cloud services och behållare ska placeras i ett privat företagsomfattande behållarregister i molnet.
 - API-tjänst som används för Husdjurets foton ska vara korrekta och tillförlitliga i verkligheten, eftersom beslut som görs av appen som måste hanteras i sina hotell. Alla husdjur beviljas åtkomst tillåts att hålla sig i hotell.

## <a name="solution-design"></a>Lösningsdesign

Efter att fästa ned sina mål och krav, Contoso utformar och granska en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som de ska använda för migreringen.

### <a name="current-app"></a>Aktuell app

- SmartHotel lokal app är nivåindelad över två virtuella datorer (WEBVM och SQLVM).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.


### <a name="proposed-architecture"></a>Föreslagna arkitektur

- Klientdelen av appen kommer att distribueras som en Azure App Services Web-app i sina primära regionen.
- En Azure-funktion får uppladdningar av Husdjurets foton och platsen kommer att interagera med den här funktionen.
- Funktionen Husdjurets foto använder Cognitive Services API för visuellt innehåll och CosmosDB.
- Serverdelen av webbplatsen har skapats med mikrotjänster. Dessa kommer att distribueras till behållare som hanteras i Azure Kubernetes service (AKS).
- Behållare kommer skapats med VSTS och push-överfört till Azure Container Registry (ACR).
- För tillfället distribuera Contoso manuellt Web-app och funktionen kod med Visual Studio.
- Mikrotjänster distribueras med hjälp av ett PowerShell-skript som anropar Kubernetes kommandoradsverktyg.

    ![Scenariots arkitektur](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Granska lösningen
Contoso utvärderar sina föreslagna design genom att sätta ihop en lista med för- och nackdelar.

**Beräkningen** | **Detaljer**
--- | ---
**Experter** | Med PaaS och serverlösa lösningar för slutpunkt till slutpunkt-distributionen avsevärt minskar hanteringstiden för som Contoso måste ange.<br/><br/> Flytta till en mikrotjänstarkitektur kan Contoso att enkelt utöka sin lösning över tid.<br/><br/> Nya funktioner kan anslutas utan att störa någon av de befintliga kodbaser lösningar.<br/><br/> Webbappen konfigureras med flera instanser med ingen enskild felpunkt.<br/><br/> Automatisk skalning aktiveras så att appen kan hantera olika trafikvolymer.<br/><br/> Contoso kan dra tillbaka gamla lösningar som körs på operativsystemet Windows Server 2008 R2 med flytten till PaaS-tjänster.<br/><br/> Cosmos DB har inbyggd feltolerans som kräver ingen konfiguration av Contoso. Det innebär att datanivån inte längre en enda åtkomstpunkt för redundans.
**Nackdelar** | Behållare är mer komplexa än andra migreringsalternativ. Inlärningskurvan bero på ett problem för Contoso.  En ny nivå av komplexitet som erbjuder en mängd olika värdet trots kurvan medför de.<br/><br/> Driftsteamet på Contoso behöver du snabbt igång med att förstå och stöd för Azure, behållare och mikrotjänster för appen.<br/><br/> Contoso har inte fullt ut DevOps för hela lösningen. De behöver för att tänka som för distribution av tjänster till AKS, functions och App Services.



### <a name="migration-process"></a>Migreringsprocessen

1. Contoso etablera den ACR och AKS CosmosDB.
2. De konfigurerar infrastrukturen för att distribuera, inklusive Azure Web App, storage-konto, funktion och API: et. 
3. När infrastrukturen är på plats, kommer de skapar sina mikrotjänster behållaravbildningar med VSTS, varpå de till i ACR.
4. Contoso distribuerar dessa mikrotjänster till fråga med hjälp av ett PowerShell-skript.
5. Slutligen kan distribuera de Azure-funktion och Web App.

    ![Migreringsprocessen](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Förenklar hantering, distribution och åtgärder för Kubernetes. Ger en fullständigt hanterad Kubernetes-behållare orchestration-tjänst.  | AKS är en kostnadsfri tjänst.  Betala för endast virtuella datorer och associerade lagringsmedia och nätverksresurser som förbrukas. [Läs mer](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Azure Functions](https://azure.microsoft.com/services/functions/) | Går det snabbare utveckling med en händelsedriven, serverlös beräkningsmiljö. Skala på begäran.  | Betala bara för förbrukade resurser. Plan faktureras utifrån sekundanvändning resursförbrukning och körningar. [Läs mer](https://azure.microsoft.com/pricing/details/functions/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Lagrar avbildningar av alla typer av behållardistributioner. | Kostnaden baserat på funktioner, lagring och varaktighet för användning. [Läs mer](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Skapa, distribuera och skala snabbt webb-, mobil- och API-appar i företagsklass som körs på valfri plattform. | App Service-planer faktureras per sekund. [Läs mer](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Förutsättningar

Här är vad du (och Contoso) behöver att köra det här scenariot:

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Du bör redan har skapat en prenumeration när du har utfört utvärderingen i den första artikeln i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.
**Developer-krav** | Contoso behöver följande verktyg på en utvecklararbetsstation:<br/><br/> - [Visual Studio 2017 Community Edition: Version 15.5](https://www.visualstudio.com/)<br/><br/> .NET-arbetsbelastning är aktiverat.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) eller Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) inställd på att använda Windows-behållare.



## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Etablera AKS och ACR**: Contoso etablerar hanterade AKS-kluster och Azure container registry med PowerShell
> * **Steg 2: Skapa Docker-behållare**: de konfigurera CI för Docker-behållare med VSTS och push-installera dem i ACR.
> * **Steg 3: Distribuera backend-mikrotjänster**: de distribuerar resten av den infrastruktur som ska användas av backend-mikrotjänster.
> * **Steg 4: Distribuera frontend infrastruktur**: de distribuera frontend-infrastruktur, inklusive blob-lagring för Husdjurets telefoner, Cosmos DB och API för visuellt innehåll.
> * **Steg 5: Migrera serverdelen**: de distribuera mikrotjänster och köras på AKS att migrera backend-servern.
> * **Steg 6: Publicera klientdelen**: de publicera appen SmartHotel i Azure App service och Funktionsappen som kommer att anropas av tjänsten Husdjurets.



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>Steg 1: Etablera ett AKS-klustret och ACR

Contoso körs ett distributionsskript för att skapa den hanterade Kubernetes-kluster med AKS och Azure Container Registry.

- Anvisningarna för det här avsnittet används den **SmartHotel360-Azure-backend** lagringsplats.
- Den **SmartHotel360-Azure-backend** GitHub-lagringsplatsen innehåller alla program som den här delen av distributionen.

De etablera enligt följande:

1. Innan de börjar säkerställer Contoso att all nödvändig programvara är installerad på en utvecklingsdator som de använder. 
2. De klona lagringsplatsen lokalt för att utvecklingsdator med Git.

    **Git-klonen https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso öppnas mappen med hjälp av Visual Studio Code och flyttas till den **/distribuera/k8s** directory, som innehåller skriptet som **gen-aks-env.ps1**.
4. De kör skriptet för att skapa den hanterade Kubernetes-kluster med AKS och Container Registry.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  Öppna filen och uppdatera de parametern $location att **usaöstra2**, och spara filen.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. De klickar på **visa** > **integrerad Terminal** att öppna den integrerade terminalen i kod.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. I PowerShell integrerade terminalen de loggar in på Azure med hjälp av kommandot Connect-AzureRmAccount. [Läs mer](https://docs.microsoft.com/powershell/azure/get-started-azureps) om att komma igång med PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. De autentiserar Azure CLI genom att köra den **az-inloggning** kommandot och följer instruktionerna för att autentisera med hjälp av webbläsaren. [Läs mer](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) håller loggar in med Azure CLI.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. De kör följande kommando, skicka resursgruppens namn av ContosoRG, namnet på den AKS-kluster smarthotel-aks-eus2 och det nya namnet för registret.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Azure skapar en annan resursgrupp som innehåller resurser för AKS-klustret.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. När distributionen är klar, Contoso installera den **kubectl** kommandoradsverktyget. Verktyget är redan installerad på Azure CloudShell.

    **AZ aks install-cli**

12. De kontrollerar anslutningen till klustret genom att köra den **kubectl få noder** kommando. Noden är samma namn som den virtuella datorn automatiskt skapade resursgruppen.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. De kör följande kommando för att starta Kubernetes-instrumentpanelen: 

    **AZ-aks Bläddra--resource-group ContosoRG--name smarthotelakseus2**

14. En webbläsarflik öppnas på instrumentpanelen. Det här är en tunnel anslutning med Azure CLI. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>Steg 2: Skapa en Docker-behållare

### <a name="create-a-vsts-and-build"></a>Skapa en VSTS och skapa

Contoso skapas ett VSTS-projekt och konfigurerar en CI skapa för att skapa behållaren och skickar den till i ACR. Anvisningarna i det här avsnittet används den [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) lagringsplats.

1. De ska skapa ett nytt konto från visualstudio.com (**contosodevops360.visualstudio.com**), och konfigurera den att använda Git.

2. De skapar ett nytt projekt (**smarthotelrefactor**) med hjälp av Git för versionskontroll och Agile för arbetsflödet.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. De importerar GitHub-lagringsplatsen.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. I **Build and Release**, de skapar en ny definition med VSTS Git som en källa från importerade **smarthotel** lagringsplats. 

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

10. Contoso konfigurerar nu den andra aktiviteten Docker (för att skicka). De väljer du prenumerationen och **smarthotelacreus2** ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Igen, de ange filen till docker-compose.yaml-filen och välj **Push underhålla avbildningar** och inkludera den senaste taggen. När åtgärden ändras till **Push underhålla avbildningar**, ändras namnet på VSTS-uppgiften till **Push-tjänster automatiskt**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Contoso med VSTS-åtgärder som konfigurerats, sparar byggesdefinition och startar skapandeprocessen.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. De klickar på build-jobbet för att kontrollera förloppet.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. När bygget har slutförts visas i ACR nya lagringsplatser, som fylls i med de behållare som används av mikrotjänster.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>Steg 3: Distribuera backend-mikrotjänster

Med AKS-kluster skapas och Docker-avbildningar versionerna, distribuerar Contoso nu resten av den infrastruktur som ska användas av backend-mikrotjänster.

- Anvisningarna i avsnittet använder de [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) lagringsplatsen.
- I den **/deploy/k8s/arm** mapp, det finns ett enda skript för att skapa alla objekt. 

De distribuerar på följande sätt:

1. Contoso använder filen deploy.cmd för att distribuera Azure-resurser i resursgruppen för ContosoRG och EUS2 region genom att skriva följande kommando:

    **. \deploy azuredeploy ContosoRG - c usaöstra2**

    ![Distribuera serverdel](./media/contoso-migration-rebuild/backend1.png)

2. De fångar in anslutningssträngen för varje databas som ska användas senare.

    ![Distribuera serverdel](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>Steg 4: Distribuera frontend-infrastruktur

Contoso behöver distribuera den infrastruktur som används av klientdelen-appar. De skapar en blob storage-behållare för att lagra de Husdjurets bilderna. Cosmos-databasen, för att lagra dokument med informationen som Husdjurets; och API för visuellt innehåll för webbplatsen. 

Anvisningar för det här avsnittet används den [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) lagringsplatsen.

### <a name="create-storage-containers"></a>Skapa lagringsbehållare

1.  Azure-portalen öppnar Contoso storage-konto som har skapats och klickar på **Blobar**.
2.  De skapar en ny behållare (**husdjur**) med offentlig åtkomstnivå som har angetts till behållaren. Användare kommer att överföra sina Husdjurets foton till den här behållaren.

    ![Lagringsblob](./media/contoso-migration-rebuild/blob1.png)

3. De skapar en andra ny behållare med namnet **inställningar**. En fil med alla inställningar för klient-appen kommer att placeras i den här behållaren.

    ![Lagringsblob](./media/contoso-migration-rebuild/blob2.png)

4. De fångar in åtkomstinformation för storage-konto i en textfil för framtida bruk.

    ![Lagringsblob](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Etablera en Cosmos-databas

Contoso etablerar en Cosmos-databas som ska användas för Husdjurets information.

1. De skapar en **Azure Cosmos DB** på Azure Marketplace.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. De ange ett namn (**contosomarthotel**), Välj SQL-API och placera den i produktion resursgruppen ContosoRG i regionen USA, östra 2 main.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. De lägger till en ny samling för databasen med standardkapacitet och dataflöde.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. De Observera anslutningsinformationen för databasen för framtida bruk. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Etablera för visuellt innehåll

Contoso etablerar den API för visuellt innehåll. API: et kommer att anropas av funktionen för att utvärdera bilder som laddats upp av användare.

1. De skapar en **visuellt** instans i Azure Marketplace.

     ![Visuellt innehåll](./media/contoso-migration-rebuild/vision1.png)

2. De etablera API: et (**smarthotelpets**) i produktionsresursen grupperar ContosoRG i regionen USA, östra 2 main.

    ![Visuellt innehåll](./media/contoso-migration-rebuild/vision2.png)

3. De kan spara anslutningsinställningar för API: et till en textfil för senare referens.

     ![Visuellt innehåll](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>Steg 5: Distribuera backend-tjänster i Azure

Contoso måste nu kan du distribuera NGINX ingress-kontrollanten för att tillåta inkommande trafik till tjänsterna och sedan distribuerar mikrotjänsterna till AKS-klustret.

Anvisningarna i det här avsnittet används den [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) lagringsplatsen.



1. De använda Visual Studio Code för att uppdatera den **/deploy/k8s/config_loal.yml** fil. Uppdateras de olika databasanslutningar från information som de antecknade tidigare.

     ![Distribuera mikrotjänster](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > Några av konfigurationsinställningarna (till exempel Active Directory B2C) inte tas upp i den här artikeln. Det finns mer information om de här inställningarna i lagringsplatsen.

2. Filen deploy.ps1 tar bort allt innehåll i klustret (utom ingångshändelser och ingress-kontrollanten) och distribuerar mikrotjänster. De kör den på följande sätt:

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. De kör följande kommando för att kontrollera status för tjänster:

    ```
    kubectl get services
    ```
4. De öppna Kubernetes-instrumentpanelen för att granska distributionen:

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>Steg 6: Publicera klientdelen

Som ett sista steg publicerar Contoso SmartHotel appen till Azure App Service och för att Funktionsappen som anropas av tjänsten husdjur.

Anvisningarna i det här avsnittet används den [SmartHotel-public-web-lagringsplatsen.](https://github.com/Microsoft/SmartHotel360-public-web) lagringsplatsen.

### <a name="set-up-web-app-to-use-contoso-resources"></a>Konfigurera Web App att använda Contoso-resurser

1. Contoso klonar lagringsplatsen lokalt för att utvecklingsdator med Git:

    **Git-klonen https://github.com/Microsoft/SmartHotel360-public-web.git**

2. I Visual Studio, kan de öppna mapp för att visa alla filer i lagringsplatsen.

    ![Publicera klientdel](./media/contoso-migration-rebuild/front-publish1.png)

3. De gör ändringar i konfigurationen som krävs för att standardinställningen.

    - När appen startar den letar efter den **SettingsUrl** appinställningen.
    - Den här variabeln måste innehålla en URL till en konfigurationsfil.
    - Som standard är inställningen används en offentlig slutpunkt.

4. De uppdatera den **/config-sample.json/sample.json** fil. Det här är konfigurationsfilen för webben när du använder den offentliga slutpunkten.

    - De redigera både den **URL: er** och **pets_config** avsnitt med värdena för AKS-API-slutpunkter, lagringskonton och Cosmos-databasen. 
    - URL: er ska matcha DNS-namnet på den nya webbappen Contoso vill skapa.
    - Detta är för Contoso, **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Publicera klientdel](./media/contoso-migration-rebuild/front-publish2.png)

5. När filen har uppdaterats, de Byt namn på den till **smarthotelsettingsurl**, och överför den till storage-blob som de skapade tidigare.

     ![Publicera klientdel](./media/contoso-migration-rebuild/front-publish3.png)

6. De klickar på filen för att hämta URL: en. Denna URL är användning av appen när den börjar dra ner konfigurationsfilen.

    ![Publicera klientdel](./media/contoso-migration-rebuild/front-publish4.png)

7. De uppdatera **SettingsUrl** i den **appsettings. Production.JSON** filer till Webbadressen för den nya filen.

    ![Publicera klientdel](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>Distribuera webbplatsen till Azure App Service

Contoso kan nu publicera sin webbplats.


1. De gör att Application Insights-övervakning i Visual Studio 2017. Om du vill göra detta måste de välja den **PublicWeb** projekt i Solution Explorer och Sök efter **Lägg till Application Insights**. De registrera appen med Application Insights-instans som skapades när de distribuerade infrastrukturen.

    ![Publicera webbplats](./media/contoso-migration-rebuild/deploy-website1.png)

2. De ansluta PublicWeb-projektet till App Insights i Visual Studio och uppdatera för att visa den är konfigurerad.
 
    ![Publicera webbplats](./media/contoso-migration-rebuild/deploy-website2.png)

3. De skapa och publicera sina webbapp i Visual Studio.

    ![Publicera webbplats](./media/contoso-migration-rebuild/deploy-website3.png)

5. De ange ett appnamn och placera den i produktion resursgruppen **ContosoRG**, i den huvudsakliga regionen östra USA 2.

    ![Publicera webbplats](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

1. De kan använda Visual Studio för att skapa och publicera funktionen. Om du vill göra detta måste de högerklickar du på den **PetCheckerFunction** > **publicera**. Sedan kan de skapa en ny App Service-funktion.

     ![Distribuera funktionen](./media/contoso-migration-rebuild/function1.png)

2. De anger du namnet **smarthotelpetchecker**, och placera den i ContosoRG resursgruppens namn och en ny app service-plan.

     ![Distribuera funktionen](./media/contoso-migration-rebuild/function2.png)

3. Välj lagring-konto och skapa funktionen.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function3.png)

4. Distributionen börjar med etablering function-app till Azure. I **publicera**, Contoso lägger till appinställningarna för lagring, Cosmos-databasen och den API för visuellt innehåll.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function4.png)

5. Du kan köra funktionen lokalt först, uppdaterar de inställningarna i **PetCheckerFunction/local.settings.json**.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function5.png)

6. När funktionen har distribuerats, visas den i Azure-portalen med den **kör** status.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function6.png)


7. De Bläddra till appen att testa att husdjur förutsättningar AI fungerar som förväntat på [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. De klickar på avatar att ladda upp en bild.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function7.png)

9. Det första foto som de vill kontrollera är av en liten hund.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function8.png)

10. Appen returnerar ett meddelande om godkännande.

    ![Distribuera funktionen](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

- Contoso är nödvändigt att säkerställa att deras nya databaser är säkra. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Appen måste uppdateras för att använda SSL med certifikat. Behållarinstansen ska distribueras för att besvara på 443.
- De bör överväga att använda KeyVault för att skydda hemligheter för sina Service Fabric-appar. [Läs mer](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Säkerhetskopiering och katastrofåterställning

- Contoso behöver granska säkerhetskopieringskrav för Azure SQL Database. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- De bör överväga att implementera SQL redundansgrupper för att tillhandahålla regional redundans för databasen. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- De kan använda geo-replikering för ACR-premium-SKU. [Läs mer](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- När alla resurser har distribuerats, Contoso ska tilldela Azure taggar baserat på deras [infrastrukturplanering](contoso-migration-infrastructure.md#set-up-tagging).
- Alla licensiering ingår i kostnaden för PaaS-tjänster som använder Contoso. Detta dras från EA.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.

## <a name="conclusion"></a>Sammanfattning

I den här artikeln återskapa Contoso SmartHotel-app i Azure. De återskapas lokal app klientdelens VM till Azure App Services Web apps. De byggde serverdel med hjälp av mikrotjänster distribueras till behållare som hanteras av Azure Kubernetes Service (AKS). De utökad funktionalitet med en Husdjurets foto-app.




