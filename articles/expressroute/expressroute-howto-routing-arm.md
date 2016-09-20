<properties
   pageTitle="Så här konfigurerar du routning för en ExpressRoute-krets | Microsoft Azure"
   description="Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/29/2016"
   ms.author="ganesr"/>

# Skapa och ändra routning för en ExpressRoute-krets


> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-routing-arm.md)
[PowerShell - klassiskt](expressroute-howto-routing-classic.md)



Den här artikeln vägleder dig igenom stegen för att skapa och hantera routningskonfiguration för en ExpressRoute-krets med hjälp av PowerShell och distributionsmodellen för Azure Resource Manager.  Stegen nedan visar också hur du kontrollerar status, uppdaterar eller tar bort och avetablerar peerings för en ExpressRoute-krets. 


**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Förutsättningar för konfiguration

- Du behöver den senaste versionen av Azure PowerShell-moduler version 1.0 eller senare. 
- Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
- Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd för att du ska kunna köra cmdletarna som beskrivs nedan.

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en Internet-leverantör som erbjuder hanteringstjänster till Layer 3 (vanligtvis en IPVPN, t.ex. MPLS) kommer anslutningsleverantören konfigurera och hantera routning för dig.

>[AZURE.IMPORTANT] Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontakta din tjänstleverantör innan du konfigurerar BGP-peerings.

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. 

## Azures privata peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures privata peeringskonfiguration för en ExpressRoute-krets. 

### Så här skapar du Azures privata peering

1. Importera PowerShell-modulen för ExpressRoute.
    
    Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

        Install-Module AzureRM

        Install-AzureRM

    Importera alla moduler som AzureRM.* inom det kända semantiska versionsintervallet

        Import-AzureRM

    Du kan också importera en vald modul inom det kända semantiska versionsintervallet 
        
        Import-Module AzureRM.Network 

    Logga in på ditt konto

        Login-AzureRmAccount

    Välj den prenumeration som du vill skapa ExpressRoute-kretsen för
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Skapa en ExpressRoute-krets.
    
    Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. 

    Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen. 

3. Kontrollera ExpressRoute-kretsen för att säkerställa att den har etablerats.

    Du måste först kontrollera om ExpressRoute-kretsen har etablerats och aktiverats. Se exemplet nedan.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Svaret bör likna exemplet nedan:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. Konfigurera Azures privata peering för kretsen.

    Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

    - Ett /30 undernät för den primära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
    - Ett /30 undernät för den sekundära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
    - Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
    - AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
    - En MD5-hash om du väljer att använda en. **Det här är valfritt**.
    
    Du kan köra följande cmdlet för att konfigurera Azures privata peering för din krets.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Du kan använda cmdleten nedan om du vill använda en MD5-hash.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.

### Så här visar du Azures privata peering-information

Du kan hämta information om konfigurationen med följande cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### Uppdatera konfigurationen av Azures privata peering

Du kan uppdatera någon del av konfigurationen med följande cmdlet. I exemplet nedan uppdateras VLAN-ID för kretsen från 100 till 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Så här tar du bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet.

>[AZURE.WARNING] Du måste se till att alla virtuella nätverk är avlänkade från ExpressRoute-kretsen innan du kör denna cmdlet. 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## Azures offentliga peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures offentliga peeringskonfiguration för en ExpressRoute-krets.

### Så här skapar du Azures offentliga peering

1. Importera PowerShell-modulen för ExpressRoute.
    
    Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

        Install-Module AzureRM

        Install-AzureRM

    Importera alla moduler som AzureRM.* inom det kända semantiska versionsintervallet

        Import-AzureRM

    Du kan också importera en vald modul inom det kända semantiska versionsintervallet 
        
        Import-Module AzureRM.Network 

    Logga in på ditt konto

        Login-AzureRmAccount

    Välj den prenumeration som du vill skapa ExpressRoute-kretsen för
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Skapa en ExpressRoute-krets.
    
    Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. 

    Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures offentliga peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen.

3. Kontrollera ExpressRoute-kretsen för att säkerställa att den har etablerats.

    Du måste först kontrollera om ExpressRoute-kretsen har etablerats och aktiverats. Se exemplet nedan.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Svaret bör likna exemplet nedan:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. Konfigurera Azures offentliga peering för kretsen.

    Kontrollera att du har följande information innan du forsätter.

    - Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
    - Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
    - Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
    - AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
    - En MD5-hash om du väljer att använda en. **Det här är valfritt**.
    
    Du kan köra följande cmdlet för att konfigurera Azures offentliga peering för din krets

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Du kan använda cmdleten nedan om du väljer att använda en MD5-hash

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.

### Så här visar du Azures offentliga peering-information

Du kan hämta information om konfigurationen med följande cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan uppdatera någon del av konfigurationen med följande cmdlet

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

VLAN-ID för kretsen uppdateras från 200 till 600 i ovanstående exempel.

### Så här tar du bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Microsoft-peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Microsofts peeringskonfiguration för en ExpressRoute-krets. 

### Så här skapar du Microsoft-peering

1. Importera PowerShell-modulen för ExpressRoute.
    
    Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

        Install-Module AzureRM

        Install-AzureRM

    Importera alla moduler som AzureRM.* inom det kända semantiska versionsintervallet

        Import-AzureRM

    Du kan också importera en vald modul inom det kända semantiska versionsintervallet 
        
        Import-Module AzureRM.Network 

    Logga in på ditt konto

        Login-AzureRmAccount

    Välj den prenumeration som du vill skapa ExpressRoute-kretsen för
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Skapa en ExpressRoute-krets.
    
    Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. 

    Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen.

3. Kontrollera ExpressRoute-kretsen för att säkerställa att den har etablerats.

    Du måste först kontrollera om ExpressRoute-kretsen har etablerats och aktiverats. Se exemplet nedan.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Svaret bör likna exemplet nedan:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Konfigurera Microsoft-peering för kretsen.

    Kontrollera att du har följande information innan du fortsätter.

    - Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
    - Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
    - Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
    - AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
    - Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Du kan skicka en kommaavgränsad lista om du planerar att skicka en uppsättning prefix. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
    - Kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS-nummer, kan du ange det AS-nummer som de är registrerade på. **Det här är valfritt**.
    - Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
    - En MD5-hash om du väljer att använda en. **Det här är valfritt.**
    
    Du kan köra följande cmdlet för att konfigurera Microsofts peering för din krets

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Så här hämtar du Microsofts peering-information

Du kan hämta information om konfigurationen med följande cmdlet.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt


### Så här uppdaterar du Microsofts peering-konfiguration

Du kan uppdatera någon del av konfigurationen med följande cmdlet.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### Så här tar du bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Nästa steg

Nästa steg [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

-  Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).

-  Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).

-  Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).




<!--HONumber=sep16_HO1-->


