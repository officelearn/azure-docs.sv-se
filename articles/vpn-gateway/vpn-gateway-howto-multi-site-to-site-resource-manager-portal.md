---
title: "Lägga till flera VPN-gateway för plats-till-plats-anslutningar till ett virtuellt nätverk: Azure-portalen: Resource Manager | Microsoft Docs"
description: "Lägga till flera platser S2S-anslutningar till en VPN-gateway som har en befintlig anslutning"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: cherylmc
ms.openlocfilehash: 7ec57789ee76f4ec54e4f7b68ea75c19522f3d7c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Lägga till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig anslutning för VPN-gateway

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
> 

Den här artikeln vägleder dig genom att använda Azure portal för att lägga till plats-till-plats (S2S)-anslutningar till en VPN-gateway som har en befintlig anslutning. Den här typen av anslutning kallas ofta ”flera” platskonfiguration. Du kan lägga till en S2S-anslutning till ett virtuellt nätverk som redan har en S2S-anslutning, punkt-till-plats-anslutning eller VNet-till-VNet-anslutningen. Det finns vissa begränsningar när du lägger till anslutningar. Kontrollera den [innan du börjar](#before) i den här artikeln för att verifiera innan du börjar din konfiguration. 

Den här artikeln gäller för Vnet som skapats med hjälp av Resource Manager-distributionsmodellen som har en RouteBased VPN-gateway. Dessa steg gäller inte för konfigurationer för samtidiga ExpressRoute/plats-till-plats-anslutning. Se [ExpressRoute/S2S samtidiga anslutningar](../expressroute/expressroute-howto-coexist-resource-manager.md) information om samtidiga anslutningar.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdatera den här tabellen som nya artiklar och ytterligare verktyg blir tillgängliga för den här konfigurationen. När det finns en artikel länka det till den från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Innan du börjar
Kontrollera följande:

* Du skapar inte en samtidiga ExpressRoute/S2S-anslutning.
* Du har ett virtuellt nätverk som har skapats med hjälp av Resource Manager-distributionsmodellen med en befintlig anslutning.
* Den virtuella nätverksgatewayen för din VNet är RouteBased. Om du har en PolicyBased VPN-gateway, måste du ta bort den virtuella nätverksgatewayen och skapa en ny VPN-gateway som RouteBased.
* Ingen av adressintervallen överlappa för någon av Vnet som ansluter till detta virtuella nätverk.
* Du har kompatibla VPN-enhet och någon som går att konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* Du har ett externt Internetriktade offentliga IP-adressen för VPN-enhet. Den här IP-adressen får inte finnas bakom en NAT.

## <a name="part1"></a>Del 1 – konfigurera en anslutning
1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **alla resurser** och leta upp din **virtuell nätverksgateway** från listan över resurser och klicka på den.
3. På den **virtuell nätverksgateway** bladet, klickar du på **anslutningar**.
   
    ![Bladet Anslutningar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Bladet Anslutningar")<br>
4. På den **anslutningar** bladet, klickar du på **+ Lägg till**.
   
    ![Lägg till anslutning för](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Lägg till anslutning för")<br>
5. På den **Lägg till anslutning** bladet, Fyll i följande fält:
   
   * **Namn:** namn som du vill ge till platsen du skapar en anslutning till.
   * **Anslutningstyp:** Välj **plats-till-plats (IPsec)**.
     
     ![Lägg till anslutning bladet](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Lägg till anslutning bladet")<br>

## <a name="part2"></a>Del 2 – Lägg till en lokal nätverksgateway
1. Klicka på **lokal nätverksgateway** ***Välj en lokal nätverksgateway***. Då öppnas den **Välj lokal nätverksgateway** bladet.
   
    ![Välj lokal nätverksgateway](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Välj lokal nätverksgateway")<br>
2. Klicka på **Skapa nytt** att öppna den **skapa lokal nätverksgateway** bladet.
   
    ![Skapa lokal nätverksgateway-bladet](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "skapa lokal nätverksgateway")<br>
3. På den **skapa lokal nätverksgateway** bladet, Fyll i följande fält:
   
   * **Namn:** namn som du vill ge till den lokala gateway-nätverksresursen.
   * **IP-adress:** offentliga IP-adressen för VPN-enhet på den plats som du vill ansluta till.
   * **Adressutrymmet:** adressutrymmet som du vill att dirigeras till den nya lokala nätverksplatsen.
4. Klicka på **OK** på den **skapa lokal nätverksgateway** bladet för att spara ändringarna.

## <a name="part3"></a>Del 3 – Lägg till den delade nyckeln och skapa anslutningen
1. På den **Lägg till anslutning** bladet Lägg till den delade nyckeln som du vill använda för att skapa anslutningen. Du kan hämta den delade nyckeln från din VPN-enhet eller skapa en här och sedan konfigurera din VPN-enhet om du vill använda samma delade nyckel. Viktigt är att de nycklarna är samma.
   
    ![Delad nyckel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Delad nyckel")<br>
2. Längst ned på bladet klickar du på **OK** att skapa anslutningen.

## <a name="part4"></a>Del 4 – verifiera VPN-anslutningen


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se de virtuella datorernas [utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) för mer information.
