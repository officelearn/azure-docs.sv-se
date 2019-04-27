---
title: 'Hämta ARP-tabeller - felsökning ExpressRoute: klassiska: Azure| Microsoft Docs'
description: Den här sidan innehåller anvisningar för att hämta ARP-tabeller för en ExpressRoute-krets - klassiska distributionsmodellen.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 3e49a1da0e8ea83faf5fc5a10d4c01a41d62fa88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883104"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Hämta ARP-tabeller i den klassiska distributionsmodellen
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

Den här artikeln vägleder dig genom stegen för att hämta ARP Address Resolution Protocol ()-tabeller för Azure ExpressRoute-kretsen.

> [!IMPORTANT]
> Det här dokumentet är avsedd att hjälpa dig att diagnostisera och åtgärda enkel problem. Det är inte avsedd att ersätta för Microsoft-supporten. Om du inte kan lösa problemet med hjälp av följande riktlinjer, öppna en supportbegäran med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Åtgärda Resolution Protocol (ARP) och ARP-tabeller
ARP är en Layer 2-protokollet som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa en Ethernet-adress (MAC-adress) till en IP-adress.

En ARP-tabell innehåller en mappning av IPv4-adress och MAC-adress för en viss peering. ARP-tabell för en ExpressRoute-krets peering innehåller följande information för varje gränssnitt (primära och sekundära):

1. Mappning av en lokal router gränssnittets IP-adress till en MAC-adress
2. Mappning av en ExpressRoute-router gränssnittets IP-adress till en MAC-adress
3. Mappningen ålder

ARP-tabeller kan hjälpa med verifierar Layer 2-konfiguration och felsökning av anslutningsproblem för grundläggande nivå 2.

Följande är ett exempel på en ARP-tabell:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Följande avsnitt innehåller information om hur du visar ARP-tabeller som visas för ExpressRoute edge-routrar.

## <a name="prerequisites-for-using-arp-tables"></a>Krav för att använda ARP-tabeller
Kontrollera att du har följande innan du fortsätter:

* En giltig ExpressRoute-krets som har konfigurerats med minst en peering. Kretsen måste vara konfigurerade av anslutningsprovidern. Du (eller din anslutningsleverantör) måste konfigurera minst en av peerings (Azure privat, Azure offentlig eller Microsoft) på den här kretsen.
* IP-adressintervall som används för att konfigurera peerings (Azure privat, Azure offentlig och Microsoft). Granska IP-adress tilldelning av exemplen i den [ExpressRoute routning kravsidan](expressroute-routing.md) att få en förståelse för hur IP-adresser mappas till gränssnitt på din sida och på ExpressRoute-sida. Du kan få information om peering-konfigurationen genom att granska den [konfigurationssidan i ExpressRoute-peering](expressroute-howto-routing-classic.md).
* Information från ditt nätverk team eller anslutningen providern om MAC-adresser för de gränssnitt som används med IP-adresserna.
* Den senaste Windows PowerShell-modulen för Azure (version 1,50 eller senare).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP-tabeller för ExpressRoute-krets
Det här avsnittet innehåller anvisningar om hur du visar ARP-tabeller för varje typ av peering med hjälp av PowerShell. Innan du fortsätter måste du eller din anslutningsleverantör att konfigurera peer-kopplingen. Varje krets har två sökvägar (primär eller sekundär). Du kan kontrollera ARP-tabell för varje sökväg oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för Azures privata peering
Följande cmdlet ger ARP tabeller för Azures privata peering:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Följande är exempel på utdata för en av sökvägarna:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för Azures offentliga peering:
Följande cmdlet ger ARP tabeller för Azures offentliga peering:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Följande är exempel på utdata för en av sökvägarna:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Följande är exempel på utdata för en av sökvägarna:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabeller för Microsoft-peering
Följande cmdlet ger ARP tabeller för Microsoft-peering:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Exempel på utdata visas nedan för en av sökvägarna.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hur du använder den här informationen
ARP-tabell med en peer-koppling kan användas för att verifiera Layer 2-konfiguration och anslutning. Det här avsnittet innehåller en översikt över hur ARP-tabeller ser i olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>När en krets är ett (förväntade) drifttillstånd ARP-tabell
* ARP-tabell har en post för den lokala sida med en giltig IP- och MAC-adress och liknande information för Microsoft-sida.
* Den sista oktetten för den lokala IP-adressen är alltid ett udda tal.
* Den sista oktetten ställdes in av Microsoft IP-adressen är alltid ett jämnt tal.
* Samma MAC-adress visas på Microsoft-sida för alla tre peerings (primär/sekundär).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP-tabell när den är lokal eller när den anslutningsleverantör sidan har problem
 Bara en post visas i ARP-tabell. Den visar mappningen mellan MAC-adressen och IP-adressen som används på Microsoft-sida.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Om det uppstår ett problem så här kan du öppna en supportbegäran din anslutningsleverantör för att lösa detta.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP-tabell när den Microsoft-sidan har problem
* En ARP-tabell som visas för en peer-koppling om det finns problem på Microsoft-sida visas inte.
* Öppna en supportbegäran med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ange att du har ett problem med Layer-2-anslutning.

## <a name="next-steps"></a>Nästa steg
* Kontrollera Layer 3-konfigurationer för ExpressRoute-krets:
  * Få en väg sammanfattning om du vill bestämma tillståndet för BGP-sessioner.
  * Få en routningstabell för att avgöra vilka prefix som annonseras över ExpressRoute.
* Verifiera dataöverföring genom att granska byte in och ut.
* Öppna en supportbegäran med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du fortfarande har problem.

