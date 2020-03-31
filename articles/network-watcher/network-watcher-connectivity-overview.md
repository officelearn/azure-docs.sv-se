---
title: Introduktion till felsökning av Anslutning till Azure Network Watcher | Microsoft-dokument
description: Den här sidan innehåller en översikt över felsökningsfunktionen för Network Watcher-anslutning
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: cae3072a3468b232e95d7c1949948b71059695ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283282"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Introduktion till felsöka anslutningar i Azure Network Watcher

Funktionen för anslutningsfelsökning i Network Watcher ger möjlighet att kontrollera en direkt TCP-anslutning från en virtuell dator till en virtuell dator (VM), fullständigt kvalificerat domännamn (FQDN), URI eller IPv4-adress. Nätverksscenarier är komplexa, de implementeras med hjälp av nätverkssäkerhetsgrupper, brandväggar, användardefinierade vägar och resurser som tillhandahålls av Azure. Komplexa konfigurationer gör det svårt att felsöka anslutningsproblem. Network Watcher hjälper till att minska tiden för att hitta och identifiera anslutningsproblem. De returnerade resultaten kan ge insikter om huruvida ett anslutningsproblem beror på en plattform eller ett problem med användarkonfigurationen. Anslutningen kan kontrolleras med [PowerShell,](network-watcher-connectivity-powershell.md) [Azure CLI](network-watcher-connectivity-cli.md)och [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Felsöka anslutningar kräver att den virtuella `AzureNetworkWatcherExtension` datorn som du felsöker från har vm-tillägget installerat. För att installera tillägget på en Windows VM besök [Azure Network Watcher Agent virtuell dator tillägg för Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och för Linux VM besök Azure Network [Watcher Agent virtuell dator tillägg för Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Tillägget krävs inte på målslutpunkten.

## <a name="response"></a>Svar

I följande tabell visas de egenskaper som returneras när felsÃ¶kningen av anslutningen har körts.

|Egenskap  |Beskrivning  |
|---------|---------|
|Anslutningsstatus     | Status för anslutningskontrollen. Möjliga resultat kan **nås** och **kan inte nås**.        |
|AvgLatencyInMs     | Genomsnittlig svarstid under anslutningskontrollen i millisekunder. (Visas endast om kontrollstatus kan nås)        |
|MinLatencyInMs     | Minsta svarstid under anslutningskontrollen i millisekunder. (Visas endast om kontrollstatus kan nås)        |
|MaxLatencyInMs     | Maximal svarstid under anslutningskontrollen i millisekunder. (Visas endast om kontrollstatus kan nås)        |
|SonderSent     | Antal avsökningar som skickats under kontrollen. Maxvärdet är 100.        |
|SonderFäst     | Antal avsökningar som misslyckades under kontrollen. Maxvärdet är 100.        |
|Hopp     | Hoppa för hoppsökväg från källa till mål.        |
|Humle[]. Typ     | Typ av resurs. Möjliga värden är **Källa,** **VirtualAppliance**, **VnetLocal**och **Internet**.        |
|Humle[]. Id | Unik identifierare för hoppet.|
|Humle[]. Adress | IP-adressen för hoppet.|
|Humle[]. ResourceId (resourceId) | ResourceID för hoppet om hoppet är en Azure-resurs. Om det är en internetresurs är ResourceID **Internet**. |
|Humle[]. NextHopIds (NextHopIds) | Den unika identifieraren för nästa hopp tas.|
|Humle[]. Frågor | En samling problem som påträffades under kontrollen vid det hoppet. Om det inte fanns några problem är värdet tomt.|
|Humle[]. Problem[]. Ursprung | Vid det aktuella hoppet, där problemet uppstod. Möjliga värden:<br/> **Inkommande** - Problemet finns på länken från föregående hopp till det aktuella hoppet<br/>**Utgående** - Problemet finns på länken från det aktuella hoppet till nästa hopp<br/>**Lokal** - Problemet är på den aktuella hopp.|
|Humle[]. Problem[]. Svårighetsgrad | Allvarlighetsgraden för problemet har identifierats. Möjliga värden är **Fel** och **Varning**. |
|Humle[]. Problem[]. Typ |Den typ av problem som hittades. Möjliga värden: <br/>**Cpu**<br/>**Minne**<br/>**GästFirewall**<br/>**DnsResolution (1)**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Humle[]. Problem[]. Sammanhang |Information om problemet hittades.|
|Humle[]. Problem[]. Sammanhang[].nyckel |Nyckeln för nyckelvärdeparet returneras.|
|Humle[]. Problem[]. Sammanhang[].värde |Värdet för nyckelvärdeparet returneras.|

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
## <a name="fault-types"></a>Feltyper

Felsökning av anslutning returnerar feltyper om anslutningen. Följande tabell innehåller en lista över de aktuella feltyper som returneras.

|Typ  |Beskrivning  |
|---------|---------|
|Processor     | Hög CPU-användning.       |
|Minne     | Hög minnesanvändning.       |
|GästFirewall     | Trafiken blockeras på grund av en brandvägg för virtuella datorer.        |
|DNSResolution (DNSResolution)     | DNS-matchningen misslyckades för måladressen.        |
|NetworkSecurityRule    | Trafiken blockeras av en NSG-regel (regeln returneras)        |
|UserDefinedRoute|Trafiken avstÃ¤nder en användardefinierad eller systemväg. |

### <a name="next-steps"></a>Nästa steg

Lär dig hur du felsöker anslutningar med [Azure-portalen,](network-watcher-connectivity-portal.md) [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md)eller [REST API](network-watcher-connectivity-rest.md).