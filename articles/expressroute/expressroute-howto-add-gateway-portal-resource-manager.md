---
title: 'Lägg till en gateway till ett virtuellt Azure nätverk för ExpressRoute: Portal | Microsoft Docs'
description: Den här artikeln beskriver hur du lägger till en virtuell nätverksgateway till en redan skapad Resource Manager-VNet för ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 37fe2e2adb947e2e9ddc86a34baf6994b5771be6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091211"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med Azure-portalen
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln vägleder dig igenom stegen för att lägga till en virtuell nätverksgateway för ett befintliga virtuellt nätverk. Den här artikeln vägleder dig igenom stegen för att lägga till, ändra storlek på och ta bort en gateway för virtuellt nätverk (VNet) för ett befintliga virtuellt nätverk. Stegen för den här konfigurationen är avsedda för virtuella nätverk som har skapats med hjälp av Resource Manager-distributionsmodellen som ska användas i en ExpressRoute-konfiguration. Läs mer om virtuella nätverksgatewayer och konfigurationsinställningar för gateway för ExpressRoute, [om virtuella nätverksgatewayer för ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Innan du börjar

Stegen för den här uppgiften använder ett virtuellt nätverk baserat på värdena i följande konfiguration är en lista. Vi använder den här listan i vårt exempel steg. Du kan kopiera listan om du vill använda som en referens, där du ersätter värdena med dina egna.

**Konfigurationen är en lista**

* Namn på virtuellt nätverk = ”TestVNet”
* Virtuella nätverkets adressutrymme = 192.168.0.0/16
* Undernätets namn = ”FrontEnd” 
    * Undernätsadressutrymme = ”192.168.1.0/24”
* Resursgrupp = ”TestRG”
* Plats = ”USA, östra”
* Namnet för gateway-undernätet: du måste alltid namnger gateway-undernätet ”GatewaySubnet” *GatewaySubnet*.
    * Adressutrymme för gateway-undernätet = ”192.168.200.0/26”
* Gateway-namn = ”ERGW”
* Namn för gateway-IP = ”MyERGWVIP”
* Gateway-typ = ”ExpressRoute” den här typen krävs för en ExpressRoute-konfigurationen.
* Gatewaynamn för offentlig IP-adress = ”MyERGWVIP”

Du kan visa en [Video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) av de här stegen innan du påbörjar konfigurationen.

## <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet

1. I [portalen](http://portal.azure.com) går du till det virtuella Resource Manager-nätverket för vilket du vill skapa en virtuell nätverksgateway.
2. I avsnittet **Inställningar** i VNet-bladet klickar du på **Undernät** för att expandera bladet undernät.
3. På bladet **Undernät** klickar du på **+Gateway-undernät** för att öppna bladet **Lägg till undernät**. 
   
    ![Lägg till gatewayundernätet](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Lägg till gatewayundernätet")


4. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **adressintervall** så att de motsvarar dina konfigurationskrav. Vi rekommenderar att du skapar ett gateway-undernät med en/27 eller större (/ 26, / 25 osv.). Klicka sedan på **OK** och spara värdena som du kan skapa gateway-undernätet.

    ![Lägga till undernätet](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Lägga till undernätet")

## <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

1. På vänstra sidan i portalen klickar du på **+** och skriver "Virtuell nätverksgateway" i sökningen. Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. På bladet **Virtuell nätverksgateway** klickar du på **Skapa** längst ned på bladet. Detta öppnar bladet **Skapa virtuell nätverksgateway**.
2. På bladet **Skapa virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

    ![Bladet Skapa virtuell nätverksgateway](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Bladet Skapa virtuell nätverksgateway")
3. **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
4. **Typ av gateway**: Välj **ExpressRoute**.
5. **SKU**: Välj en gateway-SKU från listrutan.
6. **Plats**: Justera fältet **Plats** så att det anger platsen där ditt virtuella nätverk befinner sig. Om platsen inte pekar på regionen där det virtuella nätverket finns visas inte det virtuella nätverket i listrutan ”Välj ett virtuellt nätverk”.
7. Välj i vilket virtuellt nätverk du vill lägga till denna gateway. Klicka på **Virtuella nätverk** för att öppna bladet **Välj ett virtuellt nätverk**. Välj VNet. Om du inte ser ditt VNet, kontrollera att fältet **plats** anger regionen där det virtuella nätverket befinner sig.
9. Välj en offentlig IP-adress. Klicka på **Offentlig IP-adress** för att öppna bladet **Välj offentlig IP-adress**. Klicka på **+Skapa ny**, för att öppna bladet **Skapa offentlig IP-adress**. Ange ett namn för din offentliga IP-adress. Det här bladet skapar ett objekt för en offentlig IP-adress som en offentlig IP-adress tilldelas till dynamiskt. Klicka på **OK** att spara dina ändringar i det här bladet.
10. **Prenumeration**: Kontrollera att rätt prenumerationen har valts.
11. **Resursgrupp**: den här inställningen avgörs av vilket virtuella nätverk du väljer.
12. Justera inte **platsen** när du har angett ovanstående inställningar.
13. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på bladet om du vill att din gateway ska visas på instrumentpanelen.
14. Klicka på **Skapa** för att börja skapa gatewayen. Inställningarna verifieras och gatewayen distribueras. Skapa virtuell nätverksgateway kan det ta upp till 45 minuter att slutföra.

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gateway, kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md).