---
title: 'Azure ExpressRoute: Lägga till en gateway till en VNet:-portal'
description: I den här artikeln går du igenom att lägga till en virtuell nätverksgateway till ett redan skapat Resource Manager VNet för ExpressRoute med Hjälp av Azure-portalen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264835"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med Azure-portalen
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiskt – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

I den här artikeln får du lära dig stegen för att lägga till en virtuell nätverksgateway för ett befintligt virtuellt nätverk. I den här artikeln får du lära dig stegen för att lägga till, ändra storlek på och ta bort en virtuell nätverksgateway (VNet) för ett befintligt virtuellt nätverk. Stegen för den här konfigurationen är specifikt för virtuella nätverk som har skapats med hjälp av Resurshanterarens distributionsmodell som ska användas i en ExpressRoute-konfiguration. Mer information om virtuella nätverksgateways och gatewaykonfigurationsinställningar för ExpressRoute finns i [Om virtuella nätverksgateways för ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Innan du börjar

Stegen för den här uppgiften använder ett virtuella nätverk baserat på värdena i följande konfigurationsreferenslista. Vi använder den här listan i våra exempelsteg. Du kan kopiera listan som ska användas som referens och ersätta värdena med dina egna.

**Referenslista för konfiguration**

* Namn på virtuellt nätverk = "TestVNet"
* Virtuellt nätverksadressutrymme = 192.168.0.0/16
* Undernätsnamn = "FrontEnd" 
    * Undernätsadressutrymme = "192.168.1.0/24"
* Resursgrupp = "TestRG"
* Plats = "Östra USA"
* Gateway Undernät namn: "GatewaySubnet" Du måste alltid namnge en gateway undernät *GatewaySubnet*.
    * Undernätsutrymme för gateway = "192.168.200.0/26"
* Gateway Namn = "ERGW"
* Gateway Public IP-namn = "MyERGWVIP"
* Gateway typ = "ExpressRoute" Den här typen krävs för en ExpressRoute-konfiguration.

Du kan visa en [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) av dessa steg innan du påbörjar konfigurationen.

## <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet

1. I [portalen](https://portal.azure.com)navigerar du till det virtuella nätverk i Resource Manager som du vill skapa en virtuell nätverksgateway för.
2. I avsnittet **Inställningar** i VNet-bladet klickar du på **Undernät** för att expandera bladet undernät.
3. På bladet **Undernät** klickar du på **+Gateway-undernät** för att öppna bladet **Lägg till undernät**. 
   
    ![Lägga till gateway-undernätet](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Lägg till gateway-undernätet")


4. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **adressintervall** så att de motsvarar dina konfigurationskrav. Vi rekommenderar att du skapar ett gateway-undernät med ett /27 eller större (/26, /25, etc.). Klicka sedan på **OK** för att spara värdena och skapa gateway-undernätet.

    ![Lägga till ett undernät](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Lägga till ett undernät")

## <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

1. I portalen, till vänster, **+** klickar du på och skriver "Virtual Network Gateway" i sökningen. Hitta **Virtuell nätverksgateway** bland sökresultaten och klicka på det. På bladet **Virtuell nätverksgateway** klickar du på **Skapa** längst ned på bladet. Detta öppnar bladet **Skapa virtuell nätverksgateway**.
2. På bladet **Skapa virtuell nätverksgateway** fyller du i värdena för din virtuella nätverksgateway.

    ![Skapa bladfält för virtuell nätverksgateway](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Skapa bladfält för virtuell nätverksgateway")
3. **Namn**: namnge din gateway. Det här är inte samma sak som att namnge ett gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
4. **Gateway typ:** Välj **ExpressRoute**.
5. **SKU**: Välj en gateway-SKU från listrutan.
6. **Plats**: Justera fältet **Plats** så att det anger platsen där ditt virtuella nätverk befinner sig. Om platsen inte pekar på regionen där det virtuella nätverket finns visas inte det virtuella nätverket i listrutan ”Välj ett virtuellt nätverk”.
7. Välj i vilket virtuellt nätverk du vill lägga till denna gateway. Klicka på **Virtuella nätverk** för att öppna bladet **Välj ett virtuellt nätverk**. Välj VNet. Om du inte ser ditt virtuella nätverk kontrollerar du att fältet **Plats** pekar på den region där det virtuella nätverket finns.
9. Välj en offentlig IP-adress. Klicka på **Offentlig IP-adress** för att öppna bladet **Välj offentlig IP-adress**. Klicka på **+Skapa ny**, för att öppna bladet **Skapa offentlig IP-adress**. Ange ett namn för din offentliga IP-adress. Det här bladet skapar ett objekt för en offentlig IP-adress som en offentlig IP-adress tilldelas till dynamiskt. Klicka på **OK** att spara dina ändringar i det här bladet.
10. **Prenumeration**: Kontrollera att rätt prenumerationen har valts.
11. **Resursgrupp**: den här inställningen avgörs av vilket virtuella nätverk du väljer.
12. Justera inte **platsen** när du har angett ovanstående inställningar.
13. Verifiera inställningarna. Du kan välja **Fäst vid instrumentpanelen** längst ner på bladet om du vill att din gateway ska visas på instrumentpanelen.
14. Klicka på **Skapa** för att börja skapa gatewayen. Inställningarna verifieras och gatewayen distribueras. Det kan ta upp till 45 minuter att skapa en virtuell nätverksgateway.

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gatewayen kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [Länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md).
