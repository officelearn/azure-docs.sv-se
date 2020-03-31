---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331300"
---
1. På [Portal-menyn i Azure](https://portal.azure.com) väljer du **Skapa en resurs**. 

   ![Skapa en resurs i Azure-portalen](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Skriv "Virtuell nätverksgateway" i fältet **Sök på Marketplace.** Leta reda på **virtuell nätverksgateway** i sökreturen och välj posten. På sidan **Virtual Network gateway** väljer du **Skapa**. Då öppnas sidan **Skapa virtuell nätverksgateway**.
3. Fyll i värdena för den virtuella nätverksgatewayen på fliken **Grunderna.**

   ![Skapa sidfält för virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Skapa sidfält för virtuell nätverksgateway")

   ![Skapa sidfält för virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Skapa sidfält för virtuell nätverksgateway")

   **Projektinformation**

   - **Prenumeration**: Välj den prenumeration som du vill använda i listrutan.
   - **Resursgrupp**: Den här inställningen fylls i automatiskt när du väljer ditt virtuella nätverk på den här sidan.

   **Information om instans**

   - **Namn**: namnge din gateway. Namnge din gateway inte samma sak som att namnge en gateway undernät. Det här är namnet på det gatewayobjekt som du skapar.
   - **Region**: Välj den region där du vill skapa den här resursen. Regionen för gatewayen måste vara samma som det virtuella nätverket.
   - **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway.
   - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
   - **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.
   - **Generering**: Information om VPN Gateway Generation finns i [Gateway SKU: er](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i listrutan.
   - **Gateway-undernätsadressintervall:** Det här fältet visas bara om ditt virtuella nätverk inte har något gateway-undernät. Om möjligt, gör intervallet /27 eller större (/26,/25 etc.). Vi rekommenderar inte att du skapar ett intervall som är mindre än /28. Om du redan har ett gateway-undernät kan du visa GatewaySubnet-information genom att navigera till det virtuella nätverket. Klicka på **Undernät** om du vill visa intervallet. Om du vill ändra intervallet kan du ta bort och återskapa GatewaySubnet.

   **Offentlig IP-adress**: Den här inställningen anger det offentliga IP-adressobjektet som kopplas till VPN-gatewayen. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

     - **Offentlig IP-adress**: Lämna **Skapa ny** markerad.
     - **Namn på offentlig IP-adress**: Skriv ett namn för din offentliga IP-adressinstans i textrutan.
     - **Tilldelning**: VPN-gateway stöder endast Dynamisk.

   **Aktivt aktivt läge:** Välj Bara **Aktivera aktivt aktivt läge** om du skapar en aktiv-aktiv gateway-konfiguration. Annars lämnar du den inställningen omarkerad.

   Lämna **Konfigurera BGP ASN** omarkerat, om inte inställningen krävs specifikt för konfigurationen. Om du behöver inställningen så är standard-ASN 65515, men detta kan ändras.
4. Välj **Granska + skapa** för att köra validering. När valideringen har gått väljer du **Skapa** för att distribuera VPN-gatewayen. Det kan ta upp till 45 minuter innan en gateway skapas och distribueras helt. Du kan se distributionsstatus på sidan Översikt för din gateway.

När gatewayen är skapad, kan du se IP-adressen som har tilldelats den genom att se på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet.
