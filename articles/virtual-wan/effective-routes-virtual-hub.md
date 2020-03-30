---
title: 'Visa effektiva vägar för ett virtuellt nav: Azure Virtual WAN | Microsoft-dokument'
description: Vie-effektiva vägar för ett virtuellt nav i Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515855"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Visa effektiva vägar för ett virtuellt nav

Du kan visa alla vägar för din virtuella WAN-hubb i Azure-portalen. Om du vill visa rutterna navigerar du till det virtuella navet och väljer sedan **Routning -> Visa effektiva vägar**.

## <a name="understanding-routes"></a><a name="understand"></a>Förstå rutter

Följande exempel kan hjälpa dig att bättre förstå hur virtual WAN-routning visas.

I det här exemplet har vi ett virtuellt WAN med tre hubbar. Det första navet ligger i regionen östra USA, det andra navet ligger i regionen Västeuropa och det tredje navet ligger i regionen västra USA. I ett virtuellt WAN är alla hubbar sammankopplade. I det här exemplet antar vi att knutpunkterna i östra USA och Västeuropa har anslutningar från lokala filialer (ekrar) och virtuella Azure-nätverk (ekrar).

En Azure VNet-eker (10.4.0.0/16) med en virtuell nätverksinstallation (10.4.0.6) peeras ytterligare till ett virtuellt nätverk (10.5.0.0/16). Mer [information](#abouthubroute) senare i den här artikeln finns mer information om navvägstabellen.

I det här exemplet antar vi också att Västeuropagren 1 är ansluten till navet i östra USA, liksom till navet i Västeuropa. En ExpressRoute-krets i östra USA ansluter branch 2 till navet i östra USA.

![översikt](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Visa effektiva rutter

När du väljer Visa effektiva vägar i portalen skapas utdata som visas i [hubbvägtabellen](#routetable) för hubb-hubben.

För att sätta detta i perspektiv innebär den första raden att hubben östra USA har lärt sig vägen 10.20.1.0/24 (Branch 1) på grund av ANSLUTNINGEN FÖR VPN *Nästa hopptyp* ("Nästa hopp" VPN Gateway Instance0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Flöde Origin* pekar på resurs-ID. *AS-sökvägen* anger AS-sökvägen för gren 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Navrutttabell

Använd rullningslisten längst ned i tabellen för att visa "AS Path".

| **Prefix** |  **Nexthop-typ** | **Nästa hopp** |  **Rutt ursprung** |**AS-sökväg** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Anslutning till virtuellt nätverk| På länk |  |  |
|10.5.0.0/16| IP-adress| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| IP-adress| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Fjärrnav|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Fjärrnav|  På länk |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Om knutpunkterna i östra USA och Västeuropa inte kommunicerade med varandra i exempeltopologin, skulle den lärde vägen (10.9.0.0/16) inte existera. Hubbar annonserar bara nätverk som är direkt anslutna till dem.
>

## <a name="additional-information"></a><a name="additional"></a>Ytterligare information

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Om navvägstabellen

Du kan skapa en virtuell hubbväg och använda vägen till den virtuella hubbvägstabellen. Du kan använda flera vägar i routningstabellen för den virtuella hubben. På så sätt kan du ange en väg för mål-VNet via en IP-adress (vanligtvis nva (Network Virtual Appliance) i ett eker-virtuellt nätverk). Mer information om NVA:er finns i [Dirigera trafik från ett virtuellt nav till en NVA](virtual-wan-route-table-portal.md).

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Om standardväg (0.0.0.0/0)

En virtuell hubb har möjlighet att sprida en inlärd standardväg till ett virtuellt nätverk, en VPN från plats till plats och en ExpressRoute-anslutning om flaggan är aktiverad på anslutningen. Den här flaggan visas när du redigerar en virtuell nätverksanslutning, en VPN-anslutning eller en ExpressRoute-anslutning. EnableInternetSecurity är alltid falskt som standard på Hub VNet-, ExpressRoute- och VPN-anslutningar.

Standardvägen kommer inte från den virtuella WAN-hubben. Standardvägen sprids om den redan har lärt sig av den virtuella WAN-hubben som ett resultat av att en brandvägg distribueras i navet eller om en annan ansluten plats har aktiverat tunnel.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).