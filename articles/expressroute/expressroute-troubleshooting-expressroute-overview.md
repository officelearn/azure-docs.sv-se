---
title: "Verifiera anslutningsbarhet: Azure ExpressRoute felsökningsguide för | Microsoft Docs"
description: "Den här sidan innehåller instruktioner för felsökning och verifiera slutpunkt till slutpunkt-anslutningen för en ExpressRoute-krets."
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 9aa21beb8963462b1cb6bdad6079b01e4d2e9c34
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="verifying-expressroute-connectivity"></a>Verifiera en ExpressRoute-anslutning
ExpressRoute som sträcker sig ett lokalt nätverk via en privat anslutning som möjliggörs med hjälp av en provider för anslutning till Microsoft cloud omfattar följande tre separata nätverkszoner:

-   Kundens nätverk
-   Providern nätverk
-   Microsoft-Datacenter

Syftet med det här dokumentet är att hjälpa användare att identifiera var (eller även om) ett anslutningsproblem finns och inom vilken zon, vilket för att begära hjälp från rätt team för att lösa problemet. Om Microsoft-supporten krävs för att lösa ett problem, öppna ett supportärende med [Microsoft-supporten][Support].

> [!IMPORTANT]
> Det här dokumentet är avsett för att diagnostisera och åtgärda problem med enkla. Det är inte avsedd att vara en ersättning för Microsoft-supporten. Öppna ett supportärende med [Microsoft-supporten] [ Support] om det inte går att lösa problemet med hjälp av de riktlinjer som tillhandahålls.
>
>

## <a name="overview"></a>Översikt
Följande diagram visar logiska anslutningen för en kund nätverk till Microsoft-nätverk via ExpressRoute.
[![1]][1]

I föregående diagram visar talen viktiga nätverket punkter. Nätverket punkter refereras ofta via den här artikeln av deras associerade nummer.

Beroende på anslutningen ExpressRoute modellen (moln Exchange samordning, Point-to-Point Ethernet-anslutning eller alla-till-alla (IPVPN)) kanske nätverket punkterna 3 och 4 växlar (nivå 2-enheter). Följande är viktiga nätverket punkter visas:

1.  Kunden beräkning enheten (exempelvis en server eller dator)
2.  CEs: Klientens gränsroutrar 
3.  Sämsta (CE inriktad): providern edge routrar/växlar som är riktade mot klientens gränsroutrar. Kallas PE CEs i det här dokumentet.
4.  Sämsta (MSEE inriktad): providern edge routrar/växlar som är riktade mot MSEEs. Kallas PE MSEEs i det här dokumentet.
5.  MSEEs: Microsoft Enterprise kant (MSEE) ExpressRoute routrar
6.  Gateway för virtuellt nätverk (VNet)
7.  Compute-enhet i Azure VNet

Om anslutningen modeller molnet Exchange samplacering eller Point-to-Point Ethernet-anslutning används skulle klientens gränsrouter (2) upprätta BGP-peering med MSEEs (5). Nätverket punkterna 3 och 4 skulle fortfarande finns men är något transparent som Lager2-enheter.

Om alla-till-alla (IPVPN) anslutningen modellen används PEs (MSEE inriktad) (4) skulle upprätta BGP-peering med MSEEs (5). Vägar skulle sedan spridas till kunden nätverket via IPVPN service provider nätverk.

>[!NOTE]
>Microsoft kräver ett redundant BGP-sessioner mellan MSEEs (5) och PE-MSEEs (4)-par för ExpressRoute med hög tillgänglighet. Ett redundant par nätverkssökvägar rekommenderas också mellan kundens nätverk och PE CEs. Dock kan en enda CE-enhet (2) vara ansluten till en eller flera PEs (3) i modellen för alla-till-alla (IPVPN)-anslutning.
>
>

Om du vill validera en ExpressRoute-krets beskrivs följande steg (med den nätverkspunkt som anges av tillhörande nummer):
1. [Validera kretsetablering och tillstånd (5)](#validate-circuit-provisioning-and-state)
2. [Verifiera minst en ExpressRoute-peering är konfigurerad (5)](#validate-peering-configuration)
3. [Validera ARP mellan Microsoft och service provider (länk mellan 4 och 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Validera BGP och vägar på MSEE (BGP mellan 4 – 5 och 5 till 6 om ett virtuellt nätverk är ansluten)](#validate-bgp-and-routes-on-the-msee)
5. [Kontrollera trafik statistik (trafik som passerar genom 5)](#check-the-traffic-statistics)

Mer verifieringar och kontroller som läggs i framtida, kontrollerar tillbaka varje månad!

##<a name="validate-circuit-provisioning-and-state"></a>Validera kretsetablering och tillstånd
En ExpressRoute-krets måste skapas och därför en nyckel genereras för kretsetablering oavsett connectivity-modell. Etablera en ExpressRoute-krets upprättar en redundant nivå 2-anslutningar mellan PE-MSEEs (4) och MSEEs (5). Mer information om hur du skapar, ändra, konfigurera och verifiera en ExpressRoute-krets finns i artikeln [skapa och ändra en ExpressRoute-krets][CreateCircuit].

>[!TIP]
>Nyckeln för tjänsten identifierar en ExpressRoute-krets. Den här nyckeln är obligatorisk för de flesta av powershell-kommandon som nämns i det här dokumentet. Dessutom bör du behöver hjälp från Microsoft eller från en ExpressRoute-partner om du vill felsöka problem ExpressRoute ange nyckeln för tjänsten för att lätt identifiera kretsen.
>
>

###<a name="verification-via-the-azure-portal"></a>Verifiering via Azure portal
I Azure-portalen, status för en ExpressRoute-krets kan kontrolleras genom att välja ![2][2] på vänster sidorutan-menyn och sedan välja ExpressRoute-kretsen. Om du markerar en ExpressRoute öppnas krets som anges under ”alla resurser” bladet ExpressRoute-kretsen. I den ![3][3] på bladet ExpressRoute essentials listas som visas i följande skärmbild visar:

![4][4]    

I ExpressRoute-Essentials *krets status* anger kretsstatusen på Microsoft-sida. *Providerstatus* visar om kretsen har *etablerad ej etablerad* på sida-leverantör. 

För en ExpressRoute-krets ska fungera, den *krets status* måste vara *aktiverad* och *Providerstatus* måste vara *etablerad*.

>[!NOTE]
>Om den *krets status* är inte aktiverad, kontakta [Microsoft-supporten][Support]. Om den *Providerstatus* har inte etablerats kontakta tjänstleverantören.
>
>

###<a name="verification-via-powershell"></a>Verifiering via PowerShell
Om du vill visa en lista med alla ExpressRoute-kretsar i en resursgrupp, använder du följande kommando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Du kan hämta din resursgruppens namn via Azure. Se föregående avsnitt i det här dokumentet och Observera att resursgruppens namn visas i exemplet skärmbild.
>
>

Om du vill välja en viss ExpressRoute-krets i en resursgrupp, använder du följande kommando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

En exempelsvar är:

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

För att bekräfta om en ExpressRoute-krets fungerar, särskilt noga följande fält:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Om den *CircuitProvisioningState* är inte aktiverad, kontakta [Microsoft-supporten][Support]. Om den *ServiceProviderProvisioningState* har inte etablerats kontakta tjänstleverantören.
>
>

###<a name="verification-via-powershell-classic"></a>Verifiering via PowerShell (klassisk)
Om du vill visa en lista med alla ExpressRoute-kretsar under en prenumeration, använder du följande kommando:

    Get-AzureDedicatedCircuit

Om du vill välja en viss ExpressRoute-krets, använder du följande kommando:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

En exempelsvar är:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

För att bekräfta om en ExpressRoute-krets fungerar, särskilt noga följande fält: ServiceProviderProvisioningState: etablerats Status: aktiverat

>[!NOTE]
>Om den *Status* är inte aktiverad, kontakta [Microsoft-supporten][Support]. Om den *ServiceProviderProvisioningState* har inte etablerats kontakta tjänstleverantören.
>
>

##<a name="validate-peering-configuration"></a>Verifiera Peering konfiguration
När etableringen ExpressRoute-kretsen tjänstleverantör har slutförts kan du skapa en routningskonfiguration via ExpressRoute-kretsen mellan MSEE fso (4) och MSEEs (5). Varje ExpressRoute-kretsen kan ha en, två eller tre routning kontexter aktiverad: Azure privat peering (trafik till privata virtuella nätverk i Azure), Azure offentlig peering (trafik till den offentliga IP-adresser i Azure) och Microsoft peering (trafik till Office 365 och Dynamics 365). Mer information om hur du skapar och ändra konfigurationen för routning finns i artikeln [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].

###<a name="verification-via-the-azure-portal"></a>Verifiering via Azure portal

>[!NOTE]
>Om nivå 3 tillhandahålls av leverantören och peerkopplingar är tomma på portalen, uppdatera krets konfigurationen med hjälp av uppdateringsknappen på den protal. Den här åtgärden gäller rätt routningskonfiguration kretsen. 
>
>

I Azure-portalen, status för en ExpressRoute-krets kan kontrolleras genom att välja ![2][2] på vänster sidorutan-menyn och sedan välja ExpressRoute-kretsen. Att välja en ExpressRoute öppnas krets som anges under ”alla resurser” bladet ExpressRoute-kretsen. I den ![3][3] på bladet ExpressRoute essentials anges som visas i följande skärmbild visar:

![5][5]

I föregående exempel, som noterats Azure är privat peering routning kontext aktiverad, medan Azure offentliga och Microsoft peering routning kontexter inte är aktiverade. En kontext som har aktiverats peering har också primära och sekundära point-to-point (krävs för BGP)-undernät i listan. Den/30-undernät som används för IP-adressen för gränssnittet för MSEEs och PE MSEEs. 

>[!NOTE]
>Om en peering inte är aktiverat, kontrollera om de primära och sekundära undernät som tilldelats matchar konfigurationen på PE MSEEs. Om inte, om du vill ändra konfigurationen på MSEE routrar, referera till [skapa och ändra routning för en ExpressRoute-krets][CreatePeering]
>
>

###<a name="verification-via-powershell"></a>Verifiering via PowerShell
För att få Azure privat peering konfigurationsinformation, använder du följande kommandon:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

En exempelsvar för en har konfigurerats privat peering, är:

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

 En kontext som har aktiverats peering skulle ha primära och sekundära adressprefix i listan. Den/30-undernät som används för IP-adressen för gränssnittet för MSEEs och PE MSEEs.

För att få Azure offentlig peering konfigurationsinformation, använder du följande kommandon:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

Använd följande kommandon för att hämta konfigurationsinformation för Microsoft-peering:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

Om en peering inte har konfigurerats, skulle det finnas ett felmeddelande. Ett exempelsvar, när den angivna peering (offentlig Azure-peering i det här exemplet) inte har konfigurerats i kretsen:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Om en peering inte är aktiverat, kontrollera om de primära och sekundära undernät som tilldelats matchar konfigurationen på den länkade PE-MSEE. Kontrollera också om rätt *VlanId*, *AzureASN*, och *PeerASN* används på MSEEs och om du värdena som mappar till de som används på den länkade PE-MSEE. Om du väljer MD5-hash ska delad nyckel vara samma på MSEE och PE MSEE. Om du vill ändra konfigurationen på MSEE routrar, referera till [skapa och ändra routning för en ExpressRoute-krets] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verifiering via PowerShell (klassisk)
För att få Azure privat peering konfigurationsinformation, använder du följande kommando:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

En exempelsvar för en har konfigurerats privat peering är:

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

A, aktiverats peering kontexten skulle ha primära och sekundära peer-undernät i listan. Den/30-undernät som används för IP-adressen för gränssnittet för MSEEs och PE MSEEs.

För att få Azure offentlig peering konfigurationsinformation, använder du följande kommandon:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Använd följande kommandon för att hämta konfigurationsinformation för Microsoft-peering:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Om nivå 3 peerkopplingar inställda av tjänstleverantören inställningen ExpressRoute peerkopplingar via portalen eller PowerShell skriver över tjänstinställningar för providern. Återställer providern sida peering-inställningar kräver stöd för tjänstleverantören. Ändra bara ExpressRoute peerkopplingar om det är säkert att tjänstleverantören tillhandahåller nivå 2-tjänster!
>
>

<p/>
>[!NOTE]
>Om en peering inte är aktiverat, kontrollera om de primära och sekundära peer-undernät som tilldelats matchar konfigurationen på den länkade PE-MSEE. Kontrollera också om rätt *VlanId*, *AzureAsn*, och *PeerAsn* används på MSEEs och om du värdena som mappar till de som används på den länkade PE-MSEE. Om du vill ändra konfigurationen på MSEE routrar, referera till [skapa och ändra routning för en ExpressRoute-krets] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validera ARP mellan Microsoft och -leverantör
Det här avsnittet använder PowerShell (klassisk)-kommandon. Om du har använt Azure Resource Manager för PowerShell-kommandon, kan du kontrollera att du har admin-/ medadministratör åtkomst till prenumerationen. Felsöka med Azure Resource Manager kommandon finns i den [komma ARP-tabeller i Resource Manager-distributionsmodellen] [ ARP] dokumentet.

>[!NOTE]
>Både Azure-portalen och Azure Resource Manager PowerShell-kommandon kan användas för att få ARP. Om det uppstår fel med Azure Resource Manager PowerShell-kommandon, bör klassiska PowerShell-kommandon fungera som klassiska PowerShell kommandon också fungera med Azure Resource Manager ExpressRoute-kretsar.
>
>

Om du vill hämta ARP-tabell från den primära MSEE routern för privat peering, använder du följande kommando:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempelsvar för kommandot i lyckade scenariot:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

På liknande sätt kan du ARP-tabell från MSEE i den *primära*/*sekundära* sökvägen för *privata*/*offentliga*/*Microsoft* peerkopplingar.

I följande exempel visas svaret i kommandot för en peering inte finns.

    ARP Info:
       
>[!NOTE]
>Granska följande information om ARP-tabell saknar IP-adresserna för de gränssnitt som mappats till MAC-adresser:
>1. Om den första IP-adressen för /30 undernät som har tilldelats för länken mellan MSEE PR och MSEE används för gränssnittet på MSEE PR. Azure använder alltid den andra IP-adressen för MSEEs.
>2. Kontrollera om kunden (C-tagg) och service (S-tagg) VLAN-taggarna matchar både på MSEE PR och MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Validera BGP och vägar på MSEE
Det här avsnittet använder PowerShell (klassisk)-kommandon. Om du har använt Azure Resource Manager för PowerShell-kommandon, kan du kontrollera att du har admin-/ medadministratör åtkomst till prenumerationen.

>[!NOTE]
>Både Azure-portalen och Azure Resource Manager PowerShell-kommandon kan användas för att hämta information om BGP. Om det uppstår fel med Azure Resource Manager PowerShell-kommandon, bör klassiska PowerShell-kommandon fungera som klassiska PowerShell kommandon också fungera med Azure Resource Manager ExpressRoute-kretsar.
>
>

Om du vill hämta routningstabell (BGP-Grannens) sammanfattning för en viss routning kontext, använder du följande kommando:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Ett exempelsvar är:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Som det visas i föregående exempel, är kommandot användbar för att fastställa hur länge routning kontexten har etablerats. Den anger också antalet väg prefix annonseras av peering routern.

>[!NOTE]
>Om den är i tillståndet aktivt eller inaktivt, kontrollerar du om de primära och sekundära peer-undernät som tilldelats matchar konfigurationen på den länkade PE-MSEE. Kontrollera också om rätt *VlanId*, *AzureAsn*, och *PeerAsn* används på MSEEs och om du värdena som mappar till de som används på den länkade PE-MSEE. Om du väljer MD5-hash ska delad nyckel vara samma på MSEE och PE MSEE. Om du vill ändra konfigurationen på MSEE routrar, referera till [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].
>
>

<p/>
>[!NOTE]
>Om vissa mål inte kan nås via en viss peering, kontrollera routningstabellen för MSEEs som hör till viss peering-kontexten. Om det finns ett matchande prefix (kan vara NATed IP) i routningstabellen, kontrollera om det finns ACL-brandväggar/NSG på sökvägen och de att trafiken.
>
>

Att hämta den fullständiga routningstabellen från MSEE den *primära* sökväg för en viss *privata* routning kontext, använder du följande kommando:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Exempel lyckade resultat för kommandot är:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

På liknande sätt kan du routningstabellen från MSEE i den *primära*/*sekundära* sökvägen för *privata*/*offentliga*/*Microsoft* en peering-kontext.

I följande exempel visas svaret i kommandot för en peering inte finns:

    Route Table Info:

##<a name="check-the-traffic-statistics"></a>Kontrollera trafik statistik
Om du vill få kombinerade primära och sekundära sökväg trafik statistik--använder byte in och ut – för en peering kontext, du följande kommando:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Ett exempel på utdata för kommandot är:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ett exempel på utdata i kommandot för en obefintlig peering är:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Nästa steg
Mer information eller hjälp finns på följande länkar:

- [Microsoft Support][Support]
- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logiska Expressroute-anslutning"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikon för alla resurser"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Översikt över ikonen"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials exempel skärmbild"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials exempel skärmbild"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[OldPortal]: https://manage.windowsazure.com
[ARP]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






