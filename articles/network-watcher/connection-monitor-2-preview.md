---
title: Anslutnings övervakare (för hands version) | Microsoft Docs
description: Lär dig hur du använder anslutnings övervakaren (för hands version) för att övervaka nätverkskommunikation i en distribuerad miljö
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 5dc705fbd17a12ee001e1e8de15b49e841f08b81
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049241"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>Enhetlig anslutnings övervakning med anslutnings övervakaren (för hands version)

Anslutnings övervakaren (för hands version) tillhandahåller enhetliga funktioner för övervakning av slut punkt till slut punkt i Azure Network Watcher för Hybrid-och Azure Cloud-distributioner. Azure Network Watcher innehåller verktyg för att övervaka, diagnostisera och Visa anslutnings relaterade mått för dina Azure-distributioner.

Nyckel användnings fall:

- Du har en virtuell dator för en front webb server som kommunicerar med en virtuell databas server i ett program på flera nivåer. Du vill kontrol lera nätverks anslutningen mellan de två virtuella datorerna.
- Du vill att de virtuella datorerna i regionen USA, östra, ska pinga virtuella datorer i den centrala regionen och jämföra fördröjningar i flera regioner
- Du har flera lokala Office-webbplatser i städer som Seattle. ansluter till Office 365-URL: er. Du vill jämföra svars tiderna för användarna med hjälp av Office 365-URL: er från Seattle och Ashburn.
- Du har konfigurerat ett hybrid program som behöver anslutning till en Azure Storage-slutpunkt. Du vill jämföra fördröjningar mellan en lokal plats och Azure-programmet ansluter till samma Azure Storage slut punkt.
- Du vill kontrol lera anslutningen från virtuella Azure-datorer som är värd för ditt moln program till dina lokala installationer.

I den här förhands gransknings fasen kombinerar lösningen det bästa av två viktiga funktioner – Network Watcher [anslutnings övervakare](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) och övervakare av NÄTVERKSPRESTANDA (NPM) [tjänst anslutnings övervakare](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity).

Ljus

* Enhetlig, intuitiv upplevelse för Azure och hybrid övervaknings behov
* Kors region, övervakning av anslutnings barhet för flera arbets ytor
* Högre avsöknings frekvens och bättre insyn i nätverks prestanda
* Snabbare aviseringar för dina hybrid distributioner
* Stöd för HTTP-, TCP-och ICMP-baserade anslutnings kontroller
* Mått och Log Analytics stöd för både Azure-test och icke-Azure-testkonfigurationer

![Anslutningsövervakare](./media/connection-monitor-2-preview/hero-graphic.png)

Följ stegen nedan för att starta övervakning med anslutnings övervakaren (för hands version)

## <a name="step-1-install-monitoring-agents"></a>Steg 1: installera övervaknings agenter

Anslutnings övervakaren förlitar sig på lätta körbara filer för att köra anslutnings kontroller.  Vi stöder anslutnings kontroller från både Azure och lokala miljöer. Vilken körbar fil som ska användas beror på om den virtuella datorn finns på Azure eller lokalt.

### <a name="agents-for-azure-virtual-machines"></a>Agenter för virtuella Azure-datorer

