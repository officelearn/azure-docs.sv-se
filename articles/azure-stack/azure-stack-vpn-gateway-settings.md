---
title: "VPN gateway-inställningar för Azure-stacken | Microsoft Docs"
description: "Läs mer om inställningar för VPN-gatewayer som du använder med Azure-stacken."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2018
ms.author: brenduns
ms.openlocfilehash: 1eba5df93b461eb22ab8341b4498682957c9298a
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Konfigurationsinställningar för VPN-gateway för Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik mellan det virtuella nätverket i Azure-stacken och en fjärransluten VPN-gateway. Den fjärranslutna VPN-gatewayen kan vara i Azure, en enhet i ditt datacenter eller en enhet i en annan plats.  Om det finns en nätverksanslutning mellan två slutpunkter, kan du upprätta en säker plats-till-plats (S2S) VPN-anslutning mellan två nätverk.

En VPN-gateway-anslutningen förlitar sig på konfigurationen av flera resurser som innehåller konfigurerbara inställningar. Avsnitten i den här artikeln beskriver de resurser och inställningar som är relaterade till en VPN-gateway för ett virtuellt nätverk som skapats i Resource Manager-modellen. Du kan hitta beskrivningar och Topologidiagram för varje anslutning lösning i [om VPN-Gateway för Azure-Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Inställningar för VPN-gateway

### <a name="gateway-types"></a>Gateway-typer
Varje virtuellt nätverk i Azure-stacken stöder en enda virtuell nätverksgateway, vilket måste vara av typen **Vpn**.  Det här stödet skiljer sig från Azure, vilket stöder ytterligare typer.  

När du skapar en virtuell nätverksgateway, måste du se till att gateway-typen är korrekta för din konfiguration. Kräver en VPN-gateway den `-GatewayType Vpn`.

Exempel:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Gateway-SKU:er
När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. Välj SKU: er som uppfyller dina krav baserat på typerna av arbetsbelastning, genomflöden, funktioner och servicenivåavtal.

>[!NOTE]
> Klassiska virtuella nätverk ska fortsätta att använda gamla SKU: er. Mer information om gamla gatewayen-SKU: er finns i [Arbeta med SKU: er för virtuell nätverksgateway (gamla)](/azure/vpn-gateway/vpn-gateway-about-skus-legacy).

Azure-stacken erbjuder följande VPN-gatewayen SKU: er:

|   | VPN Gateway-genomströmning |VPN-Gateway, Max. IPsec-tunnlar |
|-------|-------|-------|
|**Basic SKU**  | 100 Mbit/s  | 10    |
|**Standard-SKU**           | 100 Mbit/s  | 10    |
|**Högpresterande SKU** | 200 Mbit/s    | 30    |

### <a name="resizing-gateway-skus"></a>Ändra storlek på gateway-SKU: er
Azure-stacken stöder inte en storleksändring av SKU: er mellan stöds äldre SKU: er.

Dessutom stöder inte Azure Stack en storlek från en stöds äldre SKU: er (Basic, Standard och HighPerformance) till de nyare SKU: er som stöds av Azure (VpnGw1 VpnGw2 och VpnGw3).

### <a name="configure-the-gateway-sku"></a>Konfigurera gateway-SKU
#### <a name="azure-stack-portal"></a>Azure Stack-portalen
Om du använder Azure Stack-portal för att skapa en virtuell nätverksgateway Resource Manager kan välja du gateway SKU med hjälp av listmenyn. Alternativen visas motsvarar Gateway-typ och VPN-typ som du väljer.

#### <a name="powershell"></a>PowerShell
Följande PowerShell-exempel anger GatewaySku - som VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>Anslutningstyper
I Resource Manager-distributionsmodellen kräver varje konfiguration av en specifik virtuell gatewaytyp av nätverksanslutning. Resource Manager PowerShell tillgängliga värden för - ConnectionType är:

- IPsec

I följande PowerShell-exempel skapas en S2S-anslutning som kräver anslutningstypen IPsec.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN-typer
Du måste ange en VPN-typ när du skapar den virtuella nätverksgatewayen för en konfiguration för VPN-gateway. VPN-typ som du väljer beror på topologin anslutning som du vill skapa.  En VPN-typ kan också vara beroende av den maskinvara som du använder. S2S konfigurationer kräver en VPN-enhet. Vissa VPN-enheter stöder bara en viss typ av VPN.

> [!IMPORTANT]  
> Azure-stacken stöder endast väg-baserad VPN-typ för tillfället. Om enheten stöder endast princip-baserade VPN-anslutningar, stöds inte anslutningar till dessa enheter från Azure-stacken.

- **PolicyBased**: *(stöds av Azure, men inte av Azure-stacken)* principbaserade VPN: er krypterar och dirigerar paket via IPsec-tunnlar baserat på IPsec-principer som konfigureras med kombinationer av adressprefix mellan ditt lokala nätverk och Azure-stacken VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-enhetens konfiguration.

- **RouteBased**: RouteBased VPN: er använder ”rutter” i IP-vidarebefordrings- eller routningstabeller för att dirigera paket till sina motsvarande tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen (eller trafikväljaren) för RouteBased VPN konfigureras som alla-till-alla (eller jokertecken). Värdet för en RouteBased VPN-typ är RouteBased.

Följande PowerShell-exempel anger VpnType - som RouteBased. När du skapar en gateway måste du kontrollera att -VpnType är rätt för din konfiguration.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>Gateway-krav
I följande tabell visas kraven för VPN-gatewayer.

| |PolicyBased grundläggande VPN-Gateway | RouteBased grundläggande VPN-Gateway | RouteBased Standard VPN-Gateway | RouteBased hög prestanda VPN-Gateway|
|--|--|--|--|--|
| **Plats-till-plats-anslutning (S2S connectivity)** | Stöds inte | RouteBased VPN-konfiguration | RouteBased VPN-konfiguration | RouteBased VPN-konfiguration |
| **Autentiseringsmetod**  | Stöds inte | I förväg delad nyckel för S2S-anslutning  | I förväg delad nyckel för S2S-anslutning  | I förväg delad nyckel för S2S-anslutning  |   
| **Maximalt antal S2S-anslutningar**  | Stöds inte | 10 | 10| 30|
|**Stöd för aktiv routning (BGP)** | Stöds inte | Stöds inte | Stöds | Stöds |

### <a name="gateway-subnet"></a>Gateway-undernät 
Innan du skapar en VPN-gateway, måste du skapa en gateway-undernätet. Gateway-undernätet innehåller IP-adresser som virtuell nätverksgateway virtuella datorer och tjänster. När du skapar din virtuella nätverksgateway kan distribueras till gateway-undernätet gateway VMs och konfigurerad med de nödvändiga inställningarna för VPN-gateway. Distribuera inte något annat (till exempel ytterligare virtuella datorer) till gateway-undernät. Gateway-undernätet måste ha namnet ”GatewaySubnet' ska fungera korrekt. 'GatewaySubnet' kan namngivning av gateway-undernätet Azure stackutrymme för att identifiera undernät för att distribuera virtuell nätverksgateway virtuella datorer och tjänster.

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresser i gatewayundernätet allokeras till gateway VMs och gateway-tjänster. Vissa konfigurationer kräver fler IP-adresser än andra. Titta på anvisningarna för den konfiguration som du vill skapa och verifiera att gateway-undernätet som du vill skapa uppfyller dessa krav. Dessutom kanske du vill se till gateway-undernätet innehåller IP-adresser räcker för att anpassa framtida ytterligare konfigurationer. Du kan skapa en gateway-undernätet så liten som /29, rekommenderar vi att du skapar ett gateway-undernät för /28 eller större (/ 28 minst/27, /26 osv.). På så sätt kan om du lägger till funktioner i framtiden, du behöver inte går sönder ned din gateway och sedan ta bort och återskapa gateway-undernät så att flera IP-adresser.

I följande exempel för Resource Manager PowerShell visas ett gateway-undernät med namnet GatewaySubnet. Du kan se CIDR-notering anger en minst/27, vilket tillåter tillräckligt med IP-adresser för de flesta konfigurationer som finns för närvarande.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Om du kopplar en nätverkssäkerhetsgrupp (NSG) till det här undernätet så kan din VPN-gateway sluta fungera som förväntat. Läs mer om nätverkssäkerhetsgrupper [vad är en nätverkssäkerhetsgrupp?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Lokala nätverksgatewayer
När du skapar en konfiguration för VPN-gateway i Azure, representerar den lokala nätverksgatewayen ofta den lokala platsen. I Azure-stacken representerar alla fjärranslutna VPN-enheter som befinner sitter utanför Azure-stacken.  Detta kan vara en VPN-enhet i ditt datacenter, en fjärransluten datacenter eller en VPN-Gateway i Azure.

Du namnge den lokala nätverksgatewayen, offentliga IP-adressen för VPN-enheten och ange adressprefix som finns på den lokala platsen. Azure tittar på mål-adressprefix för nätverkstrafik, tittar du den konfiguration som du har angett för din lokala nätverksgateway och därefter dirigerar paket.

Följande PowerShell-exempel skapas en ny lokal nätverksgateway:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
Ibland måste du ändra inställningarna för lokala gateway. Till exempel när du lägger till eller ändra adressintervallet, eller om IP-adressen för VPN-enhet ändras. Se [ändra lokala gateway nätverksinställningar med hjälp av PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>IPsec/IKE-parametrar
När du konfigurerar en VPN-anslutning i Azure-stacken måste du konfigurera anslutningen i båda ändar.  Om du konfigurerar en VPN-anslutning mellan Azure-stacken och en maskinvaruenhet som en växel eller router som fungerar som en VPN-Gateway, be enheten dig för ytterligare inställningar.

Till skillnad från Azure, vilket stöder flera erbjudanden för både som initierare och en Övervakare, stöder Azure-stacken bara ett erbjudande.

###  <a name="ike-phase-1-main-mode-parameters"></a>Parametrar för IKE fas 1 (huvudläge)
| Egenskap              | Värde|
|-|-|
| IKE-version           | IKEv2 |
|Diffie-Hellman Group   | Grupp 2 (1 024 bitar) |
| Autentiseringsmetod | I förväg delad nyckel |
|Krypterings- och hash-algoritmer | AES256, SHA256 |
|SA-livstid (tid)     | 28 800 sekunder|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametrar för IKE fas 2 (snabbläge)
| Egenskap| Värde|
|-|-|
|IKE-version |IKEv2 |
|Kryptering och hash-algoritmer (kryptering)     | GCMAES256|
|Kryptering och hash-algoritmer (autentisering) | GCMAES256|
|SA-livstid (tid)  | 14 400 sekunder |
|SA-livstid (byte) | 819,200       |
|PFS (Perfect Forward Secrecy) |PFS2048 |
|Utebliven peer-identifiering | Stöds|  
