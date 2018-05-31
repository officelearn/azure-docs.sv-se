---
title: Utvärdera lokala arbetsbelastningar för migrering till Azure med DMA och Azure Migrate | Microsoft Docs
description: Lär dig hur du förbereder Azure för migrering av lokala datorer med DMA (Data Migration Assistant) och Azure Migrate-tjänsten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0d8ef36e001aaf417b84efaf99a992fd64f01b6f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366349"
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>Scenario 1: Utvärdera lokala arbetsbelastningar för migrering till Azure

Företaget Contoso överväger att migrera till Azure och de vill göra en teknisk och ekonomisk utvärdering för att ta reda på om deras lokala arbetsbelastningar lämpar sig för migrering till molnet. De vill främst utvärdera om datorn och databasen är kompatibla för migrering och beräkna kapaciteten och kostnaderna för att köra resurser i Azure.

För att komma igång och få bättre kunskaper om de inblandade teknikerna utvärderar och migrerar de en liten lokal reseapp. Det är en app på två nivåer, med en webbapp som körs på en virtuell dator och en SQL Server-databas som körs på den andra virtuella datorn. Programmet distribueras i VMware och miljön hanteras av en vCenter Server. De utför utvärderingen med hjälp av DMA (Data migrering Assistant) och tjänsten Azure Migrate. Om du vill testa scenariot med hjälp av det här illustrativa reseprogrammet kan du ladda ned det från [github](https://github.com/Microsoft/SmartHotel360).

**Teknik** | **Beskrivning** | **Kostnad**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA utvärderar och identifierar kompatibilitetsproblem som kan påverka databasfunktioner i Azure. Dessutom utvärderar DMA funktionsparitet mellan SQL Server-källan och -målet, och rekommenderar prestanda- och tillförlitlighetsförbättringar för målmiljön. | Det här verktyget kan laddas ned utan kostnad.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Tjänsten hjälper dig att utvärdera lokala datorer för migrering till Azure. Och utvärderar migreringslämpligheten för datorerna och ger uppskattningar av storlek och kostnad för körning i Azure. För närvarande kan tjänsten Azure Migrate utvärdera lokala virtuella VMware-datorer för migrering till Azure. | För närvarande (april 2018) kan den här tjänsten användas utan kostnad.
[Tjänstkarta](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate använder Tjänstkarta för att visa beroenden mellan datorer som du vill migrera. |  Tjänstkarta ingår i Azure Log Analytics. Den kan för närvarande användas i 180 dagar utan kostnad.

I det här scenariot hämtar vi och kör DMA för att utvärdera den lokala SQL Server-databasen för vår reseapp. Vi använder Azure Migrate med beroendemappning för att utvärdera de virtuella datorerna för appen innan vi migrerar dem till Azure.

> [!NOTE]
> I det här scenariot är utvärderingsmålet för databasen ”SQL Server på en virtuell Azure-dator”. Men i nästa scenario (artikel) kör vi migreringen till en hanterad Azure SQL-instans. Vi använder den här metoden eftersom DMA för närvarande inte stöder utvärdering av migrering till en hanterad Azure SQL-instans.

## <a name="architecture"></a>Arkitektur


![Arkitektur för migreringsutvärdering](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

I det här scenariot:
- Contoso är ett fiktivt namn som representerar en typisk företagsorganisation. Contoso vill utvärdera och migrera sin lokala reseapp som finns på två nivåer.
- Contoso har ett lokalt datacenter (**contoso-datacenter**) med en lokal domänkontrollant ( **contosodc1**).
- Den interna reseappen är nivåindelad över två virtuella datorer, **WEBVM** och **SQLVM**, och finns på VMware ESXi-värden **contosohost1.contoso.com**.
- VMware-miljön hanteras av vCenter Server (**vcenter.contoso.com**) som körs på en virtuell dator.




## <a name="prerequisites"></a>Nödvändiga komponenter

Det här behöver du för att distribuera det här scenariot:

- En lokal vCenter Server som kör version 5.5, 6.0 eller 6.5.
- Ett skrivskyddat kontot i vCenter Server eller behörighet att skapa ett sådant.
- Behörighet att skapa en virtuell dator på vCenter Server med hjälp av en .OVA-mall.
- Minst en ESXi-värd som kör version 5.0 eller senare.
- Minst två lokala virtuella VMware-datorer, varav en kör en SQL Server-databas.
- Du ska ha behörighet att installera Azure Migrate-agenter på varje virtuell dator.
- De virtuella datorerna ska ha direkt Internetanslutning.
        - Du kan begränsa Internetåtkomsten till [de URL:er som krävs](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - Om du har datorer utan Internetanslutning måste du ladda ned och installera [OMS-gatewayen](../log-analytics/log-analytics-oms-gateway.md) på dem.
- FQDN för den virtuella datorn som kör SQL Server-instansen (för utvärdering av databasen).
- Windows-brandväggen som körs på den virtuella SQL Server-datorn ska tillåta externa anslutningar på TCP-port 1433 (standard), så att DMA kan ansluta.


## <a name="scenario-overview"></a>Scenarioöversikt

Det här ska vi göra:


> [!div class="checklist"]
> * **Steg 1: Förbereda Azure**. Allt vi behöver är en Azure-prenumeration.
> * **Steg 2: Hämta och installera DMA**: Förbered DMA för utvärdering av den lokala SQL Server-databasen.
> * **Steg 3: Utvärdera databasen**: Kör och analysera databasutvärderingen.
> * **Steg 4: Förbereda för VM-utvärdering med Azure Migrate**: Konfigurera lokala konton och justera VMware-inställningarna.
> * **Steg 5: Identifiera lokala virtuella datorer**: Skapa en virtuell dator för Azure Migrate Collector. Kör sedan Collector för att identifiera virtuella datorer för utvärdering.
> * **Steg 6: Förbereda för beroendeanalys**: Installera Azure Migrate-agenter på de virtuella datorerna, så att vi kan se beroendemappningen mellan de virtuella datorerna.
> * **Steg 7: Utvärdera de virtuella datorerna**: Kontrollera beroenden, gruppera de virtuella datorerna och kör utvärderingen. När utvärderingen är klar kan du analysera den för att förbereda inför migreringen.


## <a name="step-1-prepare-azure"></a>Steg 1: Förbereda Azure

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

- Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.
- Om du använder en befintlig prenumeration och inte är administratör måste du be administratören tilldela dig ägar- eller deltagarbehörighet för prenumerationen, eller för resursgruppen du använder i det här scenariot.


## <a name="step-2-download-and-install-the-dma"></a>Steg 2: Hämta och installera DMA

1. Ladda ned DMA från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Du kan installera assistenten på valfri dator som kan ansluta till SQL-instansen. Du behöver inte köra den på SQL Server-datorn.
    - Du bör inte köra den på SQL Server-värddatorn.
2. Dubbelklicka på den hämtade installationsfilen (DownloadMigrationAssistant.msi) för att starta installationen.
3. På sidan **Slutför** kontrollerar du att **Starta Microsoft Data Migration Assistant** är markerat och klickar på **Slutför**.

## <a name="step-3-run-and-analyze-the-database-assessment"></a>Steg 3: Köra och analysera databasutvärderingen

Kör en utvärdering för att analysera din SQL Server-instans mot ett angivet mål.

1. I **Ny** väljer du **Utvärdering** och ger utvärderingen ett projektnamn.
2. I **Typ av källserver** väljer du **SQL Server**. I **Målservertyp** väljer du **SQL Server på Azure Virtual Machines**.

    ![Välj källa](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      För närvarande stöder DMA inte utvärdering för migrering till en hanterad SQL-instans. Som en tillfällig lösning använder vi SQL Server på Azure VM som mål för utvärderingen.

1.  I **Välj målversion** anger du målversionen av SQL Server som du vill köra i Azure, och vad du vill identifiera i utvärderingen:
    - **Kompatibilitetsproblem** anger ändringar som kan bryta migreringen eller som kräver en mindre justering före migreringen. Du får också information om vilka funktioner du använder som har blivit inaktuella. Problemen ordnas efter kompatibilitetsnivå.
    - **Nya funktionsrekommendation** informerar om nya funktioner i SQL Server-målplattformen som kan användas för din databas efter migreringen. Dessa är ordnade efter prestanda, säkerhet och lagring.

    ![Välja mål](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. I **Anslut till en server** anger du namnet på den dator som kör SQL Server-instansen, autentiseringstypen och anslutningsinformation. Klicka sedan på **Anslut**.

    ![Välja mål](./media/migrate-scenarios-assessment/dma-assessment-3.png)

3. I **Lägg till källa** markerar den databas som du vill utvärdera och klickar på **Lägg till**.
4. En utvärdering med det namn du angav skapas.

    ![Skapa utvärdering](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  Välj **Nästa** att starta utvärderingen.
6. I **Granska resultat** visas resultaten för de utvärderingstester som du har aktiverat.


### <a name="analyze-the-database-assessment"></a>Analysera databasutvärderingen

Resultatet visas i assistenten när de är tillgängliga.

1. I rapporten **Kompatibilitetsproblem** kontrollerar du om det finns problem för databasen på respektive kompatibilitetsnivå, och i så fall hur du åtgärdar dem. Kompatibilitetsnivåerna mappar till SQL Server-versioner på följande sätt:
    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Kompatibilitetsproblem](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. I rapporten **Funktionsrekommendationer** visas prestanda-, säkerhets- och lagringsfunktioner som utvärderingen rekommenderar att bör du konfigurera efter migreringen. En rad olika funktioner rekommenderas, till exempel minnesintern OLTP och Columnstore, Stretch Database, Always Encrypted, dynamisk datamaskering och transparent datakryptering.

    ![Funktionsrekommendationer](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Om du har åtgärdat problem klickar du på **Starta om utvärderingen** för att köra den på nytt.
4. Klicka på **Exportera rapport** för att hämta utvärderingsrapporten i JSON- eller CSV-format.

Om du kör en utvärdering i större skala:

- Du kan köra flera utvärderingar samtidigt och se tillståndet för utvärderingarna genom att öppna sidan **All Assessments** (Alla utvärderingar).
- Du kan [konsolidera utvärderingarna till en SQL Server-databas](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Du kan [konsolidera utvärderingarna till en PowerBI-rapport](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>Steg 4: Förbereda för VM-utvärdering med Azure Migrate

Skapa ett VMware-konto som ska användas i Azure Migrate för att automatiskt identifiera virtuella datorer för utvärdering, kontrollera att du har behörighet att skapa en virtuell dator, observera portarna som måste vara öppna och ange nivå för statistikinställningar.

### <a name="set-up-a-vmware-account"></a>Konfigurera ett VMware-konto

 Du behöver ett skrivskyddat konto i vCenter. Skapa ett VMware-konto med följande egenskaper (om du inte redan har ett):

- Användartyp: Minst en skrivskyddad användare.
- Behörigheter: Datacenter-objekt –> Sprid till underordnat objekt, roll = skrivskyddad.
- Information: Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.
- Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

### <a name="verify-permissions-to-create-a-vm"></a>Kontrollera behörigheter för att skapa en virtuell dator

Kontrollera att du har behörighet att skapa en virtuell dator genom att importera en fil i .OVA-format. [Läs mer](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Kontrollera portar

I det här scenariot ska vi konfigurera beroendemappning. Den här funktionen kräver att en agent installeras på de virtuella datorerna som du utvärderar. Agenten måste kunna ansluta till Azure från TCP-port 443 på varje virtuell dator. [Läs mer](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) om anslutningskraven.


### <a name="set-statistics-settings"></a>Ange inställningar för statistik

Innan du startar distributionen ska statistikinställningarna för vCenter Server vara inställda på nivå 3. Tänk på följande:
- När du har angett nivå måste du vänta i minst en dag innan du kör utvärderingen. Annars kanske den inte fungerar som förväntat.
- Om nivån är högre än 3 fungerar utvärderingen, men:
    - Prestandadata för diskar och nätverk samlas inte in.
    - För lagring rekommenderar Azure Migrate en standarddisk i Azure, med samma storlek som den lokala disken.
    - För nätverk rekommenderas ett nätverkskort i Azure för varje lokalt nätverkskort.
    - För beräkning tittar Azure Migrate på den virtuella datorns kärnor och minnesstorlek och rekommenderar en virtuell Azure-dator med samma konfiguration. Om det finns flera tillgängliga Azure VM-storlekar rekommenderas den billigaste.


[Läs mer](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) om storleksändring med nivå 3.

Ange nivå på följande sätt:

1. Öppna vCenter Server-instansen i vSphere-webbklienten.
2. Välj fliken **Manage** (Hantera) och under **Settings** (Inställningar) klickar du sedan på **General** (Allmänt).
3. Klicka på **Edit** (Redigera) och i **Statistics** (Statistik) anger du statistiknivåinställningen till **Level 3** (Nivå 3).

    ![Statistiknivå i vCenter](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>Steg 5: Identifiera virtuella datorer

Skapa ett Azure Migrate-projekt, hämta och konfigurera den virtuella insamlardatorn. Kör insamlaren för att identifiera dina virtuella datorer.

### <a name="create-a-project"></a>Skapa ett projekt

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Skapa en resurs**.
2. Sök efter **Azure Migrate**. Välj **Azure Migrate** i sökresultatet och klicka på **Skapa**.
3. Ange ett projektnamn och Azure-prenumerationen.
4. Skapa en ny resursgrupp.
5. Ange platsen för projektet och klicka sedan på **Skapa**.

    - Du kan bara skapa ett Azure Migrate-projekt i regionen USA, västra centrala eller USA, östra.
    - Du kan planera en migrering för valfri målplats.
    - Projektplatsen används bara för att lagra de metadata som samlas in från lokala virtuella datorer.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)




### <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Azure Migrate skapar en lokal virtuell dator som kallas för insamlarprogram. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till Azure Migrate-tjänsten. Om du vill konfigurera insamlarprogrammet hämtar du en .OVA-fil och importerar den till den lokala vCenter-servern för att skapa den virtuella datorn.

1. I Azure Migrate-projektet klickar du på **Komma igång** > **Identifiera och utvärdera** > **Identifiera datorer**.
2. I **Identifiera datorer** klickar du på **Ladda ned** för att ladda ned .OVA-filen.
3. I **Kopiera projektautentiseringsuppgifterna** kopierar du projekt-ID och nyckel. Du behöver dem när du konfigurerar insamlaren.

    ![Hämta .OVA-fil](./media/migrate-scenarios-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Kontrollera att .OVA-filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för OVA-filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha nedanstående inställningar (version 1.0.9.8)

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff


### <a name="create-the-collector-appliance"></a>Skapa insamlingsprogrammet

Importera den nedladdade filen till vCenter Server.

1. I vSphere-klientkonsolen klickar du på **Arkiv** > **Distribuera OVF-mall**.

    ![Distribuera OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF-mall > **Källa** anger du platsen för .OVA-filen och klickar på **Nästa**.
3. I **OVF Template Details** (Information om OVF-mall) klickar du på **Nästa**. På sidan med **licensavtalet** godkänner du villkoren och klickar på **Acceptera** och klickar på **Nästa**.
4. I **Namn och plats** anger du ett eget namn för den virtuella insamlardatorn och lagerplatsen där den virtuella datorn kommer att finnas och klickar på **Nästa**. Ange den värd eller det kluster som insamlingsprogrammet ska köras i.
5. I **Lagring** anger du var du vill lagra filer för insamlingsprogrammet och klickar på **Nästa**.
6. I **Diskformat** anger du hur du vill etablera lagringen.
7. I **Nätverksmappning** anger du det nätverk som den virtuella insamlardatorn kommer att ansluta till. Nätverket måste ha en Internetanslutning för att kunna skicka metadata till Azure.
8. I **Ready to Complete** (Redo att slutföra) granskar du inställningarna, väljer **Power on after deployment** (Aktivera efter distribution) och klickar på **Finish** (Slutför).

Ett meddelande som bekräftar att det är klart utfärdas när insamlingsprogrammet har skapats.

### <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

Observera innan du börjar att insamlaren för närvarande endast har stöd för ”Engelska (USA)” som operativsystemspråk och gränssnittsspråk i insamlaren.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösenordsinställningar för produkten.
3. På skrivbordet klickar du på genvägen **Kör insamlare**.

    ![Genväg till insamlaren](./media/migrate-scenarios-assessment/collector-shortcut.png)

4. I Azure Migrate Collector öppnar du **Set up prerequisites** (Ange förutsättningar).
    - Acceptera licensvillkoren och läs informationen från tredje part.
    - Insamlaren kontrollerar att den virtuella datorn är ansluten till internet, att tiden synkroniseras, att insamlartjänsten körs (den installeras som standard på den virtuella datorn). Den installerar även VMWare PowerCLI.

    > [!NOTE]
    > Vi förutsätter att den virtuella datorn har direkt åtkomst till Internet, utan proxy.

    ![Kontrollera förutsättningar](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)


5. Gör följande i **Specify vCenter Server details** (Ange vCenter Server-information):
    - Ange namn (FQDN) eller IP-adress för vCenter-servern.
    - I **Användarnamn** och **Lösenord** anger du de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer på vCenter-servern.
    - I **Välj omfattning** väljer du en omfattning för identifieringen av virtuella datorer. Insamlaren kan bara identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 500 virtuella datorer.

    ![Ansluta till vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. I **Specify migration project** (Ange migreringsprojekt) anger du det projekt-ID och den nyckel för Azure Migrate som du kopierade från portalen. Om du inte kopierade dem öppnar du Azure Portal från den virtuella insamlardatorn. På projektsidan **Översikt** klickar du på **Identifiera datorer** och kopierar värdena.  

    ![Anslut till Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. I **View collection progress** (Visa insamlingsförlopp) övervakar du identifieringen och kontrollerar att de metadata som samlas in från virtuella datorer finns i ett omfång. Insamlaren visar en ungefärlig identifieringstid.

    ![Insamling pågår](./media/migrate-scenarios-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När insamlingen är klar kontrollerar du att de virtuella datorerna visas i portalen.

1. I Azure Migrate-projektet klickar du på **Hantera** > **Datorer**.
2. Kontrollera att de virtuella datorer som du vill identifiera visas på sidan.

    ![Identifierade datorer](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Observera att Azure Migrate-agenter inte är installerade på datorerna för närvarande. Vi behöver installera dem så att vi kan visa beroenden.

    ![Identifierade datorer](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>Steg 6: Förbereda för beroendeanalys

För att kunna visa beroenden mellan de virtuella datorer som vi vill utvärdera hämtar och installerar vi de virtuella datorerna för webbappar – WEBVM och SQLVM.

### <a name="take-a-snapshot"></a>Ta en ögonblicksbild

Om du vill ha en kopia av den virtuella datorn innan du ändrar den kan du ta en ögonblicksbild innan du installerar agenterna.

![Ögonblicksbild av dator](./media/migrate-scenarios-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna

1.  I Azure Migrate-projektet > sidan **Datorer** väljer du datorn och klickar på **Kräver installation** i kolumnen **Beroenden**.
2.  På sidan **Identifiera datorer** hämtar och installerar du Microsoft Monitoring Agent (MMA) och beroendeagenten.
3.  Kopiera arbetsytans ID och nyckel. Du behöver dem när du installerar MMA.

    ![Hämning av agent](./media/migrate-scenarios-assessment/download-agents.png)



#### <a name="install-the-mma"></a>Installera MMA

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
3. I **Målmapp** behåller du standardinstallationsmappen > **Nästa**.
4. I **Installationsalternativ för Agent** väljer de **Anslut agenten till Azure Log Analytics** > **Nästa**.

    ![Installation av MMA](./media/migrate-scenarios-assessment/mma-install.png)
5. I **Azure Log Analytic** klistrar du in i arbetsytans ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.
    ![Installation av MMA](./media/migrate-scenarios-assessment/mma-install2.png)

6. I **Klar att installera** installerar du MMA.



#### <a name="install-the-dependency-agent"></a>Installera beroendeagenten

1.  Dubbelklicka på den hämtade beroendeagenten.
2.  På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
3.  I **Installerar** väntar du tills installationen har slutförts. Klicka sedan på **Nästa**.

    ![Beroendeagent](./media/migrate-scenarios-assessment/dependency-agent.png)



## <a name="step-7-run-and-analyze-the-vm-assessment"></a>Steg 7: Köra och analysera VM-utvärderingen

Kontrollera datorberoenden och skapa en grupp. Kör sedan utvärderingen.

### <a name="verify-dependencies-and-create-a-group"></a>Kontrollera beroenden och skapa en grupp.

1.  På sidan **Datorer** klickar du på **Visa beroenden** för de virtuella datorer som du vill analysera.

    ![Visa datorberoenden](./media/migrate-scenarios-assessment/view-machine-dependencies.png)

2. För SQLVM visar beroendekartan följande information:

    - Processgrupper/processer med aktiva nätverksanslutningar som körs på SQLVM under den angivna tidsperioden (en timme som standard)
    - Inkommande (klient) och utgående (server) TCP-anslutningar till och från alla beroende datorer.
    - Beroende datorer med Azure Migrate-agenter installerade visas som separata rutor
    - Datorer utan agenter installerade visar information om port och IP-adress.

 3. För datorer med agenten installerad (WEBVM) klickar du på datorrutan för att visa mer information, inklusive FQDN, operativsystem och MAC-adress.

    ![Visa gruppberoenden](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. Nu väljer du de virtuella datorerna som du vill lägga till i gruppen (SQLVM och WEBVM).  Håll ned CTRL och klicka för att välja flera virtuella datorer.
5. Klicka på **Skapa grupp** och ange ett namn (smarthotelapp).

> [!NOTE]
    > Om du vill visa mer detaljerade beroenden kan du expandera tidsintervallet. Du kan välja en viss varaktighet eller start- och slutdatum.


### <a name="run-an-assessment"></a>Köra en utvärdering


1. På sidan **Grupper**  öppnar du gruppen (smarthotelapp)
2. Klicka på **Skapa utvärdering**.

    ![Skapa en utvärdering](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. Utvärderingen visas på sidan **Hantera** > **Utvärderingar**.


### <a name="modify-assessment-settings"></a>Ändra inställningar för utvärdering

I den här självstudiekursen har vi använt standardutvärderingsinställningarna, men du kan anpassa inställningarna på följande sätt:

1. På sidan **Utvärderingar** i migreringsprojektet väljer du utvärderingen och klickar på **Redigera egenskaper**.
2. Ändra egenskaper i enlighet med följande tabell:

    **Inställning** | **Detaljer** | **Standard**
    --- | --- | ---
    **Målplats** | Azure-platsen som du vill migrera till | Inget standardvärde.
    **Lagringsredundans** | Den typ av lagringsredundans som de virtuella Azure-datorerna använder efter migreringen. | Standardvärdet är [lokalt redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md). Lägg märke till att Azure Migrate enbart stöder utvärderingar som baseras på hanterade diskar och att hanterade diskar endast stöder LRS, därav LRS-alternativet.
    **Ändra storlek på kriterium** | Kriteriet som ska användas av Azure Migrate för att ställa in rätt storlek på virtuella datorer för Azure. Du kan antingen göra en *prestandabaserad* storleksändring eller ändra storlek på de virtuella datorerna *som lokalt*, utan att överväga prestandahistorik. | Standardalternativet är prestandabaserad storleksändring.
    **Prestandahistorik** | Tidsperioden att beakta när du utvärderar prestanda för de virtuella datorerna. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*. | Standardvärdet är en dag.
    **Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*.  | Standardvärdet är 95: e percentilen.
    **prisnivå** | Du kan ange [prisnivå (Basic/Standard)](../virtual-machines/windows/sizes-general.md) för Azure-måldatorerna. Om du exempelvis planerar att migrera en produktionsmiljö bör du överväga standardnivån, som tillhandahåller virtuella datorer med låg svarstid men kan kosta mer. Om du å andra sidan har en miljö för utveckling och testning kanske du vill överväga Basic-nivån som har virtuella datorer med högre svarstider och lägre kostnader. | Som standard används [standardnivån](../virtual-machines/windows/sizes-general.md).
    **Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. | Standardinställningen är 1.3x.
    **Erbjudande** | [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du är registrerad för. | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) är standard.
    **Valuta** | Faktureringsvalutan. | Standardvärdet är USD.
    **Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet. | Standardinställningen är 0%.
    **Azure Hybrid-förmån** | Ange om du har Software Assurance och är berättigad [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. | Standardvärdet är Ja.

3. Klicka på **Spara** för att uppdatera utvärderingsinställningarna.


### <a name="analyze-the-vm-assessment"></a>Analysera VM-utvärderingen

En Azure Migrate-utvärdering innehåller information om de lokala virtuella datorerna är kompatibla med Azure eller inte, vad som är rätt VM-storlek för att köra den virtuella datorn i Azure samt de beräknade kostnaderna för Azure per månad.

![Utvärderingsrapport](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

![Visa utvärdering](./media/migrate-scenarios-assessment/assessment-display.png)

Din utvärdering får ett säkerhetsomdöme från 1 stjärna till 5 stjärnor (1 stjärna är det lägsta och 5 stjärnor det högsta omdömet).
- Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.
- Omdömet hjälper dig att beräkna tillförlitligheten för storleksrekommendationer som tillhandahålls av Azure Migrate.
- Säkerhetsomdömet är användbart när du gör *prestandabaserad storleksändring*, då Azure Migrate kanske saknar tillräckligt med datapunkter för att göra en användningsbaserad storleksändring. När *storleksändringar av typen ”som lokalt”* utförs är säkerhetsomdömet alltid 5 stjärnor eftersom Azure Migrate har tillgång till alla datapunkter som behövs för att sätta rätt storlek på den virtuella datorn.
- Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

#### <a name="verify-readiness"></a>Verifiera beredskap

![Utvärderingsberedskap](./media/migrate-scenarios-assessment/azure-readiness.png)  

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

![Utvärderingsberedskap](./media/migrate-scenarios-assessment/azure-costs.png)

- Kostnadsuppskattningarna beräknas med hjälp av storleksrekommendationer för en dator.
- Uppskattade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen.


## <a name="conclusion"></a>Sammanfattning

I det här scenariot har vi:

> [!div class="checklist"]
> * Utvärderat vår lokala databas med DMA-verktyget.
> * Utvärderat våra lokala virtuella datorer med beroendemappning med tjänsten Azure Migrate.
> * Granskat utvärderingar för att säkerställa att våra lokala resurser är klara för migrering till Azure.

## <a name="next-steps"></a>Nästa steg

Nu ska vi gå vidare till nästa scenario och gör en [Lift and Shift-migrering](migrate-scenarios-lift-and-shift.md) av de lokala virtuella datorerna och databasen till Azure.
