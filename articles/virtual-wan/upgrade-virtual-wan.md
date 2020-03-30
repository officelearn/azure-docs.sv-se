---
title: Uppgradera ett virtuellt AZURE WAN från Basic till Standard – Azure-portal | Microsoft-dokument
description: Du kan uppgradera din virtuella WAN-typ för större funktionalitet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515816"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Uppgradera ett virtuellt WAN från Basic till Standard

Den här artikeln hjälper dig att uppgradera ett basic WAN till ett standard WAN. En WAN-typ "Basic" skapar alla hubbar inuti den som grundläggande SKU-hubbar. I en grundläggande hubb är du begränsad till VPN-funktioner från plats till plats. En WAN-typ "Standard" skapar alla nav inuti den som Standard SKU-hubbar. När du använder standardhubbar kan du aktivera ExpressRoute, User (Point-to-site) VPN, en fullständig mesh-hubb och VNet-till-VNet-överföring via Azure-hubbar.

I följande tabell visas de konfigurationer som är tillgängliga för varje WAN-typ:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Så här ändrar du den virtuella WAN-typen

1. På sidan för det virtuella WAN-programmet väljer du **Konfiguration** för att öppna sidan Konfiguration.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/1.png)
2. För typen Virtuellt WAN väljer du **Standard** i listrutan.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/2.png)
3. Förstå att om du uppgraderar till ett virtuellt standard WAN kan du inte återgå till ett virtuellt WAN basic. Välj **Bekräfta** om du vill uppgradera.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/4.png)
4. När ändringen har sparats ser den virtuella WAN-sidan ut ungefär som det här exemplet.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).