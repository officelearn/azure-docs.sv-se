---
title: 'Länka ett virtuellt nätverk till en krets - ExpressRoute: Azure-portalen | Microsoft Docs'
description: Ansluta ett virtuellt nätverk till en Azure ExpressRoute-krets. Stegvisa instruktioner.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f2b22eb779a8ebe1d1fd804443663abc9a4245ec
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141653"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med hjälp av portalen
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

Den här artikeln hjälper dig att skapa en anslutning för att länka ett virtuellt nätverk till en Azure ExpressRoute-krets med Azure-portalen. De virtuella nätverk som du ansluter till din Azure ExpressRoute-krets kan antingen vara i samma prenumeration eller de kan vara en del av en annan prenumeration.

## <a name="before-you-begin"></a>Innan du börjar

* Granska den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets.
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern.
  * Kontrollera att du har Azure privat peering har konfigurerats för din krets. Se den [konfigurera routning](expressroute-howto-routing-portal-resource-manager.md) artikeln routning anvisningar.
  * Se till att Azures privata peering har konfigurerats och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera anslutning för slutpunkt till slutpunkt.
  * Kontrollera att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ anvisningarna för att [skapar en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType ExpressRoute, inte VPN.

* Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets som standard. Alla virtuella nätverken måste finnas i samma geopolitiska region när du använder en standard ExpressRoute-krets.

* Ett enskilt virtuellt nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd de här processerna för att skapa ett nytt anslutningsobjekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsar kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Du kan länka ett virtuellt nätverk utanför den geopolitiska regionen för ExpressRoute-kretsen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-krets om du har aktiverat ExpressRoute premium-tillägget. Kontrollera den [vanliga frågor och svar](expressroute-faqs.md) för mer information om premium-tillägget.

* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) innan du börjar att bättre förstå den.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Ansluta ett virtuellt nätverk till en krets – samma prenumeration

> [!NOTE]
> BGP-konfigurationsinformation visas inte om layer 3-providern konfigurerat din peerings. Om din krets är i ett etablerat tillstånd, bör du kunna skapa anslutningar.
>

### <a name="to-create-a-connection"></a>Skapa en anslutning

1. Se till att dina ExpressRoute-krets och Azures privata peering har konfigurerats. Följ instruktionerna i [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och [konfigurera routning](expressroute-howto-routing-arm.md). ExpressRoute-kretsen bör se ut som på följande bild:

  ![Skärmbild för ExpressRoute-krets](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Du kan nu börja etablera en anslutning för att länka din virtuella nätverksgateway för ExpressRoute-kretsen. Klicka på **anslutning** > **Lägg till** att öppna den **Lägg till anslutning** sidan och sedan konfigurera värden.

  ![Lägg till anslutning skärmbild](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
3. När anslutningen har konfigurerats, kommer din anslutningsobjektet visas information om anslutningen.

  ![Skärmbild av anslutningen objekt](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Ansluta ett virtuellt nätverk till en krets - annan prenumeration

Du kan dela en ExpressRoute-krets över flera prenumerationer. Bilden nedan visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

![Anslutningen mellan prenumerationer](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Var och en av de mindre moln inom det stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation.
- Var och en av avdelningarna i organisationen kan använda sin egen prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk.
- En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer i organisationen kan använda ExpressRoute-krets och auktoriseringar som är kopplad till kretsen, inklusive prenumerationer som är länkade till andra Azure Active Directory-klienter och Enterprise Agreement-registreringar.

  > [!NOTE]
  > Avgifter för anslutning och bandbredd för dedikerade kretsen tillämpas till ExpressRoute-krets ägare. Alla virtuella nätverk delar samma bandbredden.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administration – om krets ägare och kretsanvändare

Kretsen ägare är en behörig Power-användare i ExpressRoute-krets resursen. Kretsägaren kan skapa auktoriseringar som kan lösas genom att ”kretsanvändare”. Kretsanvändare är ägare till virtuella nätverksgatewayer som inte är inom samma prenumeration som ExpressRoute-kretsen. Kretsanvändare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Kretsägaren har rätt att ändra och återkalla auktoriseringar när som helst. Återkallar ett auktorisering resulterar i alla anslutningar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Du skapar en anslutning auktoriseringsregler**

Kretsägaren skapar en auktorisering. Detta resulterar i att skapa en auktoriseringsnyckeln som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgatewayer för ExpressRoute-kretsen. En auktorisering är giltig för endast en anslutning.

1. I ExpressRoute-sidan klickar du på **auktoriseringar** och skriv sedan en **namn** för auktorisering och klicka på **spara**.

  ![Auktoriseringar](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. När konfigurationen har sparats kan du kopiera den **resurs-ID** och **Auktoriseringsnyckeln**.

  ![Auktoriseringsnyckel](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Att ta bort en anslutningsauktorisering**

Du kan ta bort en anslutning genom att välja den **ta bort** ikonen på sidan för din anslutning.

### <a name="circuit-user-operations"></a>Kretsen användaråtgärder

Kretsen användaren måste resurs-ID och en auktoriseringsnyckeln från kretsägaren.

**Att kunna lösa in en anslutningsauktorisering**

1. Klicka på den **+ ny** knappen.

  ![Klicka på ny](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Sök efter **”anslutning”** på Marketplace, markerar du det och klicka på **skapa**.

  ![Sök efter anslutning](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Kontrollera att den **anslutningstypen** är inställd på ”ExpressRoute”.
4. Fyll i information och klicka sedan på **OK** på sidan grunderna.

  ![Grunderna sidan](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. I den **inställningar** markerar, den **virtuell nätverksgateway** och kontrollera den **Lös in auktorisering** markerar du kryssrutan.
6. Ange den **auktoriseringsnyckeln** och **Peer-kretsens URI** och ge ett namn för anslutningen. Klicka på **OK**.

  ![Sidan Inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Granska informationen i den **sammanfattning** och klicka på **OK**.

**Att släppa en anslutningsauktorisering**

Du kan släppa en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Ta bort en anslutning för att ta bort länken till ett virtuellt nätverk

Du kan ta bort en anslutning och ta bort länken till ditt VNet till en ExpressRoute-krets genom att välja den **ta bort** ikonen på sidan för din anslutning.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).