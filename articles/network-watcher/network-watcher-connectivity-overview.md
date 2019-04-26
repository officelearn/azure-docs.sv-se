---
title: Introduktion till Azure Network Watcher anslutning felsöka | Microsoft Docs
description: Den här sidan innehåller en översikt över den felsökning kapaciteten för Network Watcher-anslutning
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: kumud
ms.openlocfilehash: 4b1164c3dedc5d8a2fa02d70f66ff00afe604836
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532461"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Introduktion till anslutningen felsöka i Azure Network Watcher

Felsöka anslutningen funktionen i Network Watcher kan du kontrollera en TCP direktanslutning från en virtuell dator till en virtuell dator (VM), fullständigt kvalificerade domännamnet (FQDN), URI eller IPv4-adress. Scenarier för nätverk är komplexa, implementeras med hjälp av nätverkssäkerhetsgrupper, brandväggar, användardefinierade vägar och resurser som tillhandahålls av Azure. Komplexa konfigurationer gör det svårt att felsöka anslutningsproblem. Network Watcher kan du minska tiden för att hitta och identifiera anslutningsproblem. Resultatet som returneras kan ge insikter om om ett anslutningsproblem beror på en plattform eller ett problem med användare. Anslutningen kan kontrolleras med [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md), och [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Felsökning av anslutning kräver att den virtuella datorn som du felsöker från har den `AzureNetworkWatcherExtension` VM-tillägget installerat. Installera tillägget på en Windows-VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och Linux VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Tillägget krävs inte på slutpunkten för målet.

## <a name="response"></a>Svar

I följande tabell visas egenskaperna returneras när anslutningsfelsökning har körts.

|Egenskap   |Beskrivning  |
|---------|---------|
|ConnectionStatus     | Status för Anslutningskontrollen. Möjliga resultat är **nåbar** och **tillbaka**.        |
|AvgLatencyInMs     | Genomsnittlig svarstid under anslutningskontroll i millisekunder. (Endast visas om status kontrollen kan nås)        |
|MinLatencyInMs     | Lägsta svarstid under anslutningskontroll i millisekunder. (Endast visas om status kontrollen kan nås)        |
|MaxLatencyInMs     | Maximal svarstid under anslutningskontroll i millisekunder. (Endast visas om status kontrollen kan nås)        |
|ProbesSent     | Antal avsökningar som skickats under denna kontroll. Max-värdet är 100.        |
|ProbesFailed     | Antal avsökningar som misslyckades under kontrollen. Max-värdet är 100.        |
|Hopp     | Hopp för hopp-sökvägen från källa till mål.        |
|Hops[].Type     | Typ av resurs. Möjliga värden är **källa**, **VirtualAppliance**, **VnetLocal**, och **Internet**.        |
|Hops[].Id | Unik identifierare för hoppet.|
|Hops[].Address | IP-adress hopp.|
|Hops[].ResourceId | ResourceID hopp om hoppet är en Azure-resurs. Om det är en internet-resurs, ResourceID är **Internet**. |
|Hops[].NextHopIds | Den unika identifieraren för nästa hopp vidtas.|
|Hops[].Issues | En samling problem som uppstod under kontrollen som hopp. Om det finns inga problem, är värdet tomt.|
|Hops[].Issues[].Origin | I det aktuella hoppet där problemet uppstod. Möjliga värden:<br/> **Inkommande** -problem finns på länken från det föregående hoppet till det aktuella hoppet<br/>**Utgående** -problem finns på länken från det aktuella hoppet till nästa hopp<br/>**Lokala** -problem finns på det aktuella hoppet.|
|Hops[].Issues[].Severity | Allvarlighetsgrad för problem som identifierats. Möjliga värden är **fel** och **varning**. |
|Hops[].Issues[].Type |Typ av problem hittades. Möjliga värden: <br/>**CPU**<br/>**Minne**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |Information om problem hittades.|
|Hops[].Issues[].Context[].key |Nyckeln för nyckel-värdepar returnerade.|
|Hops[].Issues[].Context[].value |Det returnerade värdet för nyckel-värdepar.|

Följande är ett exempel på ett problem som finns på ett hopp.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Typer av fel

Anslutningsfelsökning returnerar fel typer om anslutningen. Följande tabell innehåller en lista över de aktuella fault-typer som returneras.

|Typ  |Beskrivning  |
|---------|---------|
|Processor     | Hög processoranvändning.       |
|Minne     | Hög minnesanvändning.       |
|GuestFirewall     | Trafik blockeras på grund av en virtuell dator brandväggskonfiguration.        |
|DNSResolution     | DNS-matchningen misslyckades för måladressen.        |
|NetworkSecurityRule    | Trafik blockeras av en NSG-regel (regel returneras)        |
|UserDefinedRoute|Trafiken sjönk på grund av en användardefinierad eller systemväg. |

### <a name="next-steps"></a>Nästa steg

Lär dig att felsöka anslutningar som använder den [Azure-portalen](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md), eller [REST API](network-watcher-connectivity-rest.md).