---
title: 'Konfigurera routning (peering) för en ExpressRoute-krets: Azure: klassisk | Microsoft Docs'
description: Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 9cebb196bd91da704798fb001763a76e6d090472
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "31594145"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Skapa och ändra peering för en ExpressRoute-krets (klassisk)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privat peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - offentlig peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Den här artikeln vägleder dig genom stegen för att skapa och hantera routningskonfiguration för en ExpressRoute-krets med PowerShell och den klassiska distributionsmodellen. Stegen nedan visar också hur du kontrollerar status, uppdaterar eller tar bort och avetablerar peerings för en ExpressRoute-krets.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration
* Du behöver den senaste versionen av Azure Service Management (SM) PowerShell-cmdlets. Mer information finns i [komma igång med Azure PowerShell-cmdlets](/powershell/azure/overview).  
* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ instruktionerna för att [skapar du en ExpressRoute-krets](expressroute-howto-circuit-classic.md) och ha kretsen aktiveras med anslutningsleverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd för att du ska kunna köra cmdletarna som beskrivs nedan.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en Internet-leverantör som erbjuder hanteringstjänster till Layer 3 (vanligtvis en IPVPN, t.ex. MPLS) kommer anslutningsleverantören konfigurera och hantera routning för dig.
> 
> 

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Logga in på ditt Azure-konto och välja en prenumeration
1. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

        Connect-AzureRmAccount

2. Kontrollera prenumerationerna för kontot.

        Get-AzureRmSubscription

3. Om du har mer än en prenumeration väljer du den du vill använda.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Därefter kan du använda följande cmdlet för att lägga till din Azure-prenumeration i PowerShell för den klassiska distributionsmodellen.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Azures privata peering
Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures privata peeringskonfiguration för en ExpressRoute-krets. 

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering
1. **Importera PowerShell-modulen för ExpressRoute.**
   
    För att börja använda ExpressRoute-cmdlets måste du importera Azure och ExpressRoute-moduler i PowerShell-sessionen. Kör följande kommandon för att importera Azure och ExpressRoute-modulerna i PowerShell-sessionen. Versionen som kan variera.    
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Skapa en ExpressRoute-krets.**
   
    Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-classic.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen. 
3. **Kontrollera ExpressRoute-kretsen för att säkerställa att den har etablerats.**
   
    Du måste först kontrollera om ExpressRoute-kretsen har etablerats och aktiverats. Se exemplet nedan.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Kontrollera att kretsen är etablerad och aktiverad. Om det inte fungera med anslutningsleverantören att hämta kretsen att nödvändiga tillstånd och status.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Konfigurera privat Azure-peering för kretsen.**
   
    Kontrollera att du har följande objekt innan du fortsätter med nästa steg:
   
   * Ett /30 undernät för den primära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
   * Ett /30 undernät för den sekundära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
   * En MD5-hash om du väljer att använda en. **Det här är valfritt**.
     
    Du kan köra följande cmdlet för att konfigurera Azures privata peering för din krets.
     
        Nya AzureBGPPeering - AccessType privat - ServiceKey ”***” - PrimaryPeerSubnet ”10.0.0.0/30” - SecondaryPeerSubnet ”10.0.0.4/30” - PeerAsn 1234 - VlanId 100
     
    Du kan använda cmdleten nedan om du vill använda en MD5-hash.
     
        Nya AzureBGPPeering - AccessType privat - ServiceKey ”***” - PrimaryPeerSubnet ”10.0.0.0/30” - SecondaryPeerSubnet ”10.0.0.4/30” - PeerAsn 1234 - VlanId 100 - SharedKey ”A1B2C3D4”
     
     > [!IMPORTANT]
     > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Så här visar du Azures privata peering-information
Du kan hämta information om konfigurationen med följande cmdlet

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Uppdatera konfigurationen av Azures privata peering
Du kan uppdatera någon del av konfigurationen med följande cmdlet. I exemplet nedan uppdateras VLAN-ID för kretsen från 100 till 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Så här tar du bort Azures privata peering
Du kan ta bort peering-konfigurationen genom att köra följande cmdlet.

