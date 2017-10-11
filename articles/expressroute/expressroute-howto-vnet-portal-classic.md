---
title: "Konfigurera ett virtuellt nätverk och Gateway för ExpressRoute i den klassiska portalen | Microsoft Docs"
description: "Den här artikeln vägleder dig genom att konfigurera ett virtuellt nätverk för ExpressRoute med hjälp av den klassiska distributionsmodellen och den klassiska portalen."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 688817d9-51c8-4372-9af8-33fa098c7c5a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2016
ms.author: cherylmc
ms.openlocfilehash: f62254b2a7df50aa55a2a49009702848a9aecebd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Skapa ett virtuellt nätverk för ExpressRoute i den klassiska portalen
Stegen i den här artikeln beskriver hur du konfigurerar ett virtuellt nätverk och en virtuell nätverksgateway för användning med ExpressRoute med hjälp av den klassiska distributionsmodellen och den klassiska portalen.

Om du behöver anvisningar för Resource Manager-distributionsmodellen kan du använda följande artiklar: [skapa ett virtuellt nätverk med hjälp av PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) och [lägga till en VPN-Gateway i en Resource Manager-VNet för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="create-a-classic-vnet-and-gateway"></a>Skapa en klassiska virtuella nätverk och gateway
Följande steg skapar du ett klassiskt virtuellt nätverk och en virtuell nätverksgateway. Om du redan har ett klassiskt virtuellt nätverk finns i [konfigurerar ett existerande klassiska VNet](#config) i den här artikeln.

1. Logga in på den [klassiska Azure-portalen](http://manage.windowsazure.com).
2. I det nedre vänstra hörnet på skärmen klickar du på **ny**. I navigeringsfönstret klickar du på **Nätverkstjänster** och sedan på **Virtuellt nätverk**. Klicka på **Skapa anpassade** för att starta konfigurationsguiden.
3. På den **virtuella nätverksinformation** anger du följande:
   
   * **Namnet** – Namnge ditt virtuella nätverk. När du distribuerar dina VM: ar och PaaS-instanser, så att du inte vill göra namnet för komplicerade ska du använda den här virtuella nätverksnamnet.
   * **Plats** – platsen är direkt relaterad till den fysiska platsen (region) där du vill att dina resurser (VM) finns. Om du vill att de virtuella datorerna som du distribuerar till det här virtuella nätverket ska placeras fysiskt i östra USA väljer du den platsen. Du kan inte ändra den region som är associerade med det virtuella nätverket när du har skapat.
4. På sidan **DNS-servrar och VPN-anslutning** anger du följande information och klickar sedan på nästa-pilen längst ned till höger. 
   
   * **DNS-servrar** – ange DNS-servernamn och IP-adress eller välj en tidigare registrerad DNS-server på snabbmenyn. Du skapar inte en DNS-server med den här inställningen. Den låter dig ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket.
   * **Plats-till-plats-anslutning** -markerar kryssrutan för **konfigurera en plats-till-plats-VPN**.
   * **ExpressRoute** – Markera kryssrutan **Använd ExpressRoute**. Det här alternativet visas bara om du har valt **konfigurera en plats-till-plats-VPN**.
   * **Lokala nätverket** -du måste ha en lokal nätverksplats för ExpressRoute. Men om en ExpressRoute-anslutning kommer adressprefix som angetts för den lokala nätverksplatsen att ignoreras. Adressprefix annonseras till Microsoft via ExpressRoute-kretsen används i stället för routning.<BR>Om du redan har ett lokalt nätverk som skapats för ExpressRoute-anslutningen kan välja du den i listrutan. Om inte, Välj **ange ett nytt lokalt nätverk**.
5. Den **plats-till-plats-anslutning** visas om du har valt för att ange ett nytt lokalt nätverk i föregående steg. Ange följande information för att konfigurera nätverket, och klicka sedan på pilen Nästa. 
   
   * **Namnet** -namnet som du vill ringa din lokala (lokalt) nätverksplats.
   * **Adressutrymmet** – inklusive första IP- och CIDR (antal adresser). Du kan ange eventuella adressintervallet så länge det inte överlappar adressintervallet för det virtuella nätverket. Vanligtvis detta anger adressintervall för ditt lokala nätverk, men när det gäller ExpressRoute, de här inställningarna används inte. Den här inställningen krävs dock för att skapa det lokala nätverket när du använder den klassiska portalen.
   * **Lägg till adressutrymme** -den här inställningen är inte relevant för ExpressRoute.
6. På den **virtuella nätverk adressutrymmen** , anger du följande information och klickar sedan på bockmarkeringen längst ned till höger att konfigurera nätverket. 
   
   * **Adressutrymmet** – inklusive startar IP och count-adress. Kontrollera att adressutrymmena som du anger inte överlappa adressutrymmen som du har på det lokala nätverket.
   * **Lägg till undernät** – inklusive första IP- och antal adresser. Ytterligare undernät krävs inte.
   * **Lägg till gateway-undernätet** -Klicka om du vill lägga till gateway-undernätet. Gateway-undernätet används bara för den virtuella nätverksgatewayen och krävs för den här konfigurationen.<BR>Gateway-undernätet CIDR (antal adresser) för ExpressRoute måste vara /28 eller större (/ 27/26 osv.). Detta ger tillräckligt med IP-adresser i undernätet så att konfigurationen ska fungera. Om du har markerat kryssrutan för att använda ExpressRoute, i den klassiska portalen anger ett gateway-undernät med /28 portalen.  Du kan justera antalet CIDR-adress i den klassiska portalen. Gateway-undernätet visas som **Gateway** i den klassiska portalen, även om det verkliga namnet på gateway-undernätet som skapas är faktiskt **GatewaySubnet**. Du kan visa det här namnet med hjälp av PowerShell eller i Azure-portalen.
7. Klicka på bockmarkeringen längst ned på sidan. Det virtuella nätverket börjar att skapas. När den har slutförts visas **Skapad** visas under **Status** på den **nätverk** sida i den klassiska portalen.

## <a name="gw"></a>Skapa gatewayen
1. På den **nätverk** sidan, klickar du på det virtuella nätverket som du precis skapade och klicka på **instrumentpanelen** överst på sidan.
2. Längst ned på den **instrumentpanelen** klickar du på **skapa Gateway** och välj **dynamisk routning**. Klicka på **Ja** att bekräfta att du vill skapa en gateway.
3. När gatewayen börjar skapa, visas ett meddelande låta som du vet att gatewayen har startats. Det kan ta upp till 45 minuter att skapa gatewayen.
4. Länka ditt nätverk till en krets. Följ anvisningarna i artikeln [hur du länkar Vnet till ExpressRoute-kretsar](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Konfigurera en befintlig klassiska virtuella nätverk för ExpressRoute
Om du redan har ett klassiskt virtuellt nätverk kan konfigurera du den att ansluta till ExpressRoute i den klassiska portalen. Inställningarna är samma som ovan, så att läsa igenom dessa avsnitt för att bekanta dig med inställningarna som krävs. Om du vill skapa en samtidiga ExpressRoute/plats-till-plats-anslutning, se [i den här artikeln](expressroute-howto-coexist-classic.md) anvisningar. De skiljer sig från stegen i den här artikeln.

1. Du måste skapa det lokala nätverket innan du uppdaterar resten av inställningarna för virtuella nätverk. Om du vill skapa en ny lokala nätverk, vilket krävs när du konfigurerar ExpressRoute via den klassiska portalen klickar du på **ny**  **>**  **nätverkstjänster**  **>**  **virtuellt nätverk**  **>**  **Lägg till lokala nätverket**. Följ stegen i guiden för att skapa det lokala nätverket.
2. Använd **konfigurera** att uppdatera resten av inställningarna för ditt virtuella nätverk och för att koppla det virtuella nätverket till det lokala nätverket.
3. När du har konfigurerat inställningarna går du till den [skapa gatewayen](#gw) i den här artikeln för att skapa gatewayen.

## <a name="next-steps"></a>Nästa steg
* Om du vill lägga till virtuella datorer i ditt virtuella nätverk finns [virtuella datorer utbildningsvägar](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
* Om du vill veta mer om ExpressRoute finns i [ExpressRoute översikt](expressroute-introduction.md).

