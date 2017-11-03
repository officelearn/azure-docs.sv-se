---
title: "Introduktion till anslutningen Kontrollera i Azure-Nätverksbevakaren | Microsoft Docs"
description: "Den här sidan innehåller en översikt över Nätverksbevakaren anslutning kapaciteten"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 16ceef9c923b6a933a5caf752991b466346e0ebc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-connectivity-check-in-azure-network-watcher"></a>Introduktion till anslutningen incheckning Azure Nätverksbevakaren

Funktionen anslutningen i Nätverksbevakaren ger möjlighet att kontrollera en direkt TCP-anslutning från en virtuell dator till en virtuell dator (VM), fullständigt kvalificerade domännamnet (FQDN), URI, eller IPv4-adress. Scenarier för nätverk är komplexa, implementeras med hjälp av Nätverkssäkerhetsgrupper, brandväggar, användardefinierade vägar och resurser som tillhandahålls av Azure. Komplexa konfigurationer gör det svårt felsöker problem med nätverksanslutningen. Nätverksbevakaren hjälper till att minska mängden tid att hitta och identifiera problem med nätverksanslutningen. Resultaten kan ge insikter om ett anslutningsproblem beror på en plattform eller ett problem med användare. Anslutningen kan kontrolleras med [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md), och [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Kontrollera anslutningen kräver ett tillägg för virtuell dator `AzureNetworkWatcherExtension`. Installera tillägget på en Windows VM finns [tillägg för virtuell dator i Azure Network Watcher Agent för Windows](../virtual-machines/windows/extensions-nwa.md) och för Linux VM besöka [tillägg för virtuell dator i Azure Network Watcher Agent för Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="response"></a>Svar

I följande tabell visas de egenskaper som returneras när en anslutning kontroll har slutförts.

|Egenskap  |Beskrivning  |
|---------|---------|
|ConnectionStatus     | Status för anslutningen kontrollen. Resultat är **nåbar** och **inte åtkomlig**.        |
|AvgLatencyInMs     | Genomsnittlig svarstid under kontrollen av anslutningen i millisekunder. (Endast visas om statusen kan nås)        |
|MinLatencyInMs     | Minsta svarstid under kontrollen av anslutningen i millisekunder. (Endast visas om statusen kan nås)        |
|MaxLatencyInMs     | Maximal svarstid under kontrollen av anslutningen i millisekunder. (Endast visas om statusen kan nås)        |
|ProbesSent     | Antal avsökningar skickas under kontrollen. Värdet för maximalt antal är 100.        |
|ProbesFailed     | Antal avsökningar som misslyckades under kontrollen. Värdet för maximalt antal är 100.        |
|Hopp     | Hopp hopp sökväg från källan till målet.        |
|Hopp []. Typ     | Typ av resurs. Möjliga värden är **källa**, **VirtualAppliance**, **VnetLocal**, och **Internet**.        |
|Hopp []. ID | Unik identifierare för hopp.|
|Hopp []. Adress | IP-adress för hopp.|
|Hopp []. Resurs-ID | ResourceID hopp om hopp är en Azure-resurs. Om det är en internet-resurs ResourceID är **Internet**. |
|Hopp []. NextHopIds | Den unika identifieraren för nästa hopp som vidtagits.|
|Hopp []. Problem | En samling problem som uppstod under kontrollen på hoppet. Om det inte fanns några problem, är värdet tomt.|
|Hopp []. Utfärdar []. Ursprung | Vid den aktuella hopp där problemet uppstod. Möjliga värden:<br/> **Inkommande** -problemet finns på länken från föregående hopp till den aktuella hopp<br/>**Utgående** -problemet finns på länken från den aktuella hopp till nästa hopp<br/>**Lokala** -problemet finns på den aktuella hopp.|
|Hopp []. Utfärdar []. Allvarlighetsgrad | Allvarlighetsgrad för det identifierade problemet. Möjliga värden är **fel** och **varning**. |
|Hopp []. Utfärdar []. Typ |Typ av problem som hittas. Möjliga värden: <br/>**CPU**<br/>**Minne**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hopp []. Utfärdar []. Kontexten |Information om problemet finns.|
|Hopp []. Utfärdar []. Kontexten [] .key |Nyckeln för nyckel/värde-par returneras.|
|Hopp []. Utfärdar []. Kontexten [] .value |Det returnerade värdet för nyckel/värde-par.|

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
## <a name="fault-types"></a>Fel-typer

Kontrollen av anslutningen returnerar fel typer om anslutningen. Följande tabell innehåller en lista över aktuella fel returneras.

|Typ  |Beskrivning  |
|---------|---------|
|Processor     | Hög processoranvändning.       |
|Minne     | Hög minnesanvändning.       |
|GuestFirewall     | Trafik blockeras på grund av en virtuell dator brandväggskonfiguration.        |
|DNSResolution     | DNS-matchningen misslyckades för måladressen.        |
|NetworkSecurityRule    | Trafik blockeras av en regel för NSG (regeln returneras)        |
|UserDefinedRoute|Trafik ignoreras på grund av en användardefinierad eller systemväg. |

### <a name="next-steps"></a>Nästa steg

Lär dig att kontrollera anslutningen till en resurs genom att besöka: [Kontrollera anslutningen till Azure Nätverksbevakaren](network-watcher-connectivity-powershell.md).

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png

