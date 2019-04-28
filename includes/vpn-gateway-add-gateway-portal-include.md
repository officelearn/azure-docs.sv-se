---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35f987f26ce47c19e3d5eb1ca5d2bb32d0c7ad1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407802"
---
1. Till vänster på portalen klickar du på **+** och skriver ”Virtuell nätverksgateway” i sökrutan. I **Resultat** letar du upp och klickar på **Virtuell nätverksgateway**.
2. Klicka på **Skapa** längst ned på sidan ”Virtuell nätverksgateway”. Då öppnas sidan **Skapa virtuell nätverksgateway**.
3. På sidan **Skapa virtuell nätverksgateway** anger du värdena för din virtuella nätverksgateway.

    ![Fält på sidan Skapa virtuell nätverksgateway](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Ny gateway")

   - **Namn**: Namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
   - **Gateway-typ**: Välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
   - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
   - **SKU**: Välj gatewayen-SKU i listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.

     Välj bara alternativet för att **aktivera läget aktiv-aktiv** om du skapar en gatewaykonfiguration som är aktiv-aktiv. Annars lämnar du den inställningen omarkerad.
   - **Plats**: Du kan behöva rulla för att se plats. Justera fältet **Plats** så att det pekar på platsen för det virtuella nätverket. Till exempel USA, västra. Om platsen inte pekar på regionen där det virtuella nätverket finns visas det inte när du väljer ett virtuellt nätverk i nästa steg.
   - **Virtuellt nätverk**: Välj i vilket virtuellt nätverk du vill lägga till denna gateway. Klicka på **Virtuellt nätverk** för att öppna sidan ”Välj ett virtuellt nätverk”. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet Plats anger regionen där det virtuella nätverket befinner sig.
   - **Adressintervall för gatewayundernät**: Den här inställningen visas endast om du inte tidigare har skapat ett gatewayundernät för det virtuella nätverket. Den här inställningen visas inte om du skapade ett giltigt gatewayundernät tidigare.
   - **Offentlig IP-adress**: Den här inställningen anger den offentliga IP-adressobjekt som kopplas till VPN-gateway. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

     - Lämna alternativet **Skapa nytt** markerat.
     - I textrutan anger du sedan ett **namn** för den offentliga IP-adressen.

4. Lämna **Konfigurera BGP ASN** omarkerat, om inte inställningen krävs specifikt för konfigurationen. Om du behöver inställningen så är standard-ASN 65515, men detta kan ändras.
5. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på sidan om du vill att din gateway ska visas på instrumentpanelen. 
6. Klicka på **Skapa** för att börja skapa VPN-gatewayen. Inställningarna verifieras och ikonen "Distribuerar virtuell nätverksgateway" visas på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.
   gateway.

När gatewayen har skapats kan du se IP-adressen som har tilldelats till den genom att visa det virtuella nätverket på portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.