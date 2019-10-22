---
title: 'Verifiera ExpressRoute fel söknings guide för anslutnings barhet: Azure | Microsoft Docs'
description: Den här sidan innehåller instruktioner om fel sökning och validering av slut punkt till slut punkt för en ExpressRoute-krets.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 026900e3dcbf7c20750bb8e17e44ba64897c9a30
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "71123448"
---
# <a name="verifying-expressroute-connectivity"></a>Verifiera ExpressRoute-anslutning
Den här artikeln hjälper dig att kontrol lera och felsöka ExpressRoute-anslutningen. ExpressRoute, som utökar ett lokalt nätverk till Microsoft-molnet via en privat anslutning som under lättas av en anslutnings leverantör, omfattar följande tre olika nätverks zoner:

-   Kund nätverk
-   Leverantörs nätverk
-   Microsoft-datacenter

Syftet med det här dokumentet är att hjälpa användarna att identifiera var (eller även om) ett anslutnings problem finns och inom vilken zon, och därmed söka efter hjälp från rätt team för att lösa problemet. Om Microsoft Support krävs för att lösa ett problem öppnar du ett support ärende med [Microsoft Support][Support].

> [!IMPORTANT]
> Det här dokumentet är avsett för att hjälpa till att diagnostisera och åtgärda enkla problem. Den är inte avsedd att ersätta Microsoft-supporten. Öppna ett support ärende med [Microsoft Support][Support] om du inte kan lösa problemet med hjälp av anvisningarna.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
Följande diagram visar den logiska anslutningen för ett kund nätverk till Microsoft-nätverk med ExpressRoute.
[![1]][1]

I föregående diagram indikerar talen viktiga nätverks punkter. Nätverks platserna refereras ofta till via den här artikeln med hjälp av deras associerade nummer.

Beroende på ExpressRoute anslutnings modell (Cloud Exchange-samplacering, punkt-till-punkt-Ethernet-anslutning eller någon-till-valfri (IPVPN)) kan nätverks punkterna 3 och 4 vara växlar (Layer 2-enheter). De viktiga nätverks punkterna illustreras på följande sätt:

1.  Kund beräknings enhet (till exempel en server eller dator)
2.  CEs: kund gräns routrar 
3.  Parameterentiteter (CE Facing): providers yttre routrar/växlar som riktar sig mot kund gräns routrar. Kallas PE-CEs i det här dokumentet.
4.  Parameterentiteter (MSEE: N Facing): providerns gräns routrar/växlar som är riktade till msee. Kallas PE-msee i det här dokumentet.
5.  Msee: Microsoft Enterprise Edge (MSEE: N) ExpressRoute-routrar
6.  Virtual Network-Gateway (VNet)
7.  Compute-enhet på Azure VNet

Om Cloud Exchange-samplaceringen eller anslutnings modellerna för punkt-till-punkt-anslutning används, skulle kundens Edge-router (2) upprätta BGP-peering med msee (5). Nätverks punkterna 3 och 4 finns fortfarande kvar, men är något transparent som Layer 2-enheter.

Om någon-till-valfri (IPVPN)-anslutnings modell används skulle Parameterentiteter (MSEE: N Facing) (4) upprätta BGP-peering med msee (5). Vägarna sprids sedan tillbaka till kund nätverket via IPVPN-tjänstens nätverk.

> [!NOTE]
>För ExpressRoute hög tillgänglighet kräver Microsoft ett redundant par BGP-sessioner mellan msee (5) och PE-msee (4). Ett redundant par nätverks Sök vägar uppmuntras också mellan kundens nätverk och PE-CEs. Men i en-till-alla (IPVPN) anslutnings modell kan en enda CE-enhet (2) anslutas till en eller flera Parameterentiteter (3).
>
>

