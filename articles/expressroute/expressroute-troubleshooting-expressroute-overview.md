---
title: 'Azure ExpressRoute: Verifiera anslutning – felsökningsguide'
description: Den här sidan innehåller instruktioner om felsökning och validering av slutanslutning till slutanslutning för en ExpressRoute-krets.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330965"
---
# <a name="verifying-expressroute-connectivity"></a>Verifiera ExpressRoute-anslutning
Den här artikeln hjälper dig att verifiera och felsöka ExpressRoute-anslutning. ExpressRoute utökar ett lokalt nätverk till Microsoft-molnet via en privat anslutning som ofta underlättas av en anslutningsleverantör. ExpressRoute-anslutning omfattar traditionellt tre olika nätverkszoner enligt följande:

-   Kundnätverk
-   Provider-nätverk
-   Microsoft-datacenter

> [!NOTE]
> I ExpressRoute direct connectivity model (som erbjuds bandbredd för 10/100 Gbit/s) kan kunderna ansluta direkt till Microsoft Enterprise Edge (MSEE) routers port. I direkt anslutningsmodellen finns det därför bara kund- och Microsoft-nätverkszoner.
>


Syftet med det här dokumentet är att hjälpa användaren att identifiera om och var det finns ett anslutningsproblem. På så sätt hjälpa till att söka stöd från lämpligt team för att lösa ett problem. Om Microsoft-support behövs för att lösa ett problem öppnar du en supportbiljett med [Microsoft Support][Support].

> [!IMPORTANT]
> Det här dokumentet är avsett att hjälpa till att diagnostisera och åtgärda enkla problem. Det är inte avsett att ersätta Microsoft-support. Öppna en supportbiljett med [Microsoft Support][Support] om du inte kan lösa problemet med hjälp av vägledningen.
>
>

## <a name="overview"></a>Översikt
I följande diagram visas den logiska anslutningen för ett kundnätverk till Microsoft-nätverk med ExpressRoute.
[![1]][1]

I föregående diagram anger siffrorna viktiga nätverkspunkter. Dessa nätverkspunkter refereras ibland i den här artikeln av deras associerade nummer. Beroende på ExpressRoute-anslutningsmodellen - Cloud Exchange Co-location, Point-to-Point Ethernet-anslutning eller Any-to-any (IPVPN) - kan nätverkspunkterna 3 och 4 vara växlar (Layer 2-enheter) eller routrar (Layer 3-enheter). I direkt anslutningsmodellen finns det inga nätverkspunkter 3 och 4. istället CEs (2) är direkt anslutna till MSEEs via mörk fiber. De viktigaste nätverkspunkterna som illustreras är följande:

1.  Kundberäkningsenhet (till exempel en server eller dator)
2.  CEs: Kundkantroutrar 
3.  PEs (CE-vänte): Provider edge routrar / switchar som är vända mot kundkant routrar. Kallas PE-CEs i detta dokument.
4.  PEs (MSEE inför): Provider kant routrar / växlar som är vända MSEEs. Kallas PE-MSEEs i det här dokumentet.
5.  MSEEs: Microsoft Enterprise Edge (MSEE) ExpressRoute-routrar
6.  Virtuell nätverksgateway (VNet)
7.  Beräkningsenhet på Azure VNet

Om cloud exchange-samlokaliserings-, Point-to-Point Ethernet- eller direktanslutningsmodeller används upprättar CEs (2) BGP-peering med MSEEs (5). 

Om anslutningsmodellen Any-to-any (IPVPN) används upprättar PE-MSEEs (4) BGP-peering med MSEEs (5). PE-MSEEs sprida de vägar som mottagits från Microsoft tillbaka till kundnätverket via IPVPN tjänsteleverantör nätverk.

> [!NOTE]
>För hög tillgänglighet upprättar Microsoft en helt redundant parallell anslutning mellan MSEEs (5) och PE-MSEEs (4) par. En helt redundant parallell nätverksväg uppmuntras också mellan kundnätverk och PE-CEs-par. Mer information om hög tillgänglighet finns i artikeln [Designa för hög tillgänglighet med ExpressRoute][HA]
>
>

Följande är de logiska stegen i felsökningen av ExpressRoute-krets:

* [Verifiera kretsetablering och tillstånd](#verify-circuit-provisioning-and-state)
  
* [Verifiera peering-konfiguration](#validate-peering-configuration)
  
* [Validera ARP](#validate-arp)
  
* [Validera BGP och vägar på MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Bekräfta trafikflödet](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verifiera kretsetablering och tillstånd
Etablering av en ExpressRoute-krets upprättar en redundant Layer 2-anslutningar mellan CEs/PE-MSEEs (2)/(4) och MSEEs (5). Mer information om hur du skapar, ändrar, etablerar och verifierar en ExpressRoute-krets finns i artikeln [Skapa och ändra en ExpressRoute-krets][CreateCircuit].

>[!TIP]
>En tjänstnyckel identifierar unikt en ExpressRoute-krets. Om du behöver hjälp från Microsoft eller från en ExpressRoute-partner för att felsöka ett ExpressRoute-problem, ange servicenyckeln för att enkelt identifiera kretsen.
>
>

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure-portalen
Öppna ExpressRoute-kretsbladet i Azure-portalen. I ![3-delen][3] av bladet listas ExpressRoute essentials som visas i följande skärmbild:

![4][4]    

I ExpressRoute Essentials anger *kretsstatusen* kretsens status på Microsoft-sidan. *Providerstatus* anger om kretsen har *etablerats/inte etablerats* på tjänsteleverantörens sida. 

För att en ExpressRoute-krets ska fungera måste *kretsstatus* *aktiveras* och *providerstatusen* måste *vara etablerad*.

> [!NOTE]
> När du har konfigurerat en ExpressRoute-krets kontaktar du Microsoft [Support][Support]om *kretsstatusen* inte är aktiverad. Om *providerstatusen* däremot inte har etablerats, kontakta din tjänsteleverantör.
>
>

### <a name="verification-via-powershell"></a>Verifiering via PowerShell
Om du vill visa en lista över alla ExpressRoute-kretsar i en resursgrupp använder du följande kommando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Om du letar efter namnet på en resursgrupp kan du hämta det genom att lista alla resursgrupper i din prenumeration med kommandot *Get-AzResourceGroup*
>


Om du vill välja en viss ExpressRoute-krets i en resursgrupp använder du följande kommando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Ett exempelsvar är:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

För att bekräfta om en ExpressRoute-krets är i drift, var särskilt uppmärksam på följande fält:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> När du har konfigurerat en ExpressRoute-krets kontaktar du Microsoft [Support][Support]om *kretsstatusen* inte är aktiverad. Om *providerstatusen* däremot inte har etablerats, kontakta din tjänsteleverantör.
>
>

## <a name="validate-peering-configuration"></a>Verifiera peering-konfiguration
När tjänsteleverantören har slutfört etableringen av ExpressRoute-kretsen kan flera eBGP-baserade routningskonfigurationer skapas över ExpressRoute-kretsen mellan CEs/MSEE-PEs (2)/(4) och MSEEs (5). Varje ExpressRoute-krets kan ha: Azure-privat peering (trafik till privata virtuella nätverk i Azure) och/eller Microsoft-peering (trafik till offentliga slutpunkter för PaaS och SaaS). Mer information om hur du skapar och ändrar routningskonfiguration finns i artikeln [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure-portalen

> [!NOTE]
> I IPVPN-anslutningsmodell tar tjänsteleverantörer på sig ansvaret att konfigurera peerings (layer 3-tjänster). I en sådan modell, efter att tjänsteleverantören har konfigurerat en peering och om peering är tom i portalen, försök att uppdatera kretskonfigurationen med hjälp av uppdateringsknappen på portalen. Den här åtgärden hämtar den aktuella routningskonfigurationen från kretsen. 
>

I Azure-portalen kan status för en ExpressRoute-krets peering kontrolleras under ExpressRoute-kretsbladet. I ![3-delen][3] av bladet skulle ExpressRoute-peerings listas som visas i följande skärmdump:

![5][5]

I föregående exempel, som nämnts Azure privat peering är etablerad, medan Azure offentliga och Microsoft peerings inte är etablerade. En peering-kontext med lyckade etablerade tjänster skulle också ha de primära och sekundära punkt-till-punkt-undernäten i listan. Undernäten /30 används för gränssnitts-IP-adressen för MSE:erna och CEs/PE-MSEEs. För peerings som etableras anger listan också vem som senast ändrade konfigurationen. 

> [!NOTE]
> Om det går att aktivera en peering kontrollerar du om de primära och sekundära undernäten som tilldelats matchar konfigurationen på den länkade CE/PE-MSEE. Kontrollera också om rätt *VlanId,* *AzureASN*och *PeerASN* används på MSEEs och om dessa värden mappar till de som används på den länkade CE/PE-MSEE. Om MD5-hashning väljs ska den delade nyckeln vara samma på MSEE- och PE-MSEE/CE-par. Tidigare konfigurerad delad nyckel skulle inte visas av säkerhetsskäl. Om du behöver ändra någon av dessa konfigurationer på en MSEE-router läser du [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering].  
>

> [!NOTE]
> I ett /30-undernät som tilldelats för gränssnittet väljer Microsoft den andra användbara IP-adressen för undernätet för MSEE-gränssnittet. Se därför till att undernätets första användbara IP-adress har tilldelats på peer-ce/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Verifiering via PowerShell
Om du vill hämta konfigurationsinformationen för Azure private peering använder du följande kommandon:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Ett exempelsvar för en privat peering som har konfigurerats är:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 En framgångsrikt aktiverad peering-kontext skulle ha de primära och sekundära adressprefixen listade. Undernäten /30 används för gränssnitts-IP-adressen för MSE:erna och CEs/PE-MSEEs.

Om du vill hämta konfigurationsinformation för Azure public peering använder du följande kommandon:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Om du vill hämta konfigurationsinformation för Microsoft peering använder du följande kommandon:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Om en peering inte är konfigurerad visas ett felmeddelande. Ett exempelsvar när den angivna peeringen (Azure Public peering i det här exemplet) inte konfigureras inom kretsen:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Om det går att aktivera en peering kontrollerar du om de primära och sekundära undernäten som tilldelats matchar konfigurationen på den länkade CE/PE-MSEE. Kontrollera också om rätt *VlanId,* *AzureASN*och *PeerASN* används på MSEEs och om dessa värden mappar till de som används på den länkade CE/PE-MSEE. Om MD5-hashning väljs ska den delade nyckeln vara samma på MSEE- och PE-MSEE/CE-par. Tidigare konfigurerad delad nyckel skulle inte visas av säkerhetsskäl. Om du behöver ändra någon av dessa konfigurationer på en MSEE-router läser du [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering].  
>
>

> [!NOTE]
> I ett /30-undernät som tilldelats för gränssnittet väljer Microsoft den andra användbara IP-adressen för undernätet för MSEE-gränssnittet. Se därför till att undernätets första användbara IP-adress har tilldelats på peer-ce/PE-MSEE.
>

## <a name="validate-arp"></a>Validera ARP

Tabellen ARP innehåller en mappning av IP-adressen och MAC-adressen för en viss peering. ARP-tabellen för en ExpressRoute-krets peering ger följande information för varje gränssnitt (primär och sekundär):
* Mappning av den lokala ipadressen för routergränssnittet till MAC-adressen
* Mappning av ExpressRoute router gränssnitt IP-adress till MAC-adressen
* Ålder för ARP-tabeller kan hjälpa till att validera konfiguration av lager 2 och felsökning av grundläggande anslutningsproblem för lager 2.


Se [Hämta ARP-tabeller i Resurshanterarens distributionsmodelldokument,][ARP] för hur du visar ARP-tabellen för en ExpressRoute-peering och hur du använder informationen för att felsöka anslutningsproblem på lager 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Validera BGP och vägar på MSEE

Om du vill hämta routningstabellen *Primary* från MSEE på primär sökvägen för den privata routningskontexten använder du följande kommando: *Private*

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Ett exempel på svar är:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Om tillståndet för en eBGP-peering mellan en MSEE och en CE/PE-MSEE är aktivt eller inaktivt, kontrollera om de primära och sekundära peer-undernäten som tilldelats matchar konfigurationen på den länkade CE/PE-MSEE. Kontrollera också om rätt *VlanId,* *AzureAsn*och *PeerAsn* används på MSEEs och om dessa värden mappar till de som används på den länkade PE-MSEE/CE. Om MD5-hashning väljs ska den delade nyckeln vara densamma på MSEE- och CE/PE-MSEE-par. Om du behöver ändra någon av dessa konfigurationer på en MSEE-router läser du [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering].
>


> [!NOTE]
> Om vissa destinationer inte kan nås via en peering kontrollerar du flödestabellen för MSE:erna för motsvarande peering-kontext. Om ett matchande prefix (kan vara NATed IP) finns i routningstabellen kontrollerar du om det finns brandväggar/NSG/ACLs på sökvägen som blockerar trafiken.
>


I följande exempel visas svaret från kommandot för en peering som inte finns:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Bekräfta trafikflödet
Om du vill hämta den kombinerade primära och sekundära trafikstatistiken för sökväg – byte och ut- i en peering-kontext använder du följande kommando:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

Ett exempel på kommandot är:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ett exempel på kommandot för en peering som inte finns är:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Efterföljande moment
Mer information eller hjälp finns i följande länkar:

- [Microsoft Support][Support]
- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Logisk expressruttanslutning"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikon för alla resurser"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikon för översikt"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Exempel skärmdump från Exempel på ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Exempel skärmdump från Exempel på ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





