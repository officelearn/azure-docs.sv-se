---
title: Ställ in agentbaserad beroendeanalys i Azure Migrate Server Assessment
description: I den här artikeln beskrivs hur du ställer in agentbaserad beroendeanalys i Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453623"
---
# <a name="set-up-dependency-visualization"></a>Konfigurera beroendevisualisering

I den här artikeln beskrivs hur du ställer in agentbaserad beroendeanalys i Azure Migrate:Server Assessment. [Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera och förstå beroenden mellan datorer som du vill bedöma och migrera till Azure.

## <a name="before-you-start"></a>Innan du börjar

- [Läs mer om](concepts-dependency-visualization.md#agent-based-analysis) agentbaserad beroendeanalys.
- Granska förutsättningarna och supportkraven för att ställa in agentbaserad beroendevisualisering för virtuella datorer med [VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [fysiska servrar](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)och [virtuella hyper-virtuella datorer](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Kontrollera att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt kontrollerar du att du har [lagt till](how-to-assess.md) verktyget Azure Migrate:Server Assessment.
- Kontrollera att du har konfigurerat en [Azure Migrate-installation för](migrate-appliance.md) att identifiera dina lokala datorer. Lär dig hur du konfigurerar en apparat för [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)eller [fysiska servrar](how-to-set-up-appliance-physical.md). Installationen identifierar lokala datorer och skickar metadata, prestandadata till Azure Migrate:Server Assessment.
- Om du vill använda beroendevisualisering associerar du en [Log Analytics-arbetsyta](../azure-monitor/platform/manage-access.md) med ett Azure Migrate-projekt:
    - Du kan bara ansluta en arbetsyta efter att du har konfigurerat Azure Migrate-enheten och upptäcker datorer i Azure Migrate-projektet.
    - Kontrollera att du har en arbetsyta i prenumerationen som innehåller Azure Migrate-projektet.
    - Arbetsytan måste finnas i regionerna Östra USA, Sydostasien eller Västeuropa. Arbetsytor i andra regioner kan inte associeras med ett projekt.
    - Arbetsytan måste finnas i en region där [Service Map stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - Du kan associera en ny eller befintlig Log Analytics-arbetsyta med ett Azure Migrate-projekt.
    - Du kan koppla arbetsytan första gången du ställer in beroendevisualisering för en dator. Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till.
    - I Logganalys taggas arbetsytan som är associerad med Azure Migrate med nyckeln Migreringsprojekt och projektnamnet.

## <a name="associate-a-workspace"></a>Associera en arbetsyta

1. När du har upptäckt datorer för bedömning klickar du på **Översikt**i **Servrar** > **Azure Migrera: Serverutvärdering**.  
2. Klicka på **Essentials**i **Azure Migrate: Server Assessment**.
3. Klicka på **Kräver konfiguration**i **OMS-arbetsytan**.

     ![Konfigurera log analytics-arbetsyta](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. I **Konfigurera OMS-arbetsyta anger**du om du vill skapa en ny arbetsyta eller använda en befintlig.
    - Du kan välja en befintlig arbetsyta från alla arbetsytor i den migrera projektprenumerationen.
    - Du behöver läsaråtkomst till arbetsytan för att associera den.
5. Om du skapar en ny arbetsyta väljer du en plats för den.

    ![Lägga till en ny arbetsyta](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna

Installera agenterna på varje dator som du vill analysera.

> [!NOTE]
> För datorer som övervakas av System Center Operations Manager 2012 R2 eller senare behöver du inte installera MMA-agenten. Service Map integreras med Operations Manager. [Följ](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) integrationsvägledningen.

1. Klicka på **Identifierade servrar**i **Azure Migrate: Server Assessment**.
2. För varje dator som du vill analysera med beroendevisualisering klickar du på **Kräver agentinstallation**i kolumnen **Beroenden** .
3. På sidan **Beroenden** laddar du ned MMA- och Dependency-agenten för Windows eller Linux.
4. Kopiera arbetsyte-ID och nyckel under **Konfigurera MMA-agent.** Du behöver dessa när du installerar MMA-agenten.

    ![Installera agenterna](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installera MMA

Installera MMA på varje Windows eller Linux-maskin som du vill analysera.

### <a name="install-mma-on-a-windows-machine"></a>Installera MMA på en Windows-dator

Så här installerar du agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. Klicka på **Jag godkänner** att acceptera licensen på sidan **Licensvillkor.**
3. Behåll eller ändra standardinstallationsmappen > **Nästa**i **målmapp**.
4. I **Alternativ för agentinställningar**väljer du **Nästa Azure Log Analytics** > **.**
5. Klicka på **Lägg till** om du vill lägga till en ny log analytics-arbetsyta. Klistra in i arbetsyte-ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.

Du kan installera agenten från kommandoraden eller använda en automatiserad metod som Configuration Manager eller [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- [Läs mer](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) om hur du använder dessa metoder för att installera MMA-agenten.
- MMA-agenten kan också installeras med detta [skript](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Läs mer](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) om Windows-operativsystem som stöds av MMA.

### <a name="install-mma-on-a-linux-machine"></a>Installera MMA på en Linux-maskin

Så här installerar du MMA på en Linux-dator:

1. Överför lämpligt paket (x86 eller x64) till din Linux-dator med scp/sftp.
2. Installera paketet med argumentet --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Läs mer](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) om listan över Stöd till Linux-operativsystem från MMA. 

## <a name="install-the-dependency-agent"></a>Installera beroendeagenten

1. Om du vill installera beroendeagenten på en Windows-dator dubbelklickar du på installationsfilen och följer guiden.
2. Om du vill installera beroendeagenten på en Linux-dator installerar du som root med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) om hur du kan använda skript för att installera beroendeagenten.
- [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) om de operativsystem som stöds av beroendeagenten.


## <a name="create-a-group-using-dependency-visualization"></a>Skapa en grupp med hjälp av beroendevisualisering

Nu skapa en grupp för bedömning. 


> [!NOTE]
> Grupper som du vill visualisera beroenden för bör inte innehålla fler än 10 datorer. Om du har fler än 10 maskiner kan du dela upp dem i mindre grupper.

1. Klicka på **Identifierade servrar**i **Azure Migrate: Server Assessment**.
2. Klicka på **Visa beroenden** för varje dator som du vill granska i kolumnen **Beroenden.**
3. På beroendekartan kan du se följande:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar, till och från datorn.
    - Beroende datorer som inte har beroendeagenterna installerade grupperas efter portnummer.
    - Beroende datorer med installerade beroendeagenter visas som separata rutor.
    - Processer som körs inuti maskinen. Expandera varje maskinruta för att visa processerna.
    - Maskinegenskaper (inklusive FQDN, operativsystem, MAC-adress). Klicka på varje maskinruta för att visa detaljerna.

4. Du kan titta på beroenden för olika tidslängder genom att klicka på tidsvaraktigheten i tidsintervalletiketten.
    - Som standard är intervallet en timme. 
    - Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.
    - Tidsintervallet kan vara upp till en timme. Om du behöver ett längre intervall använder du Azure Monitor för att fråga beroende data under en längre period.

5. När du har identifierat de beroende datorer som du vill gruppera tillsammans använder du Ctrl+Klicka för att markera flera datorer på kartan och klickar på **Gruppera datorer**.
6. Ange ett gruppnamn.
7. Kontrollera att de beroende datorerna identifieras av Azure Migrate.

    - Om en beroende dator inte identifieras av Azure Migrate: Server Assessment kan du inte lägga till den i gruppen.
    - Om du vill lägga till en dator kör du identifieringen igen och kontrollerar att datorn har identifierats.

8. Om du vill skapa en utvärdering för den här gruppen markerar du kryssrutan för att skapa en ny utvärdering för gruppen.
8. Spara gruppen genom att klicka på **OK.**

När du har skapat gruppen rekommenderar vi att du installerar agenter på alla datorer i gruppen och sedan visualiserar beroenden för hela gruppen.

## <a name="query-dependency-data-in-azure-monitor"></a>Frågeberoendedata i Azure Monitor

Du kan fråga beroendedata som samlas in av tjänstövervakare på arbetsytan Log Analytics som är associerad med Azure Migrate-projektet. Log Analytics används för att skriva och köra Azure Monitor-loggfrågor.

- [Läs om hur du](../azure-monitor/insights/service-map.md#log-analytics-records) söker efter Service Map-data i Logganalys.
- [Få en översikt över](../azure-monitor/log-query/get-started-queries.md) att skriva loggfrågor i [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Kör en fråga för beroendedata enligt följande:

1. När du har installerat agenterna går du till portalen och klickar på **Översikt**.
2. Klicka på **Översikt**i **Azure Migrate: Serverutvärdering**. Klicka på nedpilen för att expandera **Essentials**.
3. Klicka på arbetsytans namn i **OMS Workspace.**
3. Klicka på **Loggar**på sidan Logganalysarbetsyta **> Allmänt**.
4. Skriv frågan och klicka på **Kör**.

### <a name="sample-queries"></a>Exempelfrågor

Här är några exempelfrågor som du kan använda för att extrahera beroendedata.

- Du kan ändra frågorna för att extrahera önskade datapunkter.
- [Granska](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) en fullständig lista över beroendedataposter.
- [Granska](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) ytterligare exempelfrågor.

#### <a name="sample-review-inbound-connections"></a>Exempel: Granska inkommande anslutningar

Granska inkommande anslutningar för en uppsättning virtuella datorer.

- Posterna i tabellen för anslutningsmått (VMConnection) representerar inte enskilda fysiska nätverksanslutningar.
- Flera fysiska nätverksanslutningar grupperas i en logisk anslutning.
- [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) om hur fysiska nätverksanslutningsdata aggregeras i VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Exempel: Sammanfatta skickade och mottagna data

Det här exemplet sammanfattar mängden data som skickas och tas emot på inkommande anslutningar mellan en uppsättning datorer.

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

[Skapa en utvärdering](how-to-create-assessment.md) för en grupp.


