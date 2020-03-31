---
title: 'Azure ExpressRoute: ARP-tabeller – Felsökning'
description: Den här sidan innehåller instruktioner om hur du hämtar ARP-tabellerna för en ExpressRoute-krets
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 4f1bd064dbc0909be3deba9180be1d8b3c066fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076571"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Skaffa ARP-tabeller i resurshanterarens distributionsmodell
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

I den här artikeln får du lära dig ARP-tabellerna för din ExpressRoute-krets.

> [!IMPORTANT]
> Det här dokumentet är avsett att hjälpa dig att diagnostisera och åtgärda enkla problem. Det är inte avsett att ersätta Microsoft-support. Du måste öppna en supportbiljett med [Microsoft-support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du inte kan lösa problemet med hjälp av vägledningen nedan.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP-tabeller (Address Resolution Protocol) och ARP
ARP (Address Resolution Protocol) är ett layer 2-protokoll som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa Ethernet-adressen (MAC-adressen) med en IP-adress.

Tabellen ARP innehåller en mappning av ipv4-adressen och MAC-adressen för en viss peering. ARP-tabellen för en ExpressRoute-krets peering ger följande information för varje gränssnitt (primärt och sekundärt)

1. Mappning av den lokala ipadressen för routergränssnittet till MAC-adressen
2. Mappning av ExpressRoute router gränssnitt IP-adress till MAC-adressen
3. Åldern på kartläggningen

ARP-tabeller kan hjälpa till att validera konfiguration av lager 2 och felsöka grundläggande anslutningsproblem för lager 2. 

Exempel ARP tabell: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


I följande avsnitt finns information om hur du kan visa ARP-tabellerna som visas av ExpressRoute-edge-routrarna. 

## <a name="prerequisites-for-learning-arp-tables"></a>Förutsättningar för att lära sig ARP-tabeller
Se till att du har följande innan du går vidare

* En giltig ExpressRoute-krets konfigurerad med minst en peering. Kretsen måste vara helt konfigurerad av anslutningsleverantören. Du (eller din anslutningsleverantör) måste ha konfigurerat minst en av peerings (Azure private, Azure public och Microsoft) på den här kretsen.
* IP-adressintervall som används för att konfigurera peerings (Azure private, Azure public och Microsoft). Granska ip-adresstilldelningsexempelna på [sidan ExpressRoute-routningskrav](expressroute-routing.md) för att få en förståelse för hur IP-adresser mappas till gränssnitt på din sida och på ExpressRoute-sidan. Du kan få information om peering-konfigurationen genom att granska [konfigurationssidan för ExpressRoute-peering](expressroute-howto-routing-arm.md).
* Information från ditt nätverksteam /anslutningsleverantör på MAC-adresserna för gränssnitt som används med dessa IP-adresser.
* Du måste ha den senaste PowerShell-modulen för Azure (version 1.50 eller nyare).

> [!NOTE]
> Om lager 3 tillhandahålls av tjänsteleverantören och ARP-tabellerna är tomma i portalen/utdata nedan uppdaterar du kretskonfigurationen med uppdateringsknappen på portalen. Den här åtgärden kommer att tillämpa rätt routningskonfiguration på din krets. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Skaffa ARP-tabeller för din ExpressRoute-krets
Det här avsnittet innehåller instruktioner om hur du kan visa ARP-tabellerna per peering med PowerShell. Du eller din anslutningsleverantör måste ha konfigurerat peering innan du går vidare. Varje krets har två banor (primära och sekundära). Du kan kontrollera ARP-tabellen för varje bana oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för privat Azure-peering
Följande cmdlet innehåller ARP-tabeller för Azure private peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Exempelutdata visas nedan för en av banorna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för offentlig Azure-peering
Följande cmdlet innehåller ARP-tabeller för offentlig Azure-peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Exempelutdata visas nedan för en av banorna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabeller för Microsoft-peering
Följande cmdlet innehåller ARP-tabeller för Microsoft peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Exempelutdata visas nedan för en av banorna

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Så här använder du den här informationen
ARP-tabellen för en peer-peer-konfiguration kan användas för att bestämma validera konfiguration och anslutning av lager 2. Det här avsnittet innehåller en översikt över hur ARP-tabeller kommer att se ut under olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-tabell när en krets är i driftläge (förväntat tillstånd)
* ARP-tabellen har en post för den lokala sidan med en giltig IP-adress och MAC-adress och en liknande post för Microsoft-sidan. 
* Den sista oktetten för den lokala IP-adressen kommer alltid att vara ett udda nummer.
* Den sista oktetten av Microsofts IP-adress kommer alltid att vara ett jämnt nummer.
* Samma MAC-adress visas på Microsoft-sidan för alla 3 peerings (primär / sekundär). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP-tabell när lokal/anslutningsleverantörssida har problem
Om det finns problem med den lokala leverantören eller anslutningsleverantören kan du se att antingen visas endast en post i ARP-tabellen eller så visas den lokala MAC-adressen ofullständig. Detta visar mappningen mellan MAC-adressen och IP-adressen som används på Microsoft-sidan. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

eller
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Öppna en supportbegäran med din anslutningsleverantör för att felsöka sådana problem. Om ARP-tabellen inte har IP-adresser för gränssnitten som mappas till MAC-adresser läser du följande information:
> 
> 1. Om den första IP-adressen för undernätet /30 som tilldelats för länken mellan MSEE-PR och MSEE används i gränssnittet för MSEE-PR. Azure använder alltid den andra IP-adressen för MSEEs.
> 2. Kontrollera om kunden (C-Tag) och service -VLAN-taggarna (Service) matchar både på MSEE-PR- och MSEE-par.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tabell när Microsoft-sidan har problem
* Du kommer inte att se en ARP-tabell som visas för en peering om det finns problem på Microsoft-sidan. 
* Öppna en supportbiljett med [Microsoft-support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ange att du har problem med anslutning till lager 2. 

## <a name="next-steps"></a>Efterföljande moment
* Validera Layer 3-konfigurationer för din ExpressRoute-krets
  * Hämta flödessammanfattning för att fastställa tillståndet för BGP-sessioner 
  * Hämta rutttabell för att avgöra vilka prefix som annonseras över ExpressRoute
* Validera dataöverföring genom att granska byte in/ut
* Öppna en supportbiljett med [Microsoft-support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du fortfarande har problem.

