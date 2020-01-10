---
title: 'Lägg till flera VPN gateway plats-till-plats-anslutningar till ett VNet: Azure Portal'
description: Lägg till S2S-anslutningar på flera platser till en VPN-gateway som har en befintlig anslutning
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779696"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Lägga till en plats-till-plats-anslutning till ett VNet med en befintlig VPN gateway-anslutning

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
> 

Den här artikeln hjälper dig att lägga till en plats-till-plats-anslutning (S2S) till en VPN-gateway som har en befintlig anslutning med hjälp av Azure Portal. Den här typen av anslutning kallas ofta för en "konfiguration för flera platser". Du kan lägga till en S2S-anslutning till ett VNet som redan har en S2S-anslutning, punkt-till-plats-anslutning eller VNet-till-VNet-anslutning. Det finns vissa begränsningar när du lägger till anslutningar. Kontrol lera innan du börjar konfigurera i avsnittet [innan du börjar](#before) i den här artikeln. 

Den här artikeln gäller Resource Manager-virtuella nätverk som har en Routningsbaserad VPN-gateway. De här stegen gäller inte för nya ExpressRoute/plats-till-plats-sambefintliga anslutnings konfigurationer. Men om du bara lägger till en ny VPN-anslutning till en redan befintlig, kan du använda de här stegen. Se [ExpressRoute/S2S-befintliga anslutningar](../expressroute/expressroute-howto-coexist-resource-manager.md) för information om sambefintliga anslutningar.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdaterar den här tabellen som nya artiklar och ytterligare verktyg blir tillgängliga för den här konfigurationen. När en artikel är tillgänglig länkar vi direkt till den från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Innan du börjar
Verifiera följande objekt:

* Du konfigurerar inte en ny samexisterande ExpressRoute och VPN Gateway konfiguration.
* Du har ett virtuellt nätverk som har skapats med hjälp av distributions modellen Resource Manager med en befintlig anslutning.
* Den virtuella Nätverksgatewayen för ditt VNet är Routningsbaserad. Om du har en principbaserad VPN-gateway måste du ta bort den virtuella Nätverksgatewayen och skapa en ny VPN-gateway som Routningsbaserad.
* Inget av adress intervallen överlappar någon av de virtuella nätverk som det här virtuella nätverket ansluter till.
* Du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* Du har en extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.

## <a name="part1"></a>Del 1 – Konfigurera en anslutning
1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **alla resurser** och leta upp den **virtuella Nätverksgatewayen** från listan över resurser och klicka på den.
3. På sidan **virtuell nätverksgateway** klickar du på **anslutningar**.
   
    ![Sidan anslutningar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Sidan anslutningar")<br>
4. Klicka på **+ Lägg till**på sidan **anslutningar** .
   
    ![Knappen Lägg till anslutning](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Knappen Lägg till anslutning")<br>
5. På sidan **Lägg till anslutning** fyller du i följande fält:
   
   * **Namn:** Det namn som du vill ge till den plats som du skapar anslutningen till.
   * **Anslutnings typ:** Välj **plats-till-plats (IPSec)** .
     
     ![Sidan Lägg till anslutning](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Sidan Lägg till anslutning")<br>

## <a name="part2"></a>Del 2 – lägga till en lokal nätverksgateway
1. Klicka på **lokal** nätverksgateway ***Välj en lokal***nätverksgateway. Då öppnas sidan **Välj lokal nätverksgateway** .
   
    ![Välj lokal nätverksgateway](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Välj lokal nätverksgateway")<br>
2. Klicka på **Skapa ny** för att öppna sidan **skapa lokal** nätverksgateway.
   
    ![Sidan skapa lokal nätverksgateway](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Skapa lokal nätverksgateway")<br>
3. På sidan **skapa lokal** nätverksgateway fyller du i följande fält:
   
   * **Namn:** Det namn som du vill ge till resursen för den lokala Nätverksgatewayen.
   * **IP-adress:** Den offentliga IP-adressen för VPN-enheten på den plats som du vill ansluta till.
   * **Adress utrymme:** Adress utrymmet som du vill ska dirigeras till den nya lokala nätverks platsen.
4. Klicka på **OK** på sidan **skapa lokal** nätverksgateway för att spara ändringarna.

## <a name="part3"></a>Del 3 – Lägg till den delade nyckeln och skapa anslutningen
1. På sidan **Lägg till anslutning** lägger du till den delade nyckel som du vill använda för att skapa anslutningen. Du kan antingen hämta den delade nyckeln från din VPN-enhet eller göra en här och konfigurera VPN-enheten så att den använder samma delade nyckel. Det viktiga är att nycklarna är exakt desamma.
   
    ![Delad nyckel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Delad nyckel")<br>
2. Klicka på **OK** längst ned på sidan för att skapa anslutningen.

## <a name="part4"></a>Del 4 – verifiera VPN-anslutningen


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [utbildnings Sök vägen för Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/) .
