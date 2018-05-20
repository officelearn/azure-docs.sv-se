---
title: Felsöka vägar - PowerShell | Microsoft Docs
description: Lär dig hur du felsöker vägar i Azure Resource Manager-distributionsmodellen med hjälp av Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 4ef1387e3c8573a2bfa64c166f08bf47723eca62
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Felsöka flöden med hjälp av Azure PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
> 
> 

Om du stöter på problem med nätverksanslutningen till eller från din Azure virtuell dator (VM), kan vägar påverka din VM trafikflöden. Den här artikeln innehåller en översikt över diagnostikfunktionerna för vägar för att fortsätta felsökningen.

Vägtabeller är kopplad till undernät och börjar gälla på alla nätverksgränssnitt (NIC) i det undernätet. Följande typer av vägar kan tillämpas på varje gränssnitt:

* **Systemvägar:** varje undernät som skapats i ett Azure Virtual Network (VNet) har som standard system-routningstabeller som Tillåt lokal VNet-trafik, lokal trafik via VPN-gatewayer och Internet-trafik. Det finns också systemvägar för peerkoppla Vnet.
* **BGP-vägar:** sprids till nätverksgränssnitt via ExpressRoute eller plats-till-plats VPN-anslutningar. Mer information om BGP-routning genom att läsa den [BGP med VPN-gatewayer](../vpn-gateway/vpn-gateway-bgp-overview.md) och [ExpressRoute översikt](../expressroute/expressroute-introduction.md) artiklar.
* **Användardefinierade vägar (UDR):** om du använder virtuella installationer i nätverket eller är Tvingad tunneling trafik till ett lokalt nätverk via ett plats-till-plats-VPN, kan du ha användardefinierade vägar (udr: er) som är associerade med din routningstabellen för undernätet. Om du inte är bekant med udr: er kan du läsa den [användardefinierade vägar](virtual-networks-udr-overview.md#user-defined) artikel.

Det kan vara svårt att avgöra vilka sammanställd vägar fungerar med olika vägar som kan tillämpas på ett nätverksgränssnitt. För att felsöka VM nätverksanslutning, kan du visa de effektiva vägarna för ett nätverksgränssnitt i Azure Resource Manager-distributionsmodellen.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Använda effektiva vägar för att felsöka VM trafikflöde
Den här artikeln används följande scenario som exempel för att illustrera hur man felsöker effektiva vägar för ett nätverksgränssnitt:

En virtuell dator (*VM1*) ansluten till VNet (*VNet1*, prefix: 10.9.0.0/16) kan inte ansluta till en VM(VM3) i ett nyligen peered VNet (*VNet3*, prefixet 10.10.0.0/16). Det finns inga udr: er eller BGP-vägar tillämpas på VM1 NIC1 nätverksgränssnittet är anslutet till den virtuella datorn, endast systemvägar tillämpas.

Den här artikeln beskriver hur du ta reda på orsaken till anslutningsfelet med effektiva vägar funktionen i Azure Resource Manager-distributionsmodellen.
Samma steg kan användas för att fastställa inkommande och utgående anslutningsfel via en väg typ medan i exemplet används endast systemvägar.

> [!NOTE]
> Om den virtuella datorn har mer än ett nätverkskort anslutet, kontrollera effektiva vägar för varje nätverkskort att diagnostisera problem med nätverksanslutningen till och från en virtuell dator.
> 
> 

### <a name="view-effective-routes-for-a-virtual-machine"></a>Visa effektiva flöden för en virtuell dator
Om du vill se sammanställd vägar som tillämpas på en virtuell dator, gör du följande:

### <a name="view-effective-routes-for-a-network-interface"></a>Visa effektiva vägar för ett nätverksgränssnitt
Om du vill se sammanställd vägar som tillämpas på ett nätverksgränssnitt, gör du följande:

1. Starta en Azure PowerShell-sessionen och logga in på Azure. Om du inte är bekant med Azure PowerShell kan du läsa den [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) artikel. Ditt konto ha tilldelats den *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* åtgärden för nätverksgränssnittet. Information om hur du tilldelar åtgärder till konton finns [skapa anpassade roller för rollbaserad åtkomstkontroll i](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Följande kommando returnerar alla vägar som tillämpas på ett nätverksgränssnitt med namnet *VM1 NIC1* i resursgruppen *RG1*.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Om du inte vet namnet på ett nätverksgränssnitt, Skriv följande kommando för att hämta namnen på alla nätverksgränssnitt i en resurs group.*
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   Följande utdata liknar utdata för varje flöde tillämpad på undernätet som nätverkskortet är anslutet till:
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   Observera följande i utdata:
   
   * **Namnet**: effektiva vägen får vara tomt, om inte uttryckligen anges för användardefinierade vägar. 
   * **Tillstånd**: Visar status för effektiva vägen. Möjliga värden är ”aktiv” eller ”ogiltig”
   * **Matrisen AddressPrefixes**: Anger adressprefixet för effektiva vägen i CIDR-notering. 
   * **nextHopType**: Anger next hop för den angivna vägen. Möjliga värden är *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, eller *Null*. Ett värde av *Null* för **nextHopType** i en UDR kan tyda på en ogiltig väg. Till exempel om **nextHopType** är *VirtualAppliance* och den virtuella nätverksenhet virtuella datorn inte är i tillståndet etablerats/körs. Om **nextHopType** är *VPNGateway* och det finns ingen gateway etablerats/körs i den angivna VNet vägen kan bli ogiltig.
   * **NextHopIpAddress**: IP-adressen för nästa hopp för effektiva vägen.
   
   Följande kommando returnerar vägarna i en enklare att läsa tabellen:
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   Följande utdata är några av utdata togs emot för det scenario som beskrivs ovan:
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. Det finns ingen väg som listas till den *WestUS VNet3* VNet (prefixet 10.10.0.0/16)** från *WestUS VNet1* (Prefix 10.9.0.0/16) i utdata från föregående steg. I följande bild visas VNet-peering länka med den *WestUS VNet3* virtuella nätverk finns i den *frånkopplad* tillstånd.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    Länken dubbelriktad för peering har brutits, som förklarar varför VM1 kunde inte ansluta till VM3 i den *WestUS VNet3* VNet. Konfigurera en dubbelriktad VNet peering länk igen för *WestUS VNet1* och *WestUS VNet3* Vnet. Så här utdatan när länken VNet-peering har etablerats på rätt sätt:
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    När du bestämmer problemet kan du lägga till, ta bort, eller ändra vägar och routningstabeller. Skriv följande kommando för att visa en lista över de kommandon som används för att göra det:
   
        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Överväganden
Några saker att tänka på när du granskar i listan över vägar returnerades:

* Routning baserat på längsta Prefix-matchning (LPM) bland udr: er, systemet och BGP-vägar. Om mer än en väg med samma LPM-matchning väljs en väg baserat på dess ursprung i följande ordning:
  
  * Användardefinierad väg
  * BGP-väg
  * Systemväg (standard)
    
    Du kan bara se effektiva vägar som är baserat på de tillgängliga vägarna LPM-matchning med effektiva vägar. Genom att visa hur vägar faktiskt utvärderas till ett visst nätverkskort gör detta det mycket enklare att felsöka specifika vägar driftstörningar anslutning till eller från den virtuella datorn.
* Om du har udr: er och skickar trafik till en virtuell nätverksenhet (NVA) med *VirtualAppliance* som **nextHopType**, kontrollera att IP-vidarebefordring är aktiverat på en NVA som tar emot trafiken eller paket tappas. 
* Om framtvingad tunneltrafik aktiveras vidarebefordras alla utgående Internet-trafik till lokalt. RDP/SSH från Internet till den virtuella datorn kanske inte fungerar med den här inställningen, beroende på hur trafiken hanteras i lokalt. 
  Tvingad tunneltrafik kan aktiveras:
  * Om du använder plats-till-plats-VPN genom att ange en användardefinierad väg (UDR) med nextHopType som VPN-Gateway
  * Om en standardväg annonseras över BGP
* För VNet-peering trafik ska fungera korrekt, en systemväg med **nextHopType** *VNetPeering* måste finnas för peered VNet-prefix för intervallet. Om en sådan väg finns inte och länken VNet-peering verkar OK:
  * Vänta några sekunder och försök igen om det är en nyetablerade peering länk. Ibland tar längre tid att sprida väga på alla nätverksgränssnitt i ett undernät.
  * Nätverkssäkerhetsgrupp (NSG) regler kan påverka trafikflöde. Mer information finns i [felsöka Nätverkssäkerhetsgrupper](virtual-network-nsg-troubleshoot-powershell.md) artikel.

