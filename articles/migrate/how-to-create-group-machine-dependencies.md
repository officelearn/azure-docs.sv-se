---
title: Gruppera datorer med datorberoenden med Azure Migrate | Microsoft Docs
description: Beskriver hur du skapar en utvärdering med datorberoenden med tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 686c91669e5eccd7979c248db42d6f5b5079308b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280918"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Gruppera datorer med datorberoende mappning

Den här artikeln beskriver hur du skapar en grupp datorer för [Azure Migrate](migrate-overview.md) utvärdering genom att visualisera beroenden för datorer. Du använder vanligtvis den här metoden när du vill utvärdera grupper med virtuella datorer med högre förtroende genom att dubbelkontrollera datorberoenden innan du kör en utvärdering. Visualisering av beroenden kan hjälpa dig att effektivt planera din migrering till Azure. Det hjälper dig att se till att inget kvar och förvåning avbrott sker inte när du migrerar till Azure. Du kan identifiera alla beroende av varandra system som måste migrera tillsammans och identifiera om ett system som körs fortfarande fungerar som värd för användare eller är en kandidat för inaktivering av i stället för migrering.

> [!NOTE]
> Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.

## <a name="prepare-for-dependency-visualization"></a>Förbereda för visualisering av beroenden
Azure Migrate använder tjänstkarta i Azure Monitor-loggar att aktivera beroendevisualisering av datorer.

### <a name="associate-a-log-analytics-workspace"></a>Associera en Log Analytics-arbetsyta
Om du vill använda visualisering av beroenden, måste du koppla en Log Analytics-arbetsyta, ny eller befintlig, med ett Azure Migrate-projekt. Du kan bara skapa eller koppla en arbetsyta i samma prenumeration där migration-projekt skapas.

- Att koppla en Log Analytics-arbetsyta till ett projekt i **översikt**går du till **Essentials** delen av projektet klickar du på **kräver konfiguration**

    ![Associera arbetsytan för Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- När du kopplar en arbetsyta, får du alternativet för att skapa en ny arbetsyta eller bifoga en befintlig:
  - När du skapar en ny arbetsyta kan behöva du ange ett namn för arbetsytan. Arbetsytan skapas i en region i samma [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) som migration-projekt.
  - När du ansluter en befintlig arbetsyta, kan du välja från alla tillgängliga arbetsytor i samma prenumeration som migration-projekt. Observera att endast dessa arbetsytor visas som har skapats i en region där [Tjänstkarta stöds](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Se till att du har ”läsare” åtkomst till arbetsytan om du vill kunna bifoga en arbetsyta.

> [!NOTE]
> Du kan inte ändra arbetsytan som är kopplad till ett migreringsprojekt.

### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna
När du konfigurerar en arbetsyta, måste du hämta och installera agenter på varje lokal dator som du vill utvärdera. Dessutom, om du har datorer som saknar Internetanslutning måste du ladda ned och installera [Log Analytics gateway](../azure-monitor/platform/gateway.md) på dem.

1. I **översikt**, klickar du på **hantera** > **datorer**, och välj datorn som krävs.
2. I den **beroenden** kolumnen, klickar du på **installera agenter**.
3. På den **beroenden** sidan, hämta och installera Microsoft Monitoring Agent (MMA) och beroendeagenten på varje virtuell dator som du vill utvärdera.
4. Kopiera arbetsytans ID och nyckel. Du behöver dem när du installerar MMA på den lokala datorn.

> [!NOTE]
> För att automatisera installationen av agenter som du kan använda valfri distributionsverktyg som System Center Configuration Manager eller använda våra partner-verktyget [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), som har en agent distributionslösning för Azure Migrate.

### <a name="install-the-mma"></a>Installera MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installera agenten på en Windows-dator

Installera agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
3. I **målmapp**, behålla eller ändra standardinstallationsmappen > **nästa**.
4. I **installationsalternativ för Agent**väljer **Azure Log Analytics** > **nästa**.
5. Klicka på **Lägg till** att lägga till en ny Log Analytics-arbetsyta. Klistra in i arbetsytans ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.

Du kan installera agenten från kommandoraden eller med hjälp av en automatiserad metod, till exempel System Center Configuration Manager. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) om hur du använder dessa metoder för att installera MMA-agenten.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installera agenten på en Linux-dator

Installera agenten på en Linux-dator:

1. Över rätt samling (x86 eller x64) till Linux-datorn med scp/sftp.
2. Installera paketet med hjälp av argumentet--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Läs mer](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) om listan över support för Linux-operativsystem genom att MMA.

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>Installera agenten på en dator som övervakas av SCOM

