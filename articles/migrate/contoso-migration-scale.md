---
title: Contoso – skala en migrering till Azure | Microsoft Docs
description: Lär dig hur Contoso hanterar en skalade migrering till Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: c36572230387ffc33a46913dbcc1259ea65f84f5
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104241"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso – skala en migrering till Azure

I den här artikeln utför Contoso en migrering i stor skala till Azure. De överväga hur du planerar och utför en migrering av mer än 3000 arbetsbelastningar, 8000 databaser och över 10 000 virtuella datorer.


Den här artikeln är i en serie av artiklar som dokumenterar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrund och planeringsinformation och scenarier som illustrerar hur du ställer in en infrastruktur för migrering, bedöma lokala resurser för migrering och köra olika typer av migreringar. Scenarier växer i komplexitet. Vi lägger till artiklar i serien med tiden.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: Översikt över](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt.
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt   
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar appen, med hjälp av Site Recovery migrera app-datorer och Database Migration Service för att migrera app-databasen till en SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Ange ny värd för en app för Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso Slutför en lift and shift-migrering av dess osTicket app i Linux på Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 8: Ange ny värd för en app för Linux på Azure virtuella datorer och Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar dess osTicket Linux-app till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar app-databas till Azure Database för MySQL med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app i en Azure webbapp och Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar dess SmartHotel360-app till en Azure webbapp och migrerar app-databasen till en Azure SQL Server-instans med Database Migration Assistant. | Tillgängligt    
[Artikel 10: Omstrukturera en Linux-app i en Azure-webbapp och en Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera platser. Webbappen är integrerad med GitHub för kontinuerlig leverans. Den migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera Team Foundation Server på Azure DevOps-tjänsterna](contoso-migration-tfs-vsts.md) | Contoso migrerar dess lokal Team Foundation Server-distribution till Azure DevOps-tjänsterna i Azure. | Tillgängligt
[Artikel 12: Omforma en app i Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och app-databas med Azure SQL Database. | Tillgängligt    
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt 
Artikel 14: Skala en migrering till Azure | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. | Den här artikeln

## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer, orsakar hög belastning på lokala system och infrastruktur.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och förenkla processer för utvecklare och användare. Affärsbehov IT ska vara snabbt och inte spill tid eller pengar och därför leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**: Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi. Det får inte vara i vägen eller bli en blockerare för företag.
- **Skala**: När verksamheten växer måste till Contoso IT-avdelningen ange system som kommer växa i samma takt.
- **Förbättra kostnadsmodeller**: Contoso vill minska kapital kraven i IT-budget.  Contoso vill använda cloud-funktioner för att skala och minska behovet av dyr maskinvara.
- **Lägre licenskostnaden**: Contoso vill minimera molnkostnader.


## <a name="migration-goals"></a>Migreringsmål

Contoso cloud-teamet har fästs ned mål för den här migreringen. Dessa mål har använts för att avgöra den bästa migreringsmetoden.

**Krav** | **Detaljer**
--- | --- 
**Flytta till Azure snabbt** | Contoso vill börja flytta appar och virtuella datorer till Azure så snabbt som möjligt.
**Kompilera en fullständig inventering** | Contoso vill ha en fullständig inventering av alla appar, databaser och virtuella datorer i organisationen.
**Utvärdera och klassificera appar** | Contoso vill utnyttja molnet fullständigt. Som standard förutsätter Contoso att alla tjänster som ska köras som PaaS. IaaS används där PaaS inte är lämpligt. 
**Träna och flytta till DevOps** | Contoso vill flytta till en DevOps-modell. Contoso tillhandahålla Azure- och DevOps-utbildning och organisera om teams behov. 


När du fästa ned mål och krav, Contoso granskar IT-fotavtrycket och identifierar migreringsprocessen.

## <a name="current-deployment"></a>Aktuell distribution

När du planerar och konfigurerar en [Azure-infrastrukturen](contoso-migration-infrastructure.md) och prova olika proof of concept (POC) migrering kombinationer som beskrivs i tabellen ovan, Contoso är redo att sätta igång med en fullständig migrering till Azure i skala. Här är vad Contoso vill migrera.

**Objekt** | **volym** | **Detaljer**
--- | --- | ---
**Arbetsbelastningar** | Mer än 3 000 appar | Appar körs på virtuella datorer.<br/><br/>  Appar är Windows, SQL-baserade och OSS LAMP.
**Databaser** | Cirka 8,500 | Databaser är SQL Server, MySQL, PostgreSQL.
**Virtuella datorer** | Fler än 10 000 | Virtuella datorer körs på VMware-värdar och hanteras av vCenter-servrar.


## <a name="migration-process"></a>Migreringsprocessen

Nu när Contoso har fäst ned affärsdrivande faktorer och mål för migrering, anger en fyra huvudlinjer för migreringsprocessen:

- **Fas 1 – utvärdera**: Identifiera aktuella tillgångar och ta reda på om de är lämplig för migrering till Azure.
- **Fas 2 – migrera**: Flytta tillgångarna till Azure. Hur flyttas de appar och objekt till Azure som väljs beror på appen och vad de vill uppnå.
- **Fas 3 – optimera**: När du flyttar resurser till Azure måste Contoso förbättra och förenkla dem för maximal prestanda och effektivitet.
- **Fas 4 – skydda och hantera**: Med allt på plats använder Contoso nu Azure säkerhet och hantering av resurser och tjänster att styra, skydda och övervaka dess molnappar i Azure.


Faserna är inte seriell i organisationen. Varje typ av Contosos migreringsprojekt är tillgänglig med en annan del av processen bedömning och migrering. Optimering, säkerhet och hantering kommer att pågående över tid.


## <a name="phase-1-assess"></a>Fas 1: Utvärdera

Contoso startar processen med att identifiera och utvärdera lokala appar, data och infrastruktur. Här är Contoso göra:

- Contoso behöver identifiera appar, mappar beroenden bland appar och fatta beslut om migreringsordning och prioritet.
- När Contoso utvärderar, kommer den bygga ut en grundlig inventering över appar och resurser. Tillsammans med det nya lagret, Contoso använder och uppdatera befintliga Configuration Management Database (CMDB) och Tjänstkatalogen.
    - CMDB innehåller tekniska konfigurationer för Contoso-appar.
    - Tjänstkatalogen dokument Driftinformation för appar, inklusive tillhörande affärspartners och serviceavtal (SLA)

### <a name="discover-apps"></a>Identifiera appar

Contoso körs tusentals appar på många olika servrar. Förutom CMDB och Tjänstkatalogen kan behöver Contoso verktyg för identifiering och utvärdering. 

- Verktygen måste tillhandahålla en metod som kan mata in utvärderingsdata i migreringsprocessen.
- Bedömningsverktyg måste ange data som hjälper dig att bygga upp en intelligent förteckning över Contosos fysiska och virtuella resurser. Data bör ta profilinformation och prestandamått.
- När identifieringen är klar, bör Contoso ha en fullständig inventering av tillgångar och metadata som är kopplade till standardrisknivåer. Det här lagret används för att definiera migreringsplanen.

### <a name="identify-classifications"></a>Identifiera klassificeringar

Contoso identifierar några vanliga kategorier för att klassificera tillgångar i lagret. Dessa klassificeringar är kritiska för Contosos beslutsfattande för migrering. Lista över problemklassificering hjälper dig att fastställa prioriteringar för migrering och identifiera komplexa problem.

**Kategori** | **Tilldelade värdet** | **Detaljer**
--- | --- | ---
Affärsgrupp | Lista med gruppnamn för företag | Vilken grupp ansvarar för inventeringsobjektet?
POC-kandidat | J/N | Appen användas som en POC eller en tidig användare för migreringen till molnet?
Sig tekniska skulder | Ingen/vissa/allvarliga | Är inventeringsobjektet körs eller med hjälp av en produkt stöd, plattformar eller operativsystem?
Brandväggen effekter | J/N | Appen kommunicerar med Internet/utanför trafik?  Integreras den med en brandvägg?
Säkerhetsproblem | J/N | Det finns kända säkerhetsproblem med appen?  Använder appen okrypterade data eller inaktuella plattformar?


### <a name="discover-app-dependencies"></a>Identifiera appberoenden

Som en del av processen för bedömning behöver Contoso identifiera där apparna körs och ta reda på beroenden och anslutningar mellan app-servrar. Contoso mappar miljön i steg.

1. Som ett första steg identifierar Contoso hur servrar och datorer mappas till enskilda appar, nätverksplatser och grupper.
2. Med den här informationen kan Contoso tydligt identifiera appar som har några beroenden och är därför lämplig för en snabbmigrering.
3. Contoso kan använda mappning för att hjälpa dem att identifiera mer komplexa beroenden och kommunikationen mellan app-servrar. Contoso kan sedan gruppera dessa servrar logiskt för att representera appar och planera en migreringsstrategi för baserat på dessa grupper.


Med mappning slutförd, Contoso se till att alla appkomponenter som identifieras och redovisas när du skapar migreringsplanen. 

![Beroendemappning](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>Utvärdera appar

Som det sista steget i processen för identifiering och utvärdering av kan Contoso utvärdera utvärdering och mappning resultat för att ta reda på hur du migrerar varje app i Tjänstkatalogen. 

Om du vill samla in den här utvärderingsprocessen, de lägger till några ytterligare klassificeringar i lagret.

**Kategori** | **Tilldelade värdet** | **Detaljer**
--- | --- | ---
Affärsgrupp | Lista med gruppnamn för företag | Vilken grupp ansvarar för inventeringsobjektet?
POC-kandidat | J/N | Appen användas som en POC eller en tidig användare för migreringen till molnet?
Sig tekniska skulder | Ingen/vissa/allvarliga | Är inventeringsobjektet körs eller med hjälp av en produkt stöd, plattformar eller operativsystem?
Brandväggen effekter | J/N | Appen kommunicerar med Internet/utanför trafik?  Integreras den med en brandvägg?
Säkerhetsproblem | J/N | Det finns kända säkerhetsproblem med appen?  Använder appen okrypterade data eller inaktuella plattformar?
Migreringsstrategi | Rehost/omstrukturera/Omforma/återskapning | Vilken typ av migreringen behövs för appen? Hur ska appen distribueras i Azure? [Läs mer](contoso-migration-overview.md#migration-strategies).
Tekniska komplexiteten | 1-5 | Hur även komplexa är migreringen? Det här värdet ska vara definierat Contoso DevOps och relevanta partner.
Allvarlighetsgrad för företag | 1-5 | Hur viktigt är appen för företaget? Till exempel kan en liten arbetsgrupp app tilldelas ett resultat på en, medan en kritisk app som används i organisationen kan komma att tilldelas ett resultat på fem. Det här resultatet påverkar prioritetsnivå migrering.
Prioritet för migrering | 1/2/3 | Vilka migrering prioriteten för appen?
Risken för migrering  | 1-5 | Vad är risknivå för att migrera appen? Det här värdet ska vara överenskomna Contoso DevOps och relevanta partner.




### <a name="figure-out-costs"></a>Ta reda på kostnader

Om du vill ta reda på kostnaderna och potentiella besparingar med Azure-migrering, Contoso kan använda den [Total ägandekostnad (TCO) Kalkylatorn](https://azure.microsoft.com/pricing/tco/calculator/) att beräkna och jämföra TCO för Azure till en jämförbar lokal distribution.

### <a name="identify-assessment-tools"></a>Identifiera bedömningsverktyg

Contoso avgör vilket verktyg som ska användas för identifiering, utvärdering och att skapa inventeringen. Contoso identifierar en blandning av Azure-verktyg och tjänster, inbyggd app-verktyg och skript och verktyg för partner. Contoso vill framför allt, hur Azure Migrate kan användas för att utvärdera i stor skala.


#### <a name="azure-migrate"></a>Azure Migrate


Tjänsten Azure Migrate hjälper dig att identifiera och utvärdera lokala virtuella VMware-datorer, som förberedelse för migrering till Azure. Här är vad Azure Migrate gör:

1. Identifiera: Upptäck lokala virtuella VMware-datorer.
    - Azure Migrate stöder identifiering från flera vCenter-servrar (serially) och kan köra identifieringar i separata Azure Migrate-projekt.
    - Azure Migrate utför identifiering på sätt på en VMware-VM som kör Migrate Collector. Samma insamlaren kan identifiera virtuella datorer på olika vCenter-servrar och skicka data till olika projekt.
1. Utvärdera beredskap: Bedöma om lokala datorer är lämpliga att köra i Azure. Utvärdering innehåller:
    - Storleksrekommendationer: Få storleksrekommendationer för virtuella Azure-datorer baserat på prestandahistoriken för lokala virtuella datorer.
    - Uppskattad månatlig kostnad: Få uppskattade kostnader för att köra lokala virtuella datorer i Azure.
2. Identifiera beroenden:  Visualisera beroenden för lokala datorer, skapa grupper för optimal datorer för bedömning och migrering.


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>Migrera i rätt skala

Contoso behöver använda Azure Migrate korrekt ge skalan för den här migreringen. 

- Contoso kommer att göra en app med app-utvärdering med Azure Migrate. Detta säkerställer att Azure Migrate returnerar aktuella data till Azure-portalen.
- Contoso administratörer Läs mer om [distribuerar Azure Migrate i stor skala](how-to-scale-assessment.md)
- Contoso kommentarer om gränserna för Azure Migrate sammanfattas i tabellen nedan.


**Åtgärd** | **Gränsen**
--- | ---
Skapa Azure Migrate-projekt | 1 500 virtuella datorer
Identifiering | 1 500 virtuella datorer
Utvärdering | 1 500 virtuella datorer

Contoso använder Azure Migrate på följande sätt:

- I vCenter kommer Contoso ordna virtuella datorer i mappar. Detta gör det lättare för dem att fokusera eftersom de kör en utvärdering mot virtuella datorer i en viss mapp.
- Azure Migrate använder Tjänstkarta för Azure för att utvärdera beroenden mellan datorer. Detta kräver att agenterna ska installeras på virtuella datorer för att utvärdera. 
    - Contoso använder automatiserade skript för att installera nödvändiga Windows- eller Linux-agenterna.
    - Med hjälp av skript, kan Contoso skicka installationen till virtuella datorer i en vCenter-mapp.


#### <a name="database-tools"></a>Databasverktyg

Utöver Azure Migrate fokuserar Contoso på med hjälp av verktyg för utvärdering av databasen. Verktyg som de [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) hjälper dig att utvärdera SQL Server-databaser för migrering.

Data Migration Assistant (DMA) kan Contoso att ta reda på om lokala databaser är kompatibla med en rad Azure databaslösningar, till exempel Azure SQL Database, SQL Server som körs på en virtuell Azure IaaS-dator och Azure SQL Managed Instance. 

Förutom DMS har Contoso vissa andra skript som de använder för att identifiera och dokumentera SQL Server-databaser. Dessa finns i GitHub-lagringsplatsen.

#### <a name="partner-tools"></a>Verktyg för partner

Det finns flera andra partner-verktyg som kan hjälpa att Contoso utvärdera den lokala miljön för migrering till Azure. [Läs mer](https://azure.microsoft.com/migration/partners/) om Azure-migreringspartner.  

## <a name="phase-2-migrate"></a>Fas 2: Migrera

Fullständig Contoso måste identifiera verktyg för att flytta sina appar, data och infrastruktur till Azure med sina utvärdering. 




### <a name="migration-strategies"></a>Migreringsstrategier

Det finns fyra bred migreringsstrategier som Contoso kan ha i åtanke. 

**Strategi** | **Detaljer** | **Användning**
--- | --- | ---
**Ange ny värd**  | Kallas ofta ”lift and shift”-migrering, är det här ett Kodlösa alternativ för att migrera befintliga appar till Azure snabbt.<br/><br/> Migreras som en app-är med fördelarna med molnet, utan risker och kostnader i samband med ändringar i koden. | Contoso kan rehost mindre strategiska appar kräver inga kodändringar alls.
**Omstrukturera** |  Kallas även ”ompaketering”, den här strategin kräver minimal kod eller ändringar i konfigurationen måste du ansluta appen till Azure PaaS och bättre utnyttja fördelarna med molnet. | Contoso kan omstrukturera strategiska appar för att behålla samma grundläggande funktioner, men flytta dem för att köras på en Azure-plattformen, till exempel Azure App Services.<br/><br/> Detta kräver minsta kodändringar.<br/><br/> Å andra sidan har Contoso att underhålla en plattform för virtuell dator eftersom detta inte hanteras av Microsoft.
**Omforma** | Den här strategin ändrar eller utökar en app kodbas att optimera app-arkitektur för funktioner och skala.<br/><br/> Den moderniserar en app i en flexibel, skalbar, oberoende distribuerbar arkitektur.<br/><br/> Azure-tjänster kan skynda på processen, skala program med hög exakthet och hantera appar enkelt.
**Återskapa** | Den här strategin återskapar en app från grunden med hjälp av molnbaserade tekniker.<br/><br/> Azure-plattformen som en tjänst (PaaS) innehåller en fullständig miljö för utveckling och distribution i molnet. Den eliminerar vissa kostnaderna och besväret med programvarulicenser och eliminerar behovet av en underliggande appinfrastruktur, middleware och andra resurser. | Contoso kan skriva om kritiska appar från grunden, för att dra nytta av tekniker som serverlös dator eller mikrotjänster.<br/><br/> Contoso ska hantera appen och tjänsterna som det utvecklar och Azure hanterar allt annat.


Data måste också övervägas, särskilt i mängden databaser som Contoso har. Contosos standard metod är att använda PaaS-tjänster som Azure SQL Database för att dra full nytta av molnfunktioner. Genom att flytta till en PaaS-tjänst för databaser, behöver Contoso bara lagra data, lämnar den underliggande plattformen till Microsoft.

### <a name="evaluate-migration-tools"></a>Utvärdera Migreringsverktyg

Contoso främst med flera olika Azure-tjänster och verktyg för migrering:

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): Samordnar haveriberedskap och migrerar lokala virtuella datorer till Azure.
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview): Migrerar lokala databaser som SQL Server, MySQL och Oracle till Azure.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery är primär Azure-tjänsten för att samordna haveriberedskap och migrering från i Azure och från lokala platser till Azure.

1. Site Recovery får du och styr replikeringen från lokala platser till Azure.
2. När replikering har ställts in och kör, lokala datorer kan flyttas över till Azure, att slutföra migreringen.

Contoso redan [slutfört ett POC](contoso-migration-rehost-vm.md) att se hur Site Recovery kan hjälpa dem att migrera till molnet.

##### <a name="using-site-recovery-at-scale"></a>Med Site Recovery i stor skala

Contoso planer om hur du kör flera lift and shift-migreringar. För att säkerställa att det fungerar, replikerar Site Recovery batchar med cirka 100 virtuella datorer i taget. Om du vill ta reda på hur detta fungerar, måste Contoso Utför kapacitetsplanering för föreslagna Site Recovery-migrering.

- Contoso behöver samla in information om deras trafikvolymer. Särskilt:
    - Contoso behöver att fastställa förändringstakten för virtuella datorer i företaget vill replikera.
    - Contoso måste också beakta nätverksanslutningen från den lokala platsen till Azure.
- Som svar på kapacitet och krav för värdvolym måste Contoso du allokera tillräckligt mycket bandbredd baserat på den dagliga förändringstakten för data för de nödvändiga virtuella datorerna som uppfyller dess mål för återställningspunkt (RPO).
- Slutligen måste du ta reda på hur många servrar behövs för att köra Site Recovery-komponenter som behövs för distributionen.

###### <a name="gather-on-premises-information"></a>Samla in information om lokala
Contoso kan använda den [Site Recovery Deployment Planner](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) verktyg för att slutföra de här stegen:

- Contoso kan använda verktyget för att profilera virtuella datorer via en fjärranslutning utan en inverkan på produktionsmiljön. På så sätt kan du identifiera bandbredd och lagring kraven för replikering och redundans.
- Contoso kan köra verktyget utan att installera några Site Recovery-komponenter lokalt.
- Verktyget samlar in information om kompatibla och inkompatibla virtuella datorer, diskar per virtuell dator, och dataomsättning per disk. Den identifierar också kraven på nätverksbandbredd och Azure-infrastrukturen behövs för lyckad replikering och redundans.
- Contoso behöver kontrollera som kör verktyget Distributionshanteraren på en Windows Server-datorer som matchar de lägsta kraven för Site Recovery konfigurationsserver. Konfigurationsservern är en Site Recovery-dator som behövs för att kunna replikera lokala virtuella VMware-datorer.


###### <a name="identify-site-recovery-requirements"></a>Identifiera krav för Site Recovery

Förutom de virtuella datorerna replikeras, kräver ett antal komponenter i Site Recovery för VMware-migrering.

**Komponent** | **Detaljer**
--- | ---
**Konfigurationsserver** | Normalt anger en VMware VM med hjälp av en OVF-mall.<br/><br/> Konfigurationsserverkomponenten samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
**Processervern** | Installeras som standard på konfigurationsservern.<br/><br/> Processerverkomponenten tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure storage.<br/><br/> Processervern installerar också Azure Site Recovery-Mobilitetstjänsten på virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala datorer.<br/><br/> Skalade distributioner du behöver ytterligare, fristående processervrar att hantera stora mängder replikeringstrafik.
**Mobilitetstjänsten** | Mobilitetstjänsten-agenten är installerad på varje VMware VM som ska migreras med Site Recovery.  

Contoso behöver ta reda på hur du distribuerar dessa komponenter, utifrån kapacitetsöverväganden.

**Komponent** | **Kapacitetskraven**
--- | ---
**Maximal dagliga förändringstakten** | En enda processerver kan hantera en daglig ändringen Betygsätt upp till 2 TB. Eftersom en virtuell dator kan bara använda en processerver, är maximal dagliga förändringstakten för data som stöds för en replikerad virtuell dator 2 TB.
**Maximalt dataflöde** | Ett Azure storage-standardkonto kan hantera upp till 20 000 begäranden per sekund och indata/utdataåtgärder per sekund (IOPS) över en replikerande virtuella datorn ska vara i den här gränsen. Till exempel om en virtuell dator har 5 diskar, och varje disk genererar 120 IOPS (8K storlek) på den virtuella datorn, blir sedan i Azure per disk-IOPS-gränsen på 500.<br/><br/> Observera att antalet lagringskonton som behövs är lika med total källdatorn IOPS, dividerat med 20 000. En replikerad dator kan bara höra till ett enda lagringskonto i Azure.
**Konfigurationsserver** | Baserat på uppskattningen av Contosos för att replikera 100 = 200 virtuella datorer tillsammans, och [krav på konfigurationsservrar](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server), Contoso beräkna är behov en configuration server-dator på följande sätt:<br/><br/> PROCESSOR: 16 virtuella processorer (2 platser * 8 kärnor @ 2,5 GHz)<br/><br/> Minne: 32 GB<br/><br/> Cachedisk: 1 TB<br/><br/> Förändringstakten för data: 1 TB till 2 TB.<br/><br/> Förutom att ändra storlek på krav måste Contoso du se till att konfigurationsservern är optimalt placerade på samma nätverk och LAN-segment som de virtuella datorerna som ska migreras.
**Processervern** | Contoso ska distribuera en fristående dedikerad processerver med möjligheten att replikera 100-200 virtuella datorer:<br/><br/> PROCESSOR: 16 virtuella processorer (2 platser * 8 kärnor @ 2,5 GHz)<br/><br/> Minne: 32 GB<br/><br/> Cachedisk: 1 TB<br/><br/> Förändringstakten för data: 1 TB till 2 TB.<br/><br/> Processervern blir arbetar hårt och därför måste finnas på en ESXi-värd som kan hantera disk-i/o, belastning på nätverket och CPU som krävs för replikering. Contoso betraktar en dedikerad värd för detta ändamål. 
**Nätverk** | Contoso har granskat den aktuella plats-till-plats VPN-infrastrukturen och bestämde sig för att implementera Azure ExpressRoute. Implementeringen är viktigt eftersom det lägre latens och förbättra bandbredd till Contosos primära östra USA 2 Azure-region.<br/><br/> **Övervakning av**: Contoso behöver du vara noga med data som flödar från processervern. Om data overloads nätverksbandbredden Contoso betraktar [begränsning process server bandbredd](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
**Azure Storage** | För migrering, måste du identifiera rätt typ och antal Azure storage-målkonton Contoso.  Site Recovery replikerar VM-data till Azure storage.<br/><br/> Site Recovery kan replikera till standard eller premium (SSD)-lagringskonton.<br/><br/> När du ska välja om storage Contoso måste granska [Lagringsgränser](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage), och ta hänsyn till förväntade tillväxt och ökad användning över tid. Beroende på hastighet och prioritet för migreringar, har Contoso beslutat att använda premium storage-konton.<br/><br/> De skapar och återanvända flera lagringskonton under migreringsprocessen.
Contoso har bestämt dig för att använda hanterade diskar för alla virtuella datorer som distribueras till Azure.  IOPS krävs avgör om diskarna blir Standard (HDD) eller Premium (SSD).<br/>.<br/>


#### <a name="data-migration-service"></a>Data Migration Service 

Azure Database Migration Service (DMS) är en fullständigt hanterad tjänst som möjliggör sömlös migrering från flera databaskällor till azures plattformar, med minimal avbrottstid.

- DMS integrerar funktioner av befintliga verktyg och tjänster. Data Migration Assistant (DMA), används för att generera bedömningsrapporter som hitta rekommendationer om databaskompatibilitet och eventuella nödvändiga ändringar.
- DMS använder en enkel, Migreringsprocess migreringsprocessen med intelligenta utvärdering som hjälper dig att åtgärda eventuella problem med före migreringen.
- DMS kan migrera i rätt skala från flera källor till mål-Azure-databas.
- DMS ger stöd från SQL Server 2005 till SQL Server 2017.

DMS är inte bara Migreringsverktyget för Microsoft-databasen. Hämta en [jämförelse av verktyg och tjänster](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

###### <a name="using-dms-at-scale"></a>Med DMS i stor skala

Contoso använder DMS när du migrerar från SQL Server.  

- När du etablerar DMS måste Contoso så att den har rätt storlek och ange för att optimera prestanda för migrering av data. Contoso väljer alternativet ”affärskritisk nivå med 4 vCores”, vilket medför att tjänsten för att dra nytta av flera virtuella processorer för parallellisering och dataöverföringen snabbare.

    ![DMS skalning](./media/contoso-migration-scale/dms.png)

- En annan skalning möjligheten för Contoso är tillfälligt skala upp målinstansen Azure SQL eller MySQL-databas till Premium-nivån SKU vid migrering av data. Detta minskar database-begränsning som kan påverka aktiviteter för överföring av data när du använder på lägre nivå SKU: er.



##### <a name="using-other-tools"></a>Med andra verktyg

Förutom DMS använda Contoso andra verktyg och tjänster för att identifiera information om Virtuellt.

- De har skript som hjälper med manuell migrering. Dessa är tillgängliga i GitHub-lagringsplatsen.
- Ett antal [partner verktyg](https://azure.microsoft.com/migration/partners/) kan också användas för migrering.


## <a name="phase-3-optimize"></a>Fas 3: Optimera

När Contoso flyttar resurser till Azure, måste de att effektivisera dem för att förbättra prestanda och maximera Avkastningen med kostnadshanteringsverktyg. Givet att Azure är en tjänst där du betalar för användning, är det viktigt för Contoso att förstå hur system utför och se till att de är korrekt storlek.


### <a name="azure-cost-management"></a>Azure kostnadshantering

Om du vill få ut mer av sina molninvestering att Contoso utnyttja det kostnadsfria verktyget för Azure Cost Management.

- Den här licensierade lösningen som skapats av Cloudyn, ett dotterbolag till Microsoft, kan Contoso Hantera utgifter med transparens och Precision.  Det ger verktyg för att övervaka, fördela och trimma molnkostnader.
- Azure Cost Management ger enkel instrumentpanelsrapporter som hjälper med kostnadsallokering och kostnadsrapporter återbetalningar.
- Kostnadshantering kan optimera kostnader genom att identifiera underutnyttjade resurser som Contoso kan sedan hantera och justera.
- [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management.

    
![Kostnadshantering](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>Inbyggda verktyg

Contoso använder också skript för att hitta oanvända resurser.

- Vid stora migrering finns ofta överblivet av data, till exempel virtuella hårddiskar (VHD), vilket leder till någon kostnad, men ger inget värde för företaget. Skript är tillgängliga i GitHub-lagringsplatsen.
- Contoso att utnyttja arbete som utförs av Microsoft azures IT-avdelningen och Överväg att implementera verktyget Azure Resource optimering (ARO).
- Contoso kan distribuera ett Azure Automation-konto med förkonfigurerade runbooks och scheman till prenumerationen och börjar spara pengar. Azure resursoptimering sker automatiskt för en prenumeration efter ett schema har aktiverats eller som optimering på nya resurser.
- Detta ger decentraliserad automatiseringsfunktioner för att minska kostnaderna. Funktionerna omfattar:
    - Automatisk snooze Azure virtuella datorer baserat på låg CPU.
    - Schemalägga virtuella Azure-datorer till viloläge och unsnooze.
    - Schemalägga virtuella Azure-datorer till viloläge eller unsnooze i stigande och fallande ordning med hjälp av Azure taggar.
    - Massimport borttagning av resursgrupp grupperar på begäran.
- Kom igång med verktyget ARO i den här [GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit).

### <a name="partner-tools"></a>Verktyg för partner

Partner verktyg som [Hanu](https://hanu.com/insight/) och [Scalr]( https://www.scalr.com/cost-optimization/) kan utnyttjas.


## <a name="phase-4-secure--manage"></a>Steg 4: Skydda och hantera

Contoso använder Azure-säkerhet och hantering av resurser i det här steget att styra, skydda och övervaka appar i Azure. Dessa resurser att du kan köra en säker och välhanterad miljö när du använder produkter som är tillgängliga i Azure-portalen. Contoso börjar med de här tjänsterna under migreringen och fortsätter med Azures hybridstöd kan använda många av dem för en konsekvent användarupplevelse hybridmolnupplevelse.


### <a name="security"></a>Säkerhet
Contoso förlitar sig på Azure Security Center för enhetlig säkerhetshantering och Avancerat skydd mot hot i olika hybridmolnarbetsbelastningar.

- Security Center tillhandahåller en full insyn i, och kontroll över säkerheten för molnappar i Azure.
- Contoso kan snabbt identifiera och vidta åtgärder som svar på hot och minska exponeringen för säkerhet genom att aktivera adaptivt skydd.

[Läs mer](https://azure.microsoft.com/services/security-center/) om Security Center. 


### <a name="monitoring"></a>Övervakning

Contoso behöver insyn i hälsotillstånd och prestanda för den nyligen migrerade appar, infrastruktur och data som nu kör Azure. Contoso att utnyttja inbyggda Azure-molnet övervakningsverktyg som Azure Monitor, Log Analytics och Application Insights.
 
- Med dessa verktyg kan Contoso enkelt samla in data från källor och få omfattande insikter. Exempel: Contoso mäta användning av CPU-disk och minne för virtuella datorer, visa program- och nätverksberoenden över flera virtuella datorer, och spåra programprestanda.
- Contoso använder dessa molnet övervakningsverktyg för att vidta åtgärder och integrera med lösningar.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) om Azure-övervakning.

### <a name="bcdr"></a>BCDR 

Contoso behöver en affärskontinuitet och haveriberedskap (BCDR) strategi för deras Azure-resurser.

- Azure tillhandahåller [inbyggda funktioner för BCDR](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) att behålla data säkra och appar/tjänster och drift. 
- Contoso vill försäkra att den kan återställa från fel, undvika kostsamma avbrott i verksamheten, Uppfyll efterlevnadsmål och skydda data mot utpressningstrojaner och mänskliga misstag förutom inbyggda funktioner. Om du vill
    - Contoso distribuerar Azure Backup som en kostnadseffektiv lösning för säkerhetskopiering av Azure-resurser. Eftersom det är inbyggda, kan Contoso ställa in säkerhetskopior i molnet med några få enkla steg.
    - Contoso ska konfigurera haveriberedskap för virtuella Azure-datorer med Azure Site Recovery för replikering, redundans och återställning efter fel mellan Azure-regioner som anges.  Detta garanterar att appar som körs på Azure Virtual Machines är fortsatt tillgängliga i en sekundär region Contosos välja om det inträffar avbrottet på den primära regionen. [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

 
## <a name="conclusion"></a>Sammanfattning

I den här artikeln planerat Contoso för en Azure-migrering i stor skala.  De uppdelade migreringsprocessen i fyra faser. Från bedömning och migrering, genom att optimering, säkerhet och hantering när migreringen slutföras. Oftast är det viktigt att planera en migration-projekt som en hela processen, men att migrera system inom en organisation genom att bryta uppsättningar ned till klassificeringar och siffror som passar verksamheten. Genom att utvärdera data och tillämpa klassificeringar, och projekt kan delas upp i en serie av mindre migreringar som kan köras på ett säkert sätt och snabbt.  Summan av dessa mindre migreringar omvandlar snabbt till en stor lyckad migrering till Azure.
