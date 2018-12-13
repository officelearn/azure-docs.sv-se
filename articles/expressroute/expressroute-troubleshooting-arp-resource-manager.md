---
title: 'Hämta ARP-tabeller - felsökning – ExpressRoute: Azure | Microsoft Docs'
description: Den här sidan innehåller anvisningar om att hämta ARP-tabeller för en ExpressRoute-krets
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: a16b2dd61a1a04d8fc3362ce2e26c7d3c9433972
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274493"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Hämta ARP-tabeller i distributionsmodellen för Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

Den här artikeln vägleder dig igenom stegen för att lära dig ARP-tabeller för ExpressRoute-kretsen.

> [!IMPORTANT]
> Det här dokumentet är avsedd att hjälpa dig att diagnostisera och åtgärda enkel problem. Det är inte avsedd att ersätta för Microsoft-supporten. Du måste öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det inte går att lösa problemet genom att följa vägledningen beskrivs nedan.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Åtgärda Resolution Protocol (ARP) och ARP-tabeller
Protokollet ARP (Address Resolution) är en layer 2-protokollet som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa Ethernet-adress (MAC-adress) med en ip-adress.

ARP-tabell innehåller en mappning av ipv4-adress och MAC-adress för en viss peering. ARP-tabell för en ExpressRoute-krets peering innehåller följande information för varje gränssnitt (primär eller sekundär)

1. Mappning av den lokala routern gränssnittets ip-adress till MAC-adress
2. Mappning av ExpressRoute-router gränssnittets ip-adress till MAC-adress
3. Ålder på mappningen

ARP-tabeller kan hjälpa att validera konfigurationen av nivå 2 och felsöka grundläggande layer 2 anslutningsproblem. 

Exempel ARP-tabell: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Följande avsnitt innehåller information om hur du kan visa ARP-tabeller som setts av ExpressRoute edge-routrar. 

## <a name="prerequisites-for-learning-arp-tables"></a>Krav för learning ARP-tabeller
Kontrollera att du har följande innan du fortsätter ytterligare

* En giltig ExpressRoute-krets som har konfigurerats med minst en peering. Kretsen måste vara konfigurerade av anslutningsprovidern. Du (eller din anslutningsleverantör) måste ha konfigurerat minst en av peerings (Azure privat, Azure offentlig och Microsoft) på den här kretsen.
* IP-adressintervall som används för att konfigurera peerings (Azure privat, Azure offentlig och Microsoft). Granska ip-adress tilldelning av exemplen i den [ExpressRoute routning kravsidan](expressroute-routing.md) att få en förståelse för hur ip-adresser mappas till gränssnitt på din sida och på ExpressRoute-sida. Du kan få information om peering-konfigurationen genom att granska den [konfigurationssidan i ExpressRoute-peering](expressroute-howto-routing-arm.md).
* Information från ditt nätverksteam / anslutningsleverantör på MAC-adresserna för gränssnitt som används med dessa IP-adresser.
* Du måste ha den senaste PowerShell-modulen för Azure (version 1,50 eller senare).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Hämta ARP-tabeller för ExpressRoute-krets
Det här avsnittet innehåller anvisningar om hur du kan visa ARP-tabeller per peering med hjälp av PowerShell. Du eller din anslutningsleverantör måste ha konfigurerat peer-kopplingen innan du går vidare. Varje krets har två sökvägar (primär eller sekundär). Du kan kontrollera ARP-tabell för varje sökväg oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för Azures privata peering
Följande cmdlet ger ARP tabeller för Azures privata peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Exempel på utdata visas nedan för en av sökvägarna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för Azures offentliga peering
Följande cmdlet ger ARP tabeller för Azures offentliga peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Exempel på utdata visas nedan för en av sökvägarna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabeller för Microsoft-peering
Följande cmdlet ger ARP tabeller för Microsoft-peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Exempel på utdata visas nedan för en av sökvägarna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hur du använder den här informationen
ARP-tabell med en peer-koppling kan användas för att fastställa verifiera layer 2-konfiguration och anslutning. Det här avsnittet innehåller en översikt över hur ARP-tabeller ser ut under olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-tabell när en krets är drifttillstånd (förväntade tillståndet)
* ARP-tabell har en post för den lokala sidan med en giltig IP-adress och MAC-adress och liknande information för Microsoft-sida. 
* Den sista oktetten för den lokala ip-adressen kommer alltid att ett udda tal.
* Den sista oktetten ställdes in av Microsoft ip-adressen ska alltid vara ett jämnt tal.
* Samma MAC-adress visas på Microsoft-sida för alla 3 peerings (primär / sekundär). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP-tabell när den lokala / anslutning providern sida har problem
Om det finns problem med lokalt eller connectivity-leverantör som du kan se att antingen bara en post visas i ARP-tabell eller en lokal MAC-adress visas ofullständig. Det här alternativet visas mappningen mellan MAC-adress och IP-adress som används i Microsoft-sida. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

eller
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Öppna en supportbegäran om din anslutningsleverantör för att felsöka sådana problem. Granska följande information om ARP-tabell inte har IP-adresserna för de gränssnitt som mappats till MAC-adresser:
> 
> 1. Om den första IP-adressen för/30 undernät som har tilldelats för länken mellan msee: N-PR och msee: N används på gränssnittet pullförfrågan msee: N. Azure använder alltid den andra IP-adressen för msee.
> 2. Kontrollera om kunden (C-tagg) och VLAN tjänsttaggar (S-tagg) matchar både på nyckelpar msee: N-PR och msee: N.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tabell när Microsoft sida har problem
* En ARP-tabell som visas för en peer-koppling om det finns problem på Microsoft-sida visas inte. 
* Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ange att du har ett problem med layer 2-anslutning. 

## <a name="next-steps"></a>Nästa steg
* Kontrollera Layer 3-konfigurationer för ExpressRoute-krets
  * Hämta vägen sammanfattning om du vill bestämma tillståndet för BGP-sessioner 
  * Hämta routningstabellen för att avgöra vilka prefix som annonseras över ExpressRoute
* Verifiera dataöverföring genom att granska byte in / ut
* Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du fortfarande har problem.

