---
title: 'Lägga till flera VPN-gateway-anslutningar från plats till plats till ett virtuella nätverk: Azure Portal'
description: Lägga till S2S-anslutningar med flera webbplatser i en VPN-gateway som har en befintlig anslutning
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779696"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Lägga till en plats-till-plats-anslutning till ett virtuella nätverk med en befintlig VPN-gatewayanslutning

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
> 

Den här artikeln hjälper dig att lägga till S2S-anslutningar (Site-to-Site) till en VPN-gateway som har en befintlig anslutning med hjälp av Azure-portalen. Den här typen av anslutning kallas ofta för en konfiguration med flera webbplatser. Du kan lägga till en S2S-anslutning till ett virtuella nätverk som redan har en S2S-anslutning, Point-to-Site-anslutning eller VNet-till-VNet-anslutning. Det finns vissa begränsningar när du lägger till anslutningar. Kontrollera [innan du börjar](#before) i den här artikeln innan du startar konfigurationen. 

Den här artikeln gäller virtuella nätverk för Resurshanteraren som har en RouteBased VPN-gateway. De här stegen gäller inte för nya expressroute-/plats-till-plats-anslutningskonfigurationer för samtidiga anslutningar. Men om du bara lägger till en ny VPN-anslutning till en redan befintlig samexistera konfiguration kan du använda dessa steg. Se [ExpressRoute/S2S samexistera anslutningar](../expressroute/expressroute-howto-coexist-resource-manager.md) för information om samtidiga anslutningar.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdaterar den här tabellen när nya artiklar och ytterligare verktyg blir tillgängliga för den här konfigurationen. När en artikel är tillgänglig länkar vi direkt till den från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar
Verifiera följande objekt:

* Du konfigurerar inte en ny samtidig konfiguration av ExpressRoute och VPN Gateway.
* Du har ett virtuellt nätverk som har skapats med hjälp av Resurshanterarens distributionsmodell med en befintlig anslutning.
* Den virtuella nätverksgatewayen för ditt virtuella nätverk är RouteBased. Om du har en PolicyBased VPN-gateway måste du ta bort den virtuella nätverksgatewayen och skapa en ny VPN-gateway som RouteBased.
* Inget av adressintervallen överlappar något av de virtuella nätverk som det här virtuella nätverket ansluter till.
* Du har kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* Du har en externt riktad offentlig IP-adress för din VPN-enhet. Den här IP-adressen får inte finnas bakom en NAT.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Del 1 - Konfigurera en anslutning
1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **Alla resurser** och leta reda på din **virtuella nätverksgateway** från listan över resurser och klicka på den.
3. Klicka på **Anslutningar**på sidan **Virtual Network gateway** .
   
    ![Sidan Anslutningar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Sidan Anslutningar")<br>
4. Klicka på **+Lägg till**på sidan **Anslutningar** .
   
    ![Knappen Lägg till anslutning](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Knappen Lägg till anslutning")<br>
5. Fyll i följande fält på sidan **Lägg till anslutning:**
   
   * **Namn:** Namnet som du vill ge till den webbplats som du skapar anslutningen till.
   * **Anslutningstyp:** Välj **Plats till plats (IPsec)**.
     
     ![Lägg till anslutningssida](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Lägg till anslutningssida")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Del 2 - Lägga till en lokal nätverksgateway
1. Klicka på **Lokal nätverksgateway** ***Välj en lokal nätverksgateway***. Då öppnas sidan **Välj gateway för lokalt nätverk.**
   
    ![Välj lokal nätverksgateway](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Välj lokal nätverksgateway")<br>
2. Klicka på **Skapa ny** om du vill öppna sidan Skapa gateway för **lokalt nätverk.**
   
    ![Skapa sidan för lokal nätverksgateway](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Skapa lokal nätverksgateway")<br>
3. Fyll i följande fält på sidan Skapa gateway för **lokalt nätverk:**
   
   * **Namn:** Det namn som du vill ge till den lokala nätverksgatewayresursen.
   * **IP-adress:** Den offentliga IP-adressen för VPN-enheten på den webbplats som du vill ansluta till.
   * **Adressutrymme:** Det adressutrymme som du vill ska dirigeras till den nya lokala nätverksplatsen.
4. Klicka på **OK** på sidan Skapa gateway för **lokalt nätverk** för att spara ändringarna.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Del 3 - Lägg till den delade nyckeln och skapa anslutningen
1. Lägg till den delade nyckel som du vill använda för att skapa anslutningen på sidan **Lägg** till anslutning. Du kan antingen hämta den delade nyckeln från din VPN-enhet, eller göra en här uppe och sedan konfigurera din VPN-enhet för att använda samma delade nyckel. Det viktiga är att nycklarna är exakt samma.
   
    ![Delad nyckel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Delad nyckel")<br>
2. Klicka på **OK** längst ned på sidan för att skapa anslutningen.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Del 4 - Verifiera VPN-anslutningen


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i utbildningsväg för [virtuella datorer.](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
