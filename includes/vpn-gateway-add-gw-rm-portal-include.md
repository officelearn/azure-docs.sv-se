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
ms.openlocfilehash: 12c28969ed00a1370b90e06e9d57cd666c50ea29
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523913"
---
1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **skapa en resurs**. 

   ![Skapa en resurs i Azure Portal](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Skriv "Virtual Network Gateway" i fältet **Sök på Marketplace** . Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. På sidan **virtuell nätverksgateway** klickar du på **skapa**. Då öppnas sidan **Skapa virtuell nätverksgateway**.

   ![Sid fält för att skapa virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw.png "Sid fält för att skapa virtuell nätverksgateway")

   ![Sid fält för att skapa virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw2.png "Sid fält för att skapa virtuell nätverksgateway")
3. På sidan **Skapa en virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

   **Projekt information**

   - **Prenumeration**: Välj den prenumeration som du vill använda i list rutan.
   - **Resurs grupp**: den här inställningen fylls i när du väljer ditt virtuella nätverk på den här sidan.

   **Instans information**

   - **Namn**: namnge din gateway. Att namnge din gateway är inte samma sak som att namnge ett Gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
   - **Region**: Välj den region där du vill skapa den här resursen. Regionen för gatewayen måste vara samma som det virtuella nätverket.
   - **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
   - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
   - **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.

      **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i.

      **Adress intervall för Gateway-under nätet**: det här fältet visas bara om ditt VNet inte har något Gateway-undernät. Om möjligt, gör du intervallet/27 eller större (/26,/25 osv.). Vi rekommenderar inte att du skapar ett intervall som är mindre än/28. Om du redan har ett Gateway-undernät kan du Visa GatewaySubnet information genom att gå till det virtuella nätverket. Visa intervallet genom att klicka på **undernät** . Om du vill ändra intervallet kan du ta bort och återskapa GatewaySubnet.

   **Offentlig IP-adress**: Den här inställningen anger det offentliga IP-adressobjektet som kopplas till VPN-gatewayen. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

     - **Offentlig IP-adress**: lämna **Skapa ny** är markerat.
     - **Namn på offentlig IP-adress**: Skriv ett namn för din offentliga IP-tjänstinstans i text rutan.
     - **Tilldelning**: VPN-gateway stöder endast dynamisk.

   **Aktivt-aktivt läge**: Välj **aktivera aktivt-aktivt läge** om du skapar en aktiv-aktiv gateway-konfiguration. Annars lämnar du den inställningen omarkerad.

   Lämna **Konfigurera BGP ASN** omarkerat, om inte inställningen krävs specifikt för konfigurationen. Om du behöver inställningen så är standard-ASN 65515, men detta kan ändras.

4. Klicka på **Granska + skapa** för att köra verifiering. När verifieringen är klar klickar du på **skapa** för att distribuera VPN-gatewayen. Det kan ta upp till 45 minuter för en gateway att skapa och distribuera fullständigt. Du kan se distributions status på översikts sidan för din gateway.

När gatewayen är skapad, kan du se IP-adressen som har tilldelats den genom att se på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet.