Det finns datorer som övervakas av System Center Operations Manager 2012 R2 eller senare, behöver inte installera MMA-agenten. Tjänstkarta har en integrering med SCOM som utnyttjar SCOM MMA för datainsamling nödvändiga beroendet. Du kan aktivera integrationen genom att följa vägledningen [här](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Observera dock att beroendeagenten måste installeras på dessa datorer.


### <a name="install-the-dependency-agent"></a>Installera beroendeagenten
1. Installera beroendeagenten på en Windows-dator, dubbelklicka på installationsfilen och följ anvisningarna i guiden.
2. Installera beroendeagenten på en Linux-dator, installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

Mer information om stöd för beroende-agenten för den [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) och [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operativsystem.

[Läs mer](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) om hur du kan använda skript för att installera beroendeagenten.


## <a name="create-a-group"></a>Skapa en grupp

1. När du har installerat agenterna, gå till portalen och klicka på **hantera** > **datorer**.
2. Sök efter den datorn där du installerade agenterna.
3. Den **beroenden** kolumnen för datorn bör nu visa som **Visa beroenden**. Klicka på kolumnen om du vill visa datorns beroenden.
4. Beroendekarta för datorn visar följande information:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till och från datorn
        - Beroende datorer som inte har MMA och beroendeagenter agenten installerad är grupperade efter portnummer
        - Beroende datorer som har MMA och beroendeagenten installerat visas som separata rutor
    - Processer som körs på datorn, som du kan expandera varje datorrutan för att visa processer
    - Egenskaper som fullständigt kvalificerade namn, operativsystem, MAC-adress osv på varje dator som du kan klicka på varje datorrutan för att visa den här informationen

      ![Visa datorberoenden](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Du kan titta på beroenden för olika tidsvaraktigheter genom att klicka på varaktighet i Intervalletikett tid. Intervallet är en timme som standard. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.

   > [!NOTE]
   >    Beroendevisualisering Användargränssnittet stöder för närvarande inte valet av ett tidsintervall som är längre än en timme. Använd Azure Monitor loggar [frågedata beroende](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre period.

5. När du har identifierat beroende datorer som du vill gruppera kan använda Ctrl + klicka för att välja flera datorer på kartan Klicka på **gruppera datorer**.
6. Ange ett gruppnamn. Kontrollera att de beroende datorerna identifieras av Azure Migrate.

    > [!NOTE]
    > Om en beroende dator inte kan identifieras av Azure Migrate, kan du lägga till den gruppen. Om du vill lägga till sådana datorer i gruppen som du behöver köra identifieringen igen med rätt omfattning i vCenter Server och se till att datorn identifieras av Azure Migrate.  

7. Om du vill skapa en utvärdering för den här gruppen kan du markera kryssrutan för att skapa en ny värdering för gruppen.
8. Klicka på **OK** ska sparas.

När gruppen har skapats, rekommenderar vi att du installerar agenter på alla datorer i gruppen och sedan ändra gruppen genom att visualisera beroenden för hela gruppen.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Fråga efter data för programberoende från Azure Monitor-loggar

Beroendedata som hämtats av Tjänstkarta är tillgänglig för frågor i Log Analytics-arbetsyta som är associerade med Azure Migrate-projekt. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) om Tjänstkarta datatabeller att fråga i Azure Monitor-loggar. 

Köra Kusto-frågor:

1. När du har installerat agenterna, gå till portalen och klicka på **översikt**.
2. I **översikt**går du till **Essentials** delen av projektet och klicka på arbetsytans namn tillhandahålls bredvid **OMS-arbetsyta**.
3. På sidan Log Analytics-arbetsytan **Allmänt** > **loggar**.
4. Skriv din fråga för att samla in data för programberoende med Azure Monitor-loggar. Exempelfrågor för att samla in data för programberoende finns [här](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. Kör frågan genom att klicka på Kör. 

[Läs mer](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) om hur du skriver Kusto-frågor. 

### <a name="sample-azure-monitor-logs-queries"></a>Exemplet Azure Monitor loggar frågor

Följande är exempelfrågor som du kan använda för att extrahera data för programberoende. Observera att frågorna kan ändras för att extrahera önskade datapunkterna. En fullständig förteckning av fälten i beroende dataposter är tillgänglig [här](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Sammanfatta inkommande anslutningar på en uppsättning datorer

Observera att posterna i tabellen för anslutningsmått, VMConnection, inte representerar enskilda fysiska nätverksanslutningar. Flera fysiska nätverksanslutningar är grupperade i en logisk anslutning. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) om hur fysiska nätverksanslutningen samlas data i en enda logisk post i VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Sammanfatta mängden data som skickas och tas emot på inkommande anslutningar mellan en uppsättning datorer

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

- [Läs mer](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) om frågor och svar på beroendevisualisering.
- [Lär dig hur](how-to-create-group-dependencies.md) att förfina gruppen genom att visualisera gruppberoenden.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
