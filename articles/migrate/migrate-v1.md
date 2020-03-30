---
title: Arbeta med den tidigare versionen av Azure Migrate
description: Beskriver hur du arbetar med den tidigare versionen av Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c9cea52e04a991e6e3ac64426f0443939f8aaa3a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77914389"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Arbeta med den tidigare versionen av Azure Migrate

Den här artikeln innehåller information om hur du arbetar med den tidigare versionen av Azure Migrate.


Det finns två versioner av Azure Migrate-tjänsten:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate-projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: Om du använder den tidigare versionen av Azure Migrate (endast bedömning av lokala virtuella virtuella datorer med VMware stöddes), bör du nu använda den aktuella versionen. Om du fortfarande behöver använda Azure Migrate-projekt som skapats i den tidigare versionen kan och kan du inte göra följande:
    - Du kan inte längre skapa migreringsprojekt.
    - Vi rekommenderar att du inte utför nya identifieringar.
    - Du kan fortfarande komma åt befintliga projekt.
    - Du kan fortfarande köra utvärderingar.
    

## <a name="upgrade-between-versions"></a>Uppgradera mellan versioner

Du kan inte uppgradera projekt eller komponenter i den tidigare versionen till den nya versionen. Du måste [skapa ett nytt Azure Migrate-projekt](how-to-add-tool-first-time.md)och lägga till bedömnings- och migreringsverktyg i det.

## <a name="find-projects-from-previous-version"></a>Hitta projekt från tidigare version

Hitta projekt från den tidigare versionen enligt följande:

1. Sök efter och välj **Azure Migrate**i Azure-portalen > **Alla tjänster**. 
2. På instrumentpanelen för Azure Migrate finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.
3. Klicka på länken för att öppna v1-projekt.


## <a name="create-an-assessment"></a>Skapa en utvärdering

Efter att virtuella datorer har identifierats i portalen grupperar du dem och skapar utvärderingar.

- Du kan omedelbart skapa som lokala utvärderingar direkt efter att virtuella datorer har identifierats i portalen.
- För prestandabaserade utvärderingar rekommenderar vi att du väntar minst en dag innan du skapar en resultatbaserad utvärdering för att få tillförlitliga storleksrekommendationer.

Skapa en bedömning på följande sätt:

1. På projektsidan **Översikt** klickar du på **+Skapa utvärdering**.
2. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.
3. Skapa gruppen och ange ett gruppnamn.
4. Välj de datorer som du vill lägga till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och utvärderingen.
6. När utvärderingen har skapats visar du den i > **översiktsinstrumentpanelen**. **Overview**
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.

Om du vill uppdatera en befintlig utvärdering med de senaste prestandadata, kan du använda kommandot **beräkna om** för utvärdering för att uppdatera den.

## <a name="review-an-assessment"></a>Granska en bedömning 

En bedömning har tre steg:

- En utvärdering börjar med en lämplighetsanalys för att ta reda på om datorer är kompatibla i Azure.
- Storleksuppskattningar.
- Månatlig kostnadsuppskattning.

En maskin flyttas bara vidare till ett senare stadium om den passerar den föregående. Om en dator till exempel misslyckas med lämplighetskontrollen markeras den som olämplig för Azure och storlek och kostnadsredovisning görs inte.


### <a name="review-azure-readiness"></a>Granska Azure-beredskap

Vyn för Azure-beredskap i utvärderingen visar beredskapsstatus för varje virtuell dator.

