---
title: Anslutningen konfigurera från virtuellt nätverk för att SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Konfigurera från virtuellt nätverk för att använda SAP HANA på Azure (stora instanser)-anslutningar.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239472"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Ansluta ett virtuellt nätverk till stora HANA-instanser

När du har skapat ett Azure-nätverk, kan du ansluta nätverket till SAP HANA på Azure stora instanser. Skapa en Azure ExpressRoute-gateway i det virtuella nätverket. Den här gatewayen kan du länka virtuella nätverk till ExpressRoute-krets som ansluter till kundklient på stämpel för stora HANA-instansen.

> [!NOTE] 
> Det här steget kan ta upp till 30 minuter att slutföra. Ny gateway skapas i den avsedda Azure-prenumerationen och sedan anslutit till den angivna Azure-nätverken.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Om det finns redan en gateway, kontrollerar du om det är en ExpressRoute-gateway eller inte. Om det inte är en ExpressRoute-gateway, ta bort gatewayen och skapa den igen som en ExpressRoute-gateway. Om en ExpressRoute-gateway är redan etablerad, se följande avsnitt i den här artikeln ”länka virtuella nätverk”. 

- Använd antingen den [Azure-portalen](https://portal.azure.com/) eller PowerShell för att skapa en ExpressRoute VPN-gateway är ansluten till ditt virtuella nätverk.
  - Om du använder Azure-portalen, lägga till en ny **virtuell nätverksgateway**, och välj sedan **ExpressRoute** som gateway-typen.
  - Om du använder PowerShell först ladda ned och använda senast [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Följande kommandon för skapar en ExpressRoute-gateway. Texten som föregås av ett _$_ är användardefinierade variabler som ska uppdateras med din specifika information.

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

I det här exemplet användes HighPerformance-gateway-SKU. Alternativen är HighPerformance eller UltraPerformance som de enda gateway-SKU: er som stöds för SAP HANA på Azure (stora instanser).

> [!IMPORTANT]
> Du måste använda SKU för UltraPerformance-Gateway för HANA stora instanser av klassen typ II SKU.

## <a name="link-virtual-networks"></a>Länka virtuella nätverk

Azure-nätverket har nu en ExpressRoute-gateway. Använd auktoriseringsinformation som tillhandahålls av Microsoft för att ansluta ExpressRoute-gatewayen till SAP HANA stora instanser ExpressRoute-kretsen. Du kan ansluta med hjälp av Azure portal eller PowerShell. PowerShell-instruktioner är som följer. 

Kör följande kommandon för varje ExpressRoute-gateway med hjälp av en annan AuthGUID för varje anslutning. De två första posterna visas i följande skript kommer från information som tillhandahålls av Microsoft. Dessutom är AuthGUID specifika för varje virtuellt nätverk och dess gateway. Om du vill lägga till en annan Azure-nätverk måste du hämta en annan AuthID för din ExpressRoute-krets som ansluter stora HANA-instanser till Azure från Microsoft. 

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
> Den sista parametern i kommandot New-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass** är en ny parameter som möjliggör snabb ExpressRoute-sökväg. En funktion som minskar Nätverksfördröjningen mellan dina enheter för stora HANA-instansen och virtuella Azure-datorer. Funktionen har lagts till i maj 2019. Mer information finns i artikeln [SAP HANA (stora instanser) nätverksarkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Kontrollera att du kör den senaste versionen av PowerShell-cmdletar innan du kör kommandona.

För att ansluta gatewayen till mer än en ExpressRoute-krets som är associerad med din prenumeration, kan du behöva köra det här steget mer än en gång. Exempelvis kan kommer du förmodligen att ansluta samma vnet-gatewayen till ExpressRoute-krets som ansluter det virtuella nätverket till ditt lokala nätverk.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Tillämpa ExpressRoute snabb sökväg till befintliga HANA stora instans ExpressRoute-kretsar
Så här långt i dokumentationen beskrivs hur du ansluter en ny ExpressRoute-krets som har skapats med en distribution för stora HANA-instansen till en Azure ExpressRoute-gateway på en av dina Azure-nätverk. Men många kunder har redan sina ExpressRoute-kretsar installationsprogrammet redan och har sina virtuella nätverk är anslutna till stora HANA-instanser redan. När en ny ExpressRoute snabb sökväg minskar Nätverksfördröjningen, rekommenderar vi att du tillämpar ändringen om du vill använda den här funktionen. Kommandon för att ansluta en ny krets ExpreesRoute och ändra en befintlig ExpressRoute-krets är desamma. Därför måste du köra den här sekvensen av PowerShell-kommandon för att ändra en befintlig krets att använda 

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

Det är viktigt att du lägger till den sista parametern som visas ovan för att aktivera funktionen ExpressRoute snabb sökväg


## <a name="expressroute-global-reach"></a>ExpressRoute Global räckvidd
När du vill aktivera Global räckvidd för en eller båda av de två scenarierna:

 - HANA System Replication utan ytterligare Proxys eller brandväggar
 - Kopiera säkerhetskopior mellan enheter för stora HANA-instansen i två olika regioner ska kopieras eller systemuppdateringar

Du behöver överväga som:

- Du måste ange ett adressintervall utrymme för ett/29-adressutrymme. Att adressintervall inte kan överlappar något av de andra adressutrymmen som du använde hittills ansluta stora HANA-instanser till Azure och får inte överlappa med någon av dina IP-adressintervall du används någon annanstans i Azure eller lokalt.
- Det finns en begränsning på de ASN: er (autonoma Systemnummer) som kan användas för att annonsera din lokala vägar till stora HANA-instanser. Din lokala måste inte annonsera alla vägar med privata ASN: er i intervallet 65000 – 65020 eller 65515. 
- Scenario för att ansluta den lokala direkt åtkomst till HANA stora instanser, måste du beräkna en avgift för krets som ansluter till Azure. Priser för finns i priserna för [globala nå tillägg](https://azure.microsoft.com/pricing/details/expressroute/).

Om du vill ha en eller båda av de scenarier som tillämpas på din distribution, öppnar du ett meddelande för support med Azure enligt beskrivningen i [öppna en supportbegäran för HANA stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Data som behövs och nyckelord som du behöver använda för att Microsoft ska kunna vidarebefordra och kör på din begäran ut:

- Tjänst: Stor SAP HANA-instans
- Problemtyp: Konfiguration och installation
- Undertyp av problemet: Mitt problem visas inte ovan
- 'Ändra mitt nätverk – Lägg till Global räckvidd' för certifikatmottagare
- Information: ”Lägg till Global räckvidd till stora HANA-instansen till stora HANA-instansen klient eller” Lägg till Global räckvidd till en lokal till stora HANA-instansen klient.
- Ytterligare information för den stora HANA-instansen till stora HANA-instansen klient ärendet: Du behöver definiera den **två Azure-regioner** där det finns två klienter att ansluta **AND** du måste skicka den   **/29 IP-adressintervall**
- Ytterligare information för lokala till stora HANA-instansen klient ärendet: Du behöver definiera den **Azure-Region** där klienten stora HANA-instansen har distribuerats du vill ansluta till direkt. Dessutom måste du ange den **Auth GUID** och **Circuit Peer ID** att du fick när du har skapat din ExpressRoute-krets mellan lokala och Azure. Du måste även att namnge din **ASN**. Senaste leverans är en **/29 IP-adressintervall** för ExpressRoute Global räckvidd.

> [!NOTE]
> Om du vill ha båda fallen hanteras, måste du ange två olika/29 IP-adressintervall som inte överlappar med andra IP-adressintervall som används för hittills. 




## <a name="next-steps"></a>Nästa steg

- [Ytterligare krav för HLI](hana-additional-network-requirements.md)
