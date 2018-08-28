---
title: Omforma en Contoso-app i en Azure container och Azure SQL Database | Microsoft Docs
description: Lär dig hur Contoso omforma en app i Azure Windows-behållare och Azure SQL Database.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: raynew
ms.openlocfilehash: 3c8ff204ffc604c6b61a217f36c7fc4ba9a5e74b
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105102"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Contoso-migrering: omforma en lokal app till en Azure container och Azure SQL Database

Den här artikeln visar hur Contoso migrerar och omforma dess SmartHotel app i Azure. Contoso migrerar app klientdelens VM till en Azure Windows-behållare och app-databas till en Azure SQL-databas.

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, utvärdera lokala resurser för migrering och som kör olika typer av migreringar. Scenarier växer i komplexitet. Ytterligare artiklar läggs över tid.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera Azure-infrastrukturen](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt  
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel appen. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt    
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso har slutförts en lift and shift-migrering av Linux osTicket app på virtuella Azure-datorer med Azure Site Recovery | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Azure Site Recovery och migrerar app-databasen till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar SmartHotel appen till en Azure Web App och migrerar app-databasen till en Azure SQL Server-instans med Database Migration Assistant | Tillgängligt
[Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt 
[Artikel 11: Omstrukturera TFS på VSTS](contoso-migration-tfs-vsts.md) | Contoso migrerar sin lokala Team Foundation Server-distributionen till Visual Studio Team Services i Azure. | Tillgängligt
Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Den här artikeln
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB... | Tillgängligt    

I den här artikeln migrerar Contoso tvålagers-Windows. NET SmartHotel app som körs på virtuella VMware-datorer till Azure. Om du vill använda den här appen tillhandahålls som öppen källkod och du kan ladda ned det från [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Affärsdrivande faktorer

Contoso IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer, och därför har Press på dess lokala system och infrastruktur.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och förenkla processer för utvecklare och användare.  Affärsbehov IT ska vara snabbt och inte spill tid eller pengar och därför leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**: Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi.  Det får inte vara i vägen eller bli en blockerare för företag.
- **Skala**: när verksamheten växer har, Contoso IT måste tillhandahålla system som kan växa i samma takt.
- **Kostnaderna**: Contoso vill minimera licensieringskostnaderna.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för den här migreringen. Dessa mål har använts för att avgöra den bästa migreringsmetoden.

**Mål** | **Detaljer**
--- | --- 
**App reqs** | Appen i Azure finns kvar som kritiskt eftersom den är i dag.<br/><br/> Den bör ha samma prestandafunktioner som för närvarande i VMWare.<br/><br/> Contoso vill stoppa stöd för Windows Server 2008 R2, där programmet för närvarande körs och är villig att investera i appen.<br/><br/> Contoso vill flytta från SQL Server 2008 R2 till en modern plattform för PaaS-databas, vilket minimerar behovet för hantering.<br/><br/> Contoso vill utnyttja dess investeringar i SQL Server-licensiering och Software Assurance där det är möjligt.<br/><br/> Contoso vill kunna skala upp app webbnivån.
**Begränsningar** | Appen består av en ASP.NET-app och en WCF-tjänst som körs på samma virtuella dator. Contoso vill dela upp detta på två webbappar med Azure App Service. 
**Azure reqs** | Contoso vill flytta appen till Azure och kör det i en behållare för att utöka app liv. Den vill inte starta helt från början implementerar appen i Azure. 
**DevOps** | Contoso vill flytta till en DevOps modellen med hjälp av Visual Studio Team Services (VSTS) för kod som skapar och släpper pipeline.

## <a name="solution-design"></a>Lösningsdesign

Efter att fästa ned mål och krav, Contoso utformar och granska en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som använder Contoso för migreringen.

### <a name="current-app"></a>Aktuell app

- SmartHotel lokal app är nivåindelad över två virtuella datorer (WEBVM och SQLVM).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.


### <a name="proposed-architecture"></a>Föreslagna arkitektur

- För databasnivån appens Contoso jämfört med Azure SQL Database med SQL Server med hjälp av [i den här artikeln](https://docs.microsoft.com/azure/sql-database/sql-database-features). Kapitalkostnaderna genom att gå med Azure SQL Database olika orsaker:
    - Azure SQL Database är en relationsdatabas hanterad tjänst. Den levererar förutsägbar prestanda på flera servicenivåer med nästan obefintlig administration. Fördelarna är dynamisk skalbarhet utan driftstopp, inbyggd intelligent optimering, och global skalbarhet och tillgänglighet.
    - Contoso utnyttjar den lightweight Data Migration Assistant (DMA) för att utvärdera och migrera en lokal databas till Azure SQL.
    - Contoso kan utbyta sina befintliga licenser för rabatterade priser på en SQL-databas med Azure Hybrid-förmånen för SQL Server med Software Assurance. Detta kan ge besparingar på upp till 30%.
    - SQL Database tillhandahåller ett antal funktioner för säkerhet, inklusive alltid krypterad, dynamisk datamaskning och identifiering av säkerhet på radnivå/hot.
- Contoso har valt för webbnivån app konvertera den till Windows-behållare med Visual Studio.
    - Contoso distribuerar appen med Azure Service Fabric och hämta Windows-behållaravbildningen från Azure Container Registry (ACR).
    - En prototyp för att utöka appen för att inkludera attitydanalys implementeras som en annan tjänst i Service Fabric, ansluten till Cosmos DB.  Detta ska läsa information från Tweets och visas i appen.
- För att implementera en pipeline för DevOps, använder Contoso VSTS för källa kod management (SCM), med Git-lagringsplatser.  Automatiserade versioner och versioner används för att skapa kod och distribuera den till Azure Container Registry och Azure Service Fabric.

    ![Scenariots arkitektur](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Granska lösningen
Contoso utvärderar den föreslagna designen genom att sätta ihop en lista med för- och nackdelar.

**Beräkningen** | **Detaljer**
--- | ---
**Experter** | Appkod SmartHotel måste ändras för migrering till Azure Service Fabric. Dock är enklare för minimal, med hjälp av Service Fabric SDK-verktyg för att ändringarna.<br/><br/> Contoso kan börja utveckla mikrotjänster för att lägga till i programmet snabbt med tiden, utan risk för ursprungliga kodbasen med flytten till Service Fabric.<br/><br/> Windows-behållare ger samma fördelar som behållare i allmänhet. De förbättra rörlighet, flexibilitet och kontroll.<br/><br/> Contoso kan utnyttja dess investering i Software Assurance som använder Azure Hybrid-förmånen för både SQL Server och Windows Server.<br/><br/> Efter migreringen kommer den inte längre behöver som stöd för Windows Server 2008 R2. [Läs mer](https://support.microsoft.com/lifecycle).<br/><br/> Contoso kan konfigurera datanivån för appen med flera instanser, så att den inte längre en felpunkt.<br/><br/> Det kommer inte längre vara beroende av föråldras SQL Server 2008 R2.<br/><br/> SQL Database stöder Contosos tekniska krav. Contoso-administratörer den lokala databasen använder Database Migration Assistant att bedömas och hittade den kompatibla.<br/><br/> SQL Database har inbyggda feltolerans som Contoso inte behöver konfigurera. Detta säkerställer att datanivå inte längre en enda åtkomstpunkt för redundans.
**Nackdelar** | Behållare är mer komplexa än andra migreringsalternativ. Inlärningskurvan på behållare kan vara ett problem för Contoso.  En ny nivå av komplexitet som erbjuder en mängd olika värdet trots kurvan medför de.<br/><br/> Driftsteamet på Contoso behöver du snabbt igång med att förstå och stöd för Azure, behållare och mikrotjänster för appen.<br/><br/> Om Contoso använder Data Migration Assistant i stället för Data Migration Service migrera databasen kan den inte har infrastrukturen som är redo för att migrera databaser i stor skala.



### <a name="migration-process"></a>Migreringsprocessen

1. Contoso etablerar Azure service fabric-kluster för Windows.
2. Det etablerar en Azure SQL-instans och migrerar SmartHotel databasen till den.
3. Contoso konverterar webbnivån VM till en Docker-behållare med hjälp av Service Fabric SDK-verktyg.
4. Ansluter service fabric-kluster och i ACR och distribuerar appen använder Azure service fabric.

    ![Migreringsprocessen](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Utvärderar och identifierar kompatibilitetsproblem som kan påverka databasfunktioner i Azure. DMA utvärderar funktionsparitet mellan SQL-källor och mål och rekommenderar förbättringar av prestanda och tillförlitlighet. | Det här verktyget kan laddas ned utan kostnad.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Är en databastjänst för intelligent, fullt hanterad relationsdatabastjänst i molnet. | Kostnaden baserat på funktioner, dataflöde och storlek. [Läs mer](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Lagrar avbildningar av alla typer av behållardistributioner. | Kostnaden baserat på funktioner, lagring och varaktighet för användning. [Läs mer](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Skapar och driva ständig, skalbara och distribuerade appar | Kostnader beroende på storleken, plats och varaktigheten för beräkningsnoderna. [Läs mer](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>Förutsättningar

Här är vad som behöver Contoso att köra det här scenariot:

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Contoso skapat prenumerationer tidigare i den här artikeln. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso tidigare har konfigurerat en Azure-infrastruktur.
**Developer-krav** | Contoso behöver följande verktyg på en utvecklararbetsstation:<br/><br/> - [Visual Studio 2017 Community Edition: Version 15.5](https://www.visualstudio.com/)<br/><br/> -.NET-arbetsbelastning är aktiverat.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0 eller senare](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) eller Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) inställd på att använda Windows-behållare.



## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso körs migreringen:

> [!div class="checklist"]
> * **Steg 1: Etablera en SQL-databasinstans i Azure**: Contoso etablerar en SQL-instans i Azure. Efter VM har migrerats till en Azure-behållare frontend webben, pekar behållarinstans med app-webbservergrupp till den här databasen.
> * **Steg 2: Skapa ett Azure Container Registry (ACR)**: Contoso etablerar ett enterprise-behållarregister för docker-behållaravbildningar.
> * **Steg 3: Etablera Azure Service Fabric**: det tillhandahåller en Service Fabric-kluster.
> * **Steg 4: Hantera service fabric-certifikat**: Contoso ställer in certifikat för VSTS-åtkomst till klustret.
> * **Steg 5: Migrera databas med DMA**: den har migrerats app-databasen med Database Migration Assistant.
> * **Steg 6: Konfigurera VSTS**: Contoso ställer in ett nytt projekt i VSTS och importerar koden till Git-lagringsplats.
> * **Steg 7: Konvertera appen**: Contoso konverterar appen till en behållare med hjälp av Visual Studio och SDK-verktyg.
> * **Steg 8: Ställa in skapa och släpp**: Contoso ställer in skapa och släpp pipelines att skapa och publicera appen i ACR och Service Fabric-kluster.
> * **Steg 9: Utöka appen**: när appen är offentlig kan Contoso utökar den för att dra nytta av funktioner i Azure och publicerar den till Azure med hjälp av pipelinen.



## <a name="step-1-provision-an-azure-sql-database"></a>Steg 1: Etablera en Azure SQL-databas

Contoso-administratörer etablera en Azure SQL database.

1. De väljer för att skapa en **SQL Database** i Azure. 

    ![Etablera SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. De ange ett databasnamn så att den matchar den databas som körs på en lokal virtuell dator (**SmartHotel.Registration**). De placera databasen i resursgruppen ContosoRG. Det här är den resursgrupp som de använder för produktionsresurser i Azure.

    ![Etablera SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. De har konfigurerat en ny SQL Server-instans (**sql-smarthotel-eus2**) i den primära regionen.

    ![Etablera SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. De har prisnivån till passar behoven för servern och databasen. Och de väljer för att spara pengar med Azure Hybrid-förmånen eftersom de redan har en SQL Server-licens.
5. De använder v-Core-baserade köpa för storleksändringar och ange begränsningarna för förväntade kraven.

    ![Etablera SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Sedan skapar de databasinstansen.

    ![Etablera SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. När instansen har skapats kan de öppna databasen och Observera information de behöver när de använder Database Migration Assistant för migrering.

    ![Etablera SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Behöver du mer hjälp?**

- [Få hjälp](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) etablera en SQL-databas.
- [Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) v-kärnor resursbegränsningar.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Steg 2: Skapa en ACR och etablera en Azure-behållare

Azure-behållaren har skapats med de exporterade filerna från Web-VM. Behållaren finns sparad i Azure Container Registry (ACR).


1. Contoso-administratörer skapa ett Behållarregister i Azure-portalen.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. De ange ett namn för registret (**contosoacreus2**), och placera den i den primära regionen, i resursgruppen som de använder för sina resurser i infrastrukturen. De Aktivera åtkomst för administratörer och ange den som en premium-SKU, så att de kan dra nytta av geo-replikering.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Steg 3: Etablera Azure Service Fabric

Behållaren SmartHotel körs i Azure Service Fabric Sluster. Contoso-administratörer skapa Service Fabric-kluster på följande sätt:

1. Skapa en Service Fabric-resurs från Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. I **grundläggande**, de tillhandahåller ett unikt DS-namn för klustret och autentiseringsuppgifter för åtkomst till den lokala virtuella datorn. De placera resursen i hanteringsgruppen för produktion-resurs (**ContosoRG**) i den primära regionen östra USA 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. I **konfiguration av nodtyp**, de ange ett nodtypnamn, hållbarhet inställningar, VM-storlek och app-slutpunkter.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. I **skapa nyckelvalv**, de skapa ett nytt nyckelvalv i sina infrastrukturresursgrupp för certifikatet.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. I **åtkomstprinciper** de ge åtkomst till virtuella datorer som ska distribueras till nyckelvalvet.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. De ange ett namn för certifikatet.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. På sidan Sammanfattning kopierar den länk som används för att hämta certifikatet. De måste kunna ansluta till Service Fabric-kluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. När valideringen är godkänd etablera de klustret.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. I guiden Importera certifikat importera de nedladdade certifikatet till dev-datorer. Certifikatet används för att autentisera för klustret.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. När klustret har etablerats som de ansluter till Service Fabric-kluster Explorer.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Måste de välja rätt certifikat.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Service Fabric Explorer belastningarna och Contoso-administratören kan hantera klustret.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-4-manage-service-fabric-certificates"></a>Steg 4: Hantera certifikat för Service Fabric

Contoso behöver klustercertifikat att tillåta VSTS åtkomst till klustret. Contoso administratörer ställa in.

1. De öppna Azure portal och bläddra till Nyckelvalvet.
2. De öppna certifikat och kopiera tumavtrycket för certifikatet som skapades under etableringen.

    ![Kopiera tumavtrycket](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. De kopiera den till en textfil för senare referens.
4. De Lägg nu till ett certifikat som kommer att bli ett klientcertifikat för administratör på klustret. På så sätt kan VSTS att ansluta till klustret för appdistribution i versionspipelinen. För att göra de, de öppna KeyVault i portalen och välj **certifikat** > **generera/importera**.

    ![Generera klientcertifikatet](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. De anger du namnet på certifikatet och ange ett unikt namn för X.509 i **ämne**.

     ![Namn på certifikat](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. När certifikatet har skapats kan de ladda ned den lokalt i PFX-format.

     ![Ladda ner certifikat](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. Nu kan de gå tillbaka till listan över certifikat i Nyckelvalvet och kopiera tumavtrycket för det klientcertifikat som precis har skapats. De kan spara den i textfilen.

     ![Tumavtryck för klienten](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. De behöver för att fastställa Base64-värdet för certifikatet för VSTS-distribution. De kan göra detta på den lokala utvecklararbetsstation med hjälp av PowerShell. De klistra in resultatet i en textfil för senare användning.

    ```
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Base64-värde](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. Slutligen de lägger du till det nya certifikatet till Service Fabric-klustret. Att göra detta i portalen de öppna klustret och klickar på **Security**.

     ![Lägg till klientcertifikatet](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. De klickar på **Lägg till** > **administratörsklient**, och klistra in tumavtrycket för det nya klientcertifikatet. Sedan de på **Lägg till**. Det kan ta upp till 15 minuter.

     ![Lägg till klientcertifikatet](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>Steg 5: Migrera databas med DMA

Contoso-administratörer kan nu migrera SmartHotel databasen med hjälp av DMA.

### <a name="install-dma"></a>Installera DMA

1. De ladda ned verktyget från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) till en lokal SQL Server-VM (**SQLVM**).
2. De kör installationsprogrammet (DownloadMigrationAssistant.msi) på den virtuella datorn.
3. På den **Slutför** kan de välja **starta Microsoft Data Migration Assistant** innan du avslutar guiden.

### <a name="configure-the-firewall"></a>Konfigurera brandväggen

För att ansluta till Azure SQL Database, Contoso administratörer konfigurera en brandväggsregel för att tillåta åtkomst.

1. I den **brandvägg och virtuella nätverk** egenskaper för databasen, de Tillåt åtkomst till Azure-tjänster och lägga till en regel för klientens IP-adress för en lokal SQL Server-dator.
2. En brandväggsregel på servernivå skapas.

    ![Brandvägg](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Behöver du mer hjälp?

[Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) skapa och hantera brandväggsregler för Azure SQL Database.

### <a name="migrate"></a>Migrera

Contoso-administratörer kan nu migrera databasen.

1. Skapa ett nytt projekt i DMA (**SmartHotelDB**) och välj **migrering** 
2. De väljer typ av källserver som **SQL Server**, och mål som **Azure SQL Database**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Information om migrering de Lägg till i **SQLVM** som källservern och **SmartHotel.Registration** databas. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. De får ett felmeddelande som verkar vara associerad med autentisering. När du har letat, är men problemet punkt (.) i namnet på databasen. Som en lösning kan de bestämde sig att etablera en ny SQL-databas med namnet **SmartHotel registrering**, för att lösa problemet. När de kör DMA igen, de är kan du välja **SmartHotel registrering**, och fortsätta med guiden.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. I **Markera objekt**, de välja databastabellerna och generera ett SQL-skript.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. När skriptet skapas i DMS de klickar på **distribuera schemat**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA bekräftar att distributionen har utförts.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Nu kan de starta migreringen.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. När migreringen är klar kan Contoso Kontrollera att databasen körs på Azure SQL-instansen.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. De ta bort extra SQL-databasen **SmartHotel.Registration** i Azure-portalen.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)


## <a name="step-6-set-up-vsts"></a>Steg 6: Konfigurera VSTS

Contoso behöver skapa DevOps-infrastrukturen och pipelines för programmet.  Detta gör Contoso administratörer skapa ett nytt VSTS-projekt, importera sin kod, skapa och release-pipelines.

1.   De ska skapa ett nytt projekt i Contoso VSTS-konto (**ContosoSmartHotelRearchitect**), och välj **Git** för versionskontroll.

    ![Nytt projekt](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. De importerar Git-lagringsplats som för närvarande innehåller sin kod. Det är en [offentlig repo](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) och du kan hämta den.

    ![Hämta kod](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. När koden har importerats kan de ansluta Visual Studio till lagringsplatsen och klona den kod med hjälp av Team Explorer.

    ![Ansluta till lagringsplatsen](./media/contoso-migration-rearchitect-container-sql/vsts3.png)

4. När lagringsplatsen klonas till developer-dator, kan de öppna lösningsfilen för appen. I web app och wcf-tjänsten har separata projektet i filen.

    ![Lösningsfil](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>Steg 7: Konvertera appen till en behållare

Lokal app är en traditionell app med tre nivåer:

- Det innehåller WebForms och en WCF-tjänst som ansluter till SQL Server.
- Den använder Entity Framework för att integrera med data i SQL-databasen, göra den tillgänglig via en WCF-tjänst.
- WebForms programmet interagerar med WCF-tjänst.

Contoso-administratörer ska konvertera appen till en behållare med projektplats Studio och SDK-verktyg, enligt följande:


1. Med Visual Studio, de granska öppna lösningsfilen (SmartHotel.Registration.sln) i den **SmartHotel360-interna-bokning-apps\src\Registration** katalogen i den lokala lagringsplatsen.  Två appar visas. Webbservergrupp SmartHotel.Registration.Web och WCF-tjänstapp SmartHotel.Registration.WCF.

    ![Container](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. De högerklickar du på webbappen > **Lägg till** > **Behållarstödet i Orchestrator**.
3. I **lägga till stöd för behållare orkester**, de väljer **Service Fabric**.

    ![Container](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. De Upprepa processen för SmartHotel.Registration.WCF app.
5. Nu kan kontrollera de hur lösningen har ändrats.

    - Den nya appen är **SmartHotel.RegistrationApplication/**
    - Den innehåller två tjänster: **SmartHotel.Registration.WCF** och **SmartHotel.Registration.Web**.

    ![Container](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. Visual Studio skapade Docker-filen och hämtade bilderna som krävs lokalt till datorn för utvecklare.

    ![Container](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. En manifestfil (**ServiceManifest.xml**) skapas och öppnas av Visual Studio. Den här filen talar om för Service Fabric så här konfigurerar du behållaren när det distribueras till Azure.

    ![Container](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. En annan manifestfilen (** ApplicationManifest.xml) innehåller program som konfiguration för behållarna.

    ![Container](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. De öppnar den **ApplicationParameters/Cloud.xml** filen och uppdatera anslutningssträngen för att ansluta appen till Azure SQL-databasen. Anslutningssträngen kan finnas i databasen i Azure-portalen.

    ![Anslutningssträng](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. Hunnit checka in den uppdaterade koden och skicka till VSTS.

    ![Checka in](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-vsts"></a>Steg 8: Skapa och släppa pipelines i VSTS

Contoso-administratörer kan nu konfigurera VSTS för att skapa build-version och versionen processen för att åtgärden DevOps-metoder.

1. I VSTS, de klickar på **skapa och släpp** > **ny pipeline**.

    ![Ny pipeline](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. De väljer **VSTS Git** och relevanta lagringsplatsen.

    ![Git och lagringsplats](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. I **Välj en mall**, de väljer fabric med Docker-stöd.

     ![Fabric- och Docker](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. De ändra taggen bilder för att skapa avbildningen och konfigurera aktiviteten om du vill använda den etablerade ACR.

     ![Register](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. I den **skicka avbildningar** aktivitet, de konfigurera bilden ska vara puhed till ACR och välj för att inkludera den senaste taggen.
6. I **utlösare**, de aktiverar du kontinuerlig integrering och lägga till huvudgrenen.

    ![Utlösare](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. De klickar på **spara och köa** att starta en version.
8. När den har byggts flyttar de till versionspipelinen. I VSTS de klickar på **versioner** > **ny pipeline**.

    ![Releasepipeline](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. De väljer den **distribution av Azure Service Fabric** mallen och namn på miljön (**SmartHotelSF**).

    ![Miljö](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. Du får ett namn på pipeline (**ContosoSmartHotelRearchitect**). För miljön, de klickar på **fas 1, 1 aktivitet** att konfigurera Service Fabric-distribution.

    ![Fas och aktivitet](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. Nu kan de Klicka **New** att lägga till en ny klusteranslutning.

    ![Ny anslutning](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. I **lägga till Service Fabric-tjänstanslutning**, de konfigurerar anslutningen och inställningarna för autentisering som ska användas av VSTS för att distribuera appen. Klusterslutpunkten kan finnas i Azure portal och de lägger till **tcp: / /** som ett prefix.
13. Certifikatinformationen insamlade matas in i **servercertifikatets tumavtryck** och **klientcertifikat**.

    ![Certifikat](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. De klickar på pipelinen > **lägga till en artefakt**.

     ![Artefakt](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. De väljer du projektet och skapa pipeline, med hjälp av den senaste versionen.

     ![Utveckla](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. Observera att blixt på artefakten har markerats.

     ![Artefakten status](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. Observera också att utlösare av kontinuerlig distribution är aktiverat.

   ![Kontinuerlig distribution aktiverad](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. De klickar på **spara** > **skapa en version**.

    ![Frisläpp](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. När distributionen är klar körs SmartHotel nu Service Fabric.

    ![Publicera](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. För att ansluta till appen, de dirigeras trafiken till offentliga IP-adressen för Azure-belastningsutjämnaren framför sin Service Fabric-noder.

    ![Publicera](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>Steg 9: Utöka appen och publicera

När den SmartHotel app och databasen körs i Azure, vill Contoso utöka appen.

- Contosos utvecklare är prototyper ett nytt .NET Core-program som körs på Service Fabric-klustret.
- Appen kommer att användas för att hämta åsiktsdata från CosmosDB.
- Dessa data ska vara i form av Tweets som bearbetas med en funktion i Azure och Cognitive Services-Textanalys.

### <a name="provision-azure-cosmos-db"></a>Etablera Azure Cosmos DB

Contoso-administratörer etablera en Azure Cosmos-databas som ett första steg.

1. De kan skapa en Azure Cosmos DB-resurs från Azure Marketplace.

    ![Utöka](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. De ange ett databasnamn (**contososmarthotel**), Välj SQL-API och placera resursen i resursgruppen produktion på den primära regionen östra USA 2.

    ![Utöka](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. I **komma igång**, de väljer **Datautforskaren**, och Lägg till en ny samling.
4. I **Lägg till samling** de innehåller ID: N och ange kapacitet och dataflöde.

    ![Utöka](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. I portalen, de öppnar den nya databasen > **samling** > **dokument** och klicka på **nytt dokument**.
6. De klistra in följande JSON-kod i dokumentfönstret. Det här är exempel på data i form av en enda tweet.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Utöka](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. De letar reda på Cosmos DB-slutpunkten och nyckeln. Dessa används i appen för att ansluta till samlingen. I databasen, de klickar på **nycklar**, och kopiera URI och primärnyckel till anteckningar.

    ![Utöka](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Uppdatera appen sentiment

Med Cosmos DB som etablerats, kan Contoso-administratörer konfigurera att appen ansluter till den.

1. De öppnar filen ApplicationModern\ApplicationParameters\cloud.xml i Solution Explorer i Visual Studio.

    ![Sentiment app](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. De fyller du i följande två parametrar:

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Sentiment app](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Publicera appen

När du utökar appen, publicera den till Azure med pipelinen om Contoso-administratörer.

1. Hunnit checka in och skicka koden till VSTS. Detta startar skapa och släpp pipelines.

2. Efter skapande och distribution slutförs körs SmartHotel nu Service Fabric. Servie infrastrukturhantering-konsolen visas nu tre tjänster.

    ![Publicera på nytt](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. De kan klicka på via tjänster att appen SentimentIntegration är igång

    ![Publicera på nytt](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Contoso måste slutföra stegen rensning efter migreringen:  

- Ta bort de lokala virtuella datorerna från vCenter-lagret.
- Ta bort de virtuella datorerna från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentation för att visa de nya platserna för SmartHotel appen. Visa databasen som körs i Azure SQL-databas och klientdelen som körs i Service Fabric.
- Granska eventuella resurser som interagerar med de inaktiverade virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.


## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

- Contoso-administratörer måste du se till att deras nya **SmartHotel registrering** databasen är skyddad. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- I synnerhet bör de Uppdatera behållaren att använda SSL med certifikat.
- De bör överväga att använda KeyVault för att skydda hemligheter för sina Service Fabric-appar. [Läs mer](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Säkerhetskopior

- Contoso behöver granska säkerhetskopieringskrav för Azure SQL Database. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso-administratörer bör överväga att implementera redundansgrupper för att tillhandahålla regional redundans för databasen. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- De kan använda geo-replikering för ACR-premium-SKU. [Läs mer](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso måste du överväga att distribuera Web-App i centrala USA, östra 2 och USA, centrala region när Web App for Containers blir tillgänglig. Contoso-administratörer kan konfigurera Traffic Manager för att säkerställa att redundans vid regionala avbrott.
- Cosmos DB säkerhetskopierar automatiskt. Contoso [Läs mer om](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) den här processen om du vill veta mer.

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- När alla resurser har distribuerats, Contoso ska tilldela Azure taggar som baseras på [infrastrukturplanering](contoso-migration-infrastructure.md#set-up-tagging).
- Alla licensiering ingår i kostnaden för PaaS-tjänster som använder Contoso. Detta dras från EA.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.

## <a name="conclusion"></a>Sammanfattning

I den här artikeln omstrukturerade Contoso SmartHotel-app i Azure genom att migrera app-klientdelens VM till Service Fabric. App-databasen har migrerats till en Azure SQL database.





