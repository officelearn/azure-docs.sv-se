---
title: "Felsöka vägar - Portal | Microsoft Docs"
description: "Lär dig hur du felsöker vägar i Azure Resource Manager-distributionsmodellen med hjälp av Azure-portalen."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: e0b835f4cbad9855bfb7ddccf2d9bf5b4bf88231
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Felsöka flöden med hjälp av Azure Portal
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

1. Logga in på Azure-portalen på https://portal.azure.com. Ditt konto ha tilldelats den *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* åtgärden för nätverksgränssnittet. Information om hur du tilldelar åtgärder till konton finns [skapa anpassade roller för rollbaserad åtkomstkontroll i](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Klicka på **alla tjänster**, klicka på **virtuella datorer** som visas i listan.
3. Välj en virtuell dator för att felsöka i listan som visas och en VM-bladet med alternativ visas.
4. Klicka på **diagnostisera & lösa problem** och välj sedan ett vanligt problem. I det här exemplet **jag kan inte ansluta till en virtuell dator Windows** är markerad.

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. Stegen visas under problem, som visas i följande bild:

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Klicka på *effektiva vägar* i listan över rekommenderade steg.
6. Den **effektiva vägar** bladet visas som i följande bild:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Om den virtuella datorn har bara ett nätverkskort, är den markerad som standard. Om du har mer än ett nätverkskort kan du markera nätverkskortet som du vill visa de effektiva vägarna.

   > [!NOTE]
   > Om den virtuella datorn som är kopplade till nätverkskortet inte är i körningstillstånd, visas inte effektiva vägar. Endast de första 200 effektiva vägarna som visas i portalen. En fullständig lista, klickar du på **hämta**. Du kan filtrera ytterligare på resultaten från hämtade CSV-filen.
   >
   >

    Observera följande i utdata:

   * **Källan**: Anger vilken typ av vägen. Systemvägar visas som *standard*, udr: er visas som *användare* och gateway-vägar (statisk eller BGP) visas som *VPNGateway*.
   * **Tillstånd**: Visar status för effektiva vägen. Möjliga värden är *Active* eller *ogiltigt*.
   * **Matrisen AddressPrefixes**: Anger adressprefixet för effektiva vägen i CIDR-notering.
   * **nextHopType**: Anger next hop för den angivna vägen. Möjliga värden är *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, eller *Null*. Ett värde av *Null* för **nextHopType** i en UDR kan tyda på en ogiltig väg. Till exempel om **nextHopType** är *VirtualAppliance* och den virtuella nätverksenhet virtuella datorn inte är i tillståndet etablerats/körs. Om **nextHopType** är *VPNGateway* och det finns ingen gateway etablerats/körs i den angivna VNet vägen kan bli ogiltig.
7. Det finns ingen väg som listas till den *WestUS VNET3* VNet (10.10.0.0/16 Prefix) från den *WestUS VNet1* (Prefix 10.9.0.0/16) i bilden i föregående steg. I följande bild är peering länken i den *frånkopplad* tillstånd:

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Länken dubbelriktad för peering har brutits, som förklarar varför VM1 kunde inte ansluta till VM3 i den *WestUS VNet3* VNet.
8. Följande bild visar vägar när du har etablerat dubbelriktad peering länken:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Mer felsökning scenarier för Tvingad tunneltrafik och väg utvärdering läsa den [överväganden](virtual-network-routes-troubleshoot-portal.md#considerations) i den här artikeln.

### <a name="view-effective-routes-for-a-network-interface"></a>Visa effektiva vägar för ett nätverksgränssnitt
Om flödet i nätverkstrafiken påverkas för ett visst nätverksgränssnitt (NIC), kan du visa en fullständig lista över giltiga vägar på ett nätverkskort direkt. Om du vill se sammanställd vägar som tillämpas på ett nätverkskort, gör du följande:

1. Logga in på Azure-portalen på https://portal.azure.com.
2. Klicka på **alla tjänster**, klicka på **nätverksgränssnitt**
3. Sök i listan för namnet på ett nätverkskort eller välj i listan som visas. I det här exemplet **VM1 NIC1** är markerad.
4. Välj **effektiva vägar** i den **nätverksgränssnittet** bladet som visas i följande bild:

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    Den **omfång** nätverksgränssnittet som valts som standard.

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Visa effektiva flöden för en routningstabell
När du ändrar användardefinierade vägar (udr: er) i en routningstabell kanske du vill granska effekten av de vägar som läggs till på en viss virtuell dator. En routingtabell kan associeras med flera undernät. Nu kan du visa de effektiva vägarna för alla nätverkskort som en given routingtabell tillämpas på, utan att växla kontext från bladet givet flöde tabell.

Till exempel en UDR (*UDRoute*) har angetts i en routningstabell (*UDRouteTable*). Den här vägen skickar alla Internet-trafiken från *Undernät1* i den *WestUS VNet1* VNet via en virtuell nätverksenhet (NVA) i *Undernät2* av samma virtuella nätverk. Vägen visas i följande bild:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Om du vill se sammanställd vägar i en routningstabell, gör du följande:

1. Logga in på Azure-portalen på https://portal.azure.com.
2. Klicka på **alla tjänster**, klicka på **routningstabeller**
3. Sök i listan för vägtabellen som du vill se sammanställd vägar för och markera den. I det här exemplet **UDRouteTable** är markerad. Ett blad för valda vägtabellen visas som i följande bild:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Välj **effektiva vägar** i den **routningstabellen** bladet. Den **omfång** har angetts till vägtabellen du valt.
5. En routingtabell kan tillämpas på flera undernät. Välj den **undernät** du vill visa i listan. I det här exemplet **Undernät1** är markerad.
6. Välj en **nätverksgränssnittet**. Alla nätverkskort som är anslutna till det valda undernätet listas. I det här exemplet **VM1 NIC1** är markerad.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Om nätverkskortet inte är associerad med en aktiv virtuell dator, visas inga effektiva vägar.
   >
   >

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
  * Nätverkssäkerhetsgrupp (NSG) regler kan påverka trafikflöde. Mer information finns i [felsöka Nätverkssäkerhetsgrupper](virtual-network-nsg-troubleshoot-portal.md) artikel.
