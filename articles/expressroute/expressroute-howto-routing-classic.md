---
title: 'Så här konfigurerar du routning (peering) för en ExpressRoute-krets: Azure: klassisk | Microsoft Docs'
description: Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc;ganesr
ms.openlocfilehash: 14e96a36eed99d9967ac6f8188a161c922d6f334
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343669"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Skapa och ändra peering för en ExpressRoute-krets (klassisk)
> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privat peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - offentlig peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Den här artikeln vägleder dig igenom stegen för att skapa och hantera routningskonfiguration för en ExpressRoute-krets med PowerShell och den klassiska distributionsmodellen. Stegen nedan visar också hur du kontrollerar status, uppdaterar eller tar bort och avetablerar peerings för en ExpressRoute-krets. Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. 

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en leverantör av tjänster som erbjuder hanterade Layer 3-tjänster (vanligtvis en IPVPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning åt dig.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd för att du ska kunna köra cmdletarna som beskrivs nedan.

### <a name="download-the-latest-powershell-cmdlets"></a>Ladda ned det senaste PowerShell-cmdlet

Installera de senaste versionerna av Azure Service Management (SM) PowerShell-moduler och ExpressRoute-modulen. När du använder i följande exempel, Observera att versionsnumret (i det här exemplet 5.1.1) kommer att ändras när nyare versioner av cmdletarna är tillgängliga.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Mer information finns i [komma igång med Azure PowerShell-cmdlets](/powershell/azure/overview) stegvisa instruktioner om hur du konfigurerar din dator om du vill använda Azure PowerShell-moduler.

### <a name="sign-in"></a>Logga in

Använd följande exempel för att logga in på Azure-kontot:

1. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Kontrollera prenumerationerna för kontot.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Om du har mer än en prenumeration väljer du den du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

4. Använd sedan följande cmdlet för att lägga till din Azure-prenumeration i PowerShell för den klassiska distributionsmodellen.

  ```powershell
  Add-AzureAccount
  ```

## <a name="azure-private-peering"></a>Azures privata peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures privata peeringskonfiguration för en ExpressRoute-krets. 

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. **Skapa en ExpressRoute-krets.**

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-classic.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen.
2. **Kontrollera ExpressRoute-krets för att kontrollera att den har etablerats.**
   
  Kontrollera om ExpressRoute-kretsen har etablerats och aktiverats.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Returnera:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : MyTestCircuit
  Location                         : Silicon Valley
  ServiceKey                       : *********************************
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Sku                              : Standard
  Status                           : Enabled
  ```
   
  Kontrollera att kretsen visar som etablerad och aktiverad. Om det inte fungera med din anslutningsleverantör för att hämta din krets till nödvändiga tillstånd och status.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Konfigurera Azures privata peering för kretsen.**

  Kontrollera att du har följande objekt innan du fortsätter med nästa steg:
   
  * Ett /30 undernät för den primära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
  * Ett /30 undernät för den sekundära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Kontrollera att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Kontrollera att du inte använder 65515.
  * En MD5-hash om du väljer att använda en. **Valfritt**.
     
  Du kan använda följande exempel för att konfigurera Azures privata peering för din krets:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
  ```    

  Om du vill använda en MD5-hash, Använd följande exempel för att konfigurera privat peering för din krets:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
  ```
     
  > [!IMPORTANT]
  > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
  > 

### <a name="to-view-azure-private-peering-details"></a>Så här visar du Azures privata peering-information

Du kan visa information om konfigurationen med följande cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Returnera:

```
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
```

### <a name="to-update-azure-private-peering-configuration"></a>Uppdatera konfigurationen av Azures privata peering

Du kan uppdatera någon del av konfigurationen med följande cmdlet. I exemplet nedan uppdateras VLAN-ID för kretsen från 100 till 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Så här tar du bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet. Du måste se till att alla virtuella nätverk är avlänkade från ExpressRoute-kretsen innan du kör denna cmdlet.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Azures offentliga peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures offentliga peeringskonfiguration för en ExpressRoute-krets.

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering

1. **Skapa en ExpressRoute-krets**

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-classic.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures offentliga peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen.
2. **Kontrollera ExpressRoute-krets för att kontrollera att den har etablerats**

  Du måste först kontrollera om ExpressRoute-kretsen har etablerats och aktiverats.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Returnera:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : MyTestCircuit
  Location                         : Silicon Valley
  ServiceKey                       : *********************************
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Sku                              : Standard
  Status                           : Enabled
  ```
   
  Kontrollera att kretsen visar som etablerad och aktiverad. Om det inte fungera med din anslutningsleverantör för att hämta din krets till nödvändiga tillstånd och status.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
4. **Konfigurera Azures offentliga peering för kretsen**
   
  Kontrollera att du har följande information innan du fortsätter:
   
  * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Kontrollera att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
  * En MD5-hash om du väljer att använda en. **Valfritt**.

  > [!IMPORTANT]
  > Se till att du anger AS-talet som peering-ASN, inte kund-ASN.
  >  
     
  Du kan använda följande exempel för att konfigurera Azures offentliga peering för din krets:

  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
  ```
     
  Om du vill använda en MD5-hash, Använd följande exempel för att konfigurera din krets:
     
  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
  ```
     
### <a name="to-view-azure-public-peering-details"></a>Så här visar du Azures offentliga peering-information

Om du vill visa konfigurationsinformation, använder du följande cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Returnera:

```powershell
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
```

### <a name="to-update-azure-public-peering-configuration"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan uppdatera någon del av konfigurationen med följande cmdlet. I det här exemplet uppdateras VLAN-ID för kretsen från 200 till 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Kontrollera att kretsen visar som etablerad och aktiverad. 
### <a name="to-delete-azure-public-peering"></a>Så här tar du bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft-peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Microsofts peeringskonfiguration för en ExpressRoute-krets. 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. **Skapa en ExpressRoute-krets**
  
  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-classic.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig följer du anvisningarna nedan när du har skapat kretsen.
2. **Kontrollera ExpressRoute-krets för att kontrollera att den har etablerats**

  Kontrollera att kretsen visar som etablerad och aktiverad. 
   
  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Returnera:
   
  ```powershell
  Bandwidth                        : 200
  CircuitName                      : MyTestCircuit
  Location                         : Silicon Valley
  ServiceKey                       : *********************************
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Sku                              : Standard
  Status                           : Enabled
  ```
   
  Kontrollera att kretsen visar som etablerad och aktiverad. Om det inte fungera med din anslutningsleverantör för att hämta din krets till nödvändiga tillstånd och status.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Konfigurera Microsoft-peering för kretsen**
   
    Kontrollera att du har följande information innan du fortsätter.
   
   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Kontrollera att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Du kan skicka en kommaavgränsad lista om du planerar att skicka en uppsättning prefix. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * Kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS-nummer, kan du ange det AS-nummer som de är registrerade på. **Valfritt**.
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * En MD5-hash om du väljer att använda en. **Valfritt.**
     
  Kör följande cmdlet för att konfigurera Microsoft-peering för din krets:
 
  ```powershell
  New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
  ```

### <a name="to-view-microsoft-peering-details"></a>Så här visar du Microsofts peering-information

Du kan visa information om konfigurationen med följande cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Returnera:

```powershell
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
```

### <a name="to-update-microsoft-peering-configuration"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan uppdatera någon del av konfigurationen med följande cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Så här tar du bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Nästa steg

Nästa [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md).

* Mer information om arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).