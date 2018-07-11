---
title: Utvärdera lokala arbetsbelastningar för Contoso-migrering till Azure | Microsoft Docs
description: Lär dig hur Contoso utvärderar sina lokala datorer för migrering till Azure med Azure Migration och migreringen av databasen
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: f0bbfd7c63ba187ef535168cbac05c3e09420d43
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917837"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso-migrering: utvärdera lokala arbetsbelastningar för migrering till Azure

Den här artikeln visar hur Contoso utvärderar sin lokala SmartHotel app, som förberedelse för dess migrering till Azure.

Det här dokumentet är den tredje delen i serien med artiklar som dokumenterar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och ett antal scenarier som illustrerar hur du ställer in en infrastruktur för migrering, bedöma lokala resurser för migrering och kör olika typer av migreringar. Scenarier växer i komplexitet och vi ska lägga till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder sina lokala och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla scenarier för migrering av Contoso. | Tillgängligt
Artikel 3: Utvärdera lokala resurser (den här artikeln)  | Visar hur Contoso körs en bedömning av sina lokala tvålagers-SmartHotel-app som körs på VMware. De utvärdera app virtuella datorer med den [Azure Migrate](migrate-overview.md) -tjänsten och SQL Server-databas för appen med den [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
[Artikel 4: Rehost till Azure virtuella datorer och en hanterad SQL-instans](contoso-migration-rehost-vm-sql-managed-instance.md) | Visar hur Contoso migrerar SmartHotel appen till Azure. De migrera appen klientdelens VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och appen databasen med den [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) service för att migrera till en SQL-hanterad instans. | Tillgängligt
[Artikel 5: Byt värd till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrerar SmartHotel appen virtuella datorer med Site Recovery endast.
[Artikel 6: Byt värd till virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera de virtuella datorerna för appen och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso migrerar sina osService Linux-app med Azure Site Recovery.
[Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar osService Linux-app med Site Recovery för VM-migrering och MySQL Workbench för att migrera (till en Azure MySQL-Server-instans. | Tillgängligt


## <a name="overview"></a>Översikt

De överväger att migrera till Azure och företaget Contoso att köra en teknisk och ekonomisk utvärdering för att ta reda på om dess lokala arbetsbelastningar lämpar sig för migrering till molnet. I synnerhet vill i Contoso-teamet utvärdera kompatibiliteten för datorn och databasen för migrering och beräkna kapaciteten och kostnaderna för att köra sina resurser i Azure.

Sammanfattas i följande tabell för att komma igång och bättre förstå teknikerna, de kommer att utvärdera två av sina lokala appar. Observera att de utvärderar för migreringsscenarier som rehost och omstrukturera appar för migrering. Mer information om rehosting och omstrukturering i den [Contoso migreringsöversikt](contoso-migration-overview.md).

**Appens namn** | **Plattform** | **App-nivåer** | **Detaljer**
--- | --- | --- | ---
SmartHotel<br/><br/> Hanterar Contoso resa krav | Som körs på Windows med en SQL Server-databas | Två skikt app med frontend-ASP.NET-webbplats som körs på en virtuell dator (WEBVM) och SQL Server som körs på en annan virtuell dator (SQLVM) | Virtuella datorer är VMware, som körs på en ESXi-värd som hanteras av vCenter-servern.<br/><br/> Exempelappen kan laddas ned från [GitHub](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Contoso Service Desk-app | Körs på Linux/Apache med en MySQL-PHP (LAMP). | Två skikt app med en klientdel PHP-webbplats på en virtuell dator (OSTICKETWEB) och MySQL-databas som körs på en annan virtuell dator (OSTICKETMYSQL) | Appen används av customer service-appar för att spåra problem för internt anställda och externa kunder.<br/><br/> Exempelappen kan laddas ned från [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuella arkitektur


Här är ett diagram som visar den aktuella Contoso lokal infrastrukturen.

![Contoso-arkitektur](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso har en huvuddatacentret finns i den stad i New York i östra USA.
- De har tre ytterligare lokala grenar i USA.
- Huvuddatacentret är ansluten till internet med en fiber metro Ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt ansluten till internet med företag klass anslutningar med IPSec VPN-tunnlar till huvuddatacentret. På så sätt kan hela nätverket ska anslutas permanent och optimerar Internetanslutning.
- Huvuddatacentret är fullständigt virtualiserat med VMware. De har två ESXi 6.5-virtualiseringsvärdar, hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering och DNS-servrar i det interna nätverket.
- Domänkontrollanterna i datacentret körs på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.





## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad företaget vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer och det finns därför trycket på sina lokala system och infrastrukturer.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och effektivisera processer för utvecklare och användare.  Affärsbehov IT ska vara snabbt och inte spill tid eller pengar och därför leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**: Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi.  Det får inte vara i vägen eller bli en blockerare för företag.
- **Skala**: när verksamheten växer har, Contoso IT måste tillhandahålla system som kan växa i samma takt.

## <a name="assessment-goals"></a>Utvärdering av mål

Contoso cloud-teamet har fästs ned mål för sina migreringsutvärdering:

- Efter migreringen bör appar i Azure ha samma prestandafunktioner som idag i sina lokala VMWare-miljön.  Flytta till molnet innebär inte att prestanda är mindre viktigt.
- Contoso behöver förstå kompatibilitet för sina program och databaser med krav för Azure samt deras värdalternativ i Azure.
- Contosos databasadministration värden ska minimeras när appar har flyttats till molnet.  
- Contoso vill förstå inte bara migreringsalternativen, utan även kostnaderna för infrastrukturen när den har flyttats till molnet.

## <a name="assessment-tools"></a>Bedömningsverktyg
Contoso använder Microsoft-verktyg för utvärdering. Dessa verktyg överensstämmer med sina mål och ger dem den information de behöver.

**Teknik** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | De använder DMA för att utvärdera och identifiera kompatibilitetsproblem som kan påverka deras databasfunktioner i Azure. DMA utvärderar funktionsparitet mellan SQL-källor och mål och rekommenderar förbättringar av prestanda och tillförlitlighet. | Det här verktyget kan laddas ned utan kostnad.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso använder den här tjänsten för att utvärdera deras virtuella VMware-datorer. Och utvärderar migreringslämpligheten för datorerna och ger uppskattningar av storlek och kostnad för körning i Azure.  | Det finns för närvarande (2018) utan kostnad för använder den här tjänsten.
[Tjänstkarta](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate använder Tjänstkarta för att visa beroenden mellan datorer som du vill migrera. |  Tjänstkarta ingår i Azure Log Analytics. Den kan för närvarande användas i 180 dagar utan kostnad.

I det här scenariot Contoso laddar ned och kör DMA för att utvärdera den lokala SQL Server-databas för sin reseapp. De använda Azure migrate med beroendemappning för att utvärdera de virtuella datorerna för appen innan du migrerar till Azure.



## <a name="assessment-architecture"></a>Arkitektur för migreringsutvärdering


![Arkitektur för migreringsutvärdering](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso är ett fiktivt namn som representerar en typisk företags-organisation.
- Contoso har ett lokalt datacenter (**contoso-datacenter**), med en lokal domänkontrollanter (CONTOSODC1, CONTOSODC2).
- Virtuella VMware-datorer finns på en VMware ESXI-värdar som kör version 6.5. Värdar: **contosohost1**, **contosohost2**
- VMware-miljön hanteras av vCenter server 6.5 (**venter**, som körs på en virtuell dator.
- Reseapp SmartHotel:
    - Appen är nivåindelad över två virtuella VMware-datorer, **WEBVM** och **SQLVM**.
    - De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com**.
    - De virtuella datorerna som kör Windows Server 2008 R2 Datacenter med SP1.
- VMware-miljön hanteras av vCenter Server (**vcenter.contoso.com**) som körs på en virtuell dator.
- OSTicket Service Desk-app:
    - Appen är nivåindelad över två virtuella datorer, **OSTICKETWEB** och **OSTICKETMYSQL**.
    - De virtuella datorerna körs på Ubuntu Linux Server 16.04-LTS.
    - OSTICKETWEB VM kör Apache-2 och PHP 7.0.
    - OSTICKETMYSQL VM körs MySQL 5.7.22.

![Arkitektur](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Förutsättningar

Här är vad Contoso (och du) som behöver för utvärderingen:

- Ägare eller deltagare åtkomst för Azure-prenumeration eller för en resursgrupp i Azure-prenumeration.
- En lokal vCenter Server som kör version 5.5, 6.0 eller 6.5.
- Ett skrivskyddat kontot i vCenter Server eller behörighet att skapa ett sådant.
- Behörighet att skapa en virtuell dator på vCenter Server med hjälp av en .OVA-mall.
- Minst en ESXi-värd som kör version 5.0 eller senare.
- Minst två lokala virtuella VMware-datorer, varav en kör en SQL Server-databas.
- Behörigheter för att installera Azure Migrate-agenter på varje virtuell dator.
- De virtuella datorerna ska ha direkt Internetanslutning.
        - Du kan begränsa Internetåtkomsten till [de URL:er som krävs](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Om datorer som saknar Internetanslutning den [OMS-gatewayen](../log-analytics/log-analytics-oms-gateway.md) måste installeras på dem.
- FQDN för den virtuella datorn som kör SQL Server-instansen (för utvärdering av databasen).
- Windows-brandväggen som körs på den virtuella SQL Server-datorn ska tillåta externa anslutningar på TCP-port 1433 (standard), så att DMA kan ansluta.


## <a name="assessment-overview"></a>Översikt över utvärdering

Här är hur Contoso ska göra utvärderingen:


> [!div class="checklist"]
> * **Steg 1: Hämta och installera DMA**: Förbered DMA för utvärdering av en lokal SQL Server-databasen.
> * **Steg 2: Utvärdera databasen med DMA**: köra och analysera databasutvärderingen.
> * **Steg 3: Förbereda för VM-utvärdering med Azure Migrate**: konfigurera lokala konton och justera VMware inställningar.
> * **Steg 4: Identifiera lokala virtuella datorer med Azure Migrate**: skapa ett Azure Migrate collector VM. De kör insamlaren för att identifiera virtuella datorer för utvärdering.
> * **Steg 5: Förbereda för beroendeanalys med Azure Migrate**: Installera Azure Migrate-agenter på de virtuella datorerna, så att de kan se beroendemappningen mellan de virtuella datorer.
> * **Steg 6: Utvärdera de virtuella datorerna med Azure Migrate**: Kontrollera beroenden, gruppera de virtuella datorerna och kör utvärderingen. När utvärderingen är klar kan analysera dem vid förberedelserna för migreringen.


## <a name="step-1-download-and-install-the-dma"></a>Steg 1: Hämta och installera DMA

1. Contoso laddar ned DMA från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Assistenten kan installeras på en dator som kan ansluta till SQL-instansen. Du behöver inte köra den på SQL Server-datorn.
    - Det bör inte köras på SQL Server-värddatorn.
2. De kör den hämta installationsfilen (DownloadMigrationAssistant.msi) att starta installationen.
3. På den **Slutför** kan de välja **starta Microsoft Data Migration Assistant** innan du avslutar guiden.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Steg 2: Köra och analysera databasutvärderingen för SmartHotel

Contoso kan nu köra en utvärdering för att analysera sina lokala SQL Server för SmartHotel appen.

1. I Database Migration Assistant, de klickar på **New**väljer **utvärdering**, och ger utvärderingen ett projektnamn - **SmartHotel**.
2. De väljer den **typ av källserver** som **SQL Server på Azure Virtual Machines**.

    ![Välj källa](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      För närvarande stöder DMA inte utvärdering för migrering till en hanterad SQL-instans. Som en lösning kan använder Contoso SQL Server på Azure VM som det angivna målet för utvärderingen.

3. I **Välj målversion**, markeras de SQL Server 2017 som målversionen. Måste de välja detta eftersom det är den version som används av SQL-hanterad instans.
4. De väljer för att identifiera information om kompatibilitet och nya funktioner:
    - **Kompatibilitetsproblem** Observera ändringar som kan bryta migreringen eller som kräver en mindre justering före migreringen. Det håller dig informerad om vilka funktioner som används som har blivit inaktuella. Problemen ordnas efter kompatibilitetsnivå.
    - **Nya funktionsrekommendation** kommentarer om nya funktioner i SQL Server-målplattformen som kan användas för databasen efter migreringen. Dessa är ordnade efter prestanda, säkerhet och lagring.

    ![Välja mål](./media/contoso-migration-assessment/dma-assessment-2.png)

2. I **Anslut till en server**, de anger du namnet på den virtuella datorn som kör databas och autentiseringsuppgifter för att komma åt den. De behöver för att aktivera **lita på servercertifikatet** att kontrollera att de kan få till SQL Server. Sedan de på **Connect**.

    ![Välja mål](./media/contoso-migration-assessment/dma-assessment-3.png)

3. I **Lägg till källa**, de lägger till de vill utvärdera och klickar på databasen **nästa** att starta utvärderingen.
4. Utvärderingen har skapats.

    ![Skapa utvärdering](./media/contoso-migration-assessment/dma-assessment-4.png)

5. I **granskningsresultaten**, de kan se resultaten.


### <a name="analyze-the-database-assessment"></a>Analysera databasutvärderingen

Resultatet visas så fort de är tillgängliga. Om de löser problem med de måste **starta om utvärderingen** köra utvärderingen på nytt.

1. I den **kompatibilitetsproblem** rapportera de söka efter eventuella problem på respektive kompatibilitetsnivå. Kompatibilitetsnivåerna mappar till SQL Server-versioner på följande sätt:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Kompatibilitetsproblem](./media/contoso-migration-assessment/dma-assessment-5.png)

2. I den **Funktionsrekommendationerna** rapportera, Contoso kan visa funktioner för prestanda, säkerhet och lagring som utvärderingen rekommenderar efter migreringen. Olika funktioner rekommenderas, till exempel Minnesintern OLTP och Columnstore, Stretch Database, Always Encrypted, dynamisk Datamaskning och Transparent datakryptering (TDE).

    ![Funktionsrekommendationer](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Vi rekommenderar att Contoso [aktiverar TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) för alla SQL Server-databaser, vilket inte är ännu mer kritiska när databaser finns i molnet. TDE bör endast aktiveras efter migreringen. Om TDE är redan aktiverad, behöver du flytta certifikat eller asymmetriska nyckeln till master-databasen på målservern. [Läs mer](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. De kan exportera utvärdering i JSON- eller CSV-format.

Observera att om du kör en utvärdering i större skala kan du:

- Köra flera utvärderingar samtidigt och visa tillståndet för utvärderingarna genom att öppna den **alla utvärderingar** sidan.
- [Konsolidera utvärderingarna till en SQL Server-databas](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Konsolidera utvärderingarna till en PowerBI-rapport](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Steg 3: Förbereda för VM-utvärdering med Azure Migrate

Contoso måste du skapa ett VMware-konto som Azure Migrate använder för att automatiskt identifiera virtuella datorer för utvärdering, kontrollera behörigheter för att skapa en virtuell dator, Observera portarna som måste du öppna och ange statistik nivån.

### <a name="set-up-a-vmware-account"></a>Konfigurera ett VMware-konto

 VM-identifiering kräver ett skrivskyddat konto i vCenter med följande egenskaper:

- Användartyp: Minst en skrivskyddad användare.
- Behörigheter: Datacenter-objekt –> Sprid till underordnat objekt, roll = skrivskyddad.
- Information: Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.
- Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

### <a name="verify-permissions-to-create-a-vm"></a>Kontrollera behörigheter för att skapa en virtuell dator

Contoso verifiera den har behörighet att skapa en virtuell dator genom att importera en fil i. OVA-formatet. [Läs mer](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Kontrollera portar

Contoso-utvärderingen använder beroendemappning. Den här funktionen kräver en agent installeras på virtuella datorer du vill utvärdera. Agenten måste kunna ansluta till Azure från TCP-port 443 på varje virtuell dator. [Läs mer](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) om anslutningskraven.


### <a name="set-statistics-settings"></a>Ange inställningar för statistik

Innan de börjar distributionen av ange Contoso statistikinställningarna för vCenter-servern på nivå 3. Tänk på följande:

- När du har angett nivån, måste du vänta minst en dag innan du kör utvärderingen. Annars kanske den inte fungerar som förväntat.
- Om nivån är högre än 3 fungerar utvärderingen, men:
    - Prestandadata för diskar och nätverk samlas inte in.
    - För lagring rekommenderar Azure Migrate en standarddisk i Azure, med samma storlek som den lokala disken.
    - För nätverk rekommenderas ett nätverkskort i Azure för varje lokalt nätverkskort.
    - För beräkning tittar Azure Migrate på den virtuella datorns kärnor och minnesstorlek och rekommenderar en virtuell Azure-dator med samma konfiguration. Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.
- [Läs mer](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) om storleksändring med nivå 3.

De att ställa in på följande sätt:

1. De öppna vCenter server-instansen i vSphere-webbklienten.
2. I **hantera** > **inställningar** > **Allmänt**, de klickar på **redigera**.
3. I **statistik**, de anger du statistiknivåinställningen nivåinställningarna **nivå 3**.

    ![Statistiknivå i vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Steg 4: Identifiera virtuella datorer

För att identifiera virtuella datorer, skapar ett Azure Migrate-projekt i Contoso. De kan hämta och konfigurera den Virtuella insamlardatorn och kör insamlaren för att identifiera deras lokala virtuella datorer.

### <a name="create-a-project"></a>Skapa ett projekt

1. I den [Azure-portalen](https://portal.azure.com), de söker efter **Azure Migrate**, och skapa ett projekt (ContosoMigration).
2. De ange ett projektnamn och Azure-prenumeration och skapa en ny Azure resursgrupp, **ContosoFailoverRG**. Tänk på följande:

    - Du kan bara skapa ett Azure Migrate-projekt i regionen USA, västra centrala eller USA, östra.
    - Du kan planera en migrering för valfri målplats.
    - Projektplatsen används bara för att lagra de metadata som samlas in från lokala virtuella datorer.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Azure Migrate skapar en lokal virtuell dator som kallas för insamlarprogram. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till Azure Migrate-tjänsten. Om du vill konfigurera insamlingsprogrammet Contoso laddar ned en. OVA mall och importerar den till den lokala vCenter-servern för att skapa den virtuella datorn.

1. I Azure Migrate-projektet > **komma igång** > **identifiera och utvärdera** > **identifiera datorer**, laddar ned den. OVA mallfilen.
2. De Kopiera projekt-ID och nyckel. Detta behövs för att konfigurera insamlaren.

    ![Hämta .OVA-fil](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Innan du distribuerar den virtuella datorn, Contoso kontrollerar att den. OVA-filen är säker.

1. På datorn där filen hämtas öppna de ett kommandofönster för administratör.
2. De kör följande kommando för att generera en hash för ova-filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha nedanstående inställningar (version 1.0.9.12)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>Skapa insamlingsprogrammet

Contoso kan nu importera den nedladdade filen till vCenter-servern och etablera konfigurationsservern VM.

1. I vSphere-klientkonsolen de klickar på **filen** > **distribuera OVF-mall**.

    ![Distribuera OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF-mall > **källa**, de ange platsen för den. OVA-filen.
3. I **namn och plats**, de ange ett eget namn för den Virtuella insamlardatorn och lagerplatsen där den virtuella datorn kommer att finnas. De kan ange den värd eller ett kluster som insamlingsprogrammet ska köras.
5. I **Storage**, de ange lagringsplatsen, och i **diskformat**och hur de vill etablera lagringen.
7. I **nätverksmappning**, de ange det nätverk som ska ansluta till den Virtuella insamlardatorn. Nätverket måste ha en Internetanslutning för att kunna skicka metadata till Azure.
8. De granskar du inställningarna och välj **slå på strömmen efter distributionen**> **Slutför**. Ett meddelande som bekräftar att det är klart utfärdas när insamlingsprogrammet har skapats.

### <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

Nu kör de insamlaren för att identifiera virtuella datorer. Observera att insamlaren för närvarande endast stöder ”engelska (USA)” som operativsystemspråk och gränssnittsspråk i insamlaren.

1. I vSphere-klienten-konsolen > **öppna konsolen**, de ange språk, tidszon och lösenordsinställningar för den Virtuella insamlardatorn.
2. På skrivbordet, de klickar på den **kör insamlare** genväg.

    ![Genväg till insamlaren](./media/contoso-migration-assessment/collector-shortcut.png)

4. I Azure Migrate Collector > **konfigurera förhandskraven**, de accepterar licensvillkoren och Läs informationen från tredje part.
5. Insamlaren kontrollerar att den virtuella datorn är ansluten till internet, att tiden har synkroniserats och att insamlartjänsten körs (den installeras som standard på den virtuella datorn). Den installerar även VMWare PowerCLI.

    > [!NOTE]
    > Vi förutsätter att den virtuella datorn har direkt åtkomst till Internet, utan proxy.

    ![Kontrollera förutsättningar](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. I **ange vCenter-serverinformationen**de anger namn (FQDN) eller IP-adress för vCenter-servern och skrivskyddade autentiseringsuppgifter används för identifiering.
7. De väljer du en omfattning för identifieringen av virtuella datorer. Insamlaren kan bara identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 500 virtuella datorer.

    ![Ansluta till vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. I **ange migreringsprojekt**, de ange Azure Migrate projekt-ID och nyckel som du kopierade från portalen. Du kan hämta dem igen i projektet **översikt** sidan > **identifiera datorer**.  

    ![Anslut till Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. I **visa insamlingsförloppet** Contoso kan övervaka identifiering och kontrollera att metadata som samlas in från de virtuella datorerna är i omfattningen. Insamlaren visar en ungefärlig identifieringstid.

    ![Insamling pågår](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När insamlingen är klar kontrollerar Contoso att de virtuella datorerna visas i portalen.

1. I Azure Migrate-projektet > **hantera** > **datorer**, de kontrollera att de virtuella datorerna som du vill identifiera visas.

    ![Identifierade datorer](./media/contoso-migration-assessment/discovery-complete.png)

3. Observera att Azure Migrate-agenter inte är installerade på datorerna för närvarande. Contoso behöver installera dem i kunna visa beroenden.

    ![Identifierade datorer](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Steg 5: Förbereda för beroendeanalys

Om du vill visa beroenden mellan virtuella datorer som Contoso vill komma åt de ladda ned och installera agenter på de virtuella datorerna för appen. Contoso sker på alla virtuella datorer för sina appar, både Windows och Linux.

### <a name="take-a-snapshot"></a>Ta en ögonblicksbild

De Behåll en kopia av den virtuella datorn innan du ändrar dem, genom att ta en ögonblicksbild innan agenterna är installerade.

![Ögonblicksbild av dator](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna

1. På den **datorer** kan de väljer du datorn och sedan **kräver installation** i den **beroenden** kolumn.
2. På den **identifiera datorer** sidan som de gör du följande:
    - Ladda ned agent för MMA och beroendeagenter för varje Windows-VM
    - Ladda ned agent för MMA och beroendeagenter för varje Linux VM
3. Nu kopiera de arbetsytans ID och nyckel. De behöver dem när du installerar MMA.

    ![Hämning av agent](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Installera agenter på virtuella Windows-datorer

De köra installationen på varje virtuell dator.

#### <a name="install-the-mma-on-windows-vms"></a>Installera MMA på virtuella Windows-datorer

1. De dubbelklickar du på den hämta agenten.
2. I **målmapp**, de behåller standardinstallationsmappen > **nästa**.
2. I **installationsalternativ för Agent**, de väljer **Anslut agenten till Azure Log Analytics** > **nästa**.

    ![Installation av MMA](./media/contoso-migration-assessment/mma-install.png)

5. I **Azure Log Analytics**, de klistra in i arbetsytans ID och nyckel som du kopierade från portalen.

    ![Installation av MMA](./media/contoso-migration-assessment/mma-install2.png)

6. I **klar att installera**, de kan nu installera MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Installera beroendeagenten på Windows virtuella datorer

1. De dubbelklickar du på den hämta beroendeagenten.
2. De accepterar licensvillkoren och vänta tills installationen är klar.

    ![Beroendeagent](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Installera agenter på virtuella Linux-datorer

De köra installationen på varje virtuell dator.

#### <a name="install-the-mma-on-linux-vms"></a>Installera MMA på virtuella Linux-datorer

1. De installerar python ctypes-bibliotek på varje virtuell dator med hjälp av: **sudo apt-get installera python-ctypeslib**.
2. De bör köra kommandot för att installera MMA-agenten som rot.  Bli rot kör följande kommando och ange rotlösenordet: **sudo -i**.
3. Nu kan de installera MMA-agenten.
    - Infoga din rätt arbetsytans ID och nyckel i kommandot.
    - Kommandon är för 64-bitars.
    - Den **arbetsyte-ID** och **primärnyckel** kan hittas i OMS-portalen > **inställningar**i den **anslutna källor** fliken.
    - Kör följande kommandon för att ladda ned OMS-agenten, verifiera kontrollsumma och installation/publicera agenten.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>Installera beroendeagenten på virtuella Linux-datorer

När du har installerat MMA kan Contoso installera beroendeagenten på den virtuella Linux-datorer.

1. Beroende-agenten är installerad på Linux-datorer med hjälp av InstallDependencyAgent-Linux64.bin, ett kommandoskript med en självextraherande binärfilen. De kan köra filen genom att använda sh eller Lägg till körbehörighet till själva filen.

2. De installerar beroendeagenten för Linux som rot:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Steg 6: Köra och analysera VM-utvärderingen

Contoso kan nu kontrollera datorberoenden och skapa en grupp. De kör utvärdering för gruppen.

### <a name="verify-dependencies-and-create-a-group"></a>Kontrollera beroenden och skapa en grupp


1. För de virtuella datorerna att analysera, de klickar på **Visa beroenden**.

    ![Visa datorberoenden](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. För SQLVM visar beroendekartan följande information:

    - Processgrupper/processer med aktiva nätverksanslutningar som körs på SQLVM under den angivna tidsperioden (en timme som standard)
    - Inkommande (klient) och utgående (server) TCP-anslutningar till och från alla beroende datorer.
    - Beroende datorer med Azure Migrate-agenter installerade visas som separata rutor
    - Datorer utan agenter installerade visar information om port och IP-adress.

3. Datorer med agenten installerad (WEBVM) klickar de du på datorrutan för att visa mer information, inklusive FQDN, operativsystem och MAC-adress.

    ![Visa gruppberoenden](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Nu kan de välja de virtuella datorerna ska lägga till i gruppen (SQLVM och WEBVM).  De kan använda CTRL + klicka om du vill välja flera virtuella datorer.
5. De klickar på **Skapa grupp**, och ange ett namn (smarthotelapp).

> [!NOTE]
    > Om du vill visa mer detaljerade beroenden kan du expandera tidsintervallet. Du kan välja en viss varaktighet eller start- och slutdatum.


### <a name="run-an-assessment"></a>Köra en utvärdering


1. På den **grupper** kan de öppna gruppen (smarthotelapp) och klicka på **skapa utvärdering**.

    ![Skapa en utvärdering](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Utvärderingen visas på sidan **Hantera** > **Utvärderingar**.

Contoso använt standardutvärderingsinställningarna, men du kan anpassa inställningar. [Läs mer](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>Analysera VM-utvärderingen

En Azure Migrate-utvärdering innehåller information om kompatibilitet med den lokala virtuella datorer för Azure, föreslås rätt storlek för virtuell dator i Azure samt de beräknade månadskostnader för Azure.

![Utvärderingsrapport](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

![Visa utvärdering](./media/contoso-migration-assessment/assessment-display.png)

En utvärdering får ett säkerhetsomdöme från 1 stjärna till 5 stjärnor (1 stjärna är lägst och 5 stjärnor är högst).
- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Omdömet hjälper dig att beräkna tillförlitligheten för storleksrekommendationer som tillhandahålls av Azure Migrate.
- Säkerhetsomdömet är användbart när du gör *prestandabaserade storleksändringar* eftersom Azure Migrate inte kanske har tillräckligt många datapunkter för att göra användningsbaserad storleksändring. När *storleksändringar av typen ”som lokalt”* utförs är säkerhetsomdömet alltid 5 stjärnor eftersom Azure Migrate har tillgång till alla datapunkter som behövs för att sätta rätt storlek på den virtuella datorn.
- Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

#### <a name="verify-readiness"></a>Verifiera beredskap

![Utvärderingsberedskap](./media/contoso-migration-assessment/azure-readiness.png)  

Utvärderingsrapporten visar den information som sammanfattas i tabellen. Observera att om du vill visa prestandabaserad storleksändring behöver Azure Migrate följande information. Om den här informationen inte kan samlas in kan storleksutvärderingen bli felaktig.

- Användningsdata för CPU och minne.
- Lästa/skrivna IOPS och dataflöden för varje disk som är ansluten till den virtuella datorn.
- Inkommande och utgående information för varje nätverkskort som är anslutet till den virtuella datorn.


**Inställning** | **Indikation** | **Detaljer**
--- | --- | ---
**Azure-beredskap för virtuell dator** | Anger om den virtuella datorn är klar för migrering | Möjliga tillstånd:</br><br/>- Redo för Azure<br/><br/>- Redo på vissa villkor <br/><br/>- Ej redo för Azure<br/><br/>- Beredskap okänd<br/><br/> Om en virtuell dator inte är redo visar vi några reparationssteg.
**Storlek på Azure-VM** | För klara virtuella datorer rekommenderar vi en Azure VM-storlek. | Storleksrekommendationerna beror på utvärderingsegenskaperna:<br/><br/>- Om du använde prestandabaserad storleksändring tas de virtuella datorernas prestandahistorik i beaktande.<br/><br/>- Om du använde ”Som lokalt” baseras storleksändringen på storleken på de lokala virtuella datorerna och användningsdata använts inte.
**Föreslaget verktyg** | Eftersom agenterna körs på våra datorer tittar Azure Migrate på de processer som körs i datorn och identifierar om datorn är en databasdator eller inte.
**Information om virtuell dator** | Den här rapporten visar inställningar för den lokala virtuella datorn, inklusive operativsystem, starttyp, disk och lagringsinformation.


#### <a name="review-monthly-cost-estimates"></a>Granska uppskattad månadskostnad

Den här vyn visar total beräknings- och lagringskostnad för att köra de virtuella datorerna i Azure, samt information för varje dator.

![Utvärderingsberedskap](./media/contoso-migration-assessment/azure-costs.png)

- Kostnadsuppskattningarna beräknas med hjälp av storleksrekommendationer för en dator.
- Uppskattade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen.


## <a name="clean-up-after-assessment"></a>Rensa upp efter utvärdering

- När utvärderingen är klar behåller Contoso Azure Migration tillämpning för framtida utvärderingar.
- De tar bort virtuell dator med VMware. De kommer starta den igen när de använder ytterligare virtuella datorer.
- De behåller Contoso Migration-projekt i Azure.  Det är för närvarande distribuerat i resursgruppen ContosoFailoverRG i den regionen USA, östra Azure.
-  Den Virtuella insamlardatorn har en 180-dagars utvärderingsversion-licens. Om den här gränsen går ut måste de ladda ned och konfigurera insamlaren igen.


## <a name="conclusion"></a>Sammanfattning

I det här scenariot utvärderat Contoso SmartHotel app databasen med hjälp av DMA-verktyget och de lokala virtuella datorer med hjälp av Azure Migrate-tjänsten. De granskat utvärderingar för att se till att lokala resurser som är klara för migrering till Azure.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i den här serien namnkonflikt Contoso dess SmartHotel app i Azure med en lift and shift-migrering. Contoso migrerar frontend WEBVM för appen med Azure Site Recovery och app-databasen till en Azure SQL Managed Instance, med hjälp av Database Migration Service. [Kom igång](contoso-migration-rehost-vm-sql-managed-instance.md) med den här distributionen.
