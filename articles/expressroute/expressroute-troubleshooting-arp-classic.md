---
title: 'Hämtningen av ARP tabeller: klassiska: Azure ExpressRoute felsökning | Microsoft Docs'
description: Den här sidan innehåller instruktioner för att hämta ARP tabeller för en ExpressRoute-krets.
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: b5856acf-03c2-4933-8111-6ce12998d92a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: fcc847b7e30fd55ca759830e0254ab7542e7663e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850800"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Hämtningen av ARP tabeller i den klassiska distributionsmodellen
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

Den här artikeln vägleder dig genom stegen för att hämta tabellerna protokollet ARP (Address Resolution) för Azure ExpressRoute-krets.

> [!IMPORTANT]
> Det här dokumentet är avsedda att hjälpa dig att diagnostisera och åtgärda problem med enkla. Det är inte avsedd att vara en ersättning för Microsoft-supporten. Om du inte kan lösa problemet med hjälp av följande riktlinjer, öppna en supportbegäran med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Åtgärda ARP (Resolution Protocol) och ARP-tabeller
ARP är en nivå 2-protokollet som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa en Ethernet-adress (MAC-adress) till en IP-adress.

En ARP-tabell ger en mappning av IPv4-adress och MAC-adress för en viss peering. ARP-tabell för en ExpressRoute-krets peering innehåller följande information för varje gränssnitt (primär eller sekundär):

1. Mappning av en lokal router-gränssnittet IP-adress till en MAC-adress
2. Mappning av en ExpressRoute router-gränssnittet IP-adress till en MAC-adress
3. Mappningen ålder

ARP-tabeller kan hjälpa med verifierar nivå 2-konfiguration och felsökning av grundläggande nivå 2-anslutningsproblem.

Följande är ett exempel på en ARP-tabell:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Följande avsnitt innehåller information om hur du visar ARP-tabeller som ses av routrar i utkanten av ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Krav för att använda ARP-tabeller
Kontrollera att du har följande innan du fortsätter:

* En giltig ExpressRoute-krets som är konfigurerad med minst en peering. Kretsen måste vara fullständigt konfigurerade av providern för anslutningen. Du (eller anslutningsleverantören) måste konfigurera minst en av peerkopplingar (Azure privat, Azure offentliga eller Microsoft) på den här kretsen.
* IP-adressintervall som används för att konfigurera peerkopplingar (Azure privat, Azure offentliga och Microsoft). Granska IP-adresstilldelning exemplen i den [ExpressRoute routning kravsidan](expressroute-routing.md) att få en förståelse för hur IP-adresser mappas till gränssnitt på din aise och ExpressRoute-sida. Du kan få information om peering konfiguration genom att granska den [konfigurationssidan för ExpressRoute-peering](expressroute-howto-routing-classic.md).
* Information från nätverk team eller anslutning leverantören om MAC-adresser för de gränssnitt som används med IP-adresserna.
* Senaste Windows PowerShell-modulen för Azure (version 1,50 eller senare).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP-tabeller för ExpressRoute-krets
Det här avsnittet innehåller instruktioner om hur du visar ARP-tabeller för varje typ av peering med hjälp av PowerShell. Innan du fortsätter måste du eller anslutningsleverantören att konfigurera peering. Varje kretsen har två sökvägar (primär eller sekundär). Du kan kontrollera ARP-tabell för varje sökväg oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för privat Azure-peering
Följande cmdlet ger ARP tabeller för privat Azure-peering:

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


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för offentlig Azure-peering:
Följande cmdlet ger ARP tabeller för offentlig Azure-peering:

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


För en av sökvägarna visas exempel på utdata nedan:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hur du använder den här informationen
ARP-tabell för en peering kan användas för att validera nivå 2-konfiguration och anslutningen. Det här avsnittet innehåller en översikt över hur ARP-tabeller ser ut i olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>När en anslutning är i tillståndet för operativa (förväntade) ARP-tabell
* ARP-tabellen har en post för den lokala sidan med en giltig IP- och MAC-adress och liknande information för Microsoft-sida.
* Den sista oktetten i den lokala IP-adressen är alltid ett udda tal.
* Den sista oktetten av Microsoft IP-adressen är alltid ett jämnt tal.
* Samma MAC-adress visas på Microsoft-sida för alla tre peerkopplingar (primära och sekundära).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>När den lokala eller när anslutningen-provider-sida har problem med ARP-tabell
 Endast en post visas i ARP-tabell. Mappningen mellan MAC-adressen och IP-adressen som används på Microsoft-sida visas.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Öppna en supportbegäran med anslutningsleverantören för att lösa det. Om det uppstår ett problem så här.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP-tabellen när Microsoft sida har problem
* En ARP-tabell som visas för en peering om det finns problem på Microsoft-sida visas inte.
* Öppna en supportbegäran med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ange att du har ett problem med nivå 2-anslutningen.

## <a name="next-steps"></a>Nästa steg
* Kontrollera Layer 3 konfigurationer för ExpressRoute-krets:
  * Hämta en väg sammanfattning om du vill bestämma tillståndet för BGP-sessioner.
  * Hämta en routningstabell för att avgöra vilka prefix har annonserats över ExpressRoute.
* Validera dataöverföring genom att granska byte in och ut.
* Öppna en supportbegäran med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det fortfarande uppstår problem.

