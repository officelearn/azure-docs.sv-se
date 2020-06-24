---
title: 'Visa effektiva vägar för en virtuell hubb: Azure Virtual WAN | Microsoft Docs'
description: För att kunna vy effektiva vägar för en virtuell hubb i Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/02/2020
ms.author: cherylmc
ms.openlocfilehash: c7d436f2aecb021a7848ef0455a3f1c834cc38c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750551"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Visa effektiva vägar för en virtuell hubb

Du kan visa alla vägar för ditt virtuella WAN Hub i Azure Portal. Om du vill visa vägarna navigerar du till den virtuella hubben och väljer sedan **routning > Visa effektiva vägar**.

## <a name="understanding-routes"></a><a name="understand"></a>Förstå vägar

Följande exempel kan hjälpa dig att bättre förstå hur virtuell WAN-routning visas.

I det här exemplet har vi ett virtuellt WAN med tre nav. Den första hubben är i regionen USA, östra, andra navet finns i regionen Europa, västra och den tredje hubben är i regionen USA, västra. I ett virtuellt WAN-nätverk är alla nav sammankopplade. I det här exemplet förutsätter vi att hubbarna östra USA och Västeuropa har anslutningar från lokala grenar (ekrar) och virtuella Azure-nätverk (ekrar).

En Azure VNet ekrar (10.4.0.0/16) med en virtuell nätverks installation (10.4.0.6) är ytterligare peer-kopplad till ett VNet (10.5.0.0/16). Mer information om Hub Route-tabellen finns i [Ytterligare information](#abouthubroute) längre fram i den här artikeln.

I det här exemplet förutsätter vi också att grenen för Västeuropa 1 i Europa 1 är ansluten till hubben för östra USA, samt till hubben Västeuropa. En ExpressRoute-krets i östra USA ansluter gren 2 till hubben östra USA.

![översikt](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Visa effektiva vägar

När du väljer Visa effektiva vägar i portalen skapas de utdata som visas i [Hubbs väg tabellen](#routetable) för östra USA-hubben.

För att kunna använda det här perspektivet förutsätter den första raden att hubben för östra USA har lärt vägen för 10.20.1.0/24 (gren 1) på grund av anslutning till VPN *nästa hopp typ* ("nästa hopp VPN gateway Instance0 IP-10.1.0.6, instance1 IP 10.1.0.7). *Dirigera ursprung* pekar på resurs-ID: t. *Som sökväg* anger som sökväg för gren 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Hubb väg tabell

Använd rullnings listen längst ned i tabellen om du vill visa "AS-sökväg".

| **Protokollprefixet** |  **Nexthop-typ** | **Nästa hopp** |  **Flödes ursprung** |**SOM sökväg** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /Subscriptions/ `<sub>` /resourceGroups/- `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/Subscriptions/ `<sub>` /resourceGroups/- `<rg>` /providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-GW|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/Subscriptions/ `<sub>` /resourceGroups/- `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW|23000|
|10.4.0.0/16|Virtual Network anslutning| På länk |  |  |
|10.5.0.0/16| IP-adress| 10.4.0.6|/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|0.0.0.0/0| IP-adress| `<Azure Firewall IP>` |/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|10.22.1.0/16| Fjärrhubb|10.8.0.6, 10.8.0.7|/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/westhub_| 4848-22000 |
|10.9.0.0/16| Fjärrhubb|  På länk |/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/westhub_1| |

>[!NOTE]
> Om de östra USA-och Västeuropa-hubbarna inte kommunicerar med varandra i topologin för topologin, finns inte den väg som lästes (10.9.0.0/16). Hubbar annonserar bara nätverk som är direkt anslutna till dem.
>

## <a name="additional-information"></a><a name="additional"></a>Ytterligare information

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Om Hub Route-tabellen

Du kan skapa en virtuell nav väg och tillämpa den på väg tabellen för virtuella hubbar. Du kan använda flera vägar i routningstabellen för den virtuella hubben. På så sätt kan du ange en väg för ett virtuellt mål nätverk via en IP-adress (vanligt vis den virtuella nätverks enheten (NVA) i ett eker VNet). Mer information om NVA finns i [dirigera trafik från en virtuell hubb till en NVA](virtual-wan-route-table-portal.md). Observera att dessa vägar inte visas i tabellen över effektiva vägar. Tabellen över effektiva vägar innehåller bara prefixen för lokala och fjärranslutna hubbar plus anslutna Virtual Network adress utrymme och vägar som har lärts via BGP.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Om standard väg (0.0.0.0/0)

En virtuell hubb kan sprida en inlärd standard väg till ett virtuellt nätverk, en plats-till-plats-VPN-anslutning och en ExpressRoute-anslutning om flaggan är "aktive rad" på anslutningen. Den här flaggan visas när du redigerar en virtuell nätverks anslutning, en VPN-anslutning eller en ExpressRoute-anslutning. ' EnableInternetSecurity ' är alltid falskt som standard på hubb-VNet, ExpressRoute och VPN-anslutningar.

Standard vägen kommer inte från den virtuella WAN-hubben. Standard vägen sprids om den redan har inlärts av den virtuella WAN-hubben som ett resultat av att distribuera en brand vägg i hubben, eller om en annan ansluten plats har Tvingad tunnel trafik aktive rad.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).