För att verifiera en ExpressRoute-krets omfattas följande steg (med nätverks punkten som anges av det associerade antalet):
1. [Validera krets etablering och tillstånd (5)](#validate-circuit-provisioning-and-state)
2. [Verifiera att minst en ExpressRoute-peering har kon figurer ATS (5)](#validate-peering-configuration)
3. [Validera ARP mellan Microsoft och tjänst leverantören (länk mellan 4 och 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Verifiera BGP och vägar på MSEE: N (BGP mellan 4 och 5 och 5 till 6 om ett VNet är anslutet)](#validate-bgp-and-routes-on-the-msee)
5. [Kontrol lera trafik statistiken (trafik som passerar genom 5)](#check-the-traffic-statistics)

Fler verifieringar och kontroller kommer att läggas till i framtiden. kom tillbaka varje månad!

## <a name="validate-circuit-provisioning-and-state"></a>Validera krets etablering och tillstånd
Oavsett anslutnings modell måste en ExpressRoute-krets skapas och därmed en tjänst nyckel som genereras för krets etablering. Genom att etablera en ExpressRoute-krets upprättas redundant Layer 2-anslutningar mellan PE-msee (4) och msee (5). Mer information om hur du skapar, ändrar, etablerar och verifierar en ExpressRoute-krets finns i artikeln [skapa och ändra en ExpressRoute-krets][CreateCircuit].

>[!TIP]
>En tjänst nyckel identifierar unikt en ExpressRoute-krets. Den här nyckeln krävs för de flesta PowerShell-kommandon som anges i det här dokumentet. Om du behöver hjälp från Microsoft eller från en ExpressRoute-partner för att felsöka ett ExpressRoute-problem, ska du också ange tjänst nyckeln för att enkelt identifiera kretsen.
>
>

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure Portal
I Azure Portal kan du kontrol lera status för en ExpressRoute-krets genom att välja ![2][2] på menyn i den vänstra menyn och sedan välja ExpressRoute-kretsen. Om du väljer en ExpressRoute-krets som visas under "alla resurser" öppnas krets bladet ExpressRoute. I avsnittet ![3][3] på bladet visas ExpressRoute Essentials som visas på följande skärm bild:

![4][4]    

I ExpressRoute Essentials anger *kretsen* status för kretsen på Microsoft-sidan. *Leverantörs status* anger om kretsen har *etablerats/inte etablerats* på tjänstens leverantörs sida. 

För att en ExpressRoute-krets ska fungera måste *kretsens status* vara *aktive rad* och *providerns status* måste vara *etablerad*.

> [!NOTE]
> Kontakta [Microsoft Support][Support]om *krets statusen* inte är aktive rad. Kontakta tjänst leverantören om *providerns status* inte är etablerad.
>
>

### <a name="verification-via-powershell"></a>Verifiering via PowerShell
Om du vill visa en lista över alla ExpressRoute-kretsar i en resurs grupp använder du följande kommando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Du kan hämta resurs gruppens namn via Azure. Se föregående del avsnitt i det här dokumentet och Observera att resurs gruppens namn visas i skärm bilden exempel.
>
>

Använd följande kommando för att välja en viss ExpressRoute-krets i en resurs grupp:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Ett exempel svar är:

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

För att bekräfta om en ExpressRoute-krets fungerar, bör du särskilt tänka på följande fält:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Kontakta [Microsoft Support][Support]om *CircuitProvisioningState* inte är aktive rad. Kontakta tjänst leverantören om *ServiceProviderProvisioningState* inte har tillhandahållits.
>
>

### <a name="verification-via-powershell-classic"></a>Verifiering via PowerShell (klassisk)
Om du vill visa en lista över alla ExpressRoute-kretsar under en prenumeration använder du följande kommando:

    Get-AzureDedicatedCircuit

Om du vill välja en viss ExpressRoute-krets använder du följande kommando:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Ett exempel svar är:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

För att bekräfta om en ExpressRoute-krets fungerar, bör du särskilt tänka på följande fält: ServiceProviderProvisioningState: etablerings status: aktive rad

> [!NOTE]
> Kontakta [Microsoft Support][Support]om *status* inte är aktive rad. Kontakta tjänst leverantören om *ServiceProviderProvisioningState* inte har tillhandahållits.
>
>

## <a name="validate-peering-configuration"></a>Verifiera peering-konfiguration
När tjänste leverantören har slutfört etableringen av ExpressRoute-kretsen kan en konfiguration av routning skapas över ExpressRoute-kretsen mellan MSEE: N-pull (4) och msee (5). Varje ExpressRoute-krets kan ha en, två eller tre cirkulations kontexter aktiverade: Azures privata peering (trafik till privata virtuella nätverk i Azure), offentlig Azure-peering (trafik till offentliga IP-adresser i Azure) och Microsoft-peering (trafik till Office 365). Mer information om hur du skapar och ändrar konfiguration av routning finns i artikeln [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure Portal

> [!NOTE]
> Om Layer 3 tillhandahålls av tjänst leverantören och peer-kopplingarna är tomma i portalen uppdaterar du krets konfigurationen med knappen Uppdatera på portalen. Den här åtgärden kommer att tillämpa konfigurationen för rätt routning på din krets. 
>
>

I Azure Portal kan du kontrol lera status för en ExpressRoute-krets genom att välja ![2][2] på menyn i den vänstra menyn och sedan välja ExpressRoute-kretsen. Om du väljer en ExpressRoute-krets som visas under "alla resurser" öppnas krets bladet ExpressRoute. I avsnittet ![3][3] på bladet skulle ExpressRoute Essentials visas på följande skärm bild:

![5][5]

I föregående exempel, som noterade att kontexten för Azures privata peering-routning är aktive rad, medan Azures funktioner för offentlig och Microsoft peering routning inte är aktiverade. En aktive rad peering-kontext har också den primära och sekundära punkt-till-plats (krävs för BGP) undernät visas. /30-undernät används för IP-adressen för gränssnittet msee och PE-msee. 

> [!NOTE]
> Om en peering inte är aktive rad kontrollerar du om de primära och sekundära undernät som har tilldelats matchar konfigurationen på PE-msee. Om inte, för att ändra konfigurationen på MSEE: N-routrar, se [skapa och ändra routning för en ExpressRoute-krets][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Verifiering via PowerShell
Använd följande kommandon för att hämta konfigurations information för Azure privat peering:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Ett exempel svar, för en korrekt konfigurerad privat peering, är:

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

 En aktive rad peering-kontext skulle ha de primära och sekundära adress prefixen i listan. /30-undernät används för IP-adressen för gränssnittet msee och PE-msee.

Använd följande kommandon för att hämta konfigurations information för Azures offentliga peering:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Använd följande kommandon för att hämta konfigurations information för Microsoft-peering:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Om en peering inte har kon figurer ATS visas ett fel meddelande. Ett exempel svar när den angivna peering (offentlig Azure-peering i det här exemplet) inte har kon figurer ATS inom kretsen:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Om en peering inte är aktive rad kontrollerar du om de primära och sekundära undernät som har tilldelats matchar konfigurationen på den länkade PE-MSEE: N. Kontrol lera också om rätt *VlanId*, *AzureASN*och *PeerASN* används på msee och om dessa värden mappar till de som används i den länkade PE-msee: n. Om du väljer MD5-hash ska den delade nyckeln vara densamma på MSEE: N och PE-MSEE: N-paret. Om du vill ändra konfigurationen på MSEE: N-routrarna läser du [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verifiering via PowerShell (klassisk)
Använd följande kommando för att hämta konfigurations information för Azure privat peering:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Ett exempel svar för en korrekt konfigurerad privat peering är:

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

En aktive rad peering-kontext skulle ha de primära och sekundära peer-undernät som anges. /30-undernät används för IP-adressen för gränssnittet msee och PE-msee.

Använd följande kommandon för att hämta konfigurations information för Azures offentliga peering:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Använd följande kommandon för att hämta konfigurations information för Microsoft-peering:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Om Layer 3-peering har angetts av tjänst leverantören, skriver ExpressRoute-peering via portalen eller PowerShell över inställningarna för tjänst leverantören. Att återställa peering-inställningarna för leverantörs sidan kräver support från tjänst leverantören. Ändra bara ExpressRoute-peering om det är säkert att tjänste leverantören endast tillhandahåller lager 2-tjänster!
>
>

> [!NOTE]
> Om en peering inte är aktive rad kontrollerar du om de primära och sekundära peer-undernät som har tilldelats matchar konfigurationen på den länkade PE-MSEE: N. Kontrol lera också om rätt *VlanId*, *AzureAsn*och *PeerAsn* används på msee och om dessa värden mappar till de som används i den länkade PE-msee: n. Om du vill ändra konfigurationen på MSEE: N-routrarna läser du [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validera ARP mellan Microsoft och tjänst leverantören
I det här avsnittet används PowerShell-kommandon (klassiska). Om du har använt PowerShell-Azure Resource Manager kommandon, se till att du har administratörs-och medadministratörs åtkomst till prenumerationen. Fel sökning med Azure Resource Manager kommandon finns [i Hämta ARP-tabeller i distributions modell dokumentet i Resource Manager][ARP] .

> [!NOTE]
>För att få ARP kan du använda både Azure Portal-och Azure Resource Manager PowerShell-kommandon. Om fel uppstår med Azure Resource Manager PowerShell-kommandon bör klassiska PowerShell-kommandon fungera som klassiska PowerShell-kommandon fungerar också med Azure Resource Manager ExpressRoute-kretsar.
>
>

Använd följande kommando för att hämta ARP-tabellen från den primära MSEE: N-routern för privat peering:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempel på ett svar för kommandot, i ett framgångs scenario:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

På samma sätt kan du kontrol lera ARP-tabellen från MSEE: N i den *primära* /*sekundära* sökvägen för *privata* /*offentliga* /*Microsoft* -peering.

Följande exempel visar att kommandots svar inte finns för en peering.

    ARP Info:
       
> [!NOTE]
> Om ARP-tabellen inte har IP-adresser till gränssnitten som är mappade till MAC-adresser, granskar du följande information:
>1. Om den första IP-adressen för det/30-undernät som har tilldelats länken mellan MSEE: N-PR och MSEE: N används i gränssnittet för MSEE: N-PR. Azure använder alltid den andra IP-adressen för msee.
>2. Kontrol lera att VLAN-taggar för Customer (C-tag) och service (S-tag) matchar både på MSEE: N-PR-och MSEE: N-par.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Verifiera BGP och vägar på MSEE: N
I det här avsnittet används PowerShell-kommandon (klassiska). Om du har använt PowerShell-Azure Resource Manager kommandon, se till att du har administratörs-och medadministratörs åtkomst till prenumerationen.

> [!NOTE]
>För att hämta BGP-information kan både Azure Portal-och Azure Resource Manager PowerShell-kommandon användas. Om fel uppstår med Azure Resource Manager PowerShell-kommandon bör klassiska PowerShell-kommandon fungera som klassiska PowerShell-kommandon fungerar också med Azure Resource Manager ExpressRoute-kretsar.
>
>

Använd följande kommando för att hämta en sammanfattning av routningstabellen (BGP Neighbor) för en viss kontext:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempel svar är:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Som du ser i föregående exempel är kommandot användbart för att avgöra hur länge kontexten för routning har upprättats. Det anger också antalet väg-prefix som annonseras av peering-routern.

> [!NOTE]
> Om tillståndet är aktivt eller inaktivt kontrollerar du om de primära och sekundära peer-undernät som har tilldelats matchar konfigurationen på den länkade PE-MSEE: N. Kontrol lera också om rätt *VlanId*, *AzureAsn*och *PeerAsn* används på msee och om dessa värden mappar till de som används i den länkade PE-msee: n. Om du väljer MD5-hash ska den delade nyckeln vara densamma på MSEE: N och PE-MSEE: N-paret. Om du vill ändra konfigurationen på MSEE: N-routrarna läser du [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].
>
>

> [!NOTE]
> Om vissa mål inte kan kommas åt via en viss peering, kontrollerar du routningstabellen för den msee som tillhör den specifika peering-kontexten. Om det finns ett matchande prefix (NATed IP) finns i routningstabellen, kontrollerar du om det finns brand väggar/NSG/ACL: er på sökvägen och om de tillåter trafiken.
>
>

Använd följande kommando för att hämta den fullständiga routningstabellen från MSEE: N på den *primära* sökvägen för en viss *privat* routnings kontext:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempel på lyckade resultat för kommandot är:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

På samma sätt kan du kontrol lera routningstabellen från MSEE: N i den sekundära / *sekundära* sökvägen för *privat* /*offentlig* /*Microsoft* en peering-kontext.

Följande exempel visar att kommandots svar inte finns för en peering:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Kontrol lera trafik statistiken
Använd följande kommando för att få en kombinerad primär och sekundär väg för trafik statistik – byte in och ut--av en peering-kontext:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Ett exempel på utdata från kommandot är:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ett exempel på utdata från kommandot för en icke-befintlig peering är:

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
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logisk Express vägs anslutning"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikonen alla resurser"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Översikts ikon"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Skärm bild av ExpressRoute Essentials-exempel"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Skärm bild av ExpressRoute Essentials-exempel"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






