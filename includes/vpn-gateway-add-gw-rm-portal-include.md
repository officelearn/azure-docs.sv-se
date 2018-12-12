---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111891"
---
1. Logga in på Azure portal och väljer **skapa en resurs**. Den **New** öppnas.

2. I den **sökfält marketplace**, ange *virtuell nätverksgateway*, och välj **virtuell nätverksgateway** i listan. 

3. På den **virtuell nätverksgateway** väljer **skapa** att öppna den **Skapa virtuell nätverksgateway** sidan.

   ![Fält på sidan Skapa en virtuell nätverksgateway](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Fält på sidan Skapa en virtuell nätverksgateway")

4. På den **Skapa virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway:

   - **Namn på**: Ange ett namn för gateway-objekt som du skapar. Det här namnet skiljer sig från namnet på gateway-undernätet. 

   - **Typ av gateway**: Välj **VPN** för VPN-gatewayer. 

   - **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en **routningsbaserad** VPN-typ.

   - **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.

      Välj bara **aktivera aktivt-aktivt läge** om du skapar en aktiv-aktiv gatewaykonfiguration. Annars lämnar du den inställningen omarkerad.
  
   - **Plats**: du kan behöva rulla för att se **plats**. Ange **plats** till den plats där det virtuella nätverket finns. Till exempel **västra USA**. Om du inte anger platsen till den region där ditt virtuella nätverk finns, visas den inte i den nedrullningsbara listan när du väljer ett virtuellt nätverk.

   - **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen i. Välj **virtuellt nätverk** att öppna den **Välj ett virtuellt nätverk** och välj det virtuella nätverket. Om du inte ser ditt VNet, kontrollera att den **plats** anges till den region där det virtuella nätverket finns.

   - **Adressintervall för gatewayundernät**: du kan bara se den här inställningen om du inte tidigare skapade ett gatewayundernät för det virtuella nätverket. Den här inställningen visas inte om du tidigare har skapat ett giltigt gatewayundernät.

   - **Offentliga IP-adressen**: den här inställningen anger den offentliga IP-adressobjekt som är associerat med VPN-gatewayen. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. VPN-gateway stöder för närvarande endast *dynamisk* offentliga IP-adressallokering. Men innebär dynamisk allokering inte att IP-adressen ändras när den har tilldelats till din VPN-gateway. Den enda gången de offentliga IP-adressändringarna är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.
    
      - Lämna alternativet **Skapa nytt** markerat.

      - I textrutan anger du ett namn för din offentliga IP-adress.

   - **Konfigurera BGP ASN**: lämna den här inställningen omarkerade, om inte din konfiguration kräver specifikt. Om du behöver den här inställningen kan ASN är standardvärdet *65515*, som du kan ändra.
     
5. Kontrollera inställningarna och välj **skapa** att börja skapa VPN-gatewayen. Inställningarna verifieras och du ser den **distribuera virtuell nätverksgateway** panelen på instrumentpanelen. Det kan ta upp till 45 minuter att skapa en gateway. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

6. När du har skapat gatewayen, kontrollera IP-adressen som har tilldelats till den genom att visa det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet. Du kan välja den anslutna enheten (din virtuella nätverksgateway) om du vill visa mer information.