---
title: Anslutnings övervakare (för hands version) | Microsoft Docs
description: Lär dig hur du använder anslutnings övervakaren (för hands version) för att övervaka nätverkskommunikation i en distribuerad miljö.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 52d33e7292ebe7b27eede2b89aa605780f826392
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737622"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Övervakning av nätverks anslutning med anslutnings övervakare (för hands version)

Anslutnings övervakaren (för hands version) tillhandahåller enhetlig anslutnings övervakning från slut punkt till slut punkt i Azure Network Watcher. Funktionen anslutnings övervakare (för hands version) stöder hybrid-och Azure Cloud-distributioner. Network Watcher innehåller verktyg för att övervaka, diagnostisera och Visa anslutnings relaterade mått för dina Azure-distributioner.

Här är några användnings fall för anslutnings övervakaren (för hands version):

- Din frontend-webbserver för webb servern kommunicerar med en virtuell databas Server-dator i ett program med flera nivåer. Du vill kontrol lera nätverks anslutningen mellan de två virtuella datorerna.
- Du vill att virtuella datorer i regionen USA, östra, ska pinga virtuella datorer i den centrala regionen och du vill jämföra nätverks fördröjningar i flera regioner.
- Du har flera lokala kontors platser i Seattle, Washington och Ashburn, Virginia. Dina Office-platser ansluter till Office 365-URL: er. Jämför fördröjningen mellan Seattle och Ashburn för dina användare av Office 365-URL: er.
- Ditt hybrid program behöver anslutning till en Azure Storage-slutpunkt. Din lokala plats och ditt Azure-program ansluter till samma Azure Storage slut punkt. Du vill jämföra fördröjningen för den lokala platsen med fördröjningen i Azure-programmet.
- Du vill kontrol lera anslutningen mellan dina lokala installationer och de virtuella Azure-datorer som är värdar för moln programmet.

I förhands gransknings fasen kombinerar anslutnings övervakaren det bästa av två funktioner: funktionen Network Watcher [anslutnings övervakare](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) och funktionen övervakare av NÄTVERKSPRESTANDA (NPM) [tjänst anslutnings övervakare](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) .

Här följer några fördelar med anslutnings övervakaren (för hands version):

* Enhetlig, intuitiv upplevelse för Azure och hybrid övervaknings behov
* Övervakning över flera regioner och anslutningar mellan arbets ytor
* Högre avsöknings frekvens och bättre insyn i nätverks prestanda
* Snabbare aviseringar för dina hybrid distributioner
* Stöd för anslutnings kontroller som baseras på HTTP, TCP och ICMP 
* Mått och Log Analytics stöd för både Azure-test och icke-Azure-testkonfigurationer

![Diagram över hur anslutnings övervakaren interagerar med virtuella Azure-datorer, icke-Azure-värdar, slut punkter och data lagrings platser](./media/connection-monitor-2-preview/hero-graphic.png)

Börja använda anslutnings övervakaren (för hands version) för övervakning genom att följa dessa steg: 

1. Installera övervaknings agenter.
1. Aktivera Network Watcher på din prenumeration.
1. Skapa en anslutnings övervakare.
1. Konfigurera data analys och aviseringar.
1. Diagnostisera problem i nätverket.

I följande avsnitt finns information om de här stegen.

## <a name="install-monitoring-agents"></a>Installera övervaknings agenter

Anslutnings övervakaren förlitar sig på enkla körbara filer för att köra anslutnings kontroller.  Det stöder anslutnings kontroller från både Azure-miljöer och lokala miljöer. Vilken körbar fil du använder beror på om din virtuella dator finns på Azure eller lokalt.

### <a name="agents-for-azure-virtual-machines"></a>Agenter för virtuella Azure-datorer

Om du vill att anslutnings övervakaren ska identifiera dina virtuella Azure-datorer som övervaknings källor installerar du tillägget Network Watcher agent virtuell dator. Tillägget kallas även *Network Watcher-tillägget*. Virtuella Azure-datorer kräver tillägget för att utlösa övervakning från slut punkt till slut punkt och andra avancerade funktioner. 

Du kan installera Network Watcher-tillägget när du [skapar en virtuell dator](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Du kan också separat installera, konfigurera och felsöka Network Watcher-tillägget för [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) och [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Regler för en nätverks säkerhets grupp (NSG) eller brand vägg kan blockera kommunikation mellan källan och målet. Anslutnings övervakaren identifierar det här problemet och visar det som ett diagnostiskt meddelande i topologin. Om du vill aktivera anslutnings övervakning kontrollerar du att NSG-och brand Väggs reglerna tillåter paket över TCP eller ICMP mellan källan och målet.

### <a name="agents-for-on-premises-machines"></a>Agenter för lokala datorer

Om du vill att anslutnings övervakaren ska identifiera dina lokala datorer som källor för övervakning installerar du Log Analytics agent på datorerna. Aktivera sedan Övervakare av nätverksprestanda-lösningen. Dessa agenter är länkade till Log Analytics arbets ytor, så du måste konfigurera arbetsyte-ID och primär nyckel innan agenterna kan börja övervaka.

För att installera Log Analytics agent för Windows-datorer, se [Azure Monitor tillägg för virtuell dator för Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Om sökvägen omfattar brand väggar eller virtuella nätverks installationer (NVA) kontrollerar du att målet kan kontaktas.

## <a name="enable-network-watcher-on-your-subscription"></a>Aktivera Network Watcher på din prenumeration

Alla prenumerationer som har ett virtuellt nätverk har Aktiver ATS med Network Watcher. När du skapar ett virtuellt nätverk i din prenumeration aktive ras Network Watcher automatiskt i det virtuella nätverkets region och prenumeration. Den här automatiska aktiveringen påverkar inte dina resurser eller debiteras. Se till att Network Watcher inte uttryckligen har inaktiverats för din prenumeration. 

Mer information finns i [aktivera Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare 

Anslutnings övervakaren övervakar kommunikation med jämna mellanrum. Du informerar om ändringar av tillgängligheten och svars tiden. Du kan också kontrol lera den aktuella och historiska nätverks sto pol Ogin mellan käll agenter och slut punkter för mål.

Källor kan vara virtuella Azure-datorer eller lokala datorer som har en installerad övervaknings agent. Mål slut punkter kan vara Office 365-URL: er, Dynamics 365 URL: er, anpassade URL: er, resurs-ID: n för Azure VM, IPv4, IPv6, FQDN eller något annat domän namn.

### <a name="access-connection-monitor-preview"></a>Övervakaren åtkomst anslutning (för hands version)

1. På Azure Portal start sida går du till **Network Watcher**.
1. Till vänster i avsnittet **övervakning** väljer du **anslutnings övervakare (för hands version)**.
1. Du ser alla anslutnings Övervakare som skapades i anslutnings övervakaren (förhands granskning). Om du vill se de anslutnings Övervakare som skapades i den klassiska upplevelsen av anslutnings övervakaren går du till fliken **anslutnings övervakare** .

    ![Skärm bild som visar anslutnings Övervakare som har skapats i anslutnings övervakaren (förhands granskning)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare

I anslutnings Övervakare som du skapar i anslutnings övervakaren (för hands version) kan du lägga till både lokala datorer och virtuella Azure-datorer som källor. Dessa anslutnings övervakare kan också övervaka anslutningar till slut punkter. Slut punkterna kan vara på Azure eller någon annan URL eller IP-adress.

Anslutnings övervakaren (för hands version) innehåller följande entiteter:

* **Anslutnings övervaknings resurs** – en regions-/regionsspecifika Azure-resurs. Alla följande entiteter är egenskaper för en anslutnings övervaknings resurs.
* **Slut punkt** – en källa eller ett mål som deltar i anslutnings kontroller. Exempel på slut punkter är virtuella Azure-datorer, lokala agenter, URL: er och IP-adresser.
* **Test konfiguration** – en plattformsspecifik konfiguration för ett test. Utifrån det protokoll du väljer kan du definiera port, tröskelvärden, test frekvens och andra parametrar.
* **Test grupp** – gruppen som innehåller käll slut punkter, mål slut punkter och testkonfigurationer. En anslutnings övervakare kan innehålla fler än en test grupp.
* **Test** – kombinationen av en käll slut punkt, mål slut punkt och test konfiguration. Ett test är den mest detaljerade nivån där övervaknings data är tillgängliga. Övervaknings data innehåller procent andelen kontroller som misslyckades och tur och retur-tiden.

 ![Diagram som visar en anslutnings Övervakare som definierar relationen mellan test grupper och tester](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Skapa en anslutnings övervakare från Azure Portal

Följ dessa steg om du vill skapa en anslutnings övervakare från Azure Portal:

1. På instrument panelen för **anslutnings övervakaren (förhands granskning)** i det övre vänstra hörnet väljer du **skapa**.
1. På fliken **grundläggande** anger du information för anslutnings övervakaren:
   * **Namn på anslutnings övervakare** – Lägg till namnet på anslutnings övervakaren. Använd standard namngivnings reglerna för Azure-resurser.
   * **Prenumeration** – Välj en prenumeration för anslutnings övervakaren.
   * **Region** – Välj en region för anslutnings övervakaren. Du kan bara välja de virtuella käll datorer som skapas i den här regionen.
   * **Konfiguration av arbets yta** – din arbets yta innehåller dina övervaknings data. Du kan använda en anpassad arbets yta eller standard arbets ytan. 
       * Markera kryss rutan om du vill använda standard arbets ytan. 
       * Om du vill välja en anpassad arbets yta avmarkerar du kryss rutan. Välj sedan prenumeration och region för din anpassade arbets yta. 
1. Längst ned på fliken väljer du **Nästa: test grupper**.

   ![Skärm bild som visar fliken grundläggande i anslutnings övervakaren](./media/connection-monitor-2-preview/create-cm-basics.png)

1. På fliken **test grupper** väljer du **+ test grupp**. Information om hur du konfigurerar test grupper finns i [skapa test grupper i anslutnings övervakaren](#create-test-groups-in-a-connection-monitor). 
1. Längst ned på fliken väljer du **Nästa: granska + skapa** för att granska anslutnings övervakaren.

   ![Skärm bild som visar fliken test grupper och fönstret där du lägger till test grupps information](./media/connection-monitor-2-preview/create-tg.png)

1. På fliken **Granska + skapa** granskar du grundläggande information och test grupper innan du skapar anslutnings övervakaren. Om du behöver redigera anslutnings övervakaren:
   * Om du vill redigera grundläggande information väljer du Penn ikonen.
   * Om du vill redigera en test grupp väljer du den.

   > [!NOTE] 
   > På fliken **Granska + skapa** visas kostnaden per månad under förhands gransknings fasen för anslutnings övervakaren. För närvarande visar kolumnen **aktuell kostnad** ingen avgift. När anslutnings övervakaren blir allmänt tillgänglig visas en månatlig avgift i den här kolumnen. 
   > 
   > Även i förhands gransknings fasen av anslutnings övervakaren gäller Log Analytics inmatnings avgifter.

1. När du är redo att skapa anslutnings övervakaren väljer du **skapa**längst ned på fliken **Granska + skapa** .

   ![Skärm bild av anslutnings övervakaren som visar fliken Granska + skapa](./media/connection-monitor-2-preview/review-create-cm.png)

Anslutnings övervakaren (för hands version) skapar anslutnings övervaknings resursen i bakgrunden.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Skapa en anslutnings övervakare med hjälp av ARMClient

Använd följande kod för att skapa en anslutnings övervakare med hjälp av ARMClient.

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

Här är distributions kommandot:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Skapa test grupper i en anslutnings övervakare

Varje test grupp i en anslutnings övervakare innehåller källor och mål som testas på nätverks parametrar. De testas för procent andelen kontroller som inte kan utföras och sökning efter testkonfigurationer.

Skapa en test grupp i en anslutnings övervakare genom att ange värden för följande fält från Azure Portal:

* **Inaktivera test grupp** – du kan välja det här fältet om du vill inaktivera övervakning för alla källor och mål som test gruppen anger. Detta val rensas som standard.
* **Namn** – ge test gruppen ett namn.
* **Källor** – du kan ange både virtuella Azure-datorer och lokala datorer som källor om agenter är installerade på dem. Information om hur du installerar en agent för din källa finns i [Installera övervaknings agenter](#install-monitoring-agents).
   * Om du vill välja Azure-agenter väljer du fliken **Azure-agenter** . Här ser du bara virtuella datorer som är kopplade till den region som du angav när du skapade anslutnings övervakaren. Som standard grupperas de virtuella datorerna i den prenumeration som de tillhör. De här grupperna är komprimerade. 
   
       Du kan öka detalj nivån från prenumerations nivån till andra nivåer i hierarkin:

      **Prenumeration**  >  **Resurs grupper**  >  **Virtuella nätverk**  >  **Undernät**  >  **Virtuella datorer med agenter**

      Du kan också ändra värdet för fältet **Gruppera efter** för att starta trädet från en annan nivå. Om du till exempel grupperar efter virtuellt nätverk ser du de virtuella datorer som har agenter i hierarkin **virtuella nätverk**  >  **undernät**  >  **med agenter**.

      ![Skärm bild av anslutnings övervakaren, som visar panelen Lägg till källor och fliken Azure-agenter](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Välj fliken **ej – Azure-agenter** om du vill välja lokala agenter. Som standard grupperas agenter i arbets ytor efter region. Den Övervakare av nätverksprestanda-lösningen har kon figurer ATS för alla dessa arbets ytor. 
   
       Om du behöver lägga till Övervakare av nätverksprestanda i din arbets yta kan du hämta den från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Information om hur du lägger till Övervakare av nätverksprestanda finns [i övervaknings lösningar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       I vyn **skapa anslutnings övervakare** , på fliken **grundläggande** , väljs standard region. Om du ändrar region kan du välja agenter från arbets ytor i den nya regionen. Du kan också ändra värdet för fältet **Gruppera efter** för att gruppera efter undernät.

      ![Skärm bild av anslutnings övervakaren, som visar panelen Lägg till källor och fliken icke-Azure-agenter](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Om du vill granska de Azure-och icke-Azure-agenter som du har valt går du till fliken **Granska** .

      ![Skärm bild av anslutnings övervakaren, som visar panelen Lägg till källor och fliken Granska](./media/connection-monitor-2-preview/review-sources.png)

   * När du är klar med att konfigurera källor går du till slutet av panelen **Lägg till källor** och väljer **klar**.

* **Destinationer** – du kan övervaka anslutningar till virtuella Azure-datorer eller en slut punkt (en offentlig IP-adress, URL eller FQDN) genom att ange dem som mål. I en enda test grupp kan du lägga till virtuella Azure-datorer, Office 365-URL: er, Dynamics 365-URL: er och anpassade slut punkter.

    * Välj fliken **virtuella Azure-datorer** om du vill välja virtuella Azure-datorer som mål. Som standard grupperas de virtuella Azure-datorerna i en prenumerationsbegäran i samma region som du valde i vyn **skapa anslutnings övervakare** på fliken **grundläggande** . Du kan ändra region och välja virtuella Azure-datorer från den nyligen valda regionen. Sedan kan du öka detalj nivån från prenumerations nivån till andra nivåer i hierarkin, t. ex. Azure agents-nivån.

       ![Skärm bild av fönstret Lägg till destinationer, som visar fliken virtuella Azure-datorer](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Skärm bild av fönstret Lägg till destinationer som visar prenumerations nivån](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Välj fliken **slut punkter** om du vill välja slut punkter som mål. I listan över slut punkter ingår Office 365-test-URL: er och Dynamics 365-test-URL: er, grupperat efter namn. Förutom dessa slut punkter kan du välja en slut punkt som skapades i andra test grupper i samma anslutnings övervakare. 
    
        Om du vill lägga till en ny slut punkt går du till det övre högra hörnet och väljer **+ slut punkter**. Ange sedan ett slut punkts namn och en URL, IP eller FQDN.

       ![Skärm bild som visar var du kan lägga till slut punkter som mål i anslutnings övervakaren](./media/connection-monitor-2-preview/add-endpoints.png)

    * Om du vill granska de virtuella Azure-datorer och slut punkter som du har valt väljer du fliken **Granska** .
    * När du är klar med att välja mål väljer du **klar**.

* **Testa konfigurationer** – du kan koppla testkonfigurationer i en test grupp. Azure Portal tillåter endast en test konfiguration per test grupp, men du kan använda ARMClient för att lägga till fler.

    * **Namn** – ge test konfigurationen ett namn.
    * **Protokoll** – Välj TCP, ICMP eller http. Om du vill ändra HTTP till HTTPS väljer du **http** som protokoll och väljer **443** som port.
        * **Skapa nätverks test konfiguration** – den här kryss rutan visas bara om du väljer **http** i fältet **protokoll** . Markera den här rutan om du vill skapa en annan test konfiguration som använder samma källor och mål som du har angett någon annan stans i konfigurationen. Den nyligen skapade test konfigurationen heter `<the name of your test configuration>_networkTestConfig` .
        * **Inaktivera traceroute** – det här fältet gäller för test grupper vars protokoll är TCP eller ICMP. Markera den här rutan om du vill stoppa källor från att upptäcka topologi och hitta hopp efter hopp.
    * **Målport** – du kan anpassa det här fältet med en valfri mål Port.
    * **Test frekvens** – Använd det här fältet för att välja hur ofta källor ska pinga mål på det protokoll och den port som du har angett. Du kan välja 30 sekunder, 1 minut, 5 minuter, 15 minuter eller 30 minuter. Källorna kommer att testa anslutningen till destinationer utifrån det värde som du väljer.  Om du till exempel väljer 30 sekunder kommer källorna att kontrol lera anslutningen till målet minst en gång under en 30-sekunders period.
    * **Tröskelvärde för lyckad** – du kan ange tröskelvärden för följande nätverks parametrar:
       * **Misslyckade kontroller** – ange procent andelen kontroller som kan Miss lyckas när källor kontrollerar anslutningen till destinationer genom att använda de villkor som du har angett. Procent andelen misslyckade kontroller i TCP-eller ICMP-protokollet kan likställas med procent andelen av paket förlusten. För HTTP-protokoll representerar det här fältet den procent andel av HTTP-begäranden som inte fick något svar.
       * **Tur och retur-tid** – Ställ in efter hur lång tid det tar att ansluta till målet via test konfigurationen.
    
       ![Skärm bild som visar var du konfigurerar en test konfiguration i anslutnings övervakaren](./media/connection-monitor-2-preview/add-test-config.png)

Alla källor, destinationer och testkonfigurationer som du lägger till i en test grupp får delats upp till enskilda tester. Här är ett exempel på hur källor och mål är uppdelade:

* Test grupp: TG1
* Källor: 3 (A, B, C)
* Mål: 2 (D, E)
* Testa konfigurationer: 2 (Konfig 1, konfig 2)
* Totalt antal tester som skapats: 12

| Test nummer | Källa | Mål | Testa konfiguration |
| --- | --- | --- | --- |
| 1 | A | D | Konfiguration 1 |
| 2 | A | D | Konfig 2 |
| 3 | A | E | Konfiguration 1 |
| 4 | A | E | Konfig 2 |
| 5 | B | D | Konfiguration 1 |
| 6 | B | D | Konfig 2 |
| 7 | B | E | Konfiguration 1 |
| 8 | B | E | Konfig 2 |
| 9 | C | D | Konfiguration 1 |
| 10 | C | D | Konfig 2 |
| 11 | C | E | Konfiguration 1 |
| 12 | C | E | Konfig 2 |

### <a name="scale-limits"></a>Skalnings gränser

Anslutnings övervakare har följande skalnings gränser:

* Högsta antal anslutnings övervakare per prenumeration per region: 100
* Högsta antal test grupper per anslutnings Övervakare: 20
* Högsta antal källor och mål per anslutnings Övervakare: 100
* Högsta antal testkonfigurationer per anslutnings Övervakare: 
    * 20 via ARMClient
    * 2 via Azure Portal

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analysera övervaknings data och ange aviseringar

När du har skapat en anslutnings övervakare kontrollerar källorna anslutningen till destinationer baserat på din test konfiguration.

### <a name="checks-in-a-test"></a>Checkar in ett test

Baserat på det protokoll som du valde i test konfigurationen kör anslutnings övervakaren (för hands version) en serie kontroller för käll mål paret. Kontrollerna körs enligt den test frekvens som du har valt.

Om du använder HTTP beräknar tjänsten antalet HTTP-svar som returnerade en svarskod. Resultatet bestämmer procent andelen misslyckade kontroller. För att kunna beräkna en sökperiod mäter tjänsten tiden mellan ett HTTP-anrop och svaret.

Om du använder TCP eller ICMP beräknar tjänsten paket förlusten i procent för att avgöra procent andelen misslyckade kontroller. Tjänsten mäter den tid det tar att ta emot bekräftelsen (ACK) för de paket som har skickats för att beräkna den. Om du har aktiverat traceroute-data för dina nätverks test kan du se förlust och svars tid för hopp för ditt lokala nätverk.

### <a name="states-of-a-test"></a>Tillstånd för ett test

Beroende på vilka data som kontrollerna returnerar kan testerna ha följande tillstånd:

* **Pass** – faktiska värden för procent andelen misslyckade kontroller och efter frågan inom de angivna tröskelvärdena.
* **Misslyckades** – faktiska värden för procent andelen misslyckade kontroller eller söksöker överskred de angivna tröskelvärdena. Om inget tröskelvärde anges, kommer ett test att nå fel tillstånd när procent andelen misslyckade kontroller är 100.
* **Varning** – inga kriterier har angetts för procent andelen misslyckade kontroller. I avsaknad av angivna villkor tilldelar anslutnings övervakaren (för hands version) automatiskt ett tröskelvärde. När tröskelvärdet överskrids ändras test statusen till varning.

### <a name="data-collection-analysis-and-alerts"></a>Data insamling, analys och aviseringar

De data som anslutnings övervakaren (för hands version) samlar in lagras på arbets ytan Log Analytics. Du konfigurerar den här arbets ytan när du skapade anslutnings övervakaren. 

Övervaknings data finns också i Azure Monitor Mät värden. Du kan använda Log Analytics för att behålla dina övervaknings data så länge du vill. Azure Monitor lagrar mått för endast 30 dagar som standard. 

Du kan [ställa in Metric-baserade aviseringar för data](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Övervaka instrument paneler

På instrument panelen för övervakning visas en lista över de anslutnings Övervakare som du har åtkomst till för dina prenumerationer, regioner, tidsstämplar, källor och mål typer.

När du går till anslutnings övervakaren (förhands granskning) från Network Watcher kan du visa data genom att:

* **Anslutnings övervakare** – lista över alla anslutnings Övervakare som skapats för prenumerationer, regioner, tidsstämplar, källor och mål typer. Den här vyn är standard.
* **Test grupper** – lista över alla test grupper som har skapats för dina prenumerationer, regioner, tidsstämplar, källor och mål typer. Dessa test grupper filtreras inte efter anslutnings övervakare.
* **Test** – lista över alla tester som körs för dina prenumerationer, regioner, tidsstämplar, källor och mål typer. Dessa tester filtreras inte efter anslutnings övervakare eller test grupper.

I följande bild anges de tre datavyerna efter pil 1.

På instrument panelen kan du expandera varje anslutnings Övervakare för att se dess test grupper. Sedan kan du expandera varje test grupp för att se de tester som körs i den. 

Du kan filtrera en lista baserat på:

* **Filter på högsta nivån** – Välj prenumerationer, regioner, tidsstämplar och mål typer. Se ruta 2 i följande bild.
* **Tillstånds baserade filter** – filtrera efter tillstånd för anslutnings övervakaren, test gruppen eller testet. Se pil 3 i följande bild.
* **Anpassade filter** – Välj **Välj alla** om du vill göra en allmän sökning. Om du vill söka efter en speciell entitet väljer du i list rutan. Se pil 4 i följande bild.

![Skärm bild som visar hur du filtrerar vyer för anslutnings övervakare, test grupper och tester i anslutnings övervakaren (för hands version)](./media/connection-monitor-2-preview/cm-view.png)

Om du till exempel vill titta på alla tester i anslutnings övervakaren (för hands version) där käll-IP: en är 10.192.64.56:
1. Ändra vyn för att **testa**.
1. I Sök fältet skriver du *10.192.64.56*
1. Välj **källor**i list rutan.

Om du bara vill visa misslyckade tester i anslutnings övervakaren (för hands version) där käll-IP: en är 10.192.64.56:
1. Ändra vyn för att **testa**.
1. För det tillståndbaserade filtret väljer du **fungerar inte**.
1. I Sök fältet skriver du *10.192.64.56*
1. Välj **källor**i list rutan.

Visa endast misslyckade tester i anslutnings övervakaren (för hands version) där målet är outlook.office365.com:
1. Ändra vy att **testa**.
1. För det tillståndbaserade filtret väljer du **fungerar inte**.
1. Skriv *Outlook.Office365.com* i Sök fältet
1. Välj **destinationer**i list rutan.

   ![Skärm bild som visar en vy som är filtrerad för att endast Visa misslyckade tester för Outlook.Office365.com-målet](./media/connection-monitor-2-preview/tests-view.png)

Visa trender i sökrutan och procent andelen misslyckade kontroller för en anslutnings Övervakare:
1. Välj den anslutnings Övervakare som du vill undersöka. Som standard ordnas övervaknings data efter test grupp.

   ![Skärm bild som visar mått för en anslutnings Övervakare som visas av test gruppen](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Välj den test grupp som du vill undersöka.

   ![Skärm bild som visar var du väljer en test grupp](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Du ser test gruppens översta fem misslyckade tester, baserat på den aktuella sökrutan eller procent andelen misslyckade kontroller. För varje test ser du Beslags-och trend linjerna för procent andelen misslyckade kontroller.
1. Välj ett test i listan eller Välj ett annat test att undersöka. För ditt tidsintervall och procent andelen misslyckade kontroller visas tröskelvärden och faktiska värden. För att hitta sökrutan visas värdena för tröskel, genomsnitt, minimum och maximum.

   ![Skärm bild som visar ett tests resultat för fel sökning och procent andel misslyckade kontroller](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Ändra tidsintervallet för att visa mer data.
1. Ändra vyn om du vill se källor, destinationer eller testkonfigurationer. 
1. Välj en källa baserat på misslyckade tester och Undersök de fem främsta misslyckade testerna. Välj till exempel **Visa efter**  >  **källa** och **Visa efter**  >  **mål** för att undersöka relevanta tester i anslutnings övervakaren.

   ![Skärm bild som visar prestanda mått för de fem främsta misslyckade testerna](./media/connection-monitor-2-preview/cm-drill-select-source.png)

För att visa trender i sökrutan och procent andelen misslyckade kontroller för en test grupp:

1. Välj den test grupp som du vill undersöka. 

    Som standard ordnas övervaknings data efter källor, destinationer och testkonfigurationer (test). Senare kan du ändra vyn från test grupper till källor, destinationer eller testkonfigurationer. Välj sedan en entitet för att undersöka de fem främsta misslyckade testerna. Ändra till exempel vyn till källor och mål för att undersöka relevanta tester i den valda anslutnings övervakaren.
1. Välj det test som du vill undersöka.

   ![Skärm bild som visar var du väljer ett test](./media/connection-monitor-2-preview/tg-drill.png)

    För ditt tidsintervall och för din procent andel misslyckade kontroller visas tröskelvärden och faktiska värden. För att hitta en sökperiod ser du värden för tröskel, genomsnitt, minimum och maximum. Du ser även utlösta aviseringar för det test som du har valt.
1. Ändra tidsintervallet för att visa mer data.

För att visa trender i sökrutan och procent andelen misslyckade kontroller för ett test:
1. Välj källa, mål och test konfiguration som du vill undersöka.

    För ditt tidsintervall och för procent andelen misslyckade kontroller visas tröskelvärden och faktiska värden. För att hitta en sökperiod ser du värden för tröskel, genomsnitt, minimum och maximum. Du ser även utlösta aviseringar för det test som du har valt.

   ![Skärm bild som visar mått för ett test](./media/connection-monitor-2-preview/test-drill.png)

1. Om du vill se nätverk sto pol Ogin väljer du **topologi**.

   ![Skärm bild som visar fliken nätverkstopologi](./media/connection-monitor-2-preview/test-topo.png)

1. Om du vill se de identifierade problemen i topologin väljer du ett hopp i sökvägen. (Dessa hopp är Azure-resurser.) Den här funktionen är för närvarande inte tillgänglig för lokala nätverk.

   ![Skärm bild som visar en vald hopp-länk på fliken topologi](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Logg frågor i Log Analytics

Använd Log Analytics för att skapa anpassade vyer för dina övervaknings data. Alla data som visas i användar gränssnittet är från Log Analytics. Du kan analysera data interaktivt i lagrings platsen. Korrelera data från Agenthälsa eller andra lösningar som baseras på Log Analytics. Exportera data till Excel eller Power BI eller skapa en delnings bar länk.

#### <a name="metrics-in-azure-monitor"></a>Mått i Azure Monitor

I anslutnings Övervakare som har skapats före anslutnings övervakaren (förhands granskning) är alla fyra mått tillgängliga:% avsökningar misslyckades, AverageRoundtripMs, ChecksFailedPercent (för hands version) och RoundTripTimeMs (för hands version). I anslutnings Övervakare som skapades i anslutnings övervakaren (förhands granskning) är data endast tillgängliga för mått som är taggade med (för *hands version)*.

![Skärm bild som visar mått i anslutnings övervakaren (förhands granskning)](./media/connection-monitor-2-preview/monitor-metrics.png)

När du använder mått anger du resurs typen som Microsoft. Network/networkWatchers/connectionMonitors

| Mått | Visningsnamn | Enhet | Sammansättningstyp | Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % Avsökningar misslyckades | Procent | Medel | Procent av anslutnings övervaknings avsökningarna misslyckades. | Inga dimensioner |
| AverageRoundtripMs | Genomsnittlig tid för fördröjning (MS) | Millisekunder | Medel | Genomsnittlig för inblandning av nätverks belastning för anslutnings övervaknings avsökningar skickas mellan källa och mål. |             Inga dimensioner |
| ChecksFailedPercent (för hands version) | % Kontroller misslyckades (förhands granskning) | Procent | Medel | Procent andelen misslyckade kontroller för ett test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>Destination port <br>TestGroupName <br>TestConfigurationName <br>Region |
| RoundTripTimeMs (för hands version) | Tur och retur tid (MS) (för hands version) | Millisekunder | Medel | Söker efter kontroller som skickats mellan källa och mål. Värdet är inte medelvärdet. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>Destination port <br>TestGroupName <br>TestConfigurationName <br>Region |

#### <a name="metric-alerts-in-azure-monitor"></a>Mått varningar i Azure Monitor

Så här skapar du en avisering i Azure Monitor:

1. Välj den anslutnings övervaknings resurs som du skapade i anslutnings övervakaren (för hands version).
1. Se till att **måttet** visas som signal typ för anslutnings övervakaren.
1. I **Lägg till villkor**, för **signal namnet**, väljer du **ChecksFailedPercent (för hands version)** eller **RoundTripTimeMs (för hands version)**.
1. Välj **mått**för **signal typ**. Välj till exempel **ChecksFailedPercent (för hands version)**.
1. Alla mått för måttet visas. Välj dimensions namn och dimensions värde. Välj till exempel **käll adress** och ange IP-adressen för vilken källa som helst i anslutnings övervakaren.
1. Fyll i följande information i **aviserings logik**:
   * **Villkors typ**: **statisk**.
   * **Villkor** och **tröskel**.
   * **Sammansättnings precision och utvärderings frekvens**: anslutnings övervakaren uppdaterar data varje minut.
1. I **åtgärder**väljer du din åtgärds grupp.
1. Ange aviserings information.
1. Skapa varnings regeln.

   ![Skärm bild som visar avsnittet Skapa regel i Azure Monitor; "Käll adress" och "källans slut punkts namn" är markerat](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnostisera problem i nätverket

Anslutnings övervakaren (för hands version) hjälper dig att diagnostisera problem i anslutnings övervakaren och nätverket. Problem i ditt hybrid nätverk identifieras av de Log Analyticss agenter som du installerade tidigare. Problem i Azure identifieras av Network Watcher-tillägget. 

Du kan visa problem i Azure-nätverket i nätverk sto pol Ogin.

Följande problem kan identifieras för nätverk vars källor är lokala virtuella datorer:

* Tids gränsen nåddes för begäran.
* Slut punkten har inte lösts av DNS – temporär eller beständig. URL är ogiltig.
* Inga värdar hittades.
* Källan kan inte ansluta till målet. Målet kan inte nås via ICMP.
* Certifikatrelaterade problem: 
    * Klient certifikat som krävs för att autentisera agenten. 
    * Listan över återkallade certifikat är inte tillgänglig. 
    * Värd namnet för slut punkten stämmer inte överens med certifikatets ämne eller alternativa namn för certifikat mottagare. 
    * Rot certifikatet saknas i källans Arkiv för betrodda certifikat utfärdare. 
    * SSL-certifikatet har upphört att gälla, är ogiltigt, återkallat eller inkompatibelt.

Följande problem kan identifieras för nätverk vars källor är virtuella Azure-datorer:

* Agent problem:
    * Agenten stoppades.
    * DNS-matchning misslyckades.
    * Inget program eller lyssnare lyssnar på mål porten.
    * Det gick inte att öppna socketen.
* Problem med VM-tillstånd: 
    * Startar
    * Stoppas
    * Stoppad
    * Frigör
    * Frigjord
    * Starta om
    * Inte allokerat
* ARP-tabell posten saknas.
* Trafiken blockerades på grund av problem med lokala brand väggar eller NSG-regler.
* Problem med virtuell nätverksgateway: 
    * Vägar som saknas.
    * Tunneln mellan två gateways är frånkopplad eller saknas.
    * Den andra gatewayen kunde inte hittas av tunneln.
    * Ingen peering-information hittades.
* Vägen saknades i Microsoft Edge.
* Trafiken stoppades på grund av system vägar eller UDR.
* BGP är inte aktiverat på Gateway-anslutningen.
* DIP-avsökningen är nere i belastningsutjämnaren.