> [!WARNING]
> Du måste se till att alla virtuella nätverk är avlänkade från ExpressRoute-kretsen innan du kör denna cmdlet. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Azures offentliga peering
Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures offentliga peeringskonfiguration för en ExpressRoute-krets.

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering
1. **Importera PowerShell-modulen för ExpressRoute.**
   
    För att börja använda ExpressRoute-cmdlets måste du importera Azure och ExpressRoute-moduler i PowerShell-sessionen. Kör följande kommandon för att importera Azure och ExpressRoute-modulerna i PowerShell-sessionen. Versionen som kan variera.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Skapa en ExpressRoute-krets**
   
    Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-classic.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures offentliga peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen.
3. **Kontrollera ExpressRoute-krets för att säkerställa att den har etablerats**
   
    Du måste först kontrollera om ExpressRoute-kretsen har etablerats och aktiverats. Se exemplet nedan.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Kontrollera att kretsen är etablerad och aktiverad. Om det inte fungera med anslutningsleverantören att hämta kretsen att nödvändiga tillstånd och status.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Konfigurera offentlig Azure-peering för kretsen**
   
    Kontrollera att du har följande information innan du forsätter.
   
   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * En MD5-hash om du väljer att använda en. **Det här är valfritt**.
     
    Du kan köra följande cmdlet för att konfigurera Azures offentliga peering för din krets
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
     
    Du kan använda cmdleten nedan om du väljer att använda en MD5-hash
     
        Nya AzureBGPPeering - AccessType offentliga - ServiceKey ”***” - PrimaryPeerSubnet ”131.107.0.0/30” - SecondaryPeerSubnet ”131.107.0.4/30” - PeerAsn 1234 - VlanId 200 - SharedKey ”A1B2C3D4”
     
     > [!IMPORTANT]
     > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Så här visar du Azures offentliga peering-information
Du kan hämta information om konfigurationen med följande cmdlet

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering
Du kan uppdatera någon del av konfigurationen med följande cmdlet

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

VLAN-ID för kretsen uppdateras från 200 till 600 i ovanstående exempel.

### <a name="to-delete-azure-public-peering"></a>Så här tar du bort Azures offentliga peering
Du kan ta bort peering-konfigurationen genom att köra följande cmdlet

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Microsoft-peering
Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Microsofts peeringskonfiguration för en ExpressRoute-krets. 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering
1. **Importera PowerShell-modulen för ExpressRoute.**
   
    För att börja använda ExpressRoute-cmdlets måste du importera Azure och ExpressRoute-moduler i PowerShell-sessionen. Kör följande kommandon för att importera Azure och ExpressRoute-modulerna i PowerShell-sessionen. Versionen som kan variera.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Skapa en ExpressRoute-krets**
   
    Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-classic.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen.
3. **Kontrollera ExpressRoute-krets för att säkerställa att den har etablerats**
   
    Först måste du kontrollera om ExpressRoute-kretsen är etablerad och aktiverad.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Kontrollera att kretsen är etablerad och aktiverad. Om det inte fungera med anslutningsleverantören att hämta kretsen att nödvändiga tillstånd och status.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Konfigurera Microsoft-peering för kretsen**
   
    Kontrollera att du har följande information innan du fortsätter.
   
   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Du kan skicka en kommaavgränsad lista om du planerar att skicka en uppsättning prefix. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * Kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS-nummer, kan du ange det AS-nummer som de är registrerade på. **Det här är valfritt**.
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * En MD5-hash om du väljer att använda en. **Det här är valfritt.**
     
    Du kan köra följande cmdlet för att konfigurera Microsoft pering för kretsen
     
        Nya AzureBGPPeering - AccessType Microsoft - ServiceKey ”***” - PrimaryPeerSubnet ”131.107.0.0/30” - SecondaryPeerSubnet ”131.107.0.4/30” - VlanId 300 - PeerAsn 1234 - CustomerAsn 2245 - AdvertisedPublicPrefixes ”123.0.0.0/30” - RoutingRegistryName ”ARIN” - SharedKey ”A1B2C3D4”

### <a name="to-view-microsoft-peering-details"></a>Så här visar du Microsofts peering-information
Du kan hämta information om konfigurationen med följande cmdlet.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Så här uppdaterar du Microsofts peering-konfiguration
Du kan uppdatera någon del av konfigurationen med följande cmdlet.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Så här tar du bort Microsoft-peering
Du kan ta bort peering-konfigurationen genom att köra följande cmdlet.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Nästa steg
Nästa [länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md).

* Mer information om arbetsflöden finns [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).

