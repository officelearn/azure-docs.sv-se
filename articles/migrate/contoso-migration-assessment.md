---
title: Utvärdera lokala arbetsbelastningar för Contoso-migrering till Azure | Microsoft Docs
description: Lär dig hur Contoso utvärderar sina lokala datorer för migrering till Azure med hjälp av Azure Migrate och Data Migration Assistant.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: ea0987eaea8ee558df35ecce6afb5e7bab3ac4de
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087641"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso-migrering: utvärdera lokala arbetsbelastningar för migrering till Azure

I den här artikeln utvärderar Contoso dess lokal SmartHotel360-app för migrering till Azure.

Den här artikeln är en del av en serie som dokumenterar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrunden egäran om kontaktinformation och detaljerade distributions-scenarier som illustrerar hur du ställer in en infrastruktur för migrering, bedöma lokala resurser för migrering och kör olika typer av migreringar. Scenarier växer i komplexitet. Artiklar läggs till seriens över tid.

Artikel | Information | Status
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla artiklar i serien. | Tillgängligt
Artikel 3: Utvärdera lokala resurser för migrering till Azure | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Den här artikeln
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Den har migrerats appen frontend med hjälp av Azure Site Recovery-tjänsten. Den migrerar app-databasen till en Azure SQL Database Managed Instance med Azure Database Migration Service. | Tillgängligt
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel360-app med Site Recovery migrera app-datorer och Database Migration Service för att migrera app-databasen till en SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso Slutför en lift and shift-migrering av dess osTicket app i Linux på Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar dess osTicket Linux-app till Azure virtuella datorer med Site Recovery. Den har migrerats app-databas till Azure Database for MySQL med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app i en Azure webbapp och Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar dess SmartHotel360-app till en Azure webbapp och migrerar app-databasen till en Azure SQL Server-instans med Databse Migration Assistant. | Tillgängligt
[Artikel 10: Omstrukturera en Linux-app i en Azure-webbapp och en Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera Team Foundation Server på Azure DevOps-tjänsterna](contoso-migration-tfs-vsts.md) | Contoso migrerar dess lokal Team Foundation Server-distribution till Azure DevOps-tjänsterna i Azure. | Tillgängligt
[Artikel 12: Omforma en app i Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt
[Artikel 14: Skala en migrering till Azure](contoso-migration-scale.md) | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. | Tillgängligt


## <a name="overview"></a>Översikt

När Contoso betraktar migrerar till Azure kan företaget vill göra en teknisk och ekonomisk utvärdering för att avgöra om dess lokala arbetsbelastningar lämpar sig för migrering till molnet. I synnerhet vill Contoso-teamet utvärdera kompatibiliteten för datorn och databasen för migrering. Företaget vill beräkna kapaciteten och kostnaderna för att köra Contosos resurser i Azure.

Du kommer igång och att bättre förstå teknikerna utvärderar Contoso två av sin lokala appar, sammanfattas i tabellen nedan. Företaget utvärderar för migreringsscenarier som rehost och omstrukturera appar för migrering. Mer information om rehosting och omstrukturering i den [Contoso migreringsöversikt](contoso-migration-overview.md).

Appnamn | Plattform | App-nivåer | Information
--- | --- | --- | ---
SmartHotel360<br/><br/> (hanterar Contoso resa krav) | Körs på Windows med en SQL Server-databas | Två skikt app. Klientdelen ASP.NET-webbplats som körs på en virtuell dator (**WEBVM**) och SQL Server körs på en annan virtuell dator (**SQLVM**). | Virtuella datorer är VMware, som körs på en ESXi-värd som hanteras av vCenter-servern.<br/><br/> Du kan hämta exempelappen från [GitHub](https://github.com/Microsoft/SmartHotel360).
osTicket<br/><br/> (Contoso service desk-app) | Körs på Linux/Apache med MySQL PHP (LAMP) | Två skikt app. En frontend PHP-webbplats som körs på en virtuell dator (**OSTICKETWEB**) och MySQL-databas som körs på en annan virtuell dator (**OSTICKETMYSQL**). | Appen används av customer service-appar för att spåra problem för internt anställda och externa kunder.<br/><br/> Du kan hämta exemplet från [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuella arkitektur

Det här diagrammet visar den aktuella Contoso lokal infrastrukturen:

![Aktuell Contoso-arkitektur](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso har en huvuddatacentret. Datacentret finns i den stad i New York i östra USA.
- Contoso har tre ytterligare lokala grenar i USA.
- Huvuddatacentret är ansluten till internet med en fiber appar i Metro-Ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt ansluten till internet med hjälp av företagsklass anslutningar med IPsec VPN-tunnlar till huvuddatacentret. Installationsprogrammet tillåter Contosos hela nätverket ska anslutas permanent och optimerar Internetanslutning.
- Huvuddatacentret är fullständigt virtualiserat med VMware. Contoso har två ESXi 6.5-virtualiseringsvärdar som hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering. Contoso använder DNS-servrar i det interna nätverket.
- Domänkontrollanterna i datacentret körs på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.

## <a name="business-drivers"></a>Affärsdrivande faktorer

Contosos IT-ledning har arbetat tillsammans med företagets affärspartners att förstå vad företaget vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer. Därför ökat trycket på företagets lokala system och infrastruktur.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och effektivisera processer för utvecklare och användare. Affärsbehov IT är fast och som inte spill tid eller pengar, så företaget kan leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**: Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än de ändringar som sker i marketplace för företag för att utföra en global ekonomi. IT på Contoso måste inte vara i vägen eller bli en blockerare för företag.
- **Skala**: enligt företagets verksamhet växer har Contoso IT måste tillhandahålla system som kan växa i samma takt.

## <a name="assessment-goals"></a>Utvärdering av mål

Contoso cloud-teamet har identifierat mål för dess migreringsutvärdering:

- Efter migreringen bör appar i Azure ha samma prestandafunktioner som appar har idag i Contosos lokala VMWare-miljön. Flytta till molnet innebär inte att prestanda är mindre viktigt.
- Contoso behöver förstå kompatibilitet för sina program och databaser med kraven för Azure. Contoso måste också att förstå dess värdalternativ i Azure.
- Contosos databasadministration värden ska minimeras när appar flytta till molnet.  
- Contoso vill förstå dess migreringsalternativ utan även kostnaderna för infrastrukturen när den har flyttats till molnet.

## <a name="assessment-tools"></a>Bedömningsverktyg

Contoso använder Microsoft-verktyg för dess migreringsutvärdering. Verktygen överensstämmer med företagets mål och ger Contoso all information som behövs.

Teknologi | Beskrivning | Kostnad
--- | --- | ---
[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso använder Data Migration Assistant för att utvärdera och identifiera kompatibilitetsproblem som kan påverka dess databasfunktioner i Azure. Data Migration Assistant utvärderar funktionsparitet mellan SQL-källor och mål. Den rekommenderar förbättringar av prestanda och tillförlitlighet. | Data Migration Assistant är ett kostnadsfritt, nedladdningsbart verktyg.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso använder Azure Migrate-tjänsten för att utvärdera dess virtuella VMware-datorer. Azure Migrate utvärderar migreringslämplighet av datorerna. Det ger uppskattningar av storlek och kostnad för att köra i Azure.  | Från och med maj 2018 är Azure Migrate en kostnadsfri tjänst.
[Tjänstkarta](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate använder Tjänstkarta för att visa beroenden mellan datorer som företaget vill migrera. | Tjänstkarta ingår i Azure Log Analytics. Contoso kan för närvarande kan använda Tjänstkarta för 180 dagar utan kostnad.

I det här scenariot Contoso laddar ned och kör Data Migration Assistant för att utvärdera den lokala SQL Server-databas för dess reseapp. Contoso använder Azure Migrate med beroendemappning för att utvärdera de virtuella datorerna för appen innan du migrerar till Azure.

## <a name="assessment-architecture"></a>Arkitektur för migreringsutvärdering

![Arkitektur för migreringsutvärdering](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso är ett fiktivt namn som representerar en typisk företags-organisation.
- Contoso har ett lokalt datacenter (**contoso-datacenter**) och lokala domänkontrollanter (**CONTOSODC1**, **CONTOSODC2**).
- Virtuella VMware-datorer finns på VMware ESXi-värdar som kör version 6.5 (**contosohost1**, **contosohost2**).
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**, som körs på en virtuell dator).
- SmartHotel360 reseapp har följande egenskaper:
    - Appen är nivåindelad över två virtuella VMware-datorer (**WEBVM** och **SQLVM**).
    - De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com**.
    - De virtuella datorerna som kör Windows Server 2008 R2 Datacenter med SP1.
- VMware-miljön hanteras av vCenter Server (**vcenter.contoso.com**) som körs på en virtuell dator.
- Den osTicket service desk-appen:
    - Appen är nivåindelad över två virtuella datorer (**OSTICKETWEB** och **OSTICKETMYSQL**).
    - De virtuella datorerna kör Linux-Server för Ubuntu 16.04-LTS.
    - **OSTICKETWEB** kör Apache-2 och PHP 7.0.
    - **OSTICKETMYSQL** MySQL 5.7.22 körs.

## <a name="prerequisites"></a>Förutsättningar

Contoso och andra användare måste uppfylla följande krav för utvärderingen:

- Ägare eller deltagare behörigheter för Azure-prenumeration eller för en resursgrupp i Azure-prenumeration.
- En lokal vCenter Server-instans som kör version 6.5, 6.0 eller 5.5.
- Ett skrivskyddat konto i vCenter Server eller behörighet att skapa en.
- Behörighet att skapa en virtuell dator på vCenter Server-instansen med hjälp av en .ova-mall.
- Minst en ESXi-värd som kör version 5.0 eller senare.
- Minst två lokala virtuella VMware-datorer, varav en kör en SQL Server-databas.
- Behörigheter för att installera Azure Migrate-agenter på varje virtuell dator.
- De virtuella datorerna ska ha direkt Internetanslutning.  
        - Du kan begränsa Internetåtkomsten till [de URL:er som krävs](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).  
        – Om dina virtuella datorer inte har Internetanslutning, Azure [Log Analytics Gateway](../log-analytics/log-analytics-oms-gateway.md) måste vara installerad på dem, och agenten trafik dirigeras genom den.
- FQDN för den virtuella datorn som kör SQL Server-instansen (för utvärdering av databasen).
- Windows-brandväggen som körs på SQL Server-dator ska tillåta externa anslutningar på TCP-port 1433 (standard). Den här inställningen kan Data Migration Assistant att ansluta.

## <a name="assessment-overview"></a>Översikt över utvärdering

Här är hur Contoso utför dess utvärdering:

> [!div class="checklist"]
> * **Steg 1: Hämta och installera Data Migration Assistant**: Contoso förbereder Data Migration Assistant för utvärdering av en lokal SQL Server-databasen.
> * **Steg 2: Utvärdera databasen med hjälp av Data Migration Assistant**: Contoso körs och analyserar databasutvärderingen.
> * **Steg 3: Förbereda för VM-utvärdering genom att använda Azure Migrate**: Contoso ställer in lokala konton och justerar inställningarna för VMware.
> * **Steg 4: Identifiera lokala virtuella datorer med hjälp av Azure Migrate**: Contoso skapar ett Azure Migrate collector VM. Sedan körs Contoso insamlaren för att identifiera virtuella datorer för utvärdering.
> * **Steg 5: Förbereda för beroendeanalys med hjälp av Azure Migrate**: Contoso installerar Azure Migrate-agenter på de virtuella datorerna, så att företaget kan se beroendemappningen mellan de virtuella datorer.
> * **Steg 6: Utvärdera de virtuella datorerna med hjälp av Azure Migrate**: Contoso kontrollerar beroenden, grupperar de virtuella datorerna och kör utvärderingen. När utvärderingen är klar, analyserar Contoso utvärderingen vid förberedelserna för migreringen.

## <a name="step-1-download-and-install-data-migration-assistant"></a>Steg 1: Hämta och installera Data Migration Assistant

1. Contoso hämtar Data Migration Assistant från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Data Migration Assistant kan installeras på en dator som kan ansluta till SQL Server-instansen. Contoso behöver inte köra den på SQL Server-datorn.
    - Data Migration Assistant bör inte köras på SQL Server-värddatorn.
2. Contoso körs den hämta installationsfilen (DownloadMigrationAssistant.msi) för att påbörja installationen.
3. På den **Slutför** sidan Contoso väljer **starta Microsoft Data Migration Assistant** innan du avslutar guiden.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>Steg 2: Köra och analysera databasutvärderingen för SmartHotel360

Contoso kan nu köra en utvärdering för att analysera en lokal SQL Server-databasen för SmartHotel360-appen.

1. I Data Migration Assistant Contoso väljer **New** > **utvärdering**, och ger utvärderingen ett projektnamn.
2. För **typ av källserver**, Contoso väljer **SQL Server på Azure Virtual Machines**.

    ![Data Migration Assistant – Välj källa](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      För närvarande stöder Data Migration Assistant inte utvärdering för att migrera till en Azure SQL Database Managed Instance. Som en lösning kan använder Contoso SQL Server på en Azure virtuell dator som den angivna mål för utvärderingen.

3. I **Välj målversion**, Contoso väljer SQL Server 2017 som målversionen. Contoso behöver välja den här versionen eftersom det är den version som används av SQL Database Managed Instance.
4. Contoso väljer rapporter för att få hjälp med att identifiera information om kompatibilitet och nya funktioner:
    - **Kompatibilitetsproblem** Observera ändringar som kan bryta migreringen eller som kräver en mindre justering före migreringen. Den här rapporten håller Contoso informeras om alla funktioner som används som är inaktuella. Problemen ordnas efter kompatibilitetsnivå.
    - **Nya funktionsrekommendation** kommentarer om nya funktioner i SQL Server-målplattformen som kan användas för databasen efter migreringen. Nya funktionsrekommendationer ordnas under rubrikerna **prestanda**, **Security**, och **Storage**.

    ![Data Migration Assistant – kompatibilitetsproblem och nya funktioner](./media/contoso-migration-assessment/dma-assessment-2.png)

2. I **Anslut till en server**, Contoso anger du namnet på den virtuella datorn som kör den databasen och autentiseringsuppgifter för att komma åt den. Contoso väljer **lita på servercertifikatet** att kontrollera att den virtuella datorn kan komma åt SQL Server. Sedan Contoso väljer **Connect**.

    ![Data Migration Assistant – Anslut till en server](./media/contoso-migration-assessment/dma-assessment-3.png)

3. I **Lägg till källa**, Contoso lägger till den databas som företaget vill utvärdera och sedan väljer **nästa** att starta utvärderingen.
4. Utvärderingen har skapats.

    ![Data Migration Assistant – skapa utvärdering](./media/contoso-migration-assessment/dma-assessment-4.png)

5. I **granskningsresultaten**, Contoso visar resultaten.

### <a name="analyze-the-database-assessment"></a>Analysera databasutvärderingen

Resultatet visas så fort de är tillgängliga. Om Contoso åtgärdar problem, den måste välja **starta om utvärderingen** köra utvärderingen på nytt.

1. I den **kompatibilitetsproblem** rapportera, Contoso söker efter eventuella problem på respektive kompatibilitetsnivå. Kompatibilitetsnivåerna mappar till SQL Server-versioner på följande sätt:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Data Migration Assistant – kompatibilitetsproblem rapport](./media/contoso-migration-assessment/dma-assessment-5.png)

2. I den **Funktionsrekommendationerna** rapportera, Contoso visar funktioner för prestanda, säkerhet och lagring som utvärderingen rekommenderar efter migreringen. Olika funktioner rekommenderas, till exempel Minnesintern OLTP, columnstore-index, Stretch Database, Always Encrypted, dynamisk datamaskning och transparent datakryptering.

    ![Data Migration Assistant – funktionen rekommendationer rapport](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso bör [aktivera transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) för alla SQL Server-databaser. Detta är även mer kritiska när en databas är i molnet än när den har finns lokalt. Transparent datakryptering bör aktiveras endast efter migreringen. Om du redan transparent datakryptering är aktiverat, flytta Contoso certifikat eller asymmetriska nyckeln till master-databasen på målservern. Lär dig hur du [flytta en transparent data encryption-skyddad databas till en annan SQL Server-instansen](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Contoso kan exportera utvärdering i JSON- eller CSV-format.

> [!NOTE]
> För storskaliga utvärderingar:
> - Köra flera utvärderingar samtidigt och visa tillståndet för utvärderingarna på den **alla utvärderingar** sidan.
> - Konsolidera utvärderingarna till en [SQL Server-databas](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
> - Konsolidera utvärderingarna till en [Power BI-rapport](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>Steg 3: Förbereda för VM-utvärdering genom att använda Azure Migrate

Contoso behöver skapa ett VMware-konto med Azure Migrate kan använda för att automatiskt identifiera virtuella datorer för utvärdering, kontrollera behörighet att skapa en virtuell dator, Observera portarna som måste du öppna och ange nivån för statistikinställningar.

### <a name="set-up-a-vmware-account"></a>Konfigurera ett VMware-konto

VM-identifiering kräver ett skrivskyddat konto i vCenter Server som har följande egenskaper:

- **Användartyp**: minst en skrivskyddad användare.
- **Behörigheter**: datacenter-objekt, Välj den **Sprid till underordnat objekt** kryssrutan. För **rollen**väljer **skrivskyddad**.
- **Information om**: användaren tilldelas på datacenternivå åtkomst till alla objekt i datacentret.
- För att begränsa åtkomsten tilldelar den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

### <a name="verify-permissions-to-create-a-vm"></a>Kontrollera behörigheter för att skapa en virtuell dator

Contoso kontrollerar att den har behörighet att skapa en virtuell dator genom att importera en fil i .ova-format. Lär dig hur du [skapa och tilldela en roll med behörigheter](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Kontrollera portar

Contoso-utvärderingen använder beroendemappning. Beroendemappning kräver en agent installeras på virtuella datorer som används för att bedöma. Agenten måste kunna ansluta till Azure från TCP-port 443 på varje virtuell dator. Lär dig mer om [anslutningskraven](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).

### <a name="set-statistics-settings"></a>Ange inställningar för statistik

Innan Contoso börjar distributionen, måste den inställd på nivå 3 statistikinställningarna för vCenter-servern.

> [!NOTE]
> - När du har angett nivån måste Contoso vänta i minst en dag innan den körs utvärderingen. I annat fall kanske utvärderingen inte fungerar som förväntat.
> - Om nivån är högre än 3 fungerar utvärderingen, men:
>    - Prestandadata för diskar och nätverk samlas inte in.
>    - För lagring rekommenderar Azure Migrate en standarddisk i Azure, med samma storlek som den lokala disken.
>    - Nätverk för varje lokalt nätverkskort, rekommenderas ett nätverkskort i Azure.
>    - För beräkning, Azure Migrate tittar på den virtuella datorns kärnor och minnesstorlek och rekommenderar en virtuell Azure-dator med samma konfiguration. Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.
> - Läs mer om storleksändring med nivå 3 [bestämning av diskstorlek](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

Du anger:

1. I vSphere-webbklienten öppnas Contoso vCenter Server-instansen.
2. Contoso väljer **hantera** > **inställningar** > **Allmänt** > **redigera**.
3. I **statistik**, Contoso anger du statistiknivåinställningen nivåinställningarna till **nivå 3**.

    ![vCenter Server statistiknivån](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>Steg 4: Identifiera virtuella datorer

För att identifiera virtuella datorer, skapar ett Azure Migrate-projekt i Contoso. Contoso laddar ned och ställer in den Virtuella insamlardatorn. Sedan körs Contoso insamlaren för att identifiera dess lokala virtuella datorer.

### <a name="create-a-project"></a>Skapa ett projekt

1. I den [Azure-portalen](https://portal.azure.com), Contoso söker efter **Azure Migrate**. Contoso skapar sedan ett projekt.
2. Anger ett namn för Contoso (**ContosoMigration**) och Azure-prenumerationen. Den skapar en ny Azure resursgrupp (**ContosoFailoverRG**).
    > [!NOTE]
    > - Du kan skapa ett Azure Migrate-projekt i regionen USA, västra eller USA, östra.
    > - Du kan planera en migrering för valfri målplats.
    > - Projektplatsen används bara för att lagra de metadata som har samlats in från lokala virtuella datorer.

    ![Azure Migrate – skapa migreringsprojekt](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Azure Migrate skapar en lokal virtuell dator som kallas den *insamlingsprogrammet*. Den virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om de virtuella datorerna till Azure Migrate-tjänsten. Om du vill konfigurera insamlingsprogrammet Contoso hämtar en OVA-mall och importerar den till en lokal vCenter Server-instansen att skapa den virtuella datorn.

1. I Azure Migrate-projektet Contoso väljer **komma igång** > **identifiera och utvärdera** > **identifiera datorer**. Contoso hämtar OVA mallfilen.
2. Contoso kopierar projekt-ID och nyckel. Projektet och ID krävs för att konfigurera insamlaren.

    ![Azure Migrate - hämta OVA-filen](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Innan du distribuerar den virtuella datorn, kontrollerar Contoso att OVA-filen är säker:

1. Contoso öppnas en kommandotolk för administratör på datorn där filen hämtades.
2. Contoso kör följande kommando för att generera en hash för OVA-filen:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    **Exempel**

    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha nedanstående inställningar (version 1.0.9.15):

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
    SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
    SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

### <a name="create-the-collector-appliance"></a>Skapa insamlingsprogrammet

Contoso kan nu importera den nedladdade filen till vCenter Server-instansen och etablera insamlingsprogrammet VM:

1. I vSphere-klientkonsolen Contoso väljer **filen** > **distribuera OVF-mall**.

    ![vSphere-webbklienten – distribuera OVF-mall](./media/contoso-migration-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF-mall, Contoso väljer **källa**, och sedan anger platsen för OVA-filen.
3. I **namn och plats**, Contoso anger ett visningsnamn för den Virtuella insamlardatorn. Sedan väljer den lagerplatsen som ska vara värd för den virtuella datorn. Contoso anger också värden eller klustret som ska köras insamlingsprogrammet.
4. I **Storage**, Contoso Anger lagringsplatsen. I **diskformat**, Contoso väljer hur företaget vill etablera lagringen.
5. I **nätverksmappning**, Contoso anger nätverk där du vill ansluta den Virtuella insamlardatorn. Nätverket måste ha en Internetanslutning för att skicka metadata till Azure.
6. Contoso granskar du inställningarna och väljer sedan **slå på strömmen efter distributionen** > **Slutför**. Ett meddelande som bekräftar lyckades visas när insamlingsprogrammet har skapats.

### <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

Nu kan kör Contoso insamlaren för att identifiera virtuella datorer. För närvarande kan insamlaren stöder för närvarande endast **engelska (USA)** som operativsystemspråk och gränssnittsspråk i insamlaren.

1. I vSphere-klientkonsolen Contoso väljer **öppna konsolen**. Contoso anger språk, tidszon och lösenordsinställningar för den Virtuella insamlardatorn.
2. På skrivbordet, Contoso väljer den **kör insamlare** genväg.

    ![vSphere-klientkonsolen - genväg till insamlaren](./media/contoso-migration-assessment/collector-shortcut.png)

3. I Azure Migrate Collector Contoso väljer **konfigurera förhandskraven**. Contoso accepterar licensvillkoren och läser informationen från tredje part.
4. Insamlaren kontrollerar att den virtuella datorn är ansluten till internet, att tiden är synkroniserad och att collector-tjänsten körs. (Collector-tjänsten installeras som standard på den virtuella datorn.) Contoso installerar även VMware PowerCLI.

    > [!NOTE]
    > Det förutsätts att den virtuella datorn har direkt åtkomst till internet utan att använda en proxyserver.

    ![Azure Migrate Collector - Verifiera förutsättningar](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. I **ange vCenter-serverinformationen**, Contoso anger namn (FQDN) eller IP-adressen för vCenter Server-instansen och skrivskyddade autentiseringsuppgifter används för identifiering.
6. Contoso väljer en omfattning för identifieringen av virtuella datorer. Insamlaren kan identifiera endast virtuella datorer som är inom det specificerade omfånget. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Omfattningen får inte innehålla mer än 1 500 virtuella datorer.

    ![Ange vCenter Server-information](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. I **ange migreringsprojekt**, Contoso anger Azure Migrate projekt-ID och nyckel som har kopierats från portalen. Projekt-ID och nyckel, Contoso kan gå till projektet **översikt** sidan > **identifiera datorer**.  

    ![Ange migreringsprojektet](./media/contoso-migration-assessment/collector-connect-azure.png)

8. I **visa insamlingsförloppet**, Contoso kan övervaka identifiering och kontrollera att metadata som samlas in från de virtuella datorerna är i omfattningen. Insamlaren visar en ungefärlig identifieringstid.

    ![Visa insamlingsförloppet](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När samlingen är klar, kontrollerar Contoso att de virtuella datorerna visas i portalen:

1. I Azure Migrate-projektet Contoso väljer **hantera** > **datorer**. Contoso kontrollerar att de virtuella datorerna som du vill identifiera visas.

    ![Azure Migrate - identifierade datorer](./media/contoso-migration-assessment/discovery-complete.png)

2. Datorerna har för närvarande inte Azure Migrate-agenter som installerats. Contoso måste installera agenter för att visa beroenden.

    ![Azure Migrate - agentinstallation krävs](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>Steg 5: Förbereda för beroendeanalys

Om du vill visa beroenden mellan virtuella datorer som du vill utvärdera Contoso hämtar och installerar agenter på de virtuella datorerna för appen. Contoso installerar agenter på alla virtuella datorer för sina appar, både för Windows och Linux.

### <a name="take-a-snapshot"></a>Ta en ögonblicksbild

Om du vill behålla en kopia av de virtuella datorerna innan du ändrar dem, tar en ögonblicksbild av Contoso innan agenterna är installerade.

![Ögonblicksbild av dator](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna

1. I **datorer**, Contoso väljer datorn. I den **beroenden** kolumnen Contoso väljer **kräver installation**.
2. I den **identifiera datorer** fönstret Contoso:
    - Laddar ned Microsoft Monitoring Agent (MMA) och Beroendeagenten för varje Windows VM.
    - Laddar ned MMA och Beroendeagenten för varje virtuell Linux-dator.
3. Contoso kopierar arbetsytans ID och nyckel. Contoso behöver arbetsyte-ID och nyckel när du MMA installeras.

    ![Hämning av agent](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Installera agenter på virtuella Windows-datorer

Contoso kör installationen på varje virtuell dator.

#### <a name="install-the-mma-on-windows-vms"></a>Installera MMA på virtuella Windows-datorer

1. Contoso dubbelklickar du på den hämta agenten.
2. I **målmapp**, Contoso behåller standardinstallationsmappen och väljer sedan **nästa**.
3. I **installationsalternativ för Agent**, Contoso väljer **Anslut agenten till Azure Log Analytics** > **nästa**.

    ![Microsoft Monitoring agentinstallation - installationsalternativ för Agent](./media/contoso-migration-assessment/mma-install.png)

4. I **Azure Log Analytics**, Contoso klistrar in arbetsytans ID och nyckel som den kopierade från portalen.

    ![Microsoft Monitoring agentinstallation - Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. I **klar att installera**, installerar du MMA för Contoso.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Installera beroendeagenten på Windows virtuella datorer

1. Contoso dubbelklickar du på den hämtade Beroendeagenten.
2. Contoso accepterar licensvillkoren och väntar på att installationen ska slutföras.

    ![Beroendeagent-konfigurera - installation](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Installera agenter på virtuella Linux-datorer

Contoso kör installationen på varje virtuell dator.

#### <a name="install-the-mma-on-linux-vms"></a>Installera MMA på virtuella Linux-datorer

1. Contoso installeras Python ctypes-bibliotek på varje virtuell dator med hjälp av följande kommando:

    `sudo apt-get install python-ctypeslib`
2. Contoso måste köra kommandot för att installera MMA-agenten som rot. Om du vill bli rot, Contoso kör du följande kommando och anger sedan rotlösenordet:

    `sudo -i`
3. Contoso installerar MMA:
    - Contoso anger arbetsyte-ID och nyckel i kommandot.
    - Kommandon är för 64-bitars.
    - Arbetsyte-ID och den primära nyckeln finns i Log Analytics-arbetsyta i Azure-portalen. Välj **inställningar**, och välj sedan den **anslutna källor** fliken.
    - Kör följande kommandon för att ladda ned Log Analytics-agenten, validera kontrollsumman och installera och integrera agenten:

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Installera Beroendeagenten på virtuella Linux-datorer

När du MMA har installerats, installerar Contoso Beroendeagenten på den virtuella Linux-datorer:

1. Beroendeagenten installeras på Linux-datorer med hjälp av InstallDependencyAgent-Linux64.bin, ett kommandoskript som har en självextraherande binär. Contoso körs filen genom att använda sh eller det lägger till körbehörighet till själva filen.

2. Contoso installerar Beroendeagenten för Linux som rot:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Steg 6: Köra och analysera VM-utvärderingen

Contoso kan nu kontrollera datorberoenden och skapa en grupp. Sedan körs utvärdering för gruppen.

### <a name="verify-dependencies-and-create-a-group"></a>Kontrollera beroenden och skapa en grupp

1. För att avgöra vilka datorer att analysera, Contoso väljer **Visa beroenden**.

    ![Azure Migrate - visa datorberoenden](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. För SQLVM visar beroendekartan följande information:

    - Processgrupper eller processer som har aktiva nätverksanslutningar som körs på SQLVM under den angivna tidsperioden (en timme, som standard).
    - Inkommande (klient) och utgående (server) TCP-anslutningar till och från alla beroende datorer.
    - Beroende datorer som har Azure Migrate-agenter installerade visas som separata rutor.
    - Datorer som saknar agenter installerade visar port och IP-adressinformation.

3. För datorer som har agenten installerad (WEBVM), väljer Contoso datorrutan för att visa mer information. Informationen omfattar FQDN, operativsystem och MAC-adress.

    ![Azure Migrate - visa gruppberoenden](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Contoso väljer de virtuella datorerna ska lägga till i gruppen (SQLVM och WEBVM). Contoso använder Ctrl + klicka för att välja flera virtuella datorer.
5. Contoso väljer **Skapa grupp**, och anger sedan ett namn (**smarthotelapp**).

    > [!NOTE]
    > Om du vill visa mer detaljerade beroenden kan du expandera tidsintervallet. Du kan välja en viss tid eller välj start- och slutdatumen.

### <a name="run-an-assessment"></a>Köra en utvärdering

1. I **grupper**, Contoso öppnas i gruppen (**smarthotelapp**), och sedan väljer **skapa utvärdering**.

    ![Azure Migrate – skapa en utvärdering](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Om du vill visa utvärderingen Contoso väljer **hantera** > **utvärderingar**.

Contoso använder standardutvärderingsinställningarna, men du kan [Anpassa inställningar för](how-to-modify-assessment.md).

### <a name="analyze-the-vm-assessment"></a>Analysera VM-utvärderingen

En Azure Migrate-utvärdering innehåller information om kompatibiliteten för den lokala med Azure, föreslås rätt storlek för virtuell dator i Azure samt de beräknade månadskostnader för Azure.

![Azure Migrate - utvärderingsrapport](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

![Azure Migrate - visa utvärdering](./media/contoso-migration-assessment/assessment-display.png)

En utvärdering har ett säkerhetsomdöme i intervallet 1 stjärna till 5 stjärnor (1 stjärna är lägst och 5 stjärnor är högst).
- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Omdömet hjälper dig att beräkna tillförlitligheten för storleksrekommendationer som tillhandahålls av Azure Migrate.
- Säkerhetsomdömet är användbart när du gör *prestandabaserade storleksändringar*. Azure Migrate kanske inte har tillräckligt många datapunkter för användningsbaserad storleksändring. För *som lokalt* storlek, säkerhetsomdömet är alltid 5 stjärnor eftersom Azure Migrate har tillgång till alla datapunkter som behövs för att ändra storlek på den virtuella datorn.
- Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen:

   Tillgängligheten av datapunkter | Säkerhetsomdöme
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

#### <a name="verify-azure-readiness"></a>Verifiera Azure-beredskap

![Azure Migrate - utvärderingsberedskap](./media/contoso-migration-assessment/azure-readiness.png)  

Utvärderingsrapporten visar den information som sammanfattas i tabellen. Om du vill visa prestandabaserad storleksändring behöver Azure Migrate följande information. Om informationen inte kan samlas in kan kanske storleksutvärderingen inte korrekt.

- Användningsdata för CPU och minne.
- Lästa/skrivna IOPS och dataflöden för varje disk som är ansluten till den virtuella datorn.
- Inkommande och utgående information för varje nätverkskort som är anslutet till den virtuella datorn.

Inställning | Uppgift | Information
--- | --- | ---
**Azure-beredskap för virtuell dator** | Anger om den virtuella datorn är klar för migrering. | Möjliga tillstånd:</br><br/>- Redo för Azure<br/><br/>- Redo på vissa villkor <br/><br/>- Ej redo för Azure<br/><br/>- Beredskap okänd<br/><br/> Om en virtuell dator är inte redo visar Azure Migrate några reparationssteg.
**Storlek på Azure-VM** | Azure Migrate ger en rekommendation för Azure VM-storlek för klara virtuella datorer. | Storleksrekommendationerna beror på utvärderingsegenskaperna:<br/><br/>- Om du använde prestandabaserad storleksändring tas de virtuella datorernas prestandahistorik i beaktande.<br/><br/>-Om du använde *som lokalt*, storlek baseras på lokala virtuella datorstorlek och användningsdata inte används.
**Föreslaget verktyg** | Eftersom Azure datorer kör agenterna, tittar Azure Migrate på processer som körs på datorn. Den identifierar om datorn är en databasdator.
**Information om virtuell dator** | Rapporten visar inställningar för lokal virtuell dator, inklusive operativsystem, starttyp och disk och lagringsinformation.

#### <a name="review-monthly-cost-estimates"></a>Granska uppskattad månadskostnad

Den här vyn visar den totala kostnaden för beräkning och lagring för att köra de virtuella datorerna i Azure. Den visar även information för varje dator.

![Azure Migrate - Azure-kostnader](./media/contoso-migration-assessment/azure-costs.png)

- Kostnadsuppskattningarna beräknas med hjälp av storleksrekommendationer för en dator.
- Uppskattade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen.

## <a name="clean-up-after-assessment"></a>Rensa upp efter utvärdering

- När utvärderingen är klar, behåller Contoso Azure Migrate-installation för att använda i framtida utvärderingar.
- Contoso inaktiverar VMware VM. Contoso använder det igen när det utvärderar ytterligare virtuella datorer.
- Contoso håller den **Contoso migrering** projekt i Azure. Projektet för närvarande har distribuerats i den **ContosoFailoverRG** resursgrupp i regionen östra USA Azure.
-  Den Virtuella insamlardatorn har en licens för 180 dagar långa. Om den här gränsen upphör att gälla måste Contoso att hämta insamlaren och konfigurera den igen.

## <a name="conclusion"></a>Sammanfattning

I det här scenariot utvärderar Contoso SmartHotel360 app databasen med hjälp av verktyget för utvärdering av migrering av Data. Det utvärderar lokala virtuella datorer med hjälp av Azure Migrate-tjänsten. Contoso granskar utvärderingar för att se till att lokala resurser är klara för migrering till Azure.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien namnkonflikt Contoso dess SmartHotel360-app i Azure med hjälp av en lift and shift-migrering. Contoso migrerar frontend WEBVM för appen med hjälp av Azure Site Recovery. Den migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av Database Migration Service. [Kom igång](contoso-migration-rehost-vm-sql-managed-instance.md) med den här distributionen.
