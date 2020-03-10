---
title: 'ExpressRoute: länka ett VNet till en krets: Azure Portal'
description: Anslut ett VNet till en Azure ExpressRoute-krets. Stegvisa instruktioner.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384278"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med hjälp av portalen
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

Den här artikeln hjälper dig att skapa en anslutning för att länka ett virtuellt nätverk till en Azure ExpressRoute-krets med Azure-portalen. De virtuella nätverk som du ansluter till din Azure ExpressRoute-krets kan antingen vara i samma prenumeration eller de kan vara en del av en annan prenumeration.

## <a name="before-you-begin"></a>Innan du börjar

* Granska kraven [](expressroute-prerequisites.md), kraven för [routning](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets.
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och ha den krets som Aktiver ATS av anslutnings leverantören.
  * Kontrollera att du har Azure privat peering har konfigurerats för din krets. Se artikeln [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md) för peering-och routningsmetod.
  * Se till att Azures privata peering har konfigurerats och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera anslutning för slutpunkt till slutpunkt.
  * Kontrollera att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ anvisningarna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType ExpressRoute, inte VPN.

* Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets som standard. Alla virtuella nätverken måste finnas i samma geopolitiska region när du använder en standard ExpressRoute-krets.

* Ett enskilt virtuellt nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd de här processerna för att skapa ett nytt anslutningsobjekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsar kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Du kan länka ett virtuellt nätverk utanför den geopolitiska regionen för ExpressRoute-kretsen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-krets om du har aktiverat ExpressRoute premium-tillägget. Läs [vanliga frågor och svar](expressroute-faqs.md) om du vill ha mer information om Premium-tillägget.

* Du kan [Visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Ansluta ett virtuellt nätverk till en krets – samma prenumeration

> [!NOTE]
> BGP-konfigurationsinformation visas inte om layer 3-providern konfigurerat din peerings. Om din krets är i ett etablerat tillstånd, bör du kunna skapa anslutningar.
>

### <a name="to-create-a-connection"></a>Skapa en anslutning

1. Se till att dina ExpressRoute-krets och Azures privata peering har konfigurerats. Följ anvisningarna i [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-arm.md). ExpressRoute-kretsen bör se ut som på följande bild:

   [![Skärm bild av ExpressRoute-krets](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Visa krets")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Du kan nu börja etablera en anslutning för att länka din virtuella nätverksgateway för ExpressRoute-kretsen. Klicka på **anslutning** > **Lägg till** för att öppna sidan **Lägg till anslutning** och konfigurera sedan värdena.

   [![Skärm bild för Lägg till anslutning](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Skärm bild för Lägg till anslutning")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
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

**Så här skapar du en anslutnings auktorisering**

Kretsägaren skapar en auktorisering. Detta resulterar i att skapa en auktoriseringsnyckeln som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgatewayer för ExpressRoute-kretsen. En auktorisering är giltig för endast en anslutning.

> [!NOTE]
> Varje anslutning kräver en separat auktorisering.
>

1. På sidan ExpressRoute klickar du på **auktoriseringar** och anger sedan ett **namn** för auktoriseringen och klickar på **Spara**.

   ![Auktoriseringar](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. När konfigurationen har sparats kopierar du **resurs-ID** och **behörighets nyckel**.

   ![Auktoriseringsnyckel](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Ta bort en auktorisering för anslutning**

Du kan ta bort en anslutning genom att välja ikonen **ta bort** på sidan för din anslutning.

### <a name="circuit-user-operations"></a>Kretsen användaråtgärder

Kretsen användaren måste resurs-ID och en auktoriseringsnyckeln från kretsägaren.

**Så här löser du en auktorisering av anslutningen**

1. Klicka på knappen **+ ny** .

   ![Klicka på ny](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Sök efter **"anslutning"** i Marketplace, markera den och klicka på **skapa**.

   ![Sök efter anslutning](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Kontrol lera att **anslutnings typen** är inställd på "ExpressRoute".
4. Fyll i informationen och klicka sedan på **OK** på sidan grundläggande.

   ![Sidan Grundläggande inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. På sidan **Inställningar** väljer du den **virtuella Nätverksgatewayen** och markerar kryss rutan **Lös in auktorisering** .
6. Ange **verifierings nyckeln** och **peer-kretsens URI** och ge anslutningen ett namn. Klicka på **OK** **Peer-kretsens URI** är resurs-ID för ExpressRoute-kretsen (som du hittar under egenskaps inställnings fönstret i ExpressRoute-kretsen).

   ![Sidan Inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Granska informationen på sidan **Sammanfattning** och klicka på **OK**.

**Så här släpper du en auktorisering för anslutning**

Du kan släppa en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Ta bort en anslutning för att ta bort länken till ett virtuellt nätverk

Du kan ta bort en anslutning och ta bort länken mellan ditt VNet och en ExpressRoute-krets genom att välja ikonen **ta bort** på sidan för din anslutning.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
