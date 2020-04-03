---
title: 'Azure ExpressRoute: ARP-tabeller – Felsökning: klassiskt'
description: Den här sidan innehåller instruktioner för hur du hämtar ARP-tabellerna för en ExpressRoute-krets – klassisk distributionsmodell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: charwen
ms.openlocfilehash: a7a24fc6674adca21e01d2502263c9767510469e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618626"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Skaffa ARP-tabeller i den klassiska distributionsmodellen
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

I den här artikeln får du lära dig stegen för att hämta ARP-tabeller (Address Resolution Protocol) för din Azure ExpressRoute-krets.

> [!IMPORTANT]
> Det här dokumentet är avsett att hjälpa dig att diagnostisera och åtgärda enkla problem. Det är inte avsett att ersätta Microsoft-support. Om du inte kan lösa problemet med hjälp av följande vägledning öppnar du en supportbegäran med [Hjälp+support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)för Microsoft Azure .
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP-tabeller (Address Resolution Protocol) och ARP
ARP är ett Layer 2-protokoll som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa en Mac-adress (MAC-adress) till en IP-adress.

En ARP-tabell innehåller en mappning av IPv4-adressen och MAC-adressen för en viss peering. ARP-tabellen för en ExpressRoute-krets peering ger följande information för varje gränssnitt (primär och sekundär):

1. Mappning av en lokal IP-adress för routergränssnittet till en MAC-adress
2. Mappning av en IP-adress för Ett ExpressRoute-routergränssnitt till en MAC-adress
3. Åldern på kartläggningen

ARP-tabeller kan hjälpa till med att validera Layer 2-konfiguration och med felsökning av grundläggande layer 2-anslutningsproblem.

Följande är ett exempel på en ARP-tabell:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


I följande avsnitt finns information om hur du visar ARP-tabeller som visas av ExpressRoute-edge-routrarna.

## <a name="prerequisites-for-using-arp-tables"></a>Förutsättningar för att använda ARP-tabeller
Se till att du har följande innan du fortsätter:

* En giltig ExpressRoute-krets som är konfigurerad med minst en peering. Kretsen måste vara helt konfigurerad av anslutningsleverantören. Du (eller din anslutningsleverantör) måste konfigurera minst en av peerings (Azure private, Azure public eller Microsoft) på den här kretsen.
* IP-adressintervall som används för att konfigurera peerings (Azure private, Azure public och Microsoft). Granska IP-adresstilldelningsexempelna på [sidan ExpressRoute-routningskrav](expressroute-routing.md) för att få en förståelse för hur IP-adresser mappas till gränssnitt på din sida och på ExpressRoute-sidan. Du kan få information om peering-konfigurationen genom att granska [konfigurationssidan för ExpressRoute-peering](expressroute-howto-routing-classic.md).
* Information från nätverksteamet eller anslutningsleverantören om MAC-adresserna för de gränssnitt som används med dessa IP-adresser.
* Den senaste Windows PowerShell-modulen för Azure (version 1.50 eller senare).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP-tabeller för din ExpressRoute-krets
Det här avsnittet innehåller instruktioner om hur du visar ARP-tabellerna för varje typ av peering med hjälp av PowerShell. Innan du fortsätter måste antingen du eller anslutningsleverantören konfigurera peer-enheten. Varje krets har två banor (primära och sekundära). Du kan kontrollera ARP-tabellen för varje bana oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för privat Azure-peering
Följande cmdlet innehåller ARP-tabeller för privat Azure-peering:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Följande är exempelutdata för en av banorna:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för offentlig Azure-peering:
Följande cmdlet innehåller ARP-tabeller för offentlig Azure-peering:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Följande är exempelutdata för en av banorna:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Följande är exempelutdata för en av banorna:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabeller för Microsoft-peering
Följande cmdlet innehåller ARP-tabeller för Microsoft-peering:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Exempelutdata visas nedan för en av banorna:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Så här använder du den här informationen
ARP-tabellen för en peer-peer-konfiguration kan användas för att validera Layer 2-konfiguration och anslutning. Det här avsnittet innehåller en översikt över hur ARP-tabeller ser ut i olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP-tabell när en krets är i ett operativt (förväntat) tillstånd
* ARP-tabellen har en post för den lokala sidan med en giltig IP- och MAC-adress och en liknande post för Microsoft-sidan.
* Den sista oktetten för den lokala IP-adressen är alltid ett udda tal.
* Den sista oktetten av Microsofts IP-adress är alltid ett jämnt tal.
* Samma MAC-adress visas på Microsoft-sidan för alla tre peerings (primär / sekundär).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP-tabell när den är lokal eller när anslutningsprovidersidan har problem
 Endast en post visas i tabellen ARP. Den visar mappningen mellan MAC-adressen och IP-adressen som används på Microsoft-sidan.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Om du får ett problem som detta öppnar du en supportbegäran med din anslutningsleverantör för att lösa det.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP-tabell när Microsoft-sidan har problem
* Du kommer inte att se en ARP-tabell som visas för en peering om det finns problem på Microsoft-sidan.
* Öppna en supportbegäran med [Hjälp+support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)för Microsoft Azure . Ange att du har problem med Layer 2-anslutningen.

## <a name="next-steps"></a>Nästa steg
* Validera Layer 3-konfigurationer för din ExpressRoute-krets:
  * Hämta en flödessammanfattning för att fastställa tillståndet för BGP-sessioner.
  * Skaffa en rutttabell för att avgöra vilka prefix som annonseras över ExpressRoute.
* Validera dataöverföring genom att granska byte in och ut.
* Öppna en supportbegäran med [Hjälp+support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för Microsoft Azure om du fortfarande har problem.