**Beredskap** | **Status** | **Detaljer**
--- | --- | ---
Redo för Azure | Inga kompatibilitetsproblem. Datorn kan migreras som den är till Azure och den startas i Azure med fullständigt Azure-stöd. | För förberedda virtuella datorer rekommenderar Azure Migrate en VM-storlek i Azure.
Villkorligt redo för Azure | Datorn kan starta i Azure, men kanske inte har fullt Azure-stöd. Till exempel en dator med en äldre version av Windows Server som inte stöds i Azure. | Azure Migrate förklarar beredskapsproblemen och tillhandahåller åtgärder för reparation.
Ej redo för Azure |  Den virtuella datorn startar inte i Azure. Om en virtuell dator till exempel har en disk som är mer än 4 TB kan den inte vara värd för Azure. | Azure Migrate förklarar beredskapsproblemen och tillhandahåller åtgärder för reparation.
Beredskap okänd | Azure Migrate kan inte identifiera Azure-beredskap, oftast på grund av att data inte är tillgängliga.


#### <a name="azure-vm-properties"></a>Azure VM-egenskaper
Beredskap tar hänsyn till ett antal VM-egenskaper för att identifiera om den virtuella datorn kan köras i Azure.


**Egenskap** | **Detaljer** | **Beredskap**
--- | --- | ---
**Starttyp** | BIOS stöds. UEFI stöds inte. | Villkorligt klar om starttypen är UEFI.
**Kärnor** | Datorer kärnor <= det maximala antalet kärnor (128) som stöds för en Azure VM.<br/><br/> Om prestandahistorik är tillgänglig tar Azure Migrate hänsyn till de använda kärnorna.<br/>Om en komfortfaktor anges i bedömningsinställningarna multipliceras antalet utnyttjade kärnor med komfortfaktorn.<br/><br/> Om det inte finns någon prestandahistorik använder Azure Migrate de allokerade kärnorna, utan att använda komfortfaktorn. | Redo om den är mindre än eller lika med gränserna.
**Minne** | Datorns minnesstorlek <= det maximala minnet (3892 GB på&nbsp;Azure M-serien Standard_M128m<sup>2)</sup>för en Virtuell Azure.The machine memory size <= the maximum memory (3892 GB on Azure M series Standard_M128m 2 ) for an Azure VM. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Om prestandahistorik är tillgänglig tar Azure Migrate hänsyn till det använda minnet.<br/><br/>Om en komfortfaktor anges multipliceras det utnyttjade minnet med komfortfaktorn.<br/><br/> Om det inte finns någon historia det allokerade minnet används, utan att tillämpa komfortfaktorn.<br/><br/> | Redo om inom gränser.
**Lagringsdisk** | Allokerad storlek på en disk måste vara 4 TB (4096 GB) eller mindre.<br/><br/> Antalet diskar som är anslutna till datorn måste vara 65 eller mindre, inklusive OS-disken. | Redo om inom gränser.
**Nätverk** | En maskin måste ha 32 eller mindre nätverkskort anslutna till den. | Redo om inom gränser.

#### <a name="guest-operating-system"></a>Gästoperativsystem

Tillsammans med vm-egenskaper tittar Azure Migrate också på gästoperativsystemet för den lokala virtuella datorn för att identifiera om den virtuella datorn kan köras i Azure.

- Azure Migrate tar hänsyn till det operativsystem som anges i vCenter Server.
- Eftersom identifieringen som görs av Azure Migrate är apparatbaserad, har den inget sätt att kontrollera om operativsystemet som körs inuti den virtuella datorn är samma som det som anges i vCenter Server.

Följande logik används.

**Operativsystem** | **Detaljer** | **Beredskap**
--- | --- | ---
Windows Server 2016 och alla SPs-adresser | Azure ger fullt stöd. | Redo för Azure
Windows Server 2012 R2 och alla SPs | Azure ger fullt stöd. | Redo för Azure
Windows Server 2012 och alla SPs-adresser | Azure ger fullt stöd. | Redo för Azure
Windows Server 2008 R2 och alla SPs | Azure ger fullt stöd.| Redo för Azure
Windows Server 2008 (32- och 64-bitars) | Azure ger fullt stöd. | Redo för Azure
Windows Server 2003, 2003 R2 | på support och behöver ett [csa-avtal (Custom Support Agreement)](https://aka.ms/WSosstatement) för support i Azure. | Villkorligt klar för Azure, överväg att uppgradera operativsystemet innan du migrerar till Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Utanför supporten. Datorn kan starta i Azure, men inget OS-stöd tillhandahålls av Azure. | Villkorligt klar för Azure, rekommenderas att uppgradera operativsystemet innan du migrerar till Azure.
Windows-klient 7, 8 och 10 | Azure tillhandahåller endast support med [Visual Studio-prenumeration.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Villkorligt redo för Azure
Skrivbordet i Windows 10 Pro | Azure ger support med [multitenant hostingrättigheter.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Villkorligt redo för Azure
Windows Vista, XP Professional | Utanför supporten. Datorn kan starta i Azure, men inget OS-stöd tillhandahålls av Azure. | Villkorligt klar för Azure, rekommenderas att uppgradera operativsystemet innan du migrerar till Azure.
Linux | Azure stöder dessa [Linux-operativsystem](../virtual-machines/linux/endorsed-distros.md). Andra Linux-operativsystem kan starta i Azure, men vi rekommenderar att du uppgraderar operativsystemet till en godkänd version innan du migrerar till Azure. | Redo för Azure om versionen är godkänd.<br/><br/>Villkorligt klar om versionen inte är godkänd.
Andra operativsystem<br/><br/> Till exempel Oracle Solaris, Apple Mac OS etc., FreeBSD, etc. | Azure stöder inte dessa operativsystem. Datorn kan starta i Azure, men inget OS-stöd tillhandahålls av Azure. | Villkorligt klar för Azure, rekommenderas att installera ett operativsystem som stöds innan du migrerar till Azure.  
OS angivet som **Annat** i vCenter Server | Azure Migrate kan inte identifiera operativsystemet i det här fallet. | Okänd beredskap. Kontrollera att operativsystemet som körs i den virtuella datorn stöds i Azure.
32-bitars operativsystem | Datorn kan starta i Azure, men Azure kanske inte ger fullt stöd. | Villkorligt klar för Azure, överväg att uppgradera datorns operativsystem från 32-bitars operativsystem till 64-bitars operativsystem innan du migrerar till Azure.


### <a name="review-sizing"></a>Granska storlek

 Storleksrekommendationen för Azure Migrate beror på storleksvillkoret som anges i bedömningsegenskaperna.

- Om storleksbaserad storlek är prestandabaserad tar storleksrekommendationen hänsyn till prestandahistoriken för de virtuella datorerna (CPU och minne) och diskar (IOPS och dataflöde).
- Om storleksvillkoret är "som lokalt" baseras storleksrekommendationen i Azure på storleken på den virtuella datorn lokalt. Diskstorleken baseras på den lagringstyp som anges i bedömningsegenskaperna (standard är premiumdiskar). Azure Migrate tar inte hänsyn till prestandadata för den virtuella datorn och diskarna.

### <a name="review-cost-estimates"></a>Granska kostnadsuppskattningar

Kostnadsuppskattningar visar den totala beräknings- och lagringskostnaden för att köra de virtuella datorerna i Azure, tillsammans med information för varje dator.

- Kostnadsuppskattningar beräknas med hjälp av storleksrekommendationen för en VM-dator och dess diskar och bedömningsegenskaperna.
- Uppskattade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen.
- Kostnadsuppskattningen är för att köra den lokala virtuella datorn som Azure Infrastructure as a service (IaaS) virtuella datorer. Azure Migrate betraktar inte Plattform som en tjänst (PaaS) eller SaaS-kostnader (Software as a service).

### <a name="review-confidence-rating-performance-based-assessment"></a>Granska förtroendevärdering (resultatbaserad bedömning)

Varje resultatbaserad bedömning är associerad med en konfidensgrad.

- Ett konfidensbetyg varierar från enstjärnigt till femstjärnigt (en stjärna är den lägsta och femstjärniga den högsta).
- Konfidensklassificeringen tilldelas en bedömning, baserat på tillgången på datapunkter som behövs för att beräkna bedömningen.
- Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.
- Konfidensklassificering är inte tillgängligt för lokala bedömningar.

För prestandabaserad storlek behöver Azure Migrate följande:
- Användningsdata för CPU.
- VM-minnesdata.
- För varje disk som är ansluten till den virtuella datorn behöver den disk-IOPS- och dataflöde.
- För varje nätverkskort som är kopplat till en virtuell dator behöver Azure Migrate nätverksindata/utdata.
- Om något av ovanstående inte är tillgängligt kanske storleksrekommendationer (och därmed konfidensklassificeringar) inte är tillförlitliga.


Beroende på hur många procent av tillgängliga datapunkter sammanfattas de möjliga konfidensklassificeringarna i tabellen.

**Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Bedömningsfrågor som påverkar förtroendevärderingar

En bedömning kanske inte har alla datapunkter tillgängliga på grund av ett antal skäl:

- Du har inte profilera din miljö under hela utvärderingen. Om du till exempel skapar utvärderingen med prestandavaraktighet inställd på en dag måste du vänta minst en dag efter att du har påbörjat identifieringen eller alla datapunkter som ska samlas in.
- Vissa virtuella datorer stängdes under den period för vilken bedömningen beräknades. Om några virtuella datorer har stängts av under en del av varaktigheten kan Azure Migrate inte samla in prestandadata för den perioden.
- Vissa virtuella datorer skapades däremellan under bedömningsberäkningsperioden. Om du till exempel skapar en utvärdering med den senaste månadens prestandahistorik, men skapar ett antal virtuella datorer i miljön för en vecka sedan, kommer prestandahistoriken för de nya virtuella datorerna inte att vara under hela varaktigheten.

> [!NOTE]
> Om konfidensklassificeringen för en bedömning är lägre än fem stjärnor, vänta i minst en dag på att apparaten ska profilera miljön och räkna sedan om bedömningen. Om du inte gör prestandabaserad storlek kanske inte är tillförlitlig. Om du inte vill beräkna om rekommenderar vi att du byter till som lokal storlek genom att ändra bedömningsegenskaperna.



## <a name="create-groups-using-dependency-visualization"></a>Skapa grupper med hjälp av beroendevisualisering

Förutom att skapa grupper manuellt kan du skapa grupper med hjälp av beroendevisualisering.
- Du använder vanligtvis den här metoden när du vill bedöma grupper med högre konfidensnivåer genom att dubbelkontrollera datorberoenden innan du kör en utvärdering.
- Beroendevisualisering kan hjälpa dig att effektivt planera din migrering till Azure. Det hjälper dig att se till att ingenting lämnas kvar och att överrasknings avbrott inte uppstår när du migrerar till Azure.
- Du kan identifiera alla ömsesidigt beroende system som behöver migrera tillsammans och identifiera om ett system som körs fortfarande betjänar användare eller är en kandidat för att inaktivera i stället för migrering.
- Azure Migrate använder servicemappningslösningen i Azure Monitor för att aktivera beroendevisualisering.

> [!NOTE]
> Beroendevisualisering är inte tillgängligt i Azure Government.

Om du vill konfigurera beroendevisualisering associerar du en Log Analytics-arbetsyta med ett Azure Migrate-projekt, installerar agenter på datorer som du vill visualisera beroenden för och skapar sedan grupper med hjälp av beroendeinformation. 



### <a name="associate-a-log-analytics-workspace"></a>Associera en log analytics-arbetsyta

Om du vill använda beroendevisualisering associerar du en Log Analytics-arbetsyta med ett migreringsprojekt. Du kan bara skapa eller bifoga en arbetsyta i samma prenumeration där migreringsprojektet skapas.

1. Om du vill koppla en Log Analytics-arbetsyta till ett projekt klickar du på **kräver konfiguration**i **Översikt**, > **Essentials**.
2. Du kan skapa en ny arbetsyta eller bifoga en befintlig:
  - Om du vill skapa en ny arbetsyta anger du ett namn. Arbetsytan skapas i en region i samma [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) som migreringsprojektet.
  - När du ansluter en befintlig arbetsyta kan du välja från alla tillgängliga arbetsytor i samma prenumeration som migreringsprojektet. Endast de arbetsytor visas som har skapats i en [servicemappningsregion som stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Om du vill ansluta en arbetsyta kontrollerar du att du har "Reader"-åtkomst till arbetsytan.

> [!NOTE]
> Du kan inte ändra arbetsytan som är associerad med ett migreringsprojekt.

### <a name="download-and-install-vm-agents"></a>Hämta och installera VM-agenter

När du har konfigurerat en arbetsyta hämtar och installerar du agenter på varje lokal dator som du vill utvärdera. Dessutom, om du har datorer utan internetanslutning, måste du ladda ner och installera [Log Analytics gateway](../azure-monitor/platform/gateway.md) på dem.

1. Klicka på **Hantera** > **datorer i** **Översikt**och välj den dator som krävs.
2. Klicka på **Installera agenter**i kolumnen **Beroenden** .
3. På sidan **Beroenden** hämtar och installerar du MICROSOFT Monitoring Agent (MMA) och beroendeagenten på varje virtuell dator som du vill utvärdera.
4. Kopiera arbetsytans ID och nyckel. Du behöver dessa när du installerar MMA på den lokala datorn.

> [!NOTE]
> Om du vill automatisera installationen av agenter kan du använda ett distributionsverktyg som Configuration Manager eller ett partnerverktyg, till exempel [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), som tillhandahåller en agentdistributionslösning för Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Installera MMA-agenten på en Windows-dator

Så här installerar du agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. Klicka på **Jag godkänner** att acceptera licensen på sidan **Licensvillkor.**
3. Behåll eller ändra standardinstallationsmappen > **Nästa**i **målmapp**.
4. I **Alternativ för agentinställningar**väljer du **Nästa Azure Log Analytics** > **.**
5. Klicka på **Lägg till** om du vill lägga till en ny log analytics-arbetsyta. Klistra in i arbetsyte-ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.

Du kan installera agenten från kommandoraden eller använda en automatiserad metod som Configuration Manager. [Läs mer](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) om hur du använder dessa metoder för att installera MMA-agenten.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Installera MMA-agenten på en Linux-dator

Så här installerar du agenten på en Linux-dator:

1. Överför lämpligt paket (x86 eller x64) till din Linux-dator med scp/sftp.
2. Installera paketet med argumentet --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Läs mer](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) om listan över Stöd till Linux-operativsystem från MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Installera MMA-agenten på en maskin som övervakas av Operations Manager

För datorer som övervakas med System Center Operations Manager 2012 R2 eller senare behöver du inte installera MMA-agenten. Service Map integreras med Operations Manager MMA för att samla in nödvändiga beroendedata. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Beroendeagenten behöver installeras.

### <a name="install-the-dependency-agent"></a>Installera beroendeagenten

1. Om du vill installera beroendeagenten på en Windows-dator dubbelklickar du på installationsfilen och följer guiden.
2. Om du vill installera beroendeagenten på en Linux-dator installerar du som root med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

- Läs mer om [beroendeagentsupporten](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) för Windows- och Linux-operativsystemen.
- [Läs mer](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) om hur du kan använda skript för att installera beroendeagenten.

>[!NOTE]
> Azure Monitor för virtuella datorer som refereras för att ge en översikt över systemets förutsättningar och metoder för att distribuera beroendeagenten är också tillämpliga på servicemappningslösningen.

### <a name="create-a-group-with-dependency-mapping"></a>Skapa en grupp med beroendemappning

1. När du har installerat agenterna går du till portalen och klickar på **Hantera** > **datorer**.
2. Sök efter maskinen där du installerade agenterna.
3. Kolumnen **Beroenden** för datorn ska nu visas som **Visa beroenden**. Klicka på kolumnen om du vill visa beroenden på datorn.
4. Beroendekartan för maskinen visar följande information:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till/från datorn
        - De beroende datorer som inte har MMA och beroendeagenten installerad grupperas efter portnummer.
        - De beroende datorer som har MMA och beroendeagenten installerade visas som separata rutor.
    - Processer som körs inuti maskinen, kan du expandera varje maskinruta för att visa de processer
    - Maskinegenskaper, inklusive FQDN, operativsystem, MAC-adress visas. Du kan klicka på varje maskinruta för att visa detaljer.

4. Du kan visa beroenden för olika tidslängder genom att klicka på tidsvaraktigheten i tidsintervalletiketten. Som standard är intervallet en timme. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.

   > [!NOTE]
   >    Ett tidsintervall på upp till en timme stöds. Använd Azure Monitor-loggar för att [fråga beroendedata](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) under en längre varaktighet.

5. När du har identifierat beroende datorer som du vill gruppera tillsammans använder du Ctrl+Klicka för att markera flera datorer på kartan och klickar på **Gruppera datorer**.
6. Ange ett gruppnamn. Kontrollera att de beroende datorerna identifieras av Azure Migrate.

    > [!NOTE]
    > Om en beroende dator inte identifieras av Azure Migrate kan du inte lägga till den i gruppen. Om du vill lägga till sådana datorer i gruppen måste du köra identifieringsprocessen igen med rätt omfång i vCenter Server och se till att datorn identifieras av Azure Migrate.  

7. Om du vill skapa en utvärdering för den här gruppen markerar du kryssrutan för att skapa en ny utvärdering för gruppen.
8. Spara gruppen genom att klicka på **OK.**

När gruppen har skapats rekommenderas att du installerar agenter på alla datorer i gruppen och förfinar gruppen genom att visualisera beroendet för hela gruppen.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Frågeberoendedata från Azure Monitor-loggar

Beroendedata som samlas in av Service Map är tillgängliga för frågor i log analytics-arbetsytan som är associerad med ditt Azure Migrate-projekt. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) om datatabellerna Service Map som du vill fråga i Azure Monitor-loggar. 

Så här kör du Kusto-frågorna:

1. När du har installerat agenterna går du till portalen och klickar på **Översikt**.
2. I **Översikt**går du till avsnittet **Essentials** i projektet och klickar på arbetsytenamnet bredvid **OMS Workspace**.
3. Klicka på **Allmänna** > **loggar**på sidan Logganalysarbetsyta.
4. Skriv din fråga för att samla in beroendedata med Hjälp av Azure Monitor-loggar. Hitta exempelfrågor i nästa avsnitt.
5. Kör frågan genom att klicka på Kör. 

[Läs mer](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) om hur du skriver Kusto-frågor. 

### <a name="sample-azure-monitor-logs-queries"></a>Exempel på Azure Monitor-loggar frågor

Följande är exempelfrågor som du kan använda för att extrahera beroendedata. Du kan ändra frågorna för att extrahera önskade datapunkter. En uttömmande lista över fälten i beroendedataposter finns [här](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Här hittar du fler exempelfrågor [.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Sammanfatta inkommande anslutningar på en uppsättning datorer

Posterna i tabellen för anslutningsmått, VMConnection, representerar inte enskilda fysiska nätverksanslutningar. Flera fysiska nätverksanslutningar grupperas i en logisk anslutning. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) om hur fysiska nätverksanslutningsdata sammanställs till en enda logisk post i VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Summera volymen av data som skickas och tas emot på inkommande anslutningar mellan en uppsättning datorer

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Nästa steg
[Läs mer om](migrate-services-overview.md) den senaste versionen av Azure Migrate.
