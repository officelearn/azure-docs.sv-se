---
title: 'Virtuellt WAN: Skapa vägtabell för virtuella nav till NVA: Azure-portal'
description: Virtual WAN virtual hub route table för att styra trafiken till en virtuell nätverksinstallation med hjälp av portalen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402925"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Skapa en virtuell WAN-hubbvägtabell för NVA::Azure-portal

Den här artikeln visar hur du styr trafik från en filial (lokal plats) som är ansluten till virtual WAN-hubben till ett virtuellt ekrarnätverk (VNet) via en virtuell nätverksinstallation (NVA).

![Virtual WAN-diagram](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande kriterier:

*  Du har en virtuell nätverksinstallation (NVA). En virtuell nätverksinstallation är en programvara från tredje part som du väljer som vanligtvis etableras från Azure Marketplace i ett virtuellt nätverk.

    * En privat IP-adress måste tilldelas NVA-nätverksgränssnittet.

    * NVA distribueras inte i det virtuella navet. Den måste distribueras i ett separat virtuellt nätverk.

    *  Det virtuella NVA-nätverket kan ha ett eller flera virtuella nätverk anslutna till det. I den här artikeln hänvisar vi till NVA virtuella nätverk som en "indirekt talade VNet". Dessa virtuella nätverk kan anslutas till NVA VNet med hjälp av VNet-peering. VNet Peering-länkarna visas med svarta pilar i figuren ovan mellan VNet 1, VNet 2 och NVA VNet.
*  Du har skapat två virtuella nätverk. De kommer att användas som eker virtuella nätverk.

    * VNet-ekrar adressutrymmena är: VNet1: 10.0.2.0/24 och VNet2: 10.0.3.0/24. Om du behöver information om hur du skapar ett virtuellt nätverk läser du [Skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md).

    * Se till att det inte finns några virtuella nätverksgateways i något av virtuella nätverken.

    * Virtuella nätverk kräver inte ett gateway-undernät.

## <a name="1-sign-in"></a><a name="signin"></a>1. Logga in

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Skapa ett virtuellt WAN

Skapa ett virtuellt WAN. Använd följande exempelvärden:

* **Virtual WAN namn:** myVirtualWAN
* **Resursgrupp:** testRG
* **Plats:** Västra USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Skapa ett nav

Skapa navet. Använd följande exempelvärden:

* **Plats:** Västra USA
* **Namn:** westushub
* **Nav privat adressutrymme:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Skapa och tillämpa en navrutttabell

Uppdatera navet med en navvägstabell. Använd följande exempelvärden:

* **Adressutrymmen för eker:** (VNet1 och VNet2) 10.0.2.0/24 och 10.0.3.0/24
* **DMZ NVA nätverksgränssnitt privat IP-adress:** 10.0.4.5

1. Navigera till ditt virtuella WAN.
2. Klicka på den hubb som du vill skapa en flödestabell för.
3. Klicka på **...** och sedan på **Redigera virtuell hubb**.
4. På sidan **Redigera virtuella hubb** rullar du nedåt och markerar kryssrutan **Använd tabell för routning**.
5. Lägg till adressutrymmena i kolumnen **Om målprefixet är.** Lägg till den privata IP-adressen för DMZ NVA-nätverksgränssnittet i kolumnen **Skicka till nästa hopp.**
6. Klicka på **Bekräfta** om du vill uppdatera navresursen med inställningarna för vägtabellen.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Skapa VNet-anslutningar

Skapa en virtuell nätverksanslutning från varje indirekt eker-VNet (VNet1 och VNet2) till navet. Dessa virtuella nätverksanslutningar visas av de blå pilarna i figuren ovan. Skapa sedan en VNet-anslutning från NVA VNet till navet (svart pil i figuren).

 I det här steget kan du använda följande värden:

| Virtuellt nätverksnamn| Anslutningsnamn|
| --- | --- |
| VNet1 | testanslutning1 |
| VNet2 | testanslutning2 |
| NVAVNet (PÅ ANDRA) | testanslutning3 |

Upprepa följande procedur för varje virtuellt nätverk som du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **Virtuella nätverksanslutningar**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** för att skapa anslutningen.

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