För att anslutnings övervakaren ska kunna identifiera dina virtuella Azure-datorer som källa för övervakning måste du installera tillägget Network Watcher agent virtuell dator (kallas även för Network Watcher tillägg). Tillägget Network Watcher agent är ett krav för att utlösa slut punkt till slut punkts övervakning och andra avancerade funktioner på Azure Virtual Machines. Du kan [skapa en virtuell dator och installera Network Watcher tillägget](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[på den](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm).  Du kan också installera, konfigurera och felsöka Network Watcher-tillägg för [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) och [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) separat.

Om NSG-eller brand Väggs regler blockerar kommunikation mellan källa och mål, identifierar anslutnings övervakaren problemet och visar det som ett diagnostiskt meddelande i topologin. Om du vill aktivera anslutnings övervakning kontrollerar du att NSG-och brand Väggs reglerna tillåter paket över TCP eller ICMP mellan källa och mål.

### <a name="agents-for-on-premise-machines"></a>Agenter för lokala datorer

För att anslutnings övervakaren ska kunna identifiera dina lokala datorer som källor för övervakning måste du installera Log Analytics-agenten på datorerna och aktivera lösningen för övervakning av nätverks prestanda. Dessa agenter är länkade till Log Analytics arbets ytor och behöver ID för arbets yta och primär nyckel som kon figurer ATS innan de kan börja övervaka.

Om du vill installera Log Analytics-agenten för Windows-datorer följer du instruktionerna som nämns i [den här länken](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Se till att målet kan uppnås om det finns brand väggar eller virtuella nätverks enheter (NVA) i sökvägen.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Steg 2: Aktivera Network Watcher på din prenumeration

Alla prenumerationer med ett VNET aktive ras med Network Watcher. När du skapar ett virtuellt nätverk i din prenumeration kommer Network Watcher att aktive ras automatiskt i Virtual Network region och prenumeration. Det finns ingen inverkan på dina resurser eller tillhör ande avgift för automatisk aktivering av Network Watcher. Se till att Network Watcher inte uttryckligen har inaktiverats för din prenumeration. Mer information finns i [aktivera Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Steg 3: skapa anslutnings övervakare 

_Anslutnings övervakaren_ övervakar kommunikation med jämna mellanrum och informerar dig om tillgänglighet, svars tid och nätverks sto pol Ogin mellan käll agenter och mål slut punkter. Källor kan vara virtuella Azure-datorer eller lokala datorer som har en övervaknings agent installerad. Mål slut punkter kan vara Office 365-URL: er, Dynamics 365 URL: er, anpassade URL: er, resurs-ID: n för Azure VM, IPv4, IPv6, FQDN eller något annat domän namn.

### <a name="accessing-connection-monitor-preview"></a>Åtkomst till anslutnings övervakaren (för hands version)

1. På Start sidan för Azure Portal går du till Network Watcher
2. Klicka på fliken "anslutnings övervakare (förhands granskning)" i avsnittet övervakning i Network Watcher vänstra fönstret.
3. Du kan se alla anslutnings Övervakare som skapats med hjälp av anslutnings övervakaren (förhands granskning). Alla anslutnings Övervakare som skapats med den klassiska upplevelsen av anslutnings övervakaren visas på fliken anslutnings övervakare.

    ![Skapa en anslutnings övervakare](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Skapa en anslutnings övervakare

Anslutnings Övervakare som skapats med anslutnings övervakaren (för hands version) ger möjlighet att lägga till både lokala och virtuella Azure-datorer som källor och övervaka anslutningar till slut punkter som kan omfatta Azure eller andra URL/IP-adresser.

Följande är entiteterna i en anslutnings Övervakare:

* Anslutnings övervakare resurs – regions-/regionsspecifika Azure-resurs. Alla entiteter som anges nedan är egenskaper för en anslutnings övervaknings resurs.
* Slut punkter – alla källor och mål som ingår i anslutnings kontroller anropas som slut punkter. Exempel på slut punkt – virtuella Azure-datorer, lokala agenter, URL: er, IP-adresser
* Test konfiguration – varje test konfiguration är protokoll information. Utifrån det valda protokollet kan du definiera port, tröskelvärden, test frekvens och andra parametrar
* Test grupp – varje test grupp innehåller käll slut punkter, mål slut punkter och testkonfigurationer. Varje anslutnings övervakare kan innehålla fler än en test grupp
* Test – kombination av en slut punkt för källan, mål slut punkten och test konfigurationen gör ett prov. Testet är den lägsta nivån vid vilken övervaknings data (kontrollerna misslyckades% och sökkoden) är tillgängliga

 ![Skapa en anslutnings övervakare](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>Från portalen

Följ anvisningarna nedan om du vill skapa en anslutnings Övervakare:

1. I instrument panelen för anslutnings övervakaren (förhands granskning) klickar du på "skapa" från det övre vänstra hörnet.
2. På fliken grundläggande anger du information för anslutnings övervakaren
   1. Anslutnings övervakarens namn – namnet på anslutnings övervakaren. Standard namngivnings regler för Azure-resurser gäller här.
   2. Prenumeration – Välj en prenumeration för anslutnings övervakaren.
   3. Region – Välj en region för anslutnings övervakarens resurs. Du kan bara välja de virtuella käll datorer som skapas i den här regionen.
   4. Konfiguration av arbets yta – du kan använda antingen standard arbets ytan som skapats av anslutnings övervakaren för att lagra dina övervaknings data genom att klicka på kryss rutan standard. Avmarkera den här kryss rutan om du vill välja en anpassad arbets yta. Välj prenumeration och region för att välja arbets ytan, som innehåller dina övervaknings data.
   5. Klicka på Nästa: test grupper för att lägga till test grupper

      ![Skapa en anslutnings övervakare](./media/connection-monitor-2-preview/create-cm-basics.png)

3. På fliken test grupper klickar du på "+ test grupp" för att lägga till en test grupp. Använd _skapa test grupper i anslutnings övervakaren_ för att lägga till test grupper. Klicka på granska + skapa för att granska anslutnings övervakaren.

   ![Skapa en anslutnings övervakare](./media/connection-monitor-2-preview/create-tg.png)

4. På fliken "granska + skapa" granskar du grundläggande information och test grupper innan du skapar anslutnings övervakaren. Redigera anslutnings övervakaren från vyn "granska + skapa":
   1. Om du vill redigera den grundläggande informationen använder du Penn ikonen som anges i ruta 1 i bild 2
   2. Om du vill redigera enskilda test grupper klickar du på den test grupp som du vill redigera för att öppna test gruppen i redigerings läge.
   3. Aktuell kostnad/månad visade kostnaden under för hands versionen. Det finns för närvarande ingen avgift för att använda anslutnings övervakaren, så den här kolumnen visar noll. Faktisk kostnad/månad visade priset som debiteras efter allmän tillgänglighet. Observera att avgifter för Log Analytics-inmatningen gäller även under för hands versionen.

5. På fliken "granska + skapa" klickar du på knappen "skapa" för att skapa anslutnings övervakaren.

   ![Skapa en anslutnings övervakare](./media/connection-monitor-2-preview/review-create-cm.png)

6.  Anslutnings övervakaren (för hands version) skapar anslutnings övervaknings resursen i bakgrunden.

#### <a name="from-armclient"></a>Från Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

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

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Distributions kommando:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Skapa test grupper i anslutnings övervakaren

Varje test grupp i anslutnings övervakaren innehåller källor och mål som testas på nätverks parametrar för kontroller som misslyckats och sökkörning över testkonfigurationer.

#### <a name="from-portal"></a>Från portalen

Om du vill skapa en test grupp i en anslutnings övervakare anger du värdet för nedanstående fält:

1. Inaktivera test grupp – om du markerar det här fältet inaktive ras övervakningen för alla källor och mål som anges i test gruppen. Du ser det här alternativet avmarkerat som standard.
2. Namn – namnet på din test grupp
3. Källor – du kan ange både virtuella Azure-datorer och lokala datorer som källor om agenter installeras i dem. Se steg 1 för att installera agent som är unik för din källa.
   1. Klicka på fliken "Azure-agenter" om du vill välja Azure-agenter. Du ser bara de virtuella datorerna som är kopplade till den region som du angav när du skapade anslutnings övervakaren. Virtuella datorer grupperas som standard i den prenumeration de tillhör och grupperna är dolda. Du kan öka detalj nivån från prenumerations nivån till andra nivåer i hierarkin:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      Du kan också ändra värdet för fältet "Gruppera efter" om du vill starta trädet från en annan nivå. Exempel: Group by – VNET visar de virtuella datorerna med agenter i hierarkin virtuella nätverk-\&gt; Undernät-\&gt; Virtuella datorer med agenter.

      ![Lägg till källor](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Klicka på fliken "ej – Azure-agenter" om du vill välja lokala agenter. Som standard visas agenter grupperade i arbets ytor i en region. Endast de arbets ytor som har kon figurer ATS Övervakare av nätverksprestandas lösning visas. Lägg till Övervakare av nätverksprestanda-lösningen till din arbets yta från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Du kan också använda processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) . Som standard visas den region som valts på fliken grundläggande information i vyn Skapa anslutnings övervakare. Du kan ändra region och välja agenter från arbets ytor från den nyligen valda regionen. Du kan också ändra värdet för fältet "Gruppera efter" för att gruppera efter undernät.

      ![Icke-Azure-källor](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Klicka på granska om du vill granska de valda Azure-och icke-Azure-agenterna.

      ![Granska källor](./media/connection-monitor-2-preview/review-sources.png)

   4. Klicka på klar när du är klar med att välja källorna.

4. Destinationer – du kan övervaka anslutningar till virtuella Azure-datorer eller en slut punkt (offentlig IP, URL, FQDN) genom att ange dem som mål. I en enda test grupp kan du lägga till virtuella Azure-datorer, O365-URL: er, D365-URL: er eller anpassade slut punkter.

   1. Klicka på fliken "virtuella Azure-datorer" om du vill välja virtuella Azure-datorer som mål. Som standard visas Azure-VM grupperade i prenumerationsbegäran i samma region som valdes på fliken grundläggande information i vyn Skapa anslutnings övervakare. Du kan ändra region och välja virtuella Azure-datorer från den nyligen valda regionen. Du kan öka detalj nivån från prenumerations nivån till andra nivåer i hierarkin, t. ex. Azure-agenter.

      ![Lägg till mål](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Lägg till destinationer 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Klicka på fliken "slut punkter" om du vill välja virtuella Azure-datorer som mål. Slut punkts listan fylls med O365 och D365-test-URL: er, grupperat efter namn.  Du kan också välja en slut punkt som skapats i andra test grupper i samma anslutnings övervakare. Om du vill lägga till en ny slut punkt klickar du på "+ slut punkt" från det övre högra hörnet på skärmen och anger URL: en för slut punkten/IP/FQDN och namn

      ![Lägg till slut punkter](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Klicka på granska om du vill granska de valda Azure-och icke-Azure-agenterna.
   4. Klicka på klar när du är klar med att välja källorna.

5. Testa konfiguration – du kan koppla ett valfritt antal testkonfigurationer i en specifik test grupp. Portalen begränsar den till en test konfiguration per test grupp, men använder Armclient för att lägga till fler.
   1. Namn – namnet på test konfigurationen
   2. Protokoll – du kan välja mellan TCP, ICMP eller HTTP. Om du vill ändra HTTP till HTTPS väljer du HTTP som protokoll och 443 som port
   3. Skapa nätverks test konfiguration – du ser bara den här kryss rutan om du väljer HTTP i fältet protokoll. Aktivera det här fältet om du vill skapa en annan test konfiguration med samma källor och mål som anges i steg 3 och 4 över TCP/ICMP-protokollet. Den nyligen skapade test konfigurationen heter "\&lt; name anges i 5. a\&gt;\_networkTestConfig "
   4. Inaktivera traceroute – det här fältet gäller för test grupper med TCP eller ICMP som protokoll.  Markera det här fältet om du vill stoppa källor från att identifiera topologi och svars tid för hopp efter hopp.
   5. Målport – du kan anpassa det här fältet om du vill ange en valfri målport.
   6. Test frekvens – det här fältet avgör hur ofta källor ska pinga mål på det protokoll och den port som anges ovan. Du kan välja mellan 30 sekunder, 1 minut, 5 minuter, 15 minuter och 30 minuter. Källorna kommer att testa anslutningen till destinationer utifrån det värde som du väljer.  Om du till exempel väljer 30 sekunder kommer källorna att kontrol lera anslutningen till målet minst en gång på 30 sekunder – period.
   7. Hälso trösklar – du kan ange tröskelvärden för de nätverks parametrar som anges nedan
      1. Det gick inte att checka in%-procent av kontrollerna misslyckades när källor kontrollerar anslutningen till målet enligt de villkor som anges ovan. För TCP/ICMP-protokollet kan kontroller som misslyckats i% likställas med paket förlust%. För HTTP-protokoll representerar det här fältet det antal HTTP-begäranden som inte fick något svar.
      2. Efter fråga i millisekunder – fördröjning i millisekunder när källor ansluter till målet via den angivna test konfigurationen ovan.

      ![Lägg till TG](./media/connection-monitor-2-preview/add-test-config.png)

Alla källor och mål som läggs till i en test grupp med den angivna test konfigurationen får dela upp till enskilda tester. Exempel:

* Test grupp: TG1
* Källor: 3 (A, B, C)
* Mål: 2 (D, E)
* Test konfiguration: 2 (Konfig 1, konfig 2)
* Skapade tester: totalt = 12

| **Test nummer** | **Källa** | **Mål** | **Testa konfigurations namn** |
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

* Max antal anslutnings övervakare per prenumeration per region – 100
* Max antal test grupper per anslutnings övervakare-20
* Högsta antal källor + mål per anslutnings övervakare – 100
* Max antal testkonfigurationer per anslutnings övervakare – 20 via Armclient. 2 via portalen.

## <a name="step-4--data-analysis-and-alerts"></a>Steg 4: data analys och aviseringar

När en anslutnings övervakare har skapats kontrollerar källorna anslutningen till destinationer baserat på den angivna test konfigurationen.

### <a name="checks-in-a-test"></a>Checkar in ett test

Baserat på det protokoll som valts av en användare i test konfigurationen kör anslutnings övervakaren (för hands version) en serie kontroller för käll mål paret under den valda test frekvensen.

Om du väljer HTTP, beräknar tjänsten antalet HTTP-svar som returnerade en svarskod för att avgöra om kontrollerna misslyckades%.  För att beräkna en sökperiod mäter vi hur lång tid det tar att ta emot svaret på ett HTTP-anrop.

Om TCP eller ICMP har valts beräknar tjänsten paket% för att avgöra om kontrollerna misslyckades%. För att beräkna en sökperiod mäter vi hur lång tid det tar att ta emot ACK för skickade paket. Om du har aktiverat traceroute-data för dina nätverks test kan du se förlust och svars tid för hopp för ditt lokala nätverk.

### <a name="states-of-a-test"></a>Tillstånd för ett test

Baserat på de data som returneras av kontrollerna i ett test kan varje test sedan ha följande tillstånd:

* Pass = när de faktiska värdena för kontrollerna misslyckades% och efter frågan är inom angivna tröskelvärden
* Misslyckande = när faktiska värden för kontroller misslyckades% eller sökvärden tvärs över angivna tröskelvärden. Om inget tröskelvärde anges markeras ett fel när kontrollerna misslyckades% = 100%
* Varning – när villkor för kontrollerna misslyckades% inte har angetts. I sådana fall använder anslutnings övervakaren (förhands granskning) ett automatiskt uppsättnings kriterium som tröskelvärde och när tröskelvärdet för det här tröskelvärdet är inställt på "varning"

### <a name="data-collection-analysis-and-alerts"></a>Data insamling, analys och aviseringar

Alla data som samlas in av anslutnings övervakaren (förhands granskning) lagras i Log Analytics arbets ytan som kon figurer ATS när anslutnings övervakaren skapas. Övervaknings data finns också i Azure Monitor Mät värden. Du kan använda Log Analytics för att hålla övervaknings data så länge som du vill, men Azure Monitor lagrar mått som standard i 30 dagar **.** Du kan sedan [Ange måttbaserade aviseringar för data](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Övervaka instrument paneler i anslutnings övervakaren, lösning

Du ser en lista över anslutnings övervakaren som du har åtkomst till för ett specifikt urval av prenumerationer, regioner, tidsstämpel, käll-och mål typer.

När du navigerar till anslutnings övervakaren (förhands granskning) från Network Watcher-tjänsten kan du välja att **Visa**följande:

* Anslutnings övervakare (standard) – lista över alla anslutnings Övervakare som skapats för valda prenumerationer, regioner, tidsstämpel, käll-och mål typer
* Test grupper – lista över alla test grupper som har skapats för valda prenumerationer, regioner, tidsstämpel, käll-och mål typer. Dessa test grupper filtreras inte på anslutnings övervakaren
* Test – lista över alla tester som körs för valda prenumerationer, regioner, tidsstämpel, käll-och mål typer. Dessa tester filtreras inte på anslutnings övervakaren eller test grupper.

Du kan expandera varje anslutnings övervakare till test grupperna och varje test grupp i de olika enskilda test som körs i den på instrument panelen. Markerat som [1] i bilden nedan.

Du kan filtrera listan baserat på:

* Filter på översta nivån – prenumerationer, regioner, timestamp-källa och mål typer. Markerat som [2] i bilden nedan.
* Tillstånds filter – filter på andra nivån i tillstånd för anslutnings övervakaren/test gruppen/testet. Markerat som [3] i bilden nedan.
* Sök fält – Välj alla om du vill göra en allmän sökning. Om du vill söka efter en specifik entitet använder du List rutan för att begränsa Sök resultaten. Markerat som [4] i bilden nedan.

![Filtrera tester](./media/connection-monitor-2-preview/cm-view.png)

Exempel:

1. För att titta på alla tester i alla anslutnings övervakare (för hands version) där Källans IP = 10.192.64.56:
   1. Ändra vy efter till "tests"
   2. Sök arkiverat = 10.192.64.56
   3. Använd List rutan bredvid värde för att välja "källor"
2. Filtrera ut endast misslyckade tester i alla anslutnings övervakare (för hands version) där källa IP = 10.192.64.56
   1. Ändra vy efter till "tests"
   2. Välj "inte" på grund av tillstånds filter.
   3. Sök fält = 10.192.64.56
   4. Använd List rutan bredvid värde för att välja "källor"
3. Filtrera ut endast misslyckade tester i alla anslutnings övervakare (för hands version) där målet är outlook.office365.com
   1. Ändra vy efter till "tests"
   2. Välj "inte" på grund av tillstånds filter.
   3. Sök fält = outlook.office365.com
   4. Använd List rutan bredvid värde för att välja "destinationer"

   ![Misslyckade tester](./media/connection-monitor-2-preview/tests-view.png)

Så här visar du trender för kontroller som misslyckats% och eftertt läge för:

1. Anslutningsövervakare
   1. Klicka på den anslutnings Övervakare som du vill undersöka i detalj
   2. Som standard visas övervaknings data efter "test grupper"

      ![Visa mått per](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Välj den test grupp som du vill undersöka i detalj

      ![Mått per TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Du ser de 5 främsta misslyckade testerna vid kontroller som misslyckats% eller sökmsecs för den test grupp som du valde i föregående steg. För varje test ser du att trend linjer för kontroller misslyckades% och var i läge MS
   5. Välj ett test i listan ovan eller Välj ett annat test för att undersöka i detalj.
   6. För kontroller som har valts för det valda tidsintervallet visas tröskel och faktiska värden. För att hitta den här tiden kan du se tröskelvärde, AVG, min och max.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Ändra tidsintervallet för att visa mer data
  8. Du kan ändra vyn i steg b och välja att visa efter källor, destinationer eller testa konfigurationer. Välj sedan en källa baserat på misslyckade tester och Undersök de 5 främsta misslyckade testerna.  Exempel: Välj Visa efter: källor och mål för att undersöka alla tester som körs mellan den kombinationen i den valda anslutnings övervakaren.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Test grupp
   1. Klicka på den test grupp som du vill undersöka i detalj
   2. Som standard visas övervaknings data efter "källa + mål + test konfiguration (test)"

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Välj det test som du vill undersöka i detalj
   4. För kontroller som har valts för det valda tidsintervallet visas tröskel och faktiska värden. För att hitta den här tiden kan du se tröskelvärde, AVG, min och max. Du kommer också att se utlösta aviseringar som är speciella för det test du har valt.
   5. Ändra tidsintervallet för att visa mer data
   6. Du kan ändra vyn i steg b och välja att visa efter källor, destinationer eller testa konfigurationer. Välj sedan en entitet för att undersöka de 5 främsta misslyckade testerna.  Exempel: Välj Visa efter: källor och mål för att undersöka alla tester som körs mellan den kombinationen i den valda anslutnings övervakaren.

3. Testa
   1. Klicka på den källa + mål + test konfiguration som du vill undersöka i detalj
   2. För kontroller som har valts för det valda tidsintervallet visas tröskel och faktiska värden. För att hitta den här tiden kan du se tröskelvärde, AVG, min och max. Du kommer också att se utlösta aviseringar som är speciella för det test du har valt.

      ![TEST1](./media/connection-monitor-2-preview/test-drill.png)

   3. Du kan också klicka på "topologi" för att se nätverk sto pol Ogin vid en viss tidpunkt.

      ![TEST2](./media/connection-monitor-2-preview/test-topo.png)

   4. Du kan klicka på alla hopp Länkar för Azure-nätverk för att se de problem som identifierats av anslutnings övervakaren. Den här funktionen är inte tillgänglig för lokala nätverk för tillfället.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Logg frågor i Azure Monitor Log Analytics

Använd Log Analytics för att skapa anpassade vyer för dina övervaknings data. Alla data som visas i användar gränssnittet fylls i från Log Analytics. Du kan utföra interaktiv analys av data i lagrings platsen och korrelera data från olika källor, t. ex. Agent hälsa och andra Log Analytics baserade program. Du kan också exportera data till Excel, Power BI eller en delnings bar länk.

#### <a name="metrics-in-azure-monitor"></a>Mått i Azure Monitor

För anslutnings övervakaren som skapades innan anslutnings övervakaren (förhands granskning), skulle alla 4 mått vara tillgängliga. För anslutnings Övervakare som skapats via anslutnings övervakaren (förhands granskning) är data endast tillgängliga för mått som är märkta med "(för hands version)".

Resurs typ-Microsoft. Network/networkWatchers/connectionMonitors

| Mått | Metrisk visningsnamn | Enhet | Sammansättningstyp: | Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % Avsökningar misslyckades | Procent | Medel | % av anslutnings övervaknings avsökningarna misslyckades | Inga dimensioner |
| AverageRoundtripMs | Genomsnittlig tid för fördröjning (MS) | Millisekunder | Medel | Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål |             Inga dimensioner |
| ChecksFailedPercent (för hands version) | % Kontroller misslyckades (förhands granskning) | Procent | Medel | % av kontrollerna misslyckades för ett test | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * SourceResourceId <br> * SourceType <br> * Protokoll <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * Destination port <br> * TestGroupName <br> * TestConfigurationName <br> * Region |
| RoundTripTimeMs (för hands version) | Tur och retur tid (MS) (för hands version) | Millisekunder | Medel | Fördröjning för svar (MS) för kontroller som skickas mellan källa och mål. Det här värdet är inte medelvärdet | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * SourceResourceId <br> * SourceType <br> * Protokoll <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * Destination port <br> * TestGroupName <br> * TestConfigurationName <br> * Region |

 ![Övervaka mått](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Mått varningar i Azure Monitor

Så här skapar du en avisering:

1. Välj resursen för anslutnings övervakaren som skapats med anslutnings övervakaren (förhands granskning)
2. Se till att "Metric" visas som signal typ för den resurs som valts i föregående steg
3. I Lägg till villkor väljer du signal namn som ChecksFailedPercent (för hands version) eller RoundTripTimeMs (för hands version) och signal typ som mått. Tex: Välj ChecksFailedPercent (förhands granskning)
4. Alla dimensioner som gäller per mått visas.  Välj dimensions namn och dimensions värde. Tex: Välj käll adress och ange IP-adressen för vilken källa som helst i resursen för anslutnings övervakaren som valts i steg 1
5. I aviserings logik väljer du:
   1. Villkors typ – statisk
   2. Villkor och tröskel
   3. Sammansättnings precision och utvärderings frekvens – anslutnings övervakare (för hands version) uppdaterar data var 1 minut.
6.  I åtgärder väljer du din åtgärds grupp
7. Ange aviserings information
8. Skapa aviserings regel

   ![Aviseringar](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Steg 5: diagnostisera problem i nätverket

Anslutnings övervakaren hjälper dig att diagnostisera problem som motsvarar anslutnings övervaknings resursen och i nätverket. Problem i ditt hybrid nätverk identifieras av de Log Analytics-agenter som du installerade i steg 1 och problem i Azure identifieras av Network Watcher-tillägget.  Problem i hybrid nätverk visas på sidan diagnostik och problem i Azure Network visas i nätverk sto pol Ogin.

För nätverk med lokala virtuella datorer som källor identifierar vi:

* Tids gränsen nåddes för begäran
* Slut punkten har inte lösts av DNS – temporär eller beständig. URL är ogiltig.
* Inga värdar hittades.
* Källan kan inte ansluta till målet. Målet kan inte nås via ICMP.
* Certifikat relaterat problem – klient certifikat som krävs för att autentisera agenten, det går inte att komma åt listan över återkallade certifikat, värd namnet för slut punkten stämmer inte överens med certifikatets ämne eller alternativt ämnes namn, rot certifikat saknas i källans lokala betrodda certifikat utfärdare Arkiv, SSL-certifikatet har upphört att gälla/är ogiltigt

För nätverk med virtuella Azure-datorer identifieras:

* Agent problem – agenten har stoppats, DNS-matchningen misslyckades, inga program/lyssnare lyssnar på mål porten, Socket kunde inte öppnas
* Problem med VM-tillstånd – starta, stoppa, stoppa, frigöra, frikoppla, starta om, inte allokerat
* ARP-tabell post saknas
* Trafik blockerad på grund av problem med lokala brand väggar, NSG-regler
* VNET Gateway – saknade vägar, tunnel mellan två gateways är frånkopplad eller saknas eller så saknas en gateway som inte hittas av tunneln, ingen peering-information hittades
* Vägen saknas i MS Edge.
* Trafiken stoppades på grund av system vägar eller UDR
* BGP är inte aktiverat på Gateway-anslutning
* DIP-avsökningen nedåt i Load Balancer
