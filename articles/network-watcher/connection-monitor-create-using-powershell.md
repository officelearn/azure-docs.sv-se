---
title: Skapa anslutnings övervakare – PowerShell
titleSuffix: Azure Network Watcher
description: Lär dig hur du skapar anslutnings övervakare med hjälp av PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 1a554177bf7084b9a7f4c413dbe82271b3ab6b3a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545541"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Skapa en anslutnings övervakare med PowerShell

Lär dig hur du skapar anslutnings övervakaren för att övervaka kommunikationen mellan dina resurser med hjälp av PowerShell.


## <a name="before-you-begin"></a>Innan du börjar 

I anslutnings Övervakare som du skapar i anslutnings övervakaren kan du lägga till både lokala datorer och virtuella Azure-datorer som källor. Dessa anslutnings övervakare kan också övervaka anslutningar till slut punkter. Slut punkterna kan vara på Azure eller någon annan URL eller IP-adress.

Anslutnings övervakaren innehåller följande entiteter:

* **Anslutnings övervaknings resurs** – en regions-/regionsspecifika Azure-resurs. Alla följande entiteter är egenskaper för en anslutnings övervaknings resurs.
* **Slut punkt** – en källa eller ett mål som deltar i anslutnings kontroller. Exempel på slut punkter är virtuella Azure-datorer, lokala agenter, URL: er och IP-adresser.
* **Test konfiguration** – en plattformsspecifik konfiguration för ett test. Utifrån det protokoll du väljer kan du definiera port, tröskelvärden, test frekvens och andra parametrar.
* **Test grupp** – gruppen som innehåller käll slut punkter, mål slut punkter och testkonfigurationer. En anslutnings övervakare kan innehålla fler än en test grupp.
* **Test** – kombinationen av en käll slut punkt, mål slut punkt och test konfiguration. Ett test är den mest detaljerade nivån där övervaknings data är tillgängliga. Övervaknings data innehåller procent andelen kontroller som misslyckades och tur och retur-tiden.

    ![Diagram som visar en anslutnings Övervakare som definierar relationen mellan test grupper och tester](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Steg för att skapa med PowerShell

Använd följande kommandon för att skapa en anslutnings övervakare med hjälp av PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Beskrivning av egenskaper

* connectionMonitorName – namnet på anslutnings övervaknings resursen

* Underordnat prenumerations-ID för den prenumeration där du vill skapa anslutnings övervakaren

* NW-Network Watcher resurs-ID där CM kommer att skapas 

* plats-region där anslutnings övervakaren ska skapas

* Slutpunkter
    * namn – unikt namn för varje slut punkt
    * resourceId – för Azure-slutpunkter refererar resurs-ID till Azure Resource Manager resurs-ID för virtuella datorer. För icke-Azure-slutpunkter refererar resurs-ID till Azure Resource Manager resurs-ID för arbets ytan Log Analytics som är länkad till icke-Azure-agenter.
    * adress – endast tillämpligt när inget resurs-ID har angetts eller om resurs-ID: t är Log Analytics arbets ytan. Om det används med Log Analytics resurs-ID, refererar detta till det fullständiga domän namnet för den agent som kan användas för övervakning. Om det används utan resurs-ID kan detta vara webb adressen eller IP-adressen för en offentlig slut punkt.
    * Filtrera – för icke-Azure-slutpunkter använder du filter för att välja agenter från Log Analytics arbets ytan som ska användas för övervakning i anslutnings övervaknings resursen. Om inga filter anges kan alla agenter som hör till Log Analytics arbets ytan användas för övervakning
        * typ – ange typ som "agent adress"
        * adress – Ange adress som FQDN för din lokala agent

* Test grupper
    * namn – ge test gruppen ett namn.
    * testConfigurations – testa konfigurationer baserat på vilka käll slut punkter ansluter till mål slut punkter
    * källor – Välj från slut punkter som skapats ovan. Azure-baserade käll slut punkter måste ha Azure Network Watcher-tillägget installerat och Azure-baserade käll slut punkter måste ha haveAzure Log Analytics-agenten installerad. Information om hur du installerar en agent för din källa finns i [Installera övervaknings agenter](./connection-monitor-overview.md#install-monitoring-agents).
    * destinationer – Välj från slut punkter som skapats ovan. Du kan övervaka anslutningar till virtuella Azure-datorer eller en slut punkt (en offentlig IP-adress, URL eller FQDN) genom att ange dem som mål. I en enda test grupp kan du lägga till virtuella Azure-datorer, Office 365-URL: er, Dynamics 365-URL: er och anpassade slut punkter.
    * Inaktivera – Använd det här fältet om du vill inaktivera övervakning för alla källor och mål som test gruppen anger.

* Testa konfigurationer
    * Namn – namnet på test konfigurationen.
    * testFrequencySec – ange hur ofta källor ska pinga mål på det protokoll och den port som du har angett. Du kan välja 30 sekunder, 1 minut, 5 minuter, 15 minuter eller 30 minuter. Källorna kommer att testa anslutningen till destinationer utifrån det värde som du väljer. Om du till exempel väljer 30 sekunder kommer källorna att kontrol lera anslutningen till målet minst en gång under en 30-sekunders period.
    * protokoll – du kan välja TCP, ICMP, HTTP eller HTTPS. Beroende på protokollet kan du göra vissa protokolls par konfigurations information
        * preferHTTPS – ange om HTTPS ska användas över HTTP
        * Port – ange vilken målport du önskar.
        * disableTraceRoute – detta gäller för test grupper vars protokoll är TCP eller ICMP. Den stoppar källor från identifiering av topologi och sökning efter hopp.
        * metod – detta gäller för test av konfigurationer vars protokoll är HTTP. Välj metoden HTTP-begäran – antingen GET eller POST
        * Sökväg – Ange Sök vägs parametrar som ska läggas till i URL
        * validStatusCodes – Välj tillämpliga status koder. Om svars koden inte matchar den här listan visas ett diagnostiskt meddelande
        * requestHeaders – ange anpassade rubrik strängar för begäran som kommer att skickas till målet
    * successThreshold – du kan ange tröskelvärden för följande nätverks parametrar:
        * checksFailedPercent – ange procent andelen kontroller som kan avbrytas när källor kontrollerar anslutningen till destinationer genom att använda de villkor som du har angett. Procent andelen misslyckade kontroller i TCP-eller ICMP-protokollet kan likställas med procent andelen av paket förlusten. För HTTP-protokoll representerar det här fältet den procent andel av HTTP-begäranden som inte fick något svar.
        * roundTripTimeMs – ange önskad söktext i millisekunder för hur lång tid det tar att ansluta till målet via test konfigurationen.

## <a name="scale-limits"></a>Skalnings gränser

Anslutnings övervakare har följande skalnings gränser:

* Högsta antal anslutnings övervakare per prenumeration per region: 100
* Högsta antal test grupper per anslutnings Övervakare: 20
* Högsta antal källor och mål per anslutnings Övervakare: 100
* Högsta antal testkonfigurationer per anslutnings Övervakare: 20

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du analyserar övervaknings data och ställer in aviseringar](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Lär dig [hur du diagnostiserar problem i nätverket](./connection-monitor-overview.md#diagnose-issues-in-your-network)