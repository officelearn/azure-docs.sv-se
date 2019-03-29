---
title: Skapa en Azure virtuellt WAN virtuella hubben routningstabell - Azure-portalen | Microsoft Docs
description: Virtuellt WAN virtuella hubben routningstabellen för att styra trafik till en virtuell nätverksinstallation med hjälp av portalen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580590"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Skapa ett virtuellt WAN-nätverk hub-routningstabell för nva: er: Azure Portal

Den här artikeln visar hur du styra trafik från en hubb till en virtuell nätverksinstallation (NVA).

![Virtual WAN-diagram](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor:

*  Du har en virtuell nätverksinstallation (NVA). En virtuell nätverksinstallation är en programvara från tredje part som tillhandahålls vanligtvis från Azure Marketplace i ett virtuellt nätverk.

    * En privat IP-adress måste tilldelas till NVA-nätverksgränssnitt.

    * NVA är inte distribuerat i den virtuella hubben. Den måste distribueras i ett separat virtuellt nätverk.

    *  NVA-VNet kan ha en eller flera virtuella nätverk som är anslutna till den. I den här artikeln har refererar vi till NVA-VNet som en ”indirekt virtuellt ekernätverk'. Dessa virtuella nätverk kan anslutas till NVA-VNet med hjälp av VNet-peering.
*  Du har skapat 2 virtuella nätverk. De ska användas som virtuella ekernätverken.

    * I den här övningen är eker-adressutrymmen för virtuellt nätverk: VNet1: 10.0.2.0/24 och VNet2: 10.0.3.0/24. Om du behöver information om hur du skapar ett virtuellt nätverk finns i [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md).

    * Se till att det finns inga virtuella nätverksgatewayer i någon av de virtuella nätverken.
    * För den här konfigurationen kräver dessa virtuella nätverk inte ett gateway-undernät.

## <a name="signin"></a>1. Logga in

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

## <a name="vwan"></a>2. Skapa ett virtuellt WAN

Skapa ett virtuellt WAN. För den här övningen, kan du använda följande värden:

* **Namn på virtuellt WAN:** myVirtualWAN
* **Resursgrupp:** testRG
* **Plats:** Västra USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Skapa en hubb

Skapa hubben. För den här övningen, kan du använda följande värden:

* **Plats:** Västra USA
* **Namn:** westushub
* **Privat adressutrymme för Hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Skapa och tillämpa en hub routningstabell

Uppdatera hubben med en routningstabell för hubben. För den här övningen, kan du använda följande värden:

* **Indirekt eker adressutrymmen för virtuellt nätverk:** (VNet1 och VNet2) 10.0.2.0/24 och 10.0.3.0/24
* **DMZ-NVA network interface privata IP-adress:** 10.0.4.5

1. Gå till ditt virtuella WAN-nätverk.
2. Klicka på hubben som du vill skapa en routningstabell.
3. Klicka på den **...** , och klicka sedan på **redigera virtuella hubben**.
4. På den **redigera virtuella hubben** , rulla nedåt och välj kryssrutan **användningstabellen för routning**.
5. I den **om målprefix är** kolumn, Lägg till adressutrymmen. I den **skicka till nästa hopp** kolumn, Lägg till DMZ-NVA network interface privata IP-adress.
6. Klicka på **Bekräfta** att uppdatera resursen hub med inställningarna som vägen tabell.

## <a name="connections"></a>5. Skapa VNet-anslutningar

Skapa en anslutning från varje indirekt eker VNet (VNet1 och VNet2) till hubben. Skapa sedan en anslutning från det virtuella NVA-nätverket till hubben.

 För det här steget ska använda du följande värden:

| Namn på virtuellt nätverk| Anslutningsnamn|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Upprepa följande procedur för varje virtuellt nätverk som du vill ansluta till.

1. På sidan för det virtuella WAN-nätverket klickar du på **Virtuella nätverksanslutningar**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** att skapa anslutningen.

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).