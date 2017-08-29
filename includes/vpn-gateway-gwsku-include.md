När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. Välj SKU: er som uppfyller dina krav baserat på typerna av arbetsbelastning, genomflöden, funktioner och servicenivåavtal.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>Produktion *jämfört med* Arbetsbelastningar för utvecklingstest

På grund av skillnader i serviceavtal och funktioner, rekommenderar vi följande SKU: er för produktion *jämfört med* utv-test:

| **Arbetsbelastning**                       | **SKU: er**               |
| ---                                | ---                    |
| **Produktion, kritiska arbetsbelastningar** | VpnGw1 VpnGw2, VpnGw3 |
| **Utv-test eller konceptbevis**   | Basic                  |
|                                    |                        |

Om du använder gamla SKU: er är rekommendationerna för produktion-SKU:er Standard och HighPerformance. Mer information om gamla SKU:er finns i [Gateway SKUs (legacy SKUs)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) (Gateway-SKU:er (äldre SKU:er)).

###  <a name="feature"></a>Funktionsuppsättningarna för gateway-SKU:er

Nya gatewayen SKU: er förenklar funktionsuppsättningarna via gatewayer:

| **SKU**| **Funktioner**|
| ---    | ---         |
|**Basic**   | **Ruttbaserad VPN**: 10 tunnlar med P2S<br><br>**Principbaserad VPN**: (IKEv1): 1 tunnel; ingen P2S|
| **VpnGw1, VpnGw2 och VpnGw3** | **Ruttbaserad VPN**: upp till 30 tunnlar (*), P2S, BGP, aktiv-aktiv, anpassade IPsec/IKE-principer, samtidig ExpressRoute/VPN |
|        |             |

(*) Du kan konfigurera ”PolicyBasedTrafficSelectors” för att ansluta en ruttbaserad VPN-gateway (VpnGw1 VpnGw2, VpnGw3) till flera lokala principbaserade brandväggsenheter. Referera till [Ansluta VPN-gatewayer till flera lokala principbaserad VPN-enheter med hjälp av PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) för mer information.

###  <a name="resize"></a>Ändra storlek på gateway-SKU: er

1. Du kan ändra storlek mellan VpnGw1, VpnGw2 och VpnGw3 SKU: er.
2. När du arbetar med gamla gatewayen-SKU: er, kan du ändra storlek mellan Basic, Standard och HighPerformance SKU: er.
2. Du **kan inte** ändra från HighPerformance-Basic/Standard SKU: er till nya VpnGw3-VpnGw1/VpnGw2 SKU: er. Du måste i stället [migrera](#migrate) till nya SKU: er.

###  <a name="migrate"></a>Migrera från gamla SKU: er till nya SKU: er

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
