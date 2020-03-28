---
title: Självstudiekurs - Skapa ExpressRoute-anslutningar med Azure Virtual WAN
description: I den här självstudien lär du dig att använda Azure Virtual WAN för att skapa ExpressRoute-anslutningar till Azure och lokala miljöer.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209434"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Självstudiekurs: Skapa en ExpressRoute-association med Azure Virtual WAN

Den här självstudien visar hur du använder Virtuellt WAN för att ansluta till dina resurser i Azure via en ExpressRoute-krets. Mer information om virtuella WAN- och virtuella WAN-resurser finns i den [virtuella WAN-översikten](virtual-wan-about.md).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt WAN
> * Skapa ett nav och en gateway
> * Ansluta ett virtuellt nätverk till en hubb
> * Ansluta en krets till en hubbgate
> * Testa anslutning
> * Ändra en gateway-storlek
> * Annonsera en standardväg

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrollera att inget av undernäten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i [Snabbstart](../virtual-network/quick-create-portal.md).

* Det virtuella nätverket har inga virtuella nätverksgateways. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gateways. Den här konfigurationen kräver att virtuella nätverk ansluts i stället till virtual WAN-hubbgatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Navet är ett virtuellt nätverk som skapas och används av Virtual WAN. Det adressintervall som du anger för navet kan inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervallen i din lokala nätverkskonfiguration, samordna med någon som kan ange dessa uppgifter åt dig.

* ExpressRoute-kretsen måste vara en Premium-krets för att kunna ansluta till navgatewayen.

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Navigera till sidan Virtuellt WAN. Klicka på **+Skapa en resurs** i portalen. Skriv **Virtuellt WAN** i sökrutan och välj Retur.
2. Välj **Virtuellt WAN** i resultatet. På sidan Virtuellt WAN klickar du på **Skapa** för att öppna sidan Skapa WAN.
3. Fyll i följande fält på fliken **Skapa** WAN på sidan **Skapa WAN:**

   ![Skapa WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resursgrupp** – Skapa ny eller använd befintlig.
   * **Plats för resursgrupp** – Välj en resursplats i listrutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** - Skriv det namn som du vill kalla ditt WAN.
   * **Typ** - Välj **Standard**. Du kan inte skapa en ExpressRoute-gateway med hjälp av basic SKU.
4. När du har fyllt i fälten väljer du **Granska +Skapa**.
5. När valideringen har gått väljer du **Skapa** för att skapa det virtuella WAN-programmet.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Skapa ett virtuellt nav och en virtuell gateway

Ett virtuellt nav är ett virtuellt nätverk som skapas och används av Virtual WAN. Den kan innehålla olika gateways, till exempel VPN och ExpressRoute. I det här avsnittet skapar du en ExpressRoute-gateway för din virtuella hubb. Du kan antingen skapa gatewayen när du [skapar en ny virtuell hubb](#newhub)eller skapa gatewayen i ett [befintligt nav](#existinghub) genom att redigera den. 

ExpressRoute-gateways etableras i enheter på 2 Gbit/s. 1 skalenhet = 2 Gbit/s med stöd upp till 10 skalenheter = 20 Gbit/s. Det tar cirka 30 minuter för en virtuell hubb och gateway att helt skapa.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Så här skapar du ett nytt virtuellt nav och en gateway

Skapa ett nytt virtuellt nav. När ett nav har skapats debiteras du för navet, även om du inte bifogar några webbplatser.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Så här skapar du en gateway i ett befintligt nav

Du kan också skapa en gateway i ett befintligt nav genom att redigera den.

1. Navigera till den virtuella hubb som du vill redigera och markera den.
2. Markera kryssrutan **Inkludera ExpressRoute-gateway**på sidan **Redigera virtuellt nav** .
3. Välj **Bekräfta** för att bekräfta dina ändringar. Det tar cirka 30 minuter för nav- och navresurserna att skapa helt.

   ![befintligt nav](./media/virtual-wan-expressroute-portal/edithub.png "redigera ett nav")

### <a name="to-view-a-gateway"></a>Så här visar du en gateway

När du har skapat en ExpressRoute-gateway kan du visa gatewayinformation. Navigera till navet, välj **ExpressRoute**och visa gatewayen.

![Visa gateway](./media/virtual-wan-expressroute-portal/viewgw.png "visa gateway")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Ansluta ditt virtuella nätverk till navet

I det här avsnittet skapar du peering-anslutningen mellan hubben och ett virtuella nätverk. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **virtuell nätverksanslutning**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket kan inte ha en redan befintlig virtuell nätverksgateway (varken VPN eller ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Anslut kretsen till navgatewayen

När gatewayen har skapats kan du ansluta en [ExpressRoute-krets](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) till den. ExpressRoute Premium-kretsar som finns på Platser som stöds av ExpressRoute Global Reach kan ansluta till en Virtuell WAN ExpressRoute-gateway.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Så här ansluter du kretsen till navgatewayen

Gå till sidan **Virtuell hubb -> -> ExpressRoute på sidan Virtual hub ->-> ExpressRoute.** Om du har åtkomst i din prenumeration till en ExpressRoute-krets ser du den krets som du vill använda i listan över kretsar. Om du inte ser några kretsar, men har försetts med en auktoriseringsnyckel och peer circuit URI, kan du lösa in och ansluta en krets. Se [Ansluta genom att lösa in en auktoriseringsnyckel](#authkey).

1. Välj kretsen.
2. Välj **Anslut kretsar.**

   ![ansluta kretsar](./media/virtual-wan-expressroute-portal/cktconnect.png "ansluta kretsar")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Så här ansluter du genom att lösa in en auktoriseringsnyckel

Använd auktoriseringsnyckeln och krets-URI som du angav för att ansluta.

1. Klicka på **+Lös in auktoriseringsnyckel** på expressroutesidan

   ![Lösa](./media/virtual-wan-expressroute-portal/redeem.png "Lösa")
2. Fyll i värdena på sidan Lös in auktorisering.

   ![lösa in nyckelvärden](./media/virtual-wan-expressroute-portal/redeemkey2.png "lösa in nyckelvärden")
3. Välj **Lägg till** om du vill lägga till nyckeln.
4. Visa kretsen. En inlöst krets visar bara namnet (utan typ, leverantör och annan information) eftersom det finns i en annan prenumeration än användarens.

## <a name="to-test-connectivity"></a>Så här testar du anslutningen

När kretsanslutningen har upprättats anger hubbanslutningsstatusen "det här navet", vilket innebär att anslutningen upprättas till hubbexpressroutegatewayen. Vänta ungefär 5 minuter innan du testar anslutningen från en klient bakom expressroutekretsen, till exempel en virtuell dator i det virtuella nätverk som du skapade tidigare.

Om du har webbplatser anslutna till en Virtuell WAN VPN-gateway i samma nav som ExpressRoute-gatewayen kan du ha dubbelriktad anslutning mellan VPN och ExpressRoute-slutpunkter. Dynamisk routning (BGP) stöds. ASN för gateways i navet är fast och kan inte redigeras just nu.

## <a name="to-change-the-size-of-a-gateway"></a>Så här ändrar du storleken på en gateway

Om du vill ändra storleken på din ExpressRoute-gateway letar du reda på ExpressRoute-gatewayen i navet och väljer skalenheterna i listrutan. Spara din växel. Det tar cirka 30 minuter att uppdatera hubbgatewayen.

![ändra gatewaystorlek](./media/virtual-wan-expressroute-portal/changescale.png "ändra gatewaystorlek")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Så här annonserar du standardväg 0.0.0.0/0 till slutpunkter

Om du vill att den virtuella Azure-hubben ska annonsera standardvägen 0.0.0.0/0 till dina ExpressRoute-slutpunkter måste du aktivera "Propagate default route".

1. Välj anslutning **för krets ->...-> Redigera**.

   ![Redigera anslutning](./media/virtual-wan-expressroute-portal/defaultroute1.png "Redigera anslutning")

2. Välj **Aktivera** om du vill sprida standardvägen.

   ![Sprida standardväg](./media/virtual-wan-expressroute-portal/defaultroute2.png "Sprida standardväg")

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
