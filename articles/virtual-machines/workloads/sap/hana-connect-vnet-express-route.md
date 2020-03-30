---
title: Anslutnings konfigurerad från virtuellt nätverk till SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Anslutningsuppsättningen konfigureras från virtuellt nätverk för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617186"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Ansluta ett virtuellt nätverk till STORA HANA-instanser

När du har skapat ett virtuellt Azure-nätverk kan du ansluta nätverket till SAP HANA på Azure stora instanser. Skapa en Azure ExpressRoute-gateway i det virtuella nätverket. Med den här gatewayen kan du länka det virtuella nätverket till ExpressRoute-kretsen som ansluter till kundens klientorganisation på stämpeln HANA Large Instance.

> [!NOTE] 
> Det här steget kan ta upp till 30 minuter att slutföra. Den nya gatewayen skapas i den angivna Azure-prenumerationen och ansluts sedan till det angivna virtuella Azure-nätverket.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Om det redan finns en gateway kontrollerar du om det är en ExpressRoute-gateway eller inte. Om det inte är en ExpressRoute-gateway tar du bort gatewayen och återskapar den som en ExpressRoute-gateway. Om en ExpressRoute-gateway redan har upprättats läser du följande avsnitt i den här artikeln, "Länka virtuella nätverk". 

- Använd antingen [Azure-portalen](https://portal.azure.com/) eller PowerShell för att skapa en ExpressRoute VPN-gateway som är ansluten till ditt virtuella nätverk.
  - Om du använder Azure-portalen lägger du till en ny **virtual network gateway**och väljer sedan **ExpressRoute** som gatewaytyp.
  - Om du använder PowerShell hämtar och använder du först den senaste [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Följande kommandon skapar en ExpressRoute-gateway. Texterna som föregås av en _$_ är användardefinierade variabler som bör uppdateras med din specifika information.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

I det här exemplet användes HighPerformance gateway SKU. Dina alternativ är HighPerformance eller UltraPerformance som de enda gateway-SKU:er som stöds för SAP HANA på Azure (stora instanser).

> [!IMPORTANT]
> För stora HANA-instanser av klass SKU typ II måste du använda UltraPerformance Gateway SKU.

## <a name="link-virtual-networks"></a>Länka virtuella nätverk

Det virtuella Azure-nätverket har nu en ExpressRoute-gateway. Använd auktoriseringsinformationen från Microsoft för att ansluta ExpressRoute-gatewayen till SAP HANA Large Instances ExpressRoute-kretsen. Du kan ansluta med hjälp av Azure-portalen eller PowerShell. PowerShell-instruktionerna är följande. 

Kör följande kommandon för varje ExpressRoute-gateway med hjälp av en annan AuthGUID för varje anslutning. De två första posterna som visas i följande skript kommer från den information som tillhandahålls av Microsoft. Dessutom är AuthGUID specifik för varje virtuellt nätverk och dess gateway. Om du vill lägga till ytterligare ett virtuellt Azure-nätverk måste du skaffa ett annat AuthID för din ExpressRoute-krets som ansluter STORA HANA-instanser till Azure från Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Den sista parametern i kommandot New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** är en ny parameter som aktiverar ExpressRoute Fast Path. En funktion som minskar nätverksfördröjningen mellan dina HANA Large Instance-enheter och virtuella Azure-datorer. Funktionaliteten lades till i maj 2019. Mer information finns i artikeln [SAP HANA (Large Instances) nätverksarkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Kontrollera att du kör den senaste versionen av PowerShell-cmdlets innan du kör kommandona.

Om du vill ansluta gatewayen till mer än en ExpressRoute-krets som är associerad med din prenumeration kan du behöva köra det här steget mer än en gång. Du kommer till exempel sannolikt att ansluta samma virtuella nätverksgateway till ExpressRoute-kretsen som ansluter det virtuella nätverket till ditt lokala nätverk.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Tillämpa ExpressRoute Fast Path på befintliga HANA Large Instance ExpressRoute-kretsar
Dokumentationen hittills förklarade hur du ansluter en ny ExpressRoute-krets som har skapats med en HANA-distribution stor instans till en Azure ExpressRoute-gateway för ett av dina virtuella Azure-nätverk. Men många kunder har redan sina ExpressRoute-kretsar redan och har redan sina virtuella nätverk anslutna till HANA Large Instances. Eftersom den nya Snabbsökvägen ExpressRoute minskar nätverksfördröjningen rekommenderar vi att du använder ändringen för att använda den här funktionen. Kommandona för att ansluta en ny ExpreesRoute-krets och ändra en befintlig ExpressRoute Circuit är desamma. Därför måste du köra den här sekvensen av PowerShell-kommandon för att ändra en befintlig krets för att 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Det är viktigt att du lägger till den sista parametern som visas ovan för att aktivera ExpressRoute Fast Path-funktionen


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
När du vill aktivera Global Reach för ett eller båda av de två scenarierna:

 - HANA SystemReplikering utan ytterligare proxyservrar eller brandväggar
 - Kopiera säkerhetskopior mellan HANA-enheter för stora instanser i två olika regioner för att utföra systemkopior eller systemuppdatering

du behöver tänka på att:

- Du måste ange ett adressutrymmesintervall för ett /29-adressutrymme. Det adressintervallet kanske inte överlappar något av de andra adressutrymmesintervallen som du har använt hittills för att ansluta HANA-stora instanser till Azure och får inte överlappa med något av dina IP-adressintervall som du använde någon annanstans i Azure eller lokalt.
- Det finns en begränsning av ASN (Autonfritt systemnummer) som kan användas för att annonsera dina lokala rutter till STORA HANA-instanser. Din lokala plats får inte annonsera några rutter med privata ASN i intervallet 65000 - 65020 eller 65515. 
- För scenariot med att ansluta lokal direkt åtkomst till HANA Stora instanser måste du beräkna en avgift för kretsen som ansluter dig till Azure. För priser, kontrollera priserna för [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Om du vill hämta ett eller båda scenarier som tillämpas på distributionen öppnar du ett supportmeddelande med Azure enligt beskrivningen i [Öppna en supportbegäran för stora HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Data som behövs och nyckelord som du behöver använda för att Microsoft ska kunna dirigera och köra på din begäran, ser ut som:

- Tjänst: SAP HANA Stor instans
- Problemtyp: Konfiguration och installation
- Problemundertyp: Mitt problem visas inte ovan
- Ämne "Ändra mitt nätverk - lägg till global räckvidd"
- Information: Lägg till global räckvidd till HANA Large Instance till HANA Large Instance-klienten eller Lägg till global räckvidd i lokala till HANA Large Instance-klienten.
- Ytterligare information om ärendet för HANA Large Instance to HANA Large Instance tenant: Du måste definiera de **två Azure-regioner** där de två klienterna som ska ansluta finns **OCH** du måste skicka **IP-adressintervallet /29 IP**
- Ytterligare information för det lokala till HANA Large Instance-klientärendeäre fallet: Du måste definiera **Azure-regionen** där HANA Large Instance-klienten distribueras som du vill ansluta till direkt. Dessutom måste du ange **Auth GUID** och **Circuit Peer ID** som du fick när du etablerade din ExpressRoute-krets mellan lokala och Azure. Dessutom måste du namnge din **ASN**. Den sista slutprodukten är ett **/29 IP-adressintervall** för ExpressRoute Global Reach.

> [!NOTE]
> Om du vill att båda ärendena ska hanteras måste du ange två olika /29 IP-adressintervall som inte överlappar något annat IP-adressintervall som hittills använts. 




## <a name="next-steps"></a>Nästa steg

- [Ytterligare nätverkskrav för HLI](hana-additional-network-requirements.md)
