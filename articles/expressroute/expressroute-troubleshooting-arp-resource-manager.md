---
title: 'Hämtningen av ARP tabeller: hanteraren för filserverresurser: Azure ExpressRoute felsökning | Microsoft Docs'
description: Den här sidan innehåller instruktioner om hur du hämtar ARP tabeller för en ExpressRoute-krets
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850786"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Hämtningen av ARP tabeller i Resource Manager-distributionsmodellen
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

Den här artikeln vägleder dig genom stegen för att lära dig ARP-tabeller för ExpressRoute-kretsen. 

> [!IMPORTANT]
> Det här dokumentet är avsedda att hjälpa dig att diagnostisera och åtgärda problem med enkla. Det är inte avsedd att vara en ersättning för Microsoft-supporten. Du måste öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det inte går att lösa problemet med hjälp av vägledningen som beskrivs nedan.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Åtgärda ARP (Resolution Protocol) och ARP-tabeller
Protokollet ARP (Address Resolution) är en nivå 2-protokollet som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa en Ethernet-adress (MAC-adress) med en ip-adress.

ARP-tabell innehåller en mappning av ipv4-adress och MAC-adress för en viss peering. ARP-tabell för en ExpressRoute-krets peering innehåller följande information för varje gränssnitt (primär eller sekundär)

1. Mappning av lokala router-gränssnittet IP-adress till MAC-adress
2. Mappning av ExpressRoute-router-gränssnittet IP-adress till MAC-adress
3. Ålder mappningen

ARP-tabeller kan hjälpa dig att verifiera nivå 2-konfiguration och felsökning av grundläggande nivå 2 anslutningsproblem. 

Exempel ARP-tabell: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Följande avsnitt innehåller information om hur du kan visa ARP-tabeller som setts av routrar i utkanten av ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Krav för ARP-tabeller
Kontrollera att du har följande innan du fortskrider ytterligare

* En giltig ExpressRoute-kretsen har konfigurerats med minst en peering. Kretsen måste vara fullständigt konfigurerade av providern för anslutningen. Du (eller anslutningsleverantören) måste ha konfigurerat minst en av peerkopplingar (Azure privat, Azure offentliga och Microsoft) på den här kretsen.
* IP-adressintervall som används för att konfigurera peerkopplingar (Azure privat, Azure offentliga och Microsoft). Granska IP-adresstilldelning exemplen i den [ExpressRoute routning kravsidan](expressroute-routing.md) att få en förståelse för hur ip-adresser mappas till gränssnitt på din sida och på ExpressRoute-sida. Du kan få information om peering konfigurationen genom att granska den [konfigurationssidan för ExpressRoute-peering](expressroute-howto-routing-arm.md).
* Information från ditt nätverk team / anslutningen providern på MAC-adresserna för gränssnitt som används med IP-adresserna.
* Du måste ha den senaste PowerShell-modulen för Azure (version 1,50 eller senare).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Hämta ARP tabeller för ExpressRoute-krets
Det här avsnittet innehåller instruktioner om hur du kan visa ARP-tabeller per peering med hjälp av PowerShell. Du eller anslutningsleverantören måste ha konfigurerat peering innan du kommer ytterligare. Varje kretsen har två sökvägar (primär eller sekundär). Du kan kontrollera ARP-tabell för varje sökväg oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för privat Azure-peering
Följande cmdlet ger ARP tabeller för privat Azure-peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Exempel på utdata nedan för en av sökvägarna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för offentlig Azure-peering
Följande cmdlet ger ARP tabeller för offentlig Azure-peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Exempel på utdata nedan för en av sökvägarna

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


Exempel på utdata nedan för en av sökvägarna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hur du använder den här informationen
ARP-tabell för en peering kan användas för att fastställa Validera nivå 2-konfiguration och anslutningen. Det här avsnittet innehåller en översikt över hur ARP-tabeller ser ut under olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>När en anslutning har drifttillstånd (förväntade tillståndet) ARP-tabell
* ARP-tabellen har en post för den lokala sidan med en giltig IP-adress och MAC-adress och liknande information för Microsoft-sida. 
* Den sista oktetten i den lokala ip-adressen kommer alltid att ett udda tal.
* Den sista oktetten av Microsoft ip-adressen kommer alltid att ett jämnt tal.
* Samma MAC-adress visas på sidan för Microsoft för alla 3 peerkopplingar (primära / sekundära). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP-tabellen när lokalt / anslutningen providern sida har problem
Om det är problem med lokalt eller anslutningen providern kan du se att antingen en enda post visas i ARP-tabell eller lokal MAC-adress visas ofullständiga. Det här alternativet visas mappningen mellan MAC-adress och IP-adress som används i Microsoft-sida. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

eller
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Öppna en supportbegäran med anslutningsleverantören för att felsöka problem. Granska följande information om ARP-tabell saknar IP-adresserna för de gränssnitt som mappats till MAC-adresser:
> 
> 1. Om den första IP-adressen för /30 undernät som har tilldelats för länken mellan MSEE PR och MSEE används för gränssnittet på MSEE PR. Azure använder alltid den andra IP-adressen för MSEEs.
> 2. Kontrollera om kunden (C-tagg) och service (S-tagg) VLAN-taggarna matchar både på MSEE PR och MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tabellen när Microsoft sida har problem
* En ARP-tabell som visas för en peering om det finns problem på Microsoft-sida visas inte. 
* Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ange att du har ett problem med nivå 2-anslutningen. 

## <a name="next-steps"></a>Nästa steg
* Kontrollera Layer 3 konfigurationer för ExpressRoute-krets
  * Hämta väg sammanfattning om du vill bestämma tillståndet för BGP-sessioner 
  * Hämta routningstabellen för att avgöra vilka prefix har annonserats över ExpressRoute
* Validera dataöverföring genom att granska byte in / ut
* Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det fortfarande uppstår problem.

