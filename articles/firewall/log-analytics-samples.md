---
title: Azure Monitor loggar för Azure Firewall
description: Azure Monitor loggar kan användas för att analysera din Azure-brandvägg. En exempel fil skapas i View Designer i Azure Monitor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/11/2020
ms.author: victorh
ms.openlocfilehash: 86538f6d0467eb15e549179166ca957902a2d0c3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659564"
---
# <a name="azure-monitor-logs-for-azure-firewall"></a>Azure Monitor loggar för Azure Firewall

Följande Azure Monitor loggar exempel kan användas för att analysera dina Azure Firewall-loggar. Exempel filen är inbyggd i View Designer i Azure Monitor, finns mer information om visnings design koncept i [View Designer i Azure Monitor](../azure-monitor/platform/view-designer.md) artikeln.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Vyn Azure Monitor loggar

Så här kan du konfigurera ett exempel Azure Monitor loggar visualisering. Du kan ladda ned exempel visualiseringen från lagrings platsen [Azure-dok-JSON-samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) . Det enklaste sättet är att högerklicka på hyperlänken på den här sidan och välja *Spara som* och ange ett namn som **AzureFirewall. omsview**. 

Utför följande steg för att lägga till vyn i Log Analytics arbets ytan:

1. Öppna arbets ytan Log Analytics i Azure Portal.
2. Öppna **Visa designer** under **Allmänt**.
3. Klicka på **Importera**.
4. Bläddra och välj den **AzureFirewall. omsview** -fil som du laddade ned tidigare.
5. Klicka på **Spara**.

Så här ser vyn ut för program regel logg data:

![Data för program regel logg](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

Och för data för nätverks regel loggen:

![Data för nätverks regel logg]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Azure-brandväggen loggar data nedan AzureDiagnostics med kategori som antingen **AzureFirewallApplicationRule** eller **AzureFirewallNetworkRule**. Data som innehåller informationen lagras i fältet msg_s. Med hjälp av [parse](/azure/kusto/query/parseoperator) -operatorn kan vi extrahera de olika intressanta egenskaperna från msg_s fältet. Frågorna nedan extraherar informationen för båda kategorierna.

## <a name="application-rules-log-data-query"></a>Data fråga för program regel logg

Frågan nedan analyserar data för program regel loggen. I de olika kommentars raderna finns det några rikt linjer för hur frågan skapats:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

Samma fråga i ett mer komprimerat format:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>Data fråga för nätverks regler

Följande fråga analyserar data för nätverks regel loggen. I de olika kommentars raderna finns det några rikt linjer för hur frågan skapats:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

Samma fråga i ett mer komprimerat format:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Data fråga för Threat Intelligence-logg

Följande fråga analyserar loggdata för hot informations regeln:

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="sample-logs"></a>Exempelloggar

I följande logg exempel visas de data som ingår i en loggpost.

:::image type="content" source="media/log-analytics-samples/log1.png" alt-text="Skärm bild av en loggpost. Flera värden visas, till exempel en tidstämpel, ett protokoll, ett port nummer, en åtgärd, en regel samling och en regel." border="false":::

:::image type="content" source="media/log-analytics-samples/log2.png" alt-text="Skärm bild av en loggpost. Flera värden visas, till exempel en tidstämpel, ett protokoll, käll-och mål-I-P-adresser och en åtgärd." border="false":::

:::image type="content" source="media/log-analytics-samples/log3.png" alt-text="Skärm bild av en loggpost. Flera värden visas, till exempel en tidstämpel, ett protokoll, käll-och mål-I-P-adresser och-portar och ett meddelande." border="false":::
## <a name="next-steps"></a>Nästa steg

Mer information om övervakning och diagnostik i Azure-brandväggen finns i [Självstudier: övervaka Azure Firewall-loggar och-mått](./firewall-diagnostics.md).