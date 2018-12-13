---
title: 'Kontrollera anslutningen – ExpressRoute felsökningsguide: Azure | Microsoft Docs'
description: Den här sidan innehåller anvisningar för felsökning och verifiera anslutningen från slutpunkt till slutpunkt för en ExpressRoute-krets.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: a64aa59b205e8986b80a575c50041f826606e16f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272821"
---
# <a name="verifying-expressroute-connectivity"></a>Verifiera ExpressRoute-anslutning
Den här artikeln hjälper dig att kontrollera och felsökning av ExpressRoute-anslutningar. ExpressRoute, som utökar ett lokalt nätverk till Microsoft-molnet över en privat anslutning som är från en anslutningsleverantör, omfattar följande tre separata nätverkszoner:

-   Kundens nätverk
-   -Providernätverket
-   Microsoft-Datacenter

Syftet med det här dokumentet är att hjälpa användare att identifiera var (eller även om) ett anslutningsproblem finns och inom vilken zon därmed för att få hjälp från lämpligt team att lösa problemet. Om Microsoft-supporten krävs för att lösa ett problem, öppna ett supportärende med [Microsoft Support][Support].

> [!IMPORTANT]
> Det här dokumentet är avsett för att diagnostisera och åtgärda enkel problem. Det är inte avsedd att ersätta för Microsoft-supporten. Öppna ett supportärende med [Microsoft Support] [ Support] om det inte går att lösa problemet med hjälp av riktlinjerna.
>
>

## <a name="overview"></a>Översikt
Följande diagram visar logiska anslutning av en kundens nätverk till Microsoft-nätverk via ExpressRoute.
[![1]][1]

Talen anger viktiga nätverk punkter i föregående diagram. Nätverk-punkter refererar ofta via den här artikeln deras tillhörande nummer.

Beroende på modellen för ExpressRoute-anslutning (Cloud Exchange samplacering, Ethernet-anslutning eller alla-till-alla (IPVPN)) vara nätverk punkterna 3 och 4 växlar (Layer 2-enheter). Viktiga nätverk poäng illustreras är följande:

1.  Kunden compute-enhet (till exempel en server eller dator)
2.  CEs: Klientens gränsroutrar 
3.  Sämsta (CE riktar sig mot): Providern edge-routrar/växlar som står inför gränsroutrar. Kallas PE CEs i det här dokumentet.
4.  Sämsta (msee: N riktar sig mot): Providern edge-routrar/växlar som står inför msee. Kallas PE msee i det här dokumentet.
5.  Msee: Microsoft Enterprise Edge (MSEE) ExpressRoute routrar
6.  Gateway för virtuellt nätverk (VNet)
7.  Compute-enhet på Azure VNet

Om anslutningsmodeller samplacering av molnet Exchange eller Ethernet-anslutning används skulle gränsrouter (2) upprättar BGP-peering med msee (5). Nätverket punkterna 3 och 4 skulle fortfarande finns men vara något transparenta som Layer 2-enheter.

Om alla-till-alla (IPVPN) anslutning modellen används PEs (msee: N riktar sig mot) (4) skulle upprätta BGP-peering med msee (5). Vägar skulle sedan spridas till kundnätverk via IPVPN providernätverket.

>[!NOTE]
>För hög tillgänglighet för ExpressRoute-anslutningar kräver Microsoft ett redundant par med BGP-sessioner mellan msee (5) och PE-msee (4). Ett redundant par med nätverkssökvägar rekommenderas också mellan kundens nätverk och PE CEs. Dock kan en enda CE-enhet (2) vara ansluten till en eller flera PEs (3) i alla-till-alla (IPVPN) anslutning modellen.
>
>

För att verifiera en ExpressRoute-krets kan beskrivs följande steg (med den nätverkspunkt som anges av tillhörande nummer):
1. [Validera kretsetablering och tillstånd (5)](#validate-circuit-provisioning-and-state)
2. [Verifiera minst en ExpressRoute-peering är konfigurerad (5)](#validate-peering-configuration)
3. [Verifiera ARP mellan Microsoft och service provider (länk mellan 4 och 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Verifiera BGP och vägar på msee: N (BGP mellan 4 till 5 och 5 till 6 om ett virtuellt nätverk är ansluten)](#validate-bgp-and-routes-on-the-msee)
5. [Kontrollera trafik-statistik (trafik som passerar genom 5)](#check-the-traffic-statistics)

Fler verifieringar och kontroller ska läggas till i framtida, kom tillbaka varje månad!

## <a name="validate-circuit-provisioning-and-state"></a>Validera kretsetablering och tillstånd
En ExpressRoute-krets har skapas oavsett connectivity-modell och därför en Tjänstnyckel genereras för kretsetablering. Etablera en ExpressRoute-krets upprättar en redundant Layer 2-anslutningar mellan PE-msee (4) och msee (5). Mer information om hur du skapar, ändrar, etablera och verifiera en ExpressRoute-krets finns i artikeln [skapa och ändra en ExpressRoute-krets][CreateCircuit].

>[!TIP]
>En Tjänstnyckel identifierar en ExpressRoute-krets. Den här nyckeln krävs för de flesta av de powershell-kommandon som nämns i det här dokumentet. Dessutom bör du behöver hjälp från Microsoft eller från en ExpressRoute-partner att felsöka ett problem med ExpressRoute, ange tjänst-nyckel för att lätt identifiera kretsen.
>
>

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure portal
I Azure-portalen, status för en ExpressRoute-krets kan kontrolleras genom att välja ![2][2] på menyn till vänster Sidopanel och sedan välja ExpressRoute-kretsen. Om du väljer en ExpressRoute öppnas krets som visas under ”alla resurser” bladet ExpressRoute-krets. I den ![3][3] på bladet, ExpressRoute essentials visas enligt följande skärmbild:

![4][4]    

I ExpressRoute-Essentials *krets status* anger status för kretsen på Microsoft-sida. *Providerstatus* anger om kretsen har varit *etablerad/inte etablerats* på tjänstleverantör sida. 

För en ExpressRoute-krets ska fungera, den *krets status* måste vara *aktiverad* och *Providerstatus* måste vara *etablerad*.

>[!NOTE]
>Om den *krets status* är inte aktiverad, kontakta [Microsoft Support][Support]. Om den *Providerstatus* har inte etablerats kontakta tjänstleverantören.
>
>

### <a name="verification-via-powershell"></a>Verifiering via PowerShell
Om du vill visa alla ExpressRoute-kretsar i en resursgrupp, använder du följande kommando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Du kan få namn på resursgruppen via Azure. Se underavsnittet tidigare i det här dokumentet och Observera att resursgruppens namn visas i exemplet skärmbild.
>
>

Om du vill välja en viss ExpressRoute-krets i en resursgrupp, använder du följande kommando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

En exempelsvaret är:

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

För att bekräfta om en ExpressRoute-krets fungerar, särskilt uppmärksam på följande fält:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Om den *CircuitProvisioningState* är inte aktiverad, kontakta [Microsoft Support][Support]. Om den *Korsanslutningens* har inte etablerats kontakta tjänstleverantören.
>
>

### <a name="verification-via-powershell-classic"></a>Verifiering via PowerShell (klassisk)
Om du vill visa alla ExpressRoute-kretsar för en prenumeration, använder du följande kommando:

    Get-AzureDedicatedCircuit

För att välja en viss ExpressRoute-krets, använder du följande kommando:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

En exempelsvaret är:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

För att bekräfta om en ExpressRoute-krets fungerar, särskilt uppmärksam på följande fält: Korsanslutningens: Etablerade Status: Enabled

>[!NOTE]
>Om den *Status* är inte aktiverad, kontakta [Microsoft Support][Support]. Om den *Korsanslutningens* har inte etablerats kontakta tjänstleverantören.
>
>

## <a name="validate-peering-configuration"></a>Verifiera Peering-konfigurationen
När tjänstleverantören har slutförts etableringen ExpressRoute-kretsen kan kan en routningskonfiguration skapas via ExpressRoute-krets mellan msee: N-pull-begäranden (4) och msee (5). Varje ExpressRoute-krets kan ha en, två eller tre routning kontexter aktiverat: Azure privat peering (trafik till privata virtuella nätverk i Azure), Azure offentlig peering (trafik till offentliga IP-adresser i Azure) och Microsoft peering (trafik till Office 365 och Dynamics 365). Mer information om hur du skapar och ändra routningskonfiguration finns i artikeln [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure portal

>[!NOTE]
>Om nivå 3 tillhandahålls av leverantören och peerings är tomma i portalen, uppdaterar du konfigurationen krets med hjälp av uppdateringsknappen på portalen. Den här åtgärden gäller rätt routningskonfiguration på din krets. 
>
>

I Azure-portalen, status för en ExpressRoute-krets kan kontrolleras genom att välja ![2][2] på menyn till vänster Sidopanel och sedan välja ExpressRoute-kretsen. Att välja en ExpressRoute skulle krets som visas under ”alla resurser” öppna bladet ExpressRoute-krets. I den ![3][3] på bladet, ExpressRoute essentials anges enligt följande skärmbild:

![5][5]

I föregående exempel, som anges Azure är privat peering routning kontext aktiverad, medan Azure offentlig och Microsoft peering routning kontexter inte har aktiverats. En aktiverades peering kontext skulle också ha primära och sekundära point-to-point (krävs för BGP)-undernät som visas. Den/30-undernät används för IP-adressen för gränssnittet för msee och PE msee. 

>[!NOTE]
>Om en peer-koppling inte är aktiverad, kan du kontrollera om de primära och sekundära undernäten som tilldelats matchar konfigurationen på PE msee. Om inte, om du vill ändra konfigurationen på msee: N routrar, referera till [skapa och ändra routning för en ExpressRoute-krets][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Verifiering via PowerShell
För att få Azure privat peering konfigurationsinformation, använder du följande kommandon:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

En exempelsvaret för en har konfigurerats privat peering, är:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 En aktiverades peering kontext skulle ha primära och sekundära IP-adressprefixen som visas. Den/30-undernät används för IP-adressen för gränssnittet för msee och PE msee.

För att få Azure offentlig peering konfigurationsinformation, använder du följande kommandon:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Om du vill hämta konfigurationsinformationen för Microsoft-peering, använder du följande kommandon:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Om en peer-koppling har konfigurerats, skulle det finnas ett felmeddelande. Ett exempelsvar när den angivna peering (offentlig Azure-peering i det här exemplet) inte har konfigurerats i kretsen:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Om en peer-koppling inte är aktiverad, kan du kontrollera om de primära och sekundära undernäten som tilldelats matchar konfigurationen på den länkade PE-MSEE. Kontrollera också om rätt *VlanId*, *AzureASN*, och *PeerASN* används på msee och om dessa värden som mappar till de som används på den länkade PE-MSEE. Om du väljer MD5-hash ska den delade nyckeln vara samma på nyckelpar msee: N och PE msee: N. Om du vill ändra konfigurationen på msee: N routrar, referera till [skapa och ändra routning för en ExpressRoute-krets] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verifiering via PowerShell (klassisk)
För att få Azure privat peering konfigurationsinformation, använder du följande kommando:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

En exempelsvaret för en har konfigurerats privat peering är:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

En aktiverad, peering kontext skulle ha de primära och sekundära peer-undernät som visas. Den/30-undernät används för IP-adressen för gränssnittet för msee och PE msee.

För att få Azure offentlig peering konfigurationsinformation, använder du följande kommandon:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Om du vill hämta konfigurationsinformationen för Microsoft-peering, använder du följande kommandon:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Om layer 3-peerings har ställts in av tjänsteleverantören, ställa in ExpressRoute-peerkopplingar via portalen eller PowerShell skriver över tjänstinställningar för providern. När du återställer providern sida peering-inställningar kräver stöd för tjänstleverantören. Endast ändra ExpressRoute-peerkopplingar om det är säkert att tjänstleverantören tillhandahåller layer 2-tjänster!
>
>

<p/>
>[!NOTE]
>Om en peer-koppling inte är aktiverad, kan du kontrollera om de primära och sekundära peer-undernät som tilldelats matchar konfigurationen på den länkade PE-MSEE. Kontrollera också om rätt *VlanId*, *AzureAsn*, och *PeerAsn* används på msee och om dessa värden som mappar till de som används på den länkade PE-MSEE. Om du vill ändra konfigurationen på msee: N routrar, referera till [skapa och ändra routning för en ExpressRoute-krets] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Verifiera ARP mellan Microsoft och tjänstleverantören
Det här avsnittet använder PowerShell (klassisk)-kommandon. Om du har använt PowerShell Azure Resource Manager-kommandon, kan du kontrollera att du har åtkomst för administratör/medadministratör till prenumerationen. Felsöka med Azure Resource Manager kommandon finns i den [komma ARP-tabeller i distributionsmodellen för Resource Manager] [ ARP] dokumentet.

>[!NOTE]
>Både Azure-portalen och Azure Resource Managers PowerShell-kommandon kan användas för att få ARP. Om det uppstår fel med Azure Resource Managers PowerShell-kommandon, bör klassiska PowerShell-kommandon fungera som klassisk PowerShell kommandon fungerar också med Azure Resource Manager ExpressRoute-kretsar.
>
>

Om du vill hämta ARP-tabell från den primära msee: N-routern för privat peering, använder du följande kommando:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempel på ett svar för kommandot lyckas scenariot:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

På samma sätt du kan kontrollera ARP-tabell från msee: N i den *primära*/*sekundära* sökvägen för *privata*/*offentliga*  / *Microsoft* peerings.

I följande exempel visas svaret i kommandot för en peer-koppling inte finns.

    ARP Info:
       
>[!NOTE]
>Granska följande information om ARP-tabell inte har IP-adresserna för de gränssnitt som mappats till MAC-adresser:
>1. Om den första IP-adressen för/30 undernät som har tilldelats för länken mellan msee: N-PR och msee: N används på gränssnittet pullförfrågan msee: N. Azure använder alltid den andra IP-adressen för msee.
>2. Kontrollera om kunden (C-tagg) och VLAN tjänsttaggar (S-tagg) matchar både på nyckelpar msee: N-PR och msee: N.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Verifiera BGP och vägar på den msee: N
Det här avsnittet använder PowerShell (klassisk)-kommandon. Om du har använt PowerShell Azure Resource Manager-kommandon, kan du kontrollera att du har åtkomst för administratör/medadministratör till prenumerationen.

>[!NOTE]
>Både Azure-portalen och Azure Resource Managers PowerShell-kommandon kan användas för att hämta information om BGP. Om det uppstår fel med Azure Resource Managers PowerShell-kommandon, bör klassiska PowerShell-kommandon fungera som klassisk PowerShell kommandon fungerar också med Azure Resource Manager ExpressRoute-kretsar.
>
>

Om du vill hämta routningstabell (BGP-Grannens) för en viss routning kontext, använder du följande kommando:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempel på ett svar är:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

I föregående exempel visas är kommandot användbart för att fastställa hur länge routning sammanhanget har upprättats. Den anger också antalet route-prefix som annonseras av peering routern.

>[!NOTE]
>Om tillståndet är i aktivt eller inaktivt, kontrollera om de primära och sekundära peer-undernät som tilldelats matchar konfigurationen på den länkade PE-MSEE. Kontrollera också om rätt *VlanId*, *AzureAsn*, och *PeerAsn* används på msee och om dessa värden som mappar till de som används på den länkade PE-MSEE. Om du väljer MD5-hash ska den delade nyckeln vara samma på nyckelpar msee: N och PE msee: N. Om du vill ändra konfigurationen på msee: N routrar, referera till [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].
>
>

<p/>
>[!NOTE]
>Om vissa mål inte kan nås via en viss peering, kontrollerar du i routningstabellen för msee: erna som hör till viss peering kontexten. Om ett matchande prefix (kan vara NATed IP) finns i routningstabellen och kontrollera sedan om det finns brandväggar/NSG/ACL: er på sökvägen och om de att trafiken.
>
>

Att hämta den fullständiga routningstabellen från msee: N den *primära* sökvägen för speciellt *privata* routning kontext, använder du följande kommando:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempel lyckade resultat för kommandot är:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

På samma sätt du kan kontrollera routningstabellen från msee: N i den *primära*/*sekundära* sökvägen för *privata* /  *Offentliga*/*Microsoft* en peering kontext.

I följande exempel visas svaret i kommandot för en peer-koppling inte finns:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Kontrollera trafik-statistik
Om du vill hämta kombinerade vägen för primära och sekundära trafik statistik – byte in och ut – för en peering kontext använder du följande kommando:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Ett exempel på utdata för kommandot är:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ett exempel på utdata i kommandot för en icke-existerande peering är:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Nästa steg
Mer information eller hjälp finns i följande länkar:

- [Microsoft Support][Support]
- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logiska Expressroute-anslutning"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Alla resurser, ikon"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Översikt över ikonen"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials exempel skärmbild"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials exempel skärmbild"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






