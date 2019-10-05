---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ed828a09843648b129b701c9afd22aac9e2fa9b0
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970801"
---
1. På vänstra sidan i portalen klickar du på **+Skapa en resurs** och skriver ”Virtuell nätverksgateway” i sökningen. Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. På sidan **virtuell nätverksgateway** klickar du på **skapa**. Då öppnas sidan **Skapa virtuell nätverksgateway**.

   ![Fält på sidan Skapa en virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw.png "Fält på sidan Skapa en virtuell nätverksgateway")

   ![Fält på sidan Skapa en virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw2.png "Fält på sidan Skapa en virtuell nätverksgateway")
2. På sidan **Skapa en virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

   **Projekt information**

   - **Prenumeration**: Välj den prenumeration som du vill använda i list rutan.
   - **Resursgrupp**: Den här inställningen fylls i när du väljer ditt virtuella nätverk på den här sidan.

   **Instans information**

   - **Namn på**: Namnge din gateway. Att namnge din gateway är inte samma sak som att namnge ett Gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
   - **Region**: Välj den region där du vill skapa den här resursen. Regionen för gatewayen måste vara samma som det virtuella nätverket.
   - **Gateway-typ**: Välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
   - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
   - **SKU**: Välj Gateway-SKU: n i list rutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.

      **Virtuellt nätverk**: Välj i vilket virtuellt nätverk du vill lägga till denna gateway.

      **Adress intervall för Gateway-under nätet**: Det här fältet visas bara om ditt VNet inte har ett Gateway-undernät. Om möjligt, gör du intervallet/27 eller större (/26,/25 osv.). Vi rekommenderar inte att du skapar ett intervall som är mindre än/28. Om du redan har ett Gateway-undernät kan du Visa GatewaySubnet information genom att gå till det virtuella nätverket. Visa intervallet genom att klicka på **undernät** . Om du vill ändra intervallet kan du ta bort och återskapa GatewaySubnet.

   **Offentlig IP-adress**: Den här inställningen anger det offentliga IP-adressprefixet som associeras med VPN-gatewayen. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

     - **Offentlig IP-adress**: Lämna alternativet **Skapa nytt** markerat.
     - **Namn på offentlig IP-adress**: I text rutan anger du ett namn för din offentliga IP-tjänstinstans.
     - **Tilldelning**: VPN gateway stöder bara dynamisk.

   **Aktivt-aktivt läge**: Välj bara alternativet för att **aktivera läget aktiv-aktiv** om du skapar en gatewaykonfiguration som är aktiv-aktiv. Annars lämnar du den inställningen omarkerad.

   Lämna **Konfigurera BGP ASN** omarkerat, om inte inställningen krävs specifikt för konfigurationen. Om du behöver inställningen så är standard-ASN 65515, men detta kan ändras.

3. Klicka på **Granska + skapa** för att köra verifiering. När verifieringen är klar klickar du på **skapa** för att distribuera VPN-gatewayen. Det kan ta upp till 45 minuter för en gateway att skapa och distribuera fullständigt. Du kan se distributions status på översikts sidan för din gateway.

När gatewayen är skapad, kan du se IP-adressen som har tilldelats den genom att se på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet.
