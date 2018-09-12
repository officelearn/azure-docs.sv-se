---
title: 'Skapa en ruttbaserad VPN-gateway: Azure-portalen | Microsoft Docs'
description: Skapa snabbt en ruttbaserad VPN-Gateway med Azure portal
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
ms.openlocfilehash: fe05ab36f971105cf72342b8df5e2a82de7fc2b8
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391801"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Skapa en ruttbaserad VPN-gateway med Azure portal

Den här artikeln hjälper dig att snabbt skapa en ruttbaserad Azure VPN gateway med Azure portal.  En VPN-gateway för att skapa en VPN-anslutning till ditt lokala nätverk. Du kan också använda en VPN-gateway för att ansluta virtuella nätverk. 

Stegen i den här artikeln skapar ett virtuellt nätverk, ett undernät, ett gateway-undernät och en routningsbaserad VPN-gateway (virtuella nätverksgateway). Du kan sedan skapa anslutningar när skapa gatewayen är klar. Dessa steg kräver en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="vnet"></a>Skapa ett virtuellt nätverk

1. Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **Skapa en resurs**. Skriv ”virtuellt nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna sidan **Virtuellt nätverk**.
3. Nästan längst ned på sidan virtuellt nätverk från den **Välj en distributionsmodell** Kontrollera som **Resource Manager** väljs i listrutan och klicka sedan på **skapa**. Då öppnas det **skapa virtuellt nätverk** sidan.
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga. Ange följande värden:

  - **Namn på**: TestVNet1
  - **Adressutrymme**: 10.1.0.0/16
  - **Prenumeration**: Verifiera att prenumerationen som visas är den som du vill använda. Du kan ändra prenumerationer i listrutan.
  - **Resursgrupp**: TestRG1
  - **Plats**: USA, östra
  - **Undernät**: Frontend
  - **Adressintervall**: 10.1.0.0/24

  ![Sidan Skapa virtuellt nätverk](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Sidan Skapa virtuellt nätverk")
5. När du har angett värdena, Välj **fäst på instrumentpanelen** att göra det enkelt att hitta ditt VNet på instrumentpanelen och klicka sedan på **skapa**. När du klickar på **skapa**, visas en panel på instrumentpanelen som visar framstegen för ditt VNet. Panelen ändras när VNet skapas.

## <a name="gwsubnet"></a>Lägg till ett gateway-undernät

Gateway-undernätet innehåller reserverade IP-adresser som gatewaytjänsterna för virtuella nätverk använder. Skapa ett gateway-undernät.

1. På portalen går du till det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway för.
2. På sidan för virtuella nätverk klickar du på **undernät** att expandera **VNet1 - undernät** sidan.
3. Klicka på **+ Gateway-undernät** överst för att öppna den **Lägg till undernät** sidan.

  ![Lägg till gatewayundernätet](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Lägg till gatewayundernätet")
4. Den **namn** för undernätet fylls automatiskt i med det obligatoriska värdet ”GatewaySubnet”. Justera de automatiskt ifyllda **adressintervall** värdena för att matcha följande värden:

  **Adressintervall (CIDR-block)**: 10.1.255.0/27

  ![Lägg till gatewayundernät](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Lägg till gatewayundernät")
5. Om du vill skapa gateway-undernätet, klickar du på **OK** längst ned på sidan.

## <a name="gwvalues"></a>Konfigurera inställningar för gateway

1. Till vänster på portalen klickar du på **+ skapa en resurs** och skriver ”virtuell nätverksgateway” som i sökrutan, sedan trycka på **RETUR**. I **Resultat** letar du upp och klickar på **Virtuell nätverksgateway**.
2. Längst ned på sidan ”virtuell nätverksgateway” klickar du på **skapa** att öppna den **Skapa virtuell nätverksgateway** sidan.
3. På sidan **Skapa virtuell nätverksgateway** anger du värdena för din virtuella nätverksgateway.

  - **Namn på**: Vnet1GW
  - **Typ av gateway**: VPN 
  - **VPN-typ**: routningsbaserad
  - **SKU**: VpnGw1
  - **Plats**: USA, östra
  - **Virtuellt nätverk**: Klicka på **virtuella nätverk/Välj ett virtuellt nätverk** att öppna den **Välj ett virtuellt nätverk** sidan. Välj **VNet1**.

  ![Konfigurera inställningar för gateway](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "konfigurera gateway-inställningar")

## <a name="pip"></a>Skapa en offentlig IP-adress

En VPN-gateway måste ha en dynamiskt tilldelad offentlig IP-adress. När du skapar en anslutning till en VPN-gateway, är detta IP-adressen som den lokala enheten ansluts till.

1. Välj **första IP-konfiguration skapa IP-gatewaykonfiguration** att begära en offentlig IP-adress.

  ![Första IP-konfiguration](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "första IP-konfiguration")
2. På den **Välj offentlig IP-sidan**, klickar du på **+ Skapa nytt** att öppna den **skapa offentlig IP-adress** sidan.
3. Konfigurera inställningar med följande värden:

  - **Namnet**: **VNet1GWIP**
  - **SKU**: **grundläggande**

  ![Skapa offentlig IP](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "Skapa offentlig IP")
4. Klicka på **OK** längst ned på sidan för att spara dina ändringar.

## <a name="creategw"></a>Skapa VPN-gateway

1. Granska inställningarna på den **Skapa virtuell nätverksgateway** sidan. Justera värden om det behövs.

  ![Skapa VPN-gateway](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "skapa VPN-gateway")
2. Klicka på **skapa** längst ned på sidan.

När du klickar på **skapa**, inställningarna verifieras och **distribuera virtuell nätverksgateway** ikon på instrumentpanelen. En VPN-gateway kan ta upp till 45 minuter. Det är möjligt att du behöver uppdatera din portalsida för att se statusen som slutförd.

## <a name="viewgw"></a>Visa VPN-gateway

1. När gatewayen har skapats kan du gå till VNet1 i portalen. VPN-gateway visas på sidan Översikt som en ansluten enhet.

  ![Anslutna enheter](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "anslutna enheter")

2. I listan över enheter klickar du på **VNet1GW** att visa mer information.

  ![Visa VPN-gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "visa VPN-gateway")

## <a name="next-steps"></a>Nästa steg

När gatewayen har skapats, kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat virtuellt nätverk. Eller skapa en anslutning mellan ditt virtuella nätverk och en lokal plats.

> [!div class="nextstepaction"]
> [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Skapa en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Skapa en anslutning till ett annat virtuellt nätverk](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
