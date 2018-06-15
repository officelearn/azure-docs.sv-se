---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 655f0b625d9f1b7c7ad216e9276da62d8454380f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30929435"
---
1. På vänstra sidan i portalen klickar du på **+** och skriver "virtuell nätverksgateway" i sökningen. Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. På sidan **Virtuell nätverksgateway** klickar du på **Skapa** längst ned på sidan för att öppna sidan **Skapa en virtuell nätverksgateway**.
2. På sidan **Skapa en virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

  ![Fält på sidan Skapa en virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Fält på sidan Skapa en virtuell nätverksgateway")
3. På sidan **Skapa virtuell nätverksgateway** anger du värdena för din virtuella nätverksgateway.

  - **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
  - **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway. 
  - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
  - **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.
  - **Plats**: Du kan behöva rulla för att se Plats. Justera fältet **Plats** så att det pekar på platsen för det virtuella nätverket. Om platsen inte pekar på regionen där det virtuella nätverket finns visas det inte när du väljer ett virtuellt nätverk i nästa steg.
  - **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i. Klicka på **Virtuellt nätverk** för att öppna sidan ”Välj ett virtuellt nätverk”. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet Plats anger regionen där det virtuella nätverket befinner sig.
  - **Adressintervall för gatewayundernät**: Den här inställningen visas endast om du inte skapade ett gatewayundernät för det virtuella nätverket tidigare. Den här inställningen visas inte om du skapade ett giltigt gatewayundernät tidigare.
  - **Primär IP-konfiguration**: Sidan ”Välj offentlig IP-adress” skapar ett offentligt IP-adressobjekt som kopplas till VPN-gatewayen. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. VPN-gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

    - Klicka först på **Skapa en IP-gatewaykonfiguration** för att öppna sidan ”Välj offentlig IP-adress” och klicka sedan på **+Skapa ny** för att öppna sidan ”Skapa offentlig IP-adress”.
    - Ange sedan ett **Namn** för den offentliga IP-adressen. Låt SKU:n vara angiven som **Grundläggande** om det inte finns särskild anledning att ändra till något annat. Klicka sedan på **OK** längst ned på sidan för att spara ändringarna.

      ![Skapa offentlig IP](./media/vpn-gateway-add-gw-rm-portal-include/gwip.png "Skapa offentlig IP")

4. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på sidan om du vill att din gateway ska visas på instrumentpanelen. 
5. Klicka på **Skapa** för att börja skapa VPN-gatewayen. Inställningarna verifieras och ikonen "Distribuerar virtuell nätverksgateway" visas på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

När gatewayen har skapats kan du se IP-adressen som har tilldelats till den genom att visa det virtuella nätverket på portalen. Gatewayen visas som en ansluten enhet. Du kan klicka på den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.