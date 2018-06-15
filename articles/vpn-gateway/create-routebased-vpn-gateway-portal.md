---
title: 'Skapa en ruttbaserad VPN-gateway: Azure-portalen | Microsoft Docs'
description: Snabbt skapa en ruttbaserad VPN-Gateway med Azure-portalen
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 550f655f6eac5a114636978255578eb3753e0d4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918140"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Skapa en ruttbaserad VPN-gateway med Azure-portalen

Den här artikeln hjälper dig att snabbt skapa en ruttbaserad Azure VPN-gateway med Azure-portalen.  En VPN-gateway för att skapa en VPN-anslutning till lokalt nätverk. Du kan också använda en VPN-gateway för att ansluta Vnet. 

Stegen i den här artikeln skapar ett VNet, ett undernät, ett gateway-undernät och en ruttbaserad VPN-gateway (virtuella nätverksgateway). Du kan sedan skapa anslutningar när skapandet en gateway är klar. Dessa åtgärder kräver en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="vnet"></a>Skapa ett virtuellt nätverk

1. Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **Skapa en resurs**. Skriv ”virtuellt nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna sidan **Virtuellt nätverk**.
3. Nästan längst ned på sidan virtuella nätverk från den **Välj en distributionsmodell** listan, kontrollerar du att **Resource Manager** väljs i listrutan och klicka sedan på **skapa**. Då öppnas den **skapa virtuellt nätverk** sidan.
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga. Ange följande värden:

  - **Namn på**: TestVNet1
  - **Adressutrymmet**: 10.1.0.0/16
  - **Prenumerationen**: Kontrollera att prenumerationen som visas är den som du vill använda. Du kan ändra prenumerationer i listrutan.
  - **Resursgruppen**: TestRG1
  - **Plats**: östra USA
  - **Undernät**: klientdel
  - **Adressintervall**: 10.1.0.0/24

  ![Sidan Skapa virtuellt nätverk](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Sidan Skapa virtuellt nätverk")
5. När du har angett värden, Välj **fäst på instrumentpanelen** att göra det lättare att hitta ditt VNet på instrumentpanelen och klicka sedan på **skapa**. När du klickar på **skapa**, visas en panel på instrumentpanelen som visar förloppet för ditt VNet. Panelen ändras när VNet skapas.

## <a name="gwsubnet"></a>Lägg till en gatewayundernät

Gateway-undernätet innehåller reserverade IP-adresser med gateway-tjänster för virtuella nätverk. Skapa en gateway-undernätet.

1. På portalen går du till det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway för.
2. På din virtuella nätverk klickar du på **undernät** att expandera **VNet1 - undernät** sidan.
3. Klicka på **+ gatewayundernät** längst upp för att öppna den **Lägg till undernät** sidan.

  ![Lägg till gatewayundernätet](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Lägg till gatewayundernätet")
4. Den **namn** för ditt undernät fylls i automatiskt med värdet 'GatewaySubnet'. Justera det automatiskt ifylld **adressintervall** värdena för att matcha följande värden:

  **Adressintervall (CIDR-block)**: 10.1.255.0/27

  ![Lägg till gatewayundernät](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Lägg till gatewayundernät")
5. Klicka för att skapa gateway-undernätet **OK** längst ned på sidan.

## <a name="gwvalues"></a>Konfigurera inställningar för gateway

1. Klicka på vänster sida av portalsidan **+ skapa en resurs** och typ 'Virtuella nätverkets Gateway ”i sökrutan, sedan trycka på **RETUR**. I **Resultat** letar du upp och klickar på **Virtuell nätverksgateway**.
2. Klicka på längst ned på sidan virtuell nätverksgateway **skapa** att öppna den **Skapa virtuell nätverksgateway** sidan.
3. På sidan **Skapa virtuell nätverksgateway** anger du värdena för din virtuella nätverksgateway.

  - **Namn på**: Vnet1GW
  - **Gateway-typ**: VPN 
  - **VPN-typ**: ruttbaserad
  - **SKU**: VpnGw1
  - **Plats**: östra USA
  - **Virtuellt nätverk**: Klicka på **virtuella nätverk/Välj ett virtuellt nätverk** att öppna den **Välj ett virtuellt nätverk** sidan. Välj **VNet1**.

  ![Konfigurera inställningar för gateway](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "konfigurera inställningar för gateway")

## <a name="pip"></a>Skapa en offentlig IP-adress

En VPN-gateway måste ha en dynamiskt tilldelad offentliga IP-adress. När du skapar en anslutning till en VPN-gateway, är detta IP-adressen som den lokala enheten ansluter till.

1. Välj **första IP-Adressen skapa gateway IP-konfiguration** att begära en offentlig IP-adress.

  ![Första IP-konfiguration](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "första IP-konfiguration")
2. På den **Välj offentlig IP-sidan**, klickar du på **+ Skapa nytt** att öppna den **skapa offentlig IP-adress** sidan.
3. Konfigurera inställningar med följande värden:

  - **Namnet**: **VNet1GWIP**
  - **SKU**: **grundläggande**

  ![Skapa offentlig IP](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "Skapa offentlig IP")
4. Klicka på **OK** längst ned på den här sidan om du vill spara ändringarna.

## <a name="creategw"></a>Skapa en VPN-gateway

1. Kontrollera inställningarna på den **Skapa virtuell nätverksgateway** sidan. Justera värden om det behövs.

  ![Skapa VPN-gateway](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "skapa VPN-gateway")
2. Klicka på **skapa** längst ned på sidan.

När du klickar på **skapa**, inställningarna verifieras och **distribuerar virtuell nätverksgateway** panelen visas på instrumentpanelen. En VPN-gateway kan ta upp till 45 minuter. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

## <a name="viewgw"></a>Visa VPN-gateway

1. När gatewayen har skapats kan du gå till VNet1 i portalen. VPN-gatewayen visas på översiktssidan som en ansluten enhet.

  ![Anslutna enheter](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "anslutna enheter")

2. I listan över enheter klickar du på **VNet1GW** vill visa mer information.

  ![Visa VPN-gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "visa VPN-gateway")

## <a name="next-steps"></a>Nästa steg

När gatewayen har skapat, kan du skapa en anslutning mellan det virtuella nätverket och ett annat VNet. Eller skapa en anslutning mellan det virtuella nätverket och en lokal plats.

> [!div class="nextstepaction"]
> [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Skapa en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Skapa en anslutning till ett annat virtuellt nätverk](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)