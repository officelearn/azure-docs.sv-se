---
title: 'Lägg till flera VPN-gateway plats-till-plats-anslutningar till ett virtuellt nätverk: Azure Portal: Resource Manager| Microsoft Docs'
description: Lägg till flera platser S2S-anslutningar till en VPN-gateway som har en befintlig anslutning
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998828"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Lägg till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig anslutning för VPN-gateway

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
> 

Den här artikeln får du lägga till anslutningar för plats-till-plats (S2S) till en VPN-gateway som har en befintlig anslutning med hjälp av Azure portal. Den här typen av anslutning kallas ofta en konfiguration för flera platser ””. Du kan lägga till en S2S-anslutning till ett virtuellt nätverk som redan har en S2S-anslutning, punkt-till-plats-anslutning eller VNet-till-VNet-anslutning. Det finns vissa begränsningar när du lägger till anslutningar. Kontrollera den [innan du börjar](#before) i den här artikeln för att kontrollera innan du börjar din konfiguration. 

Den här artikeln gäller för Resource Manager-VNets som har en RouteBased VPN-gateway. De här stegen gäller inte för ExpressRoute/plats-till-plats kan samexistera anslutningskonfigurationer. Se [ExpressRoute/S2S-anslutningar för samexistens mellan](../expressroute/expressroute-howto-coexist-resource-manager.md) information om samtidiga anslutningar.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdaterar tabellen som nya artiklar och verktyg blir tillgängliga för den här konfigurationen. Om det finns en artikel länkar vi till den direkt från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Innan du börjar
Kontrollera följande:

* Du skapar inte en samexisterande ExpressRoute/S2S-anslutning.
* Du har ett virtuellt nätverk som har skapats med hjälp av Resource Manager-distributionsmodellen med en befintlig anslutning.
* Den virtuella nätverksgatewayen för ditt virtuella nätverk är RouteBased. Om du har en PolicyBased VPN-gateway, måste du ta bort den virtuella nätverksgatewayen och skapa en ny VPN-gateway som RouteBased.
* Ingen av adressintervallen överlappa för någon av de virtuella nätverken som ansluter till det här virtuella nätverket.
* Du har kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* Du har en extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.

## <a name="part1"></a>Del 1 – konfigurera en anslutning
1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **alla resurser** och leta upp din **virtuell nätverksgateway** från listan över resurser och klicka på den.
3. På den **virtuell nätverksgateway** klickar du på **anslutningar**.
   
    ![Sidan Anslutningar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Sidan Anslutningar")<br>
4. På den **anslutningar** klickar du på **+ Lägg till**.
   
    ![Lägg till anslutningsknappen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Lägg till knappen för anslutning")<br>
5. På den **Lägg till anslutning** fyller du i följande fält:
   
   * **Namn:** Det namn som du vill ge till den plats som du skapar anslutningen till.
   * **Anslutningstyp:** Välj **plats-till-plats (IPsec)**.
     
     ![Lägg till anslutningssidan](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Lägg till sida")<br>

## <a name="part2"></a>Del 2 – lägga till en lokal nätverksgateway
1. Klicka på **lokal nätverksgateway** ***Välj en lokal nätverksgateway***. Då öppnas det **Välj lokal nätverksgateway** sidan.
   
    ![Välj en lokal nätverksgateway](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Välj lokal nätverksgateway")<br>
2. Klicka på **Skapa nytt** att öppna den **skapa lokal nätverksgateway** sidan.
   
    ![Skapa lokala gateway-sidan](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "skapa lokal nätverksgateway")<br>
3. På den **skapa lokal nätverksgateway** fyller du i följande fält:
   
   * **Namn:** Namnet som du vill ge till den lokala gateway-resursen.
   * **IP-adress:** Offentliga IP-adress för VPN-enhet på den plats som du vill ansluta till.
   * **Adressutrymme:** Adressutrymmet som du vill att de dirigeras till den lokala nätverksplatsen.
4. Klicka på **OK** på den **skapa lokal nätverksgateway** sidan för att spara ändringarna.

## <a name="part3"></a>Del 3 – lägga till den delade nyckeln och skapa anslutningen
1. På den **Lägg till anslutning** lägger du till den delade nyckeln som du vill använda för att skapa anslutningen. Du kan hämta den delade nyckeln från din VPN-enhet eller skapa en här och sedan konfigurera VPN-enheten för att använda samma delade nyckel. Viktigt är att de nycklarna är desamma.
   
    ![Delad nyckel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Delad nyckel")<br>
2. Längst ned på sidan klickar du på **OK** att skapa anslutningen.

## <a name="part4"></a>Del 4 – verifiera VPN-anslutningen


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se den [virtuella datorer Utbildningsväg](/learn/paths/deploy-a-website-with-azure-virtual-machines/) för mer information.
