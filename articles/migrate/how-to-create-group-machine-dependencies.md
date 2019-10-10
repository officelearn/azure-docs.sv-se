---
title: Gruppera datorer som använder dator beroenden med Azure Migrate | Microsoft Docs
description: Beskriver hur du skapar en utvärdering med hjälp av dator beroenden med tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: 1cd1ff83fd706e3474426f6cc2ac99d39e74dc22
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177934"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Konfigurera beroende visualisering för utvärdering

Den här artikeln beskriver hur du konfigurerar beroende mappning i Azure Migrate: Server utvärdering.

Beroende mappning hjälper dig att visualisera beroenden mellan datorer som du vill utvärdera och migrera.

- I Azure Migrate: Server utvärdering kan du samla ihop datorer för utvärdering. Vanligt vis datorer som du vill migrera tillsammans.
- Du använder vanligt vis beroende mappning när du vill utvärdera grupper med högre Tillförlitlighets nivåer.
- Beroende mappningen hjälper dig att kontrol lera dator beroenden innan du kör en utvärdering och migrering.
- Mappning och visualisering av beroenden hjälper till att effektivt Planera migreringen till Azure. Det hjälper till att se till att ingenting är kvar bakom, så att du undviker oväntade avbrott under migreringen.
- Med hjälp av mappning kan du identifiera beroende system som behöver migreras tillsammans. Du kan också identifiera om ett system som körs fortfarande betjänar användare eller är en kandidat för inaktive ring i stället för migrering.

[Lär dig mer](concepts-dependency-visualization.md#how-does-it-work) om beroende visualisering.

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt kontrollerar du att du har [lagt till](how-to-assess.md) Azure Migrate: Server utvärderings verktyget.
- Kontrol lera att du har identifierat datorerna i Azure Migrate. Det kan du göra genom att konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md) eller [Hyper-V](how-to-set-up-appliance-hyper-v.md). Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering. [Läs mer](migrate-appliance.md).


**Funktioner** | **Obs!**
--- | ---
Tillgänglighet | Beroende visualisering är inte tillgänglig i Azure Government.
Tjänsteöversikt | Beroende visualisering använder Tjänstkarta lösning i Azure Monitor. [Tjänstkarta](../azure-monitor/insights/service-map.md) identifierar och visar automatiskt anslutningar mellan servrar.
Agenter | Om du vill använda beroende visualisering installerar du följande agenter på datorer som du vill mappa:<br/> - [Log Analytics agent](../azure-monitor/platform/log-analytics-agent.md) agent (kallades tidigare för Microsoft Monitoring Agent (MMA).<br/> - [tjänstkarta beroende agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> Om du vill automatisera Agent installationen kan du använda ett distributions verktyg som System Center Configuration Manager, som har en agent distributions lösning för Azure Migrate.
Beroendeagent | Granska [stöd för beroende agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) för Windows och Linux.<br/><br/> [Läs mer](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) om hur du använder skript för att installera beroende agenten.
Log Analytics agent (MMA) | [Läs mer](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) om installations metoder för MMA.<br/><br/> För datorer som övervakas av System Center Operations Manager 2012 R2 eller senare behöver du inte installera MMA-agenten. Tjänstkarta integreras med Operations Manager. Du kan aktivera integreringen med hjälp av vägledningen [här](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Observera dock att beroende agenten måste vara installerad på de här datorerna.<br/><br/> [Granska](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) Linux-operativsystemen som stöds av Log Analytics agenten.
Utvärderings grupper | Grupper som du vill visualisera beroenden för får inte innehålla fler än 10 datorer. Om du har fler än 10 datorer delar du in dem i mindre grupper för att visualisera beroenden.

## <a name="associate-a-log-analytics-workspace"></a>Koppla en Log Analytics-arbetsyta

Om du vill använda beroende visualisering måste du associera en [Log Analytics arbets yta](../azure-monitor/platform/manage-access.md) med ett Azure Migrate-projekt.

- Du kan bara koppla en arbets yta i Azure Migrate projekt prenumerationen.
- Du kan koppla en befintlig arbets yta eller skapa en ny.
- Du ansluter arbets ytan första gången du ställer in beroende visualisering för en dator.
- Du kan bara koppla en arbets yta efter att datorer har identifierats i Azure Migrate projektet. Det kan du göra genom att konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md) eller [Hyper-V](how-to-set-up-appliance-hyper-v.md). Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering. [Läs mer](migrate-appliance.md).

Koppla en arbets yta enligt följande:

1. I **Azure Migrate: Server utvärdering**klickar du på **Översikt**. Om du inte har lagt till Server utvärderings verktyget, [gör du det först](how-to-assess.md).
2. I **Översikt**klickar du på nedpilen för att expandera **grunderna**.
3. I **OMS-arbetsytan**klickar du på **kräver konfiguration**.
4. I **Konfigurera arbets yta**anger du om du vill skapa en ny arbets yta eller Använd en befintlig:

    ![Lägg till arbets yta](./media/how-to-create-group-machine-dependencies/workspace.png)

    - När du har angett ett namn för en ny arbets yta kan du välja den [region](https://azure.microsoft.com/global-infrastructure/regions/) där arbets ytan ska skapas.
    - När du ansluter en befintlig arbets yta kan du välja mellan alla tillgängliga arbets ytor i samma prenumeration som migreringsjobbet.
    - Du måste ha Läs behörighet till arbets ytan för att kunna koppla den.
    - Du kan inte ändra arbets ytan som är kopplad till ett projekt när den har bifogats.

## <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna

Hämta och installera agenterna på varje lokal dator som du vill visualisera med beroende mappning.

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Klicka på **kräver agent installation**för varje dator som du vill använda beroende visualisering för.
3. På sidan **beroenden** för en dator > **Ladda ned och installera MMA**, laddar du ned lämplig agent och installerar den enligt beskrivningen nedan.
4. I **Ladda ned och installera beroende agent**laddar du ned lämplig agent och installerar den enligt beskrivningen nedan.
5. Under **Konfigurera MMA agent**, kopierar du arbetsyte-ID och nyckel. Du behöver dessa när du installerar MMA-agenten.

### <a name="install-the-mma"></a>Installera MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installera agenten på en Windows-dator

Så här installerar du agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
3. I **målmappen**, Behåll eller ändra standardmappen för installationen > **Nästa**.
4. I **installations alternativ för agent**väljer du **Azure Log Analytics** > **Nästa**.
5. Klicka på **Lägg** till för att lägga till en ny Log Analytics-arbetsyta. Klistra in det arbetsyte-ID och den nyckel som du kopierade från portalen. Klicka på **Next**.

Du kan installera agenten från kommando raden eller med en automatiserad metod som System Center Configuration Manager eller [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196). [Lär dig mer](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) om att använda dessa metoder för att installera MMA-agenten. MMA-agenten kan också installeras med detta [skript](https://go.microsoft.com/fwlink/?linkid=2104394).

[Läs mer](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) om de Windows-operativsystem som stöds av MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installera agenten på en Linux-dator

Så här installerar du agenten på en Linux-dator:

1. Överför lämpligt paket (x86 eller x64) till Linux-datorn med hjälp av SCP/SFTP.
2. Installera paketet med hjälp av argumentet--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Läs mer](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) om listan med stöd för Linux-operativsystem i MMA. 

### <a name="install-the-dependency-agent"></a>Installera beroendeagenten
1. Installera beroende agenten på en Windows-dator genom att dubbelklicka på installations filen och följa guiden.
2. Installera beroende agenten på en Linux-dator genom att installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

[Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) om hur du kan använda skript för att installera beroende agenten.

[Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) om de operativ system som stöds av beroende agenten.


## <a name="create-a-group-using-dependency-visualization"></a>Skapa en grupp med hjälp av beroende visualisering

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. I kolumnen **beroenden** klickar du på **Visa beroenden** för varje dator som du vill granska.
3. På beroende kartan kan du se följande:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar, till och från datorn.
    - Beroende datorer som inte har beroende agenter installerade grupperas efter port nummer.
    - Beroende datorer med installerade beroende agenter visas som separata rutor.
    - Processer som körs inuti datorn. Expandera alla dator rutor om du vill visa processerna.
    - Dator egenskaper (inklusive fullständigt domän namn, operativ system, MAC-adress). Klicka på varje dator box för att visa information.

4. Du kan titta på beroenden för olika tids perioder genom att klicka på varaktigheten för tids området. Som standard är intervallet en timme. Du kan ändra tidsintervallet, eller ange start-och slutdatum samt varaktighet.

    > [!NOTE]
    > Tidsintervallet kan vara upp till en timme. Om du behöver ett längre intervall använder du Azure Monitor för att köra frågor mot beroende data under en längre period.

5. När du har identifierat de beroende datorer som du vill gruppera tillsammans, använder du Ctrl + klicka för att markera flera datorer på kartan och klicka på **grupp datorer**.
6. Ange ett grupp namn.
7. Kontrol lera att de beroende datorerna upptäcks av Azure Migrate.

    - Om en beroende dator inte identifieras av Azure Migrate: Server utvärdering kan du inte lägga till den i gruppen.
    - Om du vill lägga till en dator kör du identifieringen igen och kontrollerar att datorn har identifierats.

8. Om du vill skapa en utvärdering för den här gruppen, markerar du kryss rutan för att skapa en ny utvärdering för gruppen.
8. Spara gruppen genom att klicka på **OK** .

När du har skapat gruppen rekommenderar vi att du installerar agenter på alla datorer i gruppen och sedan visualiserar beroenden för hela gruppen.

## <a name="query-dependency-data-in-azure-monitor"></a>Fråga beroende data i Azure Monitor

Du kan fråga beroende data som har registrerats av Tjänstkarta i arbets ytan Log Analytics som är kopplad till ditt Azure Migrate-projekt. Log Analytics används för att skriva och köra Azure Monitor logg frågor.

- [Lär dig hur du](../azure-monitor/insights/service-map.md#log-analytics-records) söker efter tjänstkarta data i Log Analytics.
- [Få en översikt](../azure-monitor/log-query/get-started-queries.md) över att skriva logg frågor i [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Kör en fråga för beroende data på följande sätt:

1. När du har installerat agenterna går du till portalen och klickar på **Översikt**.
2. I **Azure Migrate: Server utvärdering**klickar du på **Översikt**. Klicka på nedpilen för att expandera **grunderna**.
3. I **OMS-arbetsytan**klickar du på arbets ytans namn.
3. På sidan Log Analytics arbets yta > **Allmänt**klickar du på **loggar**.
4. Skriv din fråga och klicka på **Kör**.

### <a name="sample-queries"></a>Exempel frågor

Vi tillhandahåller ett antal exempel frågor som du kan använda för att extrahera beroende data.

- Du kan ändra frågorna för att extrahera önskade data punkter.
- [Granska](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) en fullständig lista över beroende data poster.
- [Granska](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) ytterligare exempel frågor.

#### <a name="sample-review-inbound-connections"></a>Exempel: granska inkommande anslutningar

Granska inkommande anslutningar för en uppsättning virtuella datorer.

- Posterna i tabellen för anslutnings mått (VMConnection) representerar inte enskilda fysiska nätverks anslutningar.
- Flera fysiska nätverks anslutningar är grupperade i en logisk anslutning.
- [Läs mer](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) om hur data för fysiska nätverks anslutningar sammanställs i VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Exempel: sammanfatta skickade och mottagna data

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
