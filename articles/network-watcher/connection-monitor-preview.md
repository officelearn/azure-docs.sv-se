---
title: Anslutningsövervakaren (förhandsversion) | Microsoft-dokument
description: Lär dig hur du använder Anslutningsövervakaren (förhandsversion) för att övervaka nätverkskommunikation i en distribuerad miljö.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: d926a9f686f0f4c39203b8a217a7c608cfad926e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548107"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Övervakning av nätverksanslutning med anslutningsövervakare (förhandsversion)

Anslutningsövervakaren (förhandsversion) ger enhetlig anslutningsövervakning från på sluten tid i Azure Network Watcher. Funktionen Anslutningsövervakaren (förhandsversion) stöder hybrid- och Azure-molndistributioner. Network Watcher innehåller verktyg för att övervaka, diagnostisera och visa anslutningsrelaterade mått för dina Azure-distributioner.

Här är några användningsfall för Anslutningsövervakaren (förhandsversion):

- Den virtuella datorn för frontend-webbservern kommunicerar med en virtuell databasserver i ett program på flera nivåer. Du vill kontrollera nätverksanslutningen mellan de två virtuella datorerna.
- Du vill att virtuella datorer i regionen östra USA ska pinga virtuella datorer i regionen Centrala USA och jämföra nätverksdämpningar mellan regioner.
- Du har flera lokala kontorsplatser i Seattle, Washington och i Ashburn, Virginia. Dina kontorswebbplatser ansluter till Office 365-url:er. För användare av Office 365-url:er kan du jämföra svarstiderna mellan Seattle och Ashburn.
- Ditt hybridprogram behöver anslutning till en Azure Storage-slutpunkt. Din lokala webbplats och ditt Azure-program ansluter till samma Azure Storage-slutpunkt. Du vill jämföra svaren på den lokala platsen med latenserna för Azure-programmet.
- Du vill kontrollera anslutningen mellan dina lokala inställningar och de virtuella Azure-datorerna som är värdar för ditt molnprogram.

I förhandsgranskningsfasen kombinerar Anslutningsövervakaren det bästa av två funktioner: funktionen [Anslutningsövervakare](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) för nätverksväxlare och [NPM-funktionen](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) (Network Performance Monitor).

Här är några fördelar med Anslutningsövervakaren (förhandsversion):

* Enhetlig, intuitiv upplevelse för Azure- och hybridövervakningsbehov
* Övervakning av anslutning mellan regioner och anslutning mellan olika regioner
* Högre sonderingsfrekvenser och bättre insyn i nätverkets prestanda
* Snabbare aviseringar för hybriddistributioner
* Stöd för anslutningskontroller som baseras på HTTP, TCP och ICMP 
* Mått och Log Analytics-stöd för både Azure- och icke-Azure-testinställningar

![Diagram som visar hur Anslutningsövervakaren interagerar med virtuella Azure-datorer, icke-Azure-värdar, slutpunkter och datalagringsplatser](./media/connection-monitor-2-preview/hero-graphic.png)

Så här börjar du använda Anslutningsövervakaren (förhandsversion) för övervakning: 

1. Installera övervakningsagenter.
1. Aktivera Network Watcher på din prenumeration.
1. Skapa en anslutningsövervakare.
1. Ställ in dataanalys och aviseringar.
1. Diagnostisera problem i nätverket.

I följande avsnitt finns information om dessa steg.

## <a name="install-monitoring-agents"></a>Installera övervakningsagenter

Anslutningsövervakaren är beroende av lättkörbara körbara filer för att köra anslutningskontroller.  Den stöder anslutningskontroller från både Azure-miljöer och lokala miljöer. Den körbara filen som du använder beror på om din virtuella dator finns på Azure eller lokalt.

### <a name="agents-for-azure-virtual-machines"></a>Agenter för virtuella Azure-datorer

Om du vill att Anslutningsövervakaren ska känna igen dina virtuella Azure-datorer som övervakningskällor installerar du tillägget Nätverksbevakningsagent på dem. Det här tillägget kallas även *Network Watcher-tillägget*. Virtuella Azure-datorer kräver tillägget för att utlösa heltäckande övervakning och andra avancerade funktioner. 

Du kan installera tillägget Network Watcher när du [skapar en virtuell dator.](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm) Du kan också installera, konfigurera och felsöka tillägget Network Watcher för [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) och [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)separat.

Regler för en nätverkssäkerhetsgrupp (NSG) eller brandvägg kan blockera kommunikationen mellan källan och målet. Anslutningsövervakaren upptäcker problemet och visar det som ett diagnostikmeddelande i topologin. Om du vill aktivera anslutningsövervakning kontrollerar du att NSG- och brandväggsregler tillåter paket över TCP eller ICMP mellan källan och målet.

### <a name="agents-for-on-premises-machines"></a>Agenter för lokala maskiner

Om du vill att Anslutningsövervakaren ska känna igen dina lokala datorer som källor för övervakning installerar du Log Analytics-agenten på datorerna. Aktivera sedan lösningen Network Performance Monitor. Dessa agenter är länkade till Log Analytics-arbetsytor, så du måste ställa in arbetsyte-ID och primärnyckel innan agenterna kan börja övervaka.

Information om hur du installerar Log Analytics-agenten för Windows-datorer finns i [Azure Monitor-tillägget för virtuella datorer för Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Om sökvägen innehåller brandväggar eller virtuella nätverksinstallationer (NVA) kontrollerar du att målet kan nås.

## <a name="enable-network-watcher-on-your-subscription"></a>Aktivera Network Watcher på din prenumeration

Alla prenumerationer som har ett virtuellt nätverk är aktiverade med Network Watcher. När du skapar ett virtuellt nätverk i din prenumeration aktiveras Network Watcher automatiskt i det virtuella nätverkets region och prenumeration. Den här automatiska aktiveringen påverkar inte dina resurser eller medför en debitering. Kontrollera att Network Watcher inte uttryckligen är inaktiverad på din prenumeration. 

Mer information finns i [Aktivera Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare 

Anslutningsövervakaren övervakar kommunikationen med jämna mellanrum. Den informerar dig om förändringar i räckvidd och latens. Du kan också kontrollera den aktuella och historiska nätverkstopologin mellan källagenter och målslutpunkter.

Källor kan vara virtuella Azure-datorer eller lokala datorer som har en installerad övervakningsagent. Målslutpunkter kan vara Office 365-URL:er, Dynamics 365-URL:er, anpassade URL:er, Azure VM-resurs-ID:n, IPv4, IPv6, FQDN eller valfritt domännamn.

### <a name="access-connection-monitor-preview"></a>Åtkomstanslutningsövervakare (förhandsgranskning)

1. Gå till **Network Watcher**på startsidan för Azure-portalen .
1. Välj **Anslutningsövervakaren (Förhandsgranska)** i avsnittet **Övervakning** till vänster .
1. Du ser alla anslutningsövervakare som har skapats i Anslutningsövervakaren (förhandsversion). Om du vill se de anslutningsövervakare som skapades i den klassiska upplevelsen av Anslutningsövervakaren går du till fliken **Anslutningsövervakare.**

    ![Skärmbild som visar anslutningsövervakare som har skapats i Anslutningsövervakaren (förhandsversion)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare

I anslutningsövervakare som du skapar i Anslutningsövervakaren (förhandsversion) kan du lägga till både lokala datorer och virtuella Azure-datorer som källor. Dessa anslutningsövervakare kan också övervaka anslutningen till slutpunkter. Slutpunkterna kan finnas på Azure eller någon annan URL eller IP.

Anslutningsövervakaren (förhandsversion) innehåller följande entiteter:

* **Anslutningsövervakarresurs** – En regionspecifik Azure-resurs. Alla följande entiteter är egenskaper för en anslutningsövervakarresurs.
* **Slutpunkt** – En källa eller ett mål som deltar i anslutningskontroller. Exempel på slutpunkter är virtuella Azure-datorer, lokala agenter, webbadresser och IPs.
* **Testkonfiguration** – En protokollspecifik konfiguration för ett test. Baserat på det protokoll du valde kan du definiera port, tröskelvärden, testfrekvens och andra parametrar.
* **Testgrupp** – Gruppen som innehåller källslutpunkter, målslutpunkter och testkonfigurationer. En anslutningsövervakare kan innehålla mer än en testgrupp.
* **Test** – Kombinationen av en källslutpunkt, målslutpunkt och testkonfiguration. Ett test är den mest detaljerade nivå där övervakningsdata är tillgängliga. Övervakningsdata omfattar procentandelen kontroller som misslyckades och rundresatiden (RTT).

 ![Diagram som visar en anslutningsövervakare, som definierar förhållandet mellan testgrupper och tester](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Skapa en anslutningsövervakare från Azure-portalen

Så här skapar du en anslutningsövervakare från Azure-portalen:

1. Välj **Skapa**i det övre vänstra hörnet på instrumentpanelen **för anslutningsövervakaren (förhandsgranska)** .
1. Ange information för anslutningsövervakaren på fliken **Grunderna:**
   * **Namn på anslutningsövervakaren** – Lägg till namnet på anslutningsövervakaren. Använd standardnamnordningarna för Azure-resurser.
   * **Prenumeration** – Välj en prenumeration för anslutningsövervakaren.
   * **Region** – Välj en region för anslutningsövervakaren. Du kan bara välja de virtuella källdrarna som skapas i det här området.
   * **Konfiguration av arbetsytan** - Arbetsytan innehåller dina övervakningsdata. Du kan använda en anpassad arbetsyta eller standardarbetsytan. 
       * Om du vill använda standardarbetsytan markerar du kryssrutan. 
       * Om du vill välja en anpassad arbetsyta avmarkerar du kryssrutan. Välj sedan prenumeration och region för din anpassade arbetsyta. 
1. Längst ned på fliken väljer du **Nästa: Testa grupper**.

   ![Skärmbild som visar fliken Grunderna i Anslutningsövervakaren](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Välj **+ Testgrupp**på fliken **Testgrupper** . Om du vill konfigurera testgrupperna finns i [Skapa testgrupper i Anslutningsövervakaren](#create-test-groups-in-a-connection-monitor). 
1. Längst ned på fliken väljer du **Nästa: Granska + skapa** för att granska anslutningsövervakaren.

   ![Skärmbild som visar fliken Testgrupper och fönstret där du lägger till information om testgrupp](./media/connection-monitor-2-preview/create-tg.png)

1. På fliken **Granska + skapa** granskar du den grundläggande informationen och testgrupperna innan du skapar anslutningsövervakaren. Om du behöver redigera anslutningsövervakaren:
   * Om du vill redigera grundläggande information markerar du pennikonen.
   * Om du vill redigera en testgrupp markerar du den.

   > [!NOTE] 
   > På fliken **Granska + skapa** visas kostnaden per månad under förhandsgranskningsfasen för anslutningsövervakaren. För närvarande visar kolumnen **AKTUELL KOSTNAD** ingen kostnad. När Anslutningsövervakaren blir allmänt tillgänglig visar den här kolumnen en månadsavgift. 
   > 
   > Även i förhandsversionen av Anslutningsövervakaren gäller avgifter för inmatning av Logganalys.

1. När du är redo att skapa anslutningsövervakaren väljer du **Skapa**längst ned på fliken **Granska + skapa** .

   ![Skärmbild av Anslutningsövervakaren, som visar fliken Granska + skapa](./media/connection-monitor-2-preview/review-create-cm.png)

Anslutningsövervakaren (förhandsversion) skapar anslutningsövervakarresursen i bakgrunden.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Skapa en anslutningsmonitor med ARMClient

Använd följande kod för att skapa en anslutningsmonitor med HJÄLP AV ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Här är distributionskommandot:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Skapa testgrupper i en anslutningsövervakare

Varje testgrupp i en anslutningsövervakare innehåller källor och mål som testas på nätverksparametrar. De testas för procentandelen kontroller som misslyckas och RTT över testkonfigurationer.

Om du vill skapa en testgrupp i en anslutningsövervakare i Azure-portalen anger du värden för följande fält:

* **Inaktivera testgrupp** – Du kan välja det här fältet om du vill inaktivera övervakning för alla källor och mål som testgruppen anger. Det här valet är avmarkerat som standard.
* **Namn** – Namnge din testgrupp.
* **Källor** – Du kan ange både virtuella Azure-datorer och lokala datorer som källor om agenter är installerade på dem. Om du vill installera en agent för källan finns i [Installera övervakningsagenter](#install-monitoring-agents).
   * Om du vill välja Azure-agenter väljer du fliken **Azure-agenter.** Här ser du bara virtuella datorer som är bundna till det område som du angav när du skapade anslutningsövervakaren. Som standard grupperas virtuella datorer i den prenumeration som de tillhör. Dessa grupper är kollapsade. 
   
       Du kan öka detaljnivån från prenumerationsnivån till andra nivåer i hierarkin:

      **Virtuella** > datorer för prenumerationsresursgrupper**Resource groups** > med agenter för**prenumerationsresurser** > **Subnets** > **VMs with agents**

      Du kan också ändra värdet för fältet **Gruppera efter** för att starta trädet från någon annan nivå. Om du till exempel**grupperar** > efter virtuellt nätverk visas de virtuella datorer som har agenter i hierarkin **virtuella** > **datorer med agenter**.

      ![Skärmbild av Anslutningsövervakaren, som visar panelen Lägg till källor och fliken Azure-agenter](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Om du vill välja lokala agenter väljer du fliken **Icke-Azure-agenter.** Som standard grupperas agenter i arbetsytor efter region. Alla dessa arbetsytor har lösningen Network Performance Monitor konfigurerad. 
   
       Om du behöver lägga till Network Performance Monitor på din arbetsyta kan du hämta den från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Information om hur du lägger till Network Performance Monitor finns [i Övervakningslösningar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       I vyn **Skapa anslutningsövervakare** är standardområdet markerat på fliken **Grunderna.** Om du ändrar regionen kan du välja agenter från arbetsytor i den nya regionen. Du kan också ändra värdet för fältet **Gruppera efter** för grupp efter undernät.

      ![Skärmbild av Anslutningsövervakaren som visar panelen Lägg till källor och fliken Icke-Azure-agenter](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Om du vill granska Azure- och icke-Azure-agenter som du har valt går du till fliken **Granska.**

      ![Skärmbild av Anslutningsövervakaren, som visar panelen Lägg till källor och fliken Granska](./media/connection-monitor-2-preview/review-sources.png)

   * När du är klar med att ställa in källor väljer du **Klar**längst ned på panelen **Lägg till källor** .

* **Destinationer** – Du kan övervaka anslutningen till virtuella Azure-datorer eller en slutpunkt (en offentlig IP, URL eller FQDN) genom att ange dem som mål. I en enda testgrupp kan du lägga till virtuella Azure-datorer, Office 365-URL:er, Dynamics 365-URL:er och anpassade slutpunkter.

    * Om du vill välja virtuella Azure-datorer som destinationer väljer du fliken **Virtuella Azure-datorer.** Som standard grupperas virtuella Azure-datorer i en prenumerationshierarki som finns i samma region som du valde i vyn **Skapa anslutningsövervakare** på fliken **Grunderna.** Du kan ändra regionen och välja Virtuella Azure-datorer från den nyligen valda regionen. Sedan kan du öka detaljnivån från prenumerationsnivå till andra nivåer i hierarkin, till exempel nivån Azure Agents.

       ![Skärmbild av fönstret Lägg till mål, som visar fliken Virtuella Azure-datorer](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Skärmbild av fönstret Lägg till mål, som visar prenumerationsnivån](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Om du vill välja slutpunkter som mål väljer du fliken **Slutpunkter.** Listan över slutpunkter innehåller Office 365-test-URL:er och Dynamics 365-test-URL:er, grupperade efter namn. Utöver dessa slutpunkter kan du välja en slutpunkt som har skapats i andra testgrupper i samma anslutningsövervakare. 
    
        Om du vill lägga till en ny slutpunkt väljer du **+ Slutpunkter**i det övre högra hörnet . Ange sedan ett slutpunktsnamn och ENS, IP eller FQDN.

       ![Skärmbild som visar var slutpunkter ska läggas till som mål i Anslutningsövervakaren](./media/connection-monitor-2-preview/add-endpoints.png)

    * Om du vill granska virtuella Azure-datorer och slutpunkter som du har valt väljer du fliken **Granska.**
    * När du är klar med att välja destinationer väljer du **Klar**.

* **Testkonfigurationer** – Du kan associera testkonfigurationer i en testgrupp. Azure-portalen tillåter bara en testkonfiguration per testgrupp, men du kan använda ARMClient för att lägga till fler.

    * **Namn** – Namnge testkonfigurationen.
    * **Protokoll** – Välj TCP, ICMP eller HTTP. Om du vill ändra HTTP till HTTPS väljer du **HTTP** som protokoll och väljer **443** som port.
        * **Skapa nätverkstestkonfiguration** – Den här kryssrutan visas bara om du markerar **HTTP** i **fältet Protokoll.** Markera den här rutan om du vill skapa en annan testkonfiguration som använder samma källor och mål som du angav någon annanstans i konfigurationen. Den nyskapade testkonfigurationen heter `<the name of your test configuration>_networkTestConfig`.
        * **Inaktivera traceroute** – Det här fältet gäller för testgrupper vars protokoll är TCP eller ICMP. Välj den här rutan om du vill hindra källor från att upptäcka topologi och hop-by-hop RTT.
    * **Målport** – Du kan anpassa det här fältet med valfri målport.
    * **Testfrekvens** – Använd det här fältet för att välja hur ofta källor ska pinga mål på det protokoll och den port som du har angett. Du kan välja 30 sekunder, 1 minut, 5 minuter, 15 minuter eller 30 minuter. Källor testar anslutningen till mål baserat på det värde du väljer.  Om du till exempel väljer 30 sekunder kontrollerar källorna anslutningen till målet minst en gång under en 30-sekundersperiod.
    * **Tröskelvärde för lyckades** – Du kan ange tröskelvärden för följande nätverksparametrar:
       * **Kontroller misslyckades** – Ange procentandelen kontroller som kan misslyckas när källor kontrollerar anslutningen till mål med hjälp av de kriterier som du angav. För TCP- eller ICMP-protokoll kan procentandelen misslyckade kontroller likställas med procentandelen paketförlust. För HTTP-protokollet representerar det här fältet procentandelen HTTP-begäranden som inte fick något svar.
       * **Tur** och retur – Ställ in RTT i millisekunder för hur lång tid det kan ta för källor att ansluta till målet över testkonfigurationen.
    
       ![Skärmbild som visar var en testkonfiguration ska konfigureras i Anslutningsövervakaren](./media/connection-monitor-2-preview/add-test-config.png)

Alla källor, mål och testkonfigurationer som du lägger till i en testgrupp delas upp i enskilda tester. Här är ett exempel på hur källor och destinationer bryts ned:

* Testgrupp: TG1
* Källor: 3 (A, B, C)
* Destinationer: 2 (D, E)
* Testkonfigurationer: 2 (Config 1, Config 2)
* Totalt antal tester skapade: 12

| Testnummer | Källa | Mål | Testkonfiguration |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 (Konfekt 2) |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 (Konfekt 2) |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 (Konfekt 2) |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 (Konfekt 2) |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 (Konfekt 2) |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 (Konfekt 2) |

### <a name="scale-limits"></a>Skalbegränsningar

Anslutningsövervakare har följande skalbegränsningar:

* Maximala anslutningsövervakare per prenumeration per region: 100
* Maximala testgrupper per anslutningsövervakare: 20
* Maximala källor och destinationer per anslutningsövervakare: 100
* Maximala testkonfigurationer per anslutningsövervakare: 
    * 20 via ARMClient
    * 2 via Azure-portalen

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analysera övervakningsdata och ställa in aviseringar

När du har skapat en anslutningsövervakare kontrollerar källor anslutningen till mål baserat på testkonfigurationen.

### <a name="checks-in-a-test"></a>Kontroller i ett test

Baserat på det protokoll som du valde i testkonfigurationen kör Anslutningsövervakaren (förhandsversion) en serie kontroller för käll-mål-paret. Kontrollerna körs enligt den testfrekvens som du har valt.

Om du använder HTTP beräknar tjänsten antalet HTTP-svar som returnerade en svarskod. Resultatet avgör procentandelen misslyckade kontroller. För att beräkna RTT mäter tjänsten tiden mellan ett HTTP-anrop och svaret.

Om du använder TCP eller ICMP beräknar tjänsten paketförlustprocenten för att fastställa procentandelen misslyckade kontroller. För att beräkna RTT mäter tjänsten den tid det tar att ta emot bekräftelsen (ACK) för paketen som skickades. Om du har aktiverat spårningsdata för nätverkstester kan du se hoppa-för-hopp-förlust och svarstid för det lokala nätverket.

### <a name="states-of-a-test"></a>Stater för ett test

Baserat på de data som kontrollerna returnerar kan testerna ha följande tillstånd:

* **Pass** – Faktiska värden för procentandelen misslyckade kontroller och RTT ligger inom de angivna tröskelvärdena.
* **Misslyckas** – Faktiska värden för procentandelen misslyckade kontroller eller RTT överskred de angivna tröskelvärdena. Om inget tröskelvärde anges når ett test tillståndet Underkänd när procentandelen misslyckade kontroller är 100.
* **Varning** – Inga villkor angavs för procentandelen misslyckade kontroller. I avsaknad av angivna villkor tilldelar Anslutningsövervakaren (förhandsversion) automatiskt ett tröskelvärde. När tröskelvärdet överskrids ändras teststatusen till Varning.

### <a name="data-collection-analysis-and-alerts"></a>Datainsamling, analys och varningar

De data som Samlas in i Anslutningsövervakaren lagras på log analytics-arbetsytan. Du ställer in den här arbetsytan när du skapade anslutningsövervakaren. 

Övervakningsdata är också tillgängligt i Azure Monitor Metrics. Du kan använda Log Analytics för att behålla dina övervakningsdata så länge du vill. Azure Monitor lagrar mått för endast 30 dagar som standard. 

Du kan [ange måttbaserade aviseringar på data](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Övervaka instrumentpaneler

På övervakningsinstrumentpanelerna visas en lista över de anslutningsövervakare som du kan komma åt för prenumerationer, regioner, tidsstämplar, källor och måltyper.

När du går till Anslutningsövervakaren (Förhandsversion) från Network Watcher kan du visa data efter:

* **Anslutningsövervakaren** – Lista över alla anslutningsövervakare som skapats för prenumerationer, regioner, tidsstämplar, källor och måltyper. Den här vyn är standard.
* **Testgrupper** – Lista över alla testgrupper som skapats för prenumerationer, regioner, tidsstämplar, källor och måltyper. Dessa testgrupper filtreras inte av anslutningsövervakare.
* **Test** – Lista över alla tester som körs för prenumerationer, regioner, tidsstämplar, källor och måltyper. Dessa tester filtreras inte av anslutningsövervakare eller testgrupper.

I följande bild visas de tre datavyerna med pil 1.

På instrumentpanelen kan du expandera varje anslutningsövervakare för att se dess testgrupper. Sedan kan du expandera varje testgrupp för att se de tester som körs i den. 

Du kan filtrera en lista baserat på:

* **Filter på översta nivån** – Välj prenumerationer, regioner, tidsstämpelkällor och måltyper. Se ruta 2 i följande bild.
* **Tillståndsbaserade filter** – Filtrera efter anslutningsövervakarens, testgruppens eller testets tillstånd. Se pil 3 i följande bild.
* **Anpassade filter** – Välj **Markera alla** för att göra en allmän sökning. Om du vill söka efter en viss entitet väljer du i listrutan. Se pil 4 i följande bild.

![Skärmbild som visar hur du filtrerar vyer av anslutningsövervakare, testgrupper och tester i Anslutningsövervakaren (förhandsversion)](./media/connection-monitor-2-preview/cm-view.png)

Om du till exempel vill titta på alla tester i Anslutningsövervakaren (förhandsversion) där käll-IP är 10.192.64.56:
1. Ändra vyn till **Testa**.
1. Skriv *10.192.64.56* I sökfältet
1. Välj **Källor**i listrutan .

Så här visar du bara misslyckade tester i Anslutningsövervakaren (förhandsversion) där käll-IP är 10.192.64.56:
1. Ändra vyn till **Testa**.
1. För det tillståndsbaserade filtret väljer du **Misslyckas**.
1. Skriv *10.192.64.56* I sökfältet
1. Välj **Källor**i listrutan .

Så här visar du bara misslyckade tester i Anslutningsövervakaren (förhandsversion) där målet är outlook.office365.com:
1. Ändra vyn till **Testa**.
1. För det tillståndsbaserade filtret väljer du **Misslyckas**.
1. Ange *outlook.office365.com i* sökfältet
1. Välj **Mål**i listrutan .

   ![Skärmbild som visar en vy som filtreras för att bara visa misslyckade tester för Outlook.Office365.com mål](./media/connection-monitor-2-preview/tests-view.png)

Så här visar du trenderna i RTT och procentandelen misslyckade kontroller för en anslutningsövervakare:
1. Välj den anslutningsövervakare som du vill undersöka. Som standard ordnas övervakningsdata efter testgrupp.

   ![Skärmbild som visar mått för en anslutningsövervakare, visad efter testgrupp](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Välj den testgrupp som du vill undersöka.

   ![Skärmbild som visar var du vill välja en testgrupp](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Du ser testgruppens fem misslyckade tester, baserat på RTT eller procentandelen misslyckade kontroller. För varje test visas RTT- och trendlinjerna för procentandelen misslyckade kontroller.
1. Välj ett test i listan eller välj ett annat test att undersöka. För tidsintervallet och procentandelen misslyckade kontroller visas tröskelvärden och faktiska värden. För RTT visas värdena för tröskelvärde, medelvärde, minimum och maximum.

   ![Skärmbild som visar ett tests resultat för RTT och procentandel misslyckade kontroller](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Ändra tidsintervallet om du vill visa mer data.
1. Ändra vyn om du vill visa källor, mål eller testkonfigurationer. 
1. Välj en källa baserat på misslyckade tester och undersök de fem misslyckade testerna. Välj till exempel **Visa** > **efter källor** och Visa **efter** > **mål** för att undersöka relevanta tester i anslutningsövervakaren.

   ![Skärmbild som visar prestandamått för de fem bästa misslyckade testerna](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Så här visar du trenderna i RTT och procentandelen misslyckade kontroller för en testgrupp:

1. Välj den testgrupp som du vill undersöka. 

    Som standard ordnas övervakningsdata av källor, mål och testkonfigurationer (tester). Senare kan du ändra vyn från testgrupper till källor, mål eller testkonfigurationer. Välj sedan en entitet för att undersöka de fem misslyckade testerna. Ändra till exempel vyn till källor och mål för att undersöka relevanta tester i den valda anslutningsövervakaren.
1. Välj det test som du vill undersöka.

   ![Skärmbild som visar var ett test ska väljas](./media/connection-monitor-2-preview/tg-drill.png)

    För tidsintervallet och för din procentandel misslyckade kontroller visas tröskelvärden och faktiska värden. För RTT visas värden för tröskelvärde, medelvärde, minimum och maximum. Du ser också avfyrade aviseringar för testet som du har valt.
1. Ändra tidsintervallet om du vill visa mer data.

Så här visar du trenderna i RTT och procentandelen misslyckade kontroller för ett test:
1. Välj den käll-, mål- och testkonfiguration som du vill undersöka.

    För tidsintervallet och för procentandelen misslyckade kontroller visas tröskelvärden och faktiska värden. För RTT visas värden för tröskelvärde, medelvärde, minimum och maximum. Du ser också avfyrade aviseringar för testet som du har valt.

   ![Skärmbild som visar mått för ett test](./media/connection-monitor-2-preview/test-drill.png)

1. Om du vill visa nätverkstopologin väljer du **Topologi**.

   ![Skärmbild som visar fliken Nätverkstopologi](./media/connection-monitor-2-preview/test-topo.png)

1. Om du vill se de identifierade problemen väljer du ett hopp i sökvägen i topologin. (Dessa hopp är Azure-resurser.) Den här funktionen är för närvarande inte tillgänglig för lokala nätverk.

   ![Skärmbild som visar en markerad hopplänk på fliken Topologi](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Logga frågor i Logganalys

Använd Log Analytics för att skapa anpassade vyer av dina övervakningsdata. Alla data som användargränssnittet visar kommer från Log Analytics. Du kan interaktivt analysera data i databasen. Korrelera data från Agent Health eller andra lösningar som är baserade i Log Analytics. Exportera data till Excel eller Power BI eller skapa en delningsbar länk.

#### <a name="metrics-in-azure-monitor"></a>Mått i Azure Monitor

I anslutningsövervakare som skapades före upplevelsen av anslutningsövervakaren (förhandsversionen) är alla fyra mått tillgängliga: % Avsökningar misslyckades, AverageRoundtripMs, ChecksFailedPercent (Preview) och RoundTripTimeMs (Preview). I anslutningsövervakare som har skapats i upplevelsen av anslutningsövervakaren (förhandsversion) är data endast tillgängliga för de mått som taggas med *(förhandsgranska)*.

![Skärmbild som visar mätvärden i Anslutningsövervakaren (förhandsgranskning)](./media/connection-monitor-2-preview/monitor-metrics.png)

När du använder mått anger du resurstypen som Microsoft.NetworkWatchers/connectionMonitors

| Mått | Visningsnamn | Enhet | Sammansättningstyp | Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| SonderFailedPercent | % avsökningar misslyckades | Procent | Medel | Procentandel av avsökningarna för anslutningsövervakning misslyckades. | Inga dimensioner |
| AverageRoundtripMs | Genomsnittlig tid tur och retur (ms) | Millisekunder | Medel | Genomsnittlig nätverks-RTT för anslutningsövervakning avsökningar som skickas mellan källa och mål. |             Inga dimensioner |
| ChecksFailedPercent (förhandsgranskning) | % misslyckades (förhandsgranskning) | Procent | Medel | Procentandel misslyckade kontroller för ett test. | ConnectionMonitorResourceId <br>KällaAdress <br>SourceName (SourceName) <br>KällaKällaId <br>SourceType <br>Protokoll <br>DestinationAdress <br>Målnamn <br>DestinationResourceId <br>Måltyp <br>DestinationPort (DestinationPort) <br>TestGroupName <br>TestKonfigurationName <br>Region |
| RoundTripTimeM (förhandsgranskning) | Tid för tur och retur (ms) (förhandsgranskning) | Millisekunder | Medel | RTT för kontroller som skickas mellan källa och destination. Det här värdet är inte medelvärde. | ConnectionMonitorResourceId <br>KällaAdress <br>SourceName (SourceName) <br>KällaKällaId <br>SourceType <br>Protokoll <br>DestinationAdress <br>Målnamn <br>DestinationResourceId <br>Måltyp <br>DestinationPort (DestinationPort) <br>TestGroupName <br>TestKonfigurationName <br>Region |

#### <a name="metric-alerts-in-azure-monitor"></a>Måttaviseringar i Azure Monitor

Så här skapar du en avisering i Azure Monitor:

1. Välj den anslutningsövervakarresurs som du skapade i Anslutningsövervakaren (förhandsversion).
1. Se till att **Måttet** visas som signaltyp för anslutningsövervakaren.
1. Välj **ChecksFailedPercent(Preview)** eller **RoundTripTimeMs(Preview)** för **signalnamn**i Lägg **till**villkor.
1. För **signaltyp**väljer du **Mått**. Välj till exempel **ChecksFailedPercent(Preview)**.
1. Alla dimensioner för måttet visas. Välj dimensionsnamn och dimensionsvärde. Välj till exempel **Källadress** och ange sedan IP-adressen för en källa i anslutningsövervakaren.
1. Fyll i följande i **Varningslogik:**
   * **Villkorstyp**: **Statisk**.
   * **Villkor** och **tröskel**.
   * **Aggregering Granularitet och frekvens för utvärdering:** Anslutningsövervakaren (Förhandsgranska) uppdaterar data varje minut.
1. Välj din åtgärdsgrupp i **Åtgärder.**
1. Ange varningsinformation.
1. Skapa aviseringsregeln.

   ![Skärmbild som visar området Skapa regel i Azure Monitor. "Källadress" och "Källslutpunktsnamn" är markerade](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnostisera problem i nätverket

Anslutningsövervakaren (förhandsversion) hjälper dig att diagnostisera problem i anslutningsövervakaren och nätverket. Problem i hybridnätverket identifieras av Log Analytics-agenterna som du installerade tidigare. Problem i Azure identifieras av network watcher-tillägget. 

Du kan visa problem i Azure-nätverket i nätverkstopologin.

För nätverk vars källor är lokala virtuella datorer kan följande problem identifieras:

* Tidssstr för begäran.
* Slutpunkten har inte lösts av DNS – tillfälligt eller beständigt. URL ogiltig.
* Inga värdar hittades.
* Det går inte att ansluta till målet. Målet kan inte nås via ICMP.
* Certifikatrelaterade frågor: 
    * Klientcertifikat krävs för att autentisera agenten. 
    * Listan för omplacering av certifikat är inte tillgänglig. 
    * Slutpunktens värdnamn matchar inte certifikatets ämnes- eller ämnesparvikingnamn. 
    * Rotcertifikat saknas i källans arkiv för betrodda certifikatutfärdare för lokala datorer. 
    * SSL-certifikatet har upphört att gälla, ogiltigt, återkallats eller inkompatibelt.

För nätverk vars källor är virtuella Azure-datorer kan följande problem identifieras:

* Agent frågor:
    * Agenten stannade.
    * Det gick inte att lösa DNS.
    * Inget program eller lyssnare lyssnar på målporten.
    * Socket kunde inte öppnas.
* Problem med vm-tillstånd: 
    * Startar
    * Stoppas
    * Stoppad
    * Frigör
    * Frigjord
    * Omstart
    * Ej allokerad
* ARP-tabellpost saknas.
* Trafiken blockerades på grund av lokala brandväggsproblem eller NSG-regler.
* Problem med den virtuella nätverksgatewayen: 
    * Saknade rutter.
    * Tunneln mellan två gateways är frånkopplad eller saknas.
    * Den andra porten hittades inte vid tunneln.
    * Ingen peering-information hittades.
* Route saknades i Microsoft Edge.
* Trafiken stoppas på grund av systemvägar eller UDR.
* BGP är inte aktiverat på gateway-anslutningen.
* DIP-sonden är nere vid belastningsutjämnaren.
