---
title: VPN gateway-inställningar för Azure Stack | Microsoft Docs
description: Läs mer om inställningar för VPN-gatewayer som du använder med Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.openlocfilehash: ab82e604eb1af3d5e64571b115dc48d213466328
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414833"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Konfigurationsinställningar för VPN-gateway för Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik mellan ditt virtuella nätverk i Azure Stack och en fjärransluten VPN-gateway. Den fjärranslutna VPN-gatewayen kan vara i Azure, en enhet i ditt datacenter eller en enhet på en annan plats. Om det finns nätverksanslutning mellan två slutpunkter kan upprätta du en säker plats-till-plats (S2S) VPN-anslutning mellan de två nätverken.

En anslutning för VPN-gateway är beroende av konfigurationen av flera resurser som innehåller konfigurerbara inställningar. Den här artikeln beskriver de resurser och inställningar som är relaterade till en VPN-gateway för ett virtuellt nätverk som du skapar i Resource Manager-distributionsmodellen. Du hittar beskrivningar och Topologidiagram för varje anslutning lösning [om VPN Gateway för Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>VPN gateway-inställningar

### <a name="gateway-types"></a>Gateway-typer

Varje virtuellt nätverk för Azure Stack har stöd för en enda virtuell nätverksgateway, vilket måste vara av typen **Vpn**.  Det här stödet skiljer sig från Azure, som har stöd för fler typer.  

När du skapar en virtuell nätverksgateway måste du se till att gateway-typen är rätt för din konfiguration. En VPN-gateway kräver den `-GatewayType Vpn`flaggan; till exempel:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Gateway-SKU:er

När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. Välj SKU: er som uppfyller dina krav baserat på typerna av arbetsbelastning, genomflöden, funktioner och servicenivåavtal.

Azure Stack har en VPN-gateway SKU: er som visas i följande tabell.

|   | VPN-gateway, genomflöde |Maximal IPsec-tunnlar för VPN-gateway |
|-------|-------|-------|
|**Basic SKU**  | 100 Mbit/s  | 20    |
|**Standard-SKU**           | 100 Mbit/s  | 20    |
|**Högpresterande SKU** | 200 Mbit/s    | 10    |

### <a name="resizing-gateway-skus"></a>Ändra storlek på gateway-SKU: er

Azure Stack stöder inte en storleksändring av SKU: er mellan stöds äldre SKU: er.

På samma sätt kan har Azure Stack inte stöd för en storleksändring från stöds äldre SKU (Basic, Standard och HighPerformance) till en nyare SKU som stöds av Azure (VpnGw1, VpnGw2 och VpnGw3.)

### <a name="configure-the-gateway-sku"></a>Konfigurera gateway-SKU

#### <a name="azure-stack-portal"></a>Azure Stack-portalen

Om du använder Azure Stack-portalen för att skapa en virtuell nätverksgateway för Resource Manager kan välja du gatewayen-SKU med hjälp av den nedrullningsbara listan. Alternativen motsvarar den typ av gateway och VPN-typ som du väljer.

#### <a name="powershell"></a>PowerShell

Följande PowerShell-exempel anger den **- GatewaySku** som `VpnGw1`:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Anslutningstyper

Varje konfiguration kräver anslutningstypen för gatewayen ett specifikt virtuellt nätverk i Resource Manager-distributionsmodellen. Tillgängliga Resource Managers PowerShell-värden för **- ConnectionType** är:

* IPsec

   I följande PowerShell-exempel skapas en S2S-anslutning som kräver IPsec-anslutningstyp:

   ```PowerShell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>VPN-typer

Du måste ange en VPN-typ när du skapar den virtuella nätverksgatewayen för en VPN-gateway-konfiguration. VPN-typ som du väljer beror på den anslutningstopologi som du vill skapa. En VPN-typ kan också vara beroende av den maskinvara som du använder. S2S-konfigurationer kräver en VPN-enhet. Vissa VPN-enheter har endast stöd för en viss typ av VPN.

> [!IMPORTANT]  
> Azure Stack stöder för närvarande endast Route-baserad VPN-typen. Om din enhet bara stöder princip-baserade VPN-anslutningar, sedan stöds anslutningar till dessa enheter från Azure Stack inte.  
>
> Dessutom stöder Azure Stack inte med principen baserat Trafikväljare för vägen baserat gatewayer just nu, eftersom anpassade IPSec/IKE-principkonfigurationer inte stöds.

* **Principbaserad**: Principbaserade VPN: er krypterar och dirigerar paket via IPsec-tunnlar baserat på IPsec-principer som konfigureras med kombinationer av adressprefix mellan ditt lokala nätverk och Azure Stack VNet. Principen eller trafikväljaren, är vanligtvis en åtkomstlista i VPN-enhetens konfiguration.

  >[!NOTE]
  >**Principbaserad** stöds i Azure, men inte i Azure Stack.

* **Routningsbaserad**: Ruttbaserad VPN använder vägar som är konfigurerade i IP-vidarebefordring eller routningstabellen för att dirigera paket till sina respektive tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen eller trafikväljaren, för **RouteBased** VPN: er konfigureras som alla-till-alla (eller använda jokertecken.) Som standard, kan inte de ändras. Värdet för en **RouteBased** VPN-typ är **RouteBased**.

Följande PowerShell-exempel anger den **- VpnType** som **RouteBased**. När du skapar en gateway, måste du kontrollera att den **- VpnType** är rätt för din konfiguration.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Gateway-krav

I följande tabell visas kraven för VPN-gatewayer.

| |Principbaserad Basic VPN Gateway | Routningsbaserad Basic VPN Gateway | Standard för RouteBased VPN-Gateway | VPN-Gateway för RouteBased hög prestanda|
|--|--|--|--|--|
| **Plats-till-plats-anslutning (S2S-anslutning)** | Stöds inte | RouteBased VPN-konfiguration | RouteBased VPN-konfiguration | RouteBased VPN-konfiguration |
| **Autentiseringsmetod**  | Stöds inte | I förväg delad nyckel för S2S-anslutning  | I förväg delad nyckel för S2S-anslutning  | I förväg delad nyckel för S2S-anslutning  |   
| **Maximalt antal S2S-anslutningar**  | Stöds inte | 20 | 20| 10|
|**Stöd för aktiv routning (BGP)** | Stöds inte | Stöds inte | Stöds | Stöds |

### <a name="gateway-subnet"></a>Gateway-undernät 

Innan du skapar en VPN-gateway, måste du skapa ett gateway-undernät. Gateway-undernätet har IP-adresser som den virtuella nätverksgatewayen virtuella datorer och tjänster. När du skapar din virtuella nätverksgateway Gatewaydatorer distribueras till gateway-undernätet och konfigurerad med inställningarna som krävs VPN-gateway. Distribuera inte något annat (till exempel ytterligare VM) till gateway-undernätet.

>[!IMPORTANT]
>Gateway-undernätet måste ha namnet **GatewaySubnet** för att fungera korrekt. Azure Stack använder det här namnet för att identifiera undernät för att distribuera virtuell nätverksgateway virtuella datorer och tjänster.

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i gatewayundernätet allokeras till virtuella Gatewaydatorer och gateway-tjänster. Vissa konfigurationer kräver fler IP-adresser än andra. Titta på anvisningarna för den konfiguration som du vill skapa och verifiera att gateway-undernätet som du vill skapa uppfyller dessa krav.

Dessutom bör du kontrollera att gateway-undernätet har tillräckligt med IP-adresser för att hantera ytterligare framtida konfigurationer. Men du kan skapa ett gateway-undernät som är så litet som/29, rekommenderar vi att du skapar ett gateway-undernät på/28 eller större (/ 28, / 27, / 26, osv.) På så sätt kan om du lägger till funktioner i framtiden kommer du inte har plocka ner din gateway och sedan ta bort och återskapa gateway-undernätet så att fler IP-adresser.

I följande Resource Managers PowerShell-exempel visar ett gateway-undernät med namnet **GatewaySubnet**. Du kan se att CIDR-noteringen anger en/27, vilket gör att tillräckligt många IP-adresser för de flesta konfigurationer som finns för närvarande.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Associera en nätverkssäkerhetsgrupp till det här undernätet kan orsaka din VPN-gateway att sluta fungera som förväntat. Mer information om nätverkssäkerhetsgrupper finns i [vad är en nätverkssäkerhetsgrupp?](../../virtual-network/virtual-networks-nsg.md).

### <a name="local-network-gateways"></a>Lokala nätverksgatewayer

När du skapar en VPN-gateway-konfiguration i Azure, representerar den lokala nätverksgatewayen ofta din lokala plats. I Azure Stack som, representerar den fjärranslutna VPN-enhet som finns utanför Azure Stack. Detta kan vara en VPN-enhet i ditt datacenter (eller ett fjärranslutet datacenter) eller en VPN-Gateway i Azure.

Du ger den lokala nätverksgatewayen ett namn, offentliga IP-adress för VPN-enhet och ange de adressprefix som finns på den lokala platsen. Azure tittar på måladressprefixen för nätverkstrafiken, konsultationer den konfiguration som du har angett för din lokala nätverksgateway och dirigerar paket på lämpligt sätt.

I nästa PowerShell-exempel skapas en ny lokal nätverksgateway:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Ibland måste du ändra inställningar för lokalt nätverk gateway; till exempel när du lägger till eller ändra adressintervallet, eller om IP-adressen för VPN-enhet ändras. Se [ändra gateway-inställningar för lokalt nätverk med hjälp av PowerShell](../../vpn-gateway/vpn-gateway-modify-local-network-gateway.md).

## <a name="ipsecike-parameters"></a>IPsec/IKE-parametrar

När du konfigurerar en VPN-anslutning i Azure Stack, måste du konfigurera anslutningen i båda ändar. Om du konfigurerar en VPN-anslutning mellan Azure Stack och en maskinvaruenhet som en växel eller router som fungerar som en VPN-gateway, kan enheten be dig för ytterligare inställningar.

Till skillnad från Azure, som har stöd för flera erbjudanden som både en initierare och en Övervakare, stöder Azure Stack bara ett erbjudande.

### <a name="ike-phase-1-main-mode-parameters"></a>Parametrar för IKE fas 1 (huvudläge)

| Egenskap               | Värde|
|-|-|
| IKE-version           | IKEv2 |
|Diffie-Hellman Group   | Grupp 2 (1 024 bitar) |
| Autentiseringsmetod | I förväg delad nyckel |
|Krypterings- och hash-algoritmer | AES256, SHA256 |
|SA-livstid (tid)     | 28 800 sekunder|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametrar för IKE fas 2 (snabbläge)

| Egenskap | Värde|
|-|-|
|IKE-version |IKEv2 |
|Kryptering och hash-algoritmer (kryptering)     | GCMAES256|
|Kryptering och hash-algoritmer (autentisering) | GCMAES256|
|SA-livstid (tid)  | 27 000 sekunder  |
|SA-livstid (kB) | 33,553,408     |
|PFS (Perfect Forward Secrecy) |Ingen<sup>se anmärkning 1</sup> |
|Utebliven peer-identifiering | Stöds|  

* *Anmärkning 1:*  Före version 1807 använder Azure Stack PFS2048 värdet för Perfect Forward Secrecy (PFS).

## <a name="next-steps"></a>Nästa steg

* [Ansluta med ExpressRoute](../azure-stack-connect-expressroute.md)
