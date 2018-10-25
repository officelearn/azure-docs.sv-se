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
ms.openlocfilehash: 977eb96f0dc7ec207c239ee8a458b47a881a1e5d
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026660"
---
1. På vänstra sidan i portalen klickar du på **+Skapa en resurs** och skriver ”Virtuell nätverksgateway” i sökningen. Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. På den **virtuell nätverksgateway** klickar du på **skapa** längst ned på sidan. Då öppnas sidan **Skapa virtuell nätverksgateway**.
2. På sidan **Skapa en virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

  ![Fält på sidan Skapa en virtuell nätverksgateway](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Fält på sidan Skapa en virtuell nätverksgateway")
3. På sidan **Skapa virtuell nätverksgateway** anger du värdena för din virtuella nätverksgateway.

   - **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
  - **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
  - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
  - **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.

    Välj bara alternativet för att **aktivera läget aktiv-aktiv** om du skapar en gatewaykonfiguration som är aktiv-aktiv. Annars lämnar du den inställningen omarkerad.
  - **Plats**: Du kan behöva rulla för att se Plats. Justera fältet **Plats** så att det pekar på platsen för det virtuella nätverket. Till exempel USA, västra. Om platsen inte pekar på regionen där det virtuella nätverket finns visas det inte när du väljer ett virtuellt nätverk i nästa steg.
  - **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i. Klicka på **Virtuellt nätverk** för att öppna sidan ”Välj ett virtuellt nätverk”. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet Plats anger regionen där det virtuella nätverket befinner sig.
  - **Adressintervall för gatewayundernät**: Den här inställningen visas endast om du inte skapade ett gatewayundernät för det virtuella nätverket tidigare. Den här inställningen visas inte om du skapade ett giltigt gatewayundernät tidigare.
  - **Offentlig IP-adress**: Den här inställningen anger det offentliga IP-adressobjektet som kopplas till VPN-gatewayen. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

    - Lämna alternativet **Skapa nytt** markerat.
    - I textrutan anger du sedan ett **namn** för den offentliga IP-adressen.

4. Lämna **Konfigurera BGP ASN** omarkerat, om inte inställningen krävs specifikt för konfigurationen. Om du behöver inställningen så är standard-ASN 65515, men detta kan ändras.
5. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på sidan om du vill att din gateway ska visas på instrumentpanelen.
6. Klicka på **Skapa** för att börja skapa VPN-gatewayen. Inställningarna verifieras och ikonen "Distribuerar virtuell nätverksgateway" visas på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

När gatewayen har skapats kan du se IP-adressen som har tilldelats till den genom att visa det virtuella nätverket på portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.