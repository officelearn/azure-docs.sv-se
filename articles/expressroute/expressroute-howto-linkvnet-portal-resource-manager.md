---
title: 'ExpressRoute: länka ett VNet till en krets: Azure Portal'
description: Anslut ett VNet till en Azure ExpressRoute-krets. Anvisningar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: a4ed99d69d1c2389e73c215bccfd6112895e791d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737078"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Länka ett virtuellt nätverk till en ExpressRoute-krets med Azure-portalen
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

Den här artikeln hjälper dig att skapa en anslutning för att länka ett virtuellt nätverk till en Azure ExpressRoute-krets med hjälp av Azure Portal. De virtuella nätverk som du ansluter till din Azure ExpressRoute-krets kan antingen finnas i samma prenumeration eller ingå i en annan prenumeration.

## <a name="before-you-begin"></a>Innan du börjar

* Granska kraven [prerequisites](expressroute-prerequisites.md), kraven för [routning](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets.
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och ha den krets som Aktiver ATS av anslutnings leverantören.
  * Se till att du har konfigurerat Azures privata peering för din krets. Se artikeln [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md) för peering-och routningsmetod.
  * Se till att Azures privata peering har kon figurer ATS och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera slut punkt till slut punkt.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway som skapats och är helt etablerad. Följ anvisningarna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType "ExpressRoute", inte VPN.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma naturpolitisk region när man använder en standard ExpressRoute-krets.

* Ett enda VNet kan länkas till upp till fyra ExpressRoute-kretsar. Använd processen nedan för att skapa ett nytt anslutnings objekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan vara i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Du kan länka ett virtuellt nätverk utanför det politiska ExpressRoute-kretsen eller ansluta ett större antal virtuella nätverk till din ExpressRoute-krets om du har aktiverat ExpressRoute Premium-tillägget. Läs [vanliga frågor och svar](expressroute-faqs.md) om du vill ha mer information om Premium-tillägget.

* Du kan [Visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Ansluta ett VNet till en krets-samma prenumeration

> [!NOTE]
> BGP-konfigurationsinformation visas inte om Layer 3-providern har konfigurerat dina peer-konfigurationer. Om kretsen är i ett etablerings läge, bör du kunna skapa anslutningar.
>

### <a name="to-create-a-connection"></a>Skapa en anslutning

1. Se till att din ExpressRoute-krets och Azures privata peering har kon figurer ATS korrekt. Följ anvisningarna i [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-arm.md). Din ExpressRoute-krets bör se ut som på följande bild:

   [![Skärm bild av ExpressRoute-krets](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Visa krets")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Nu kan du starta etableringen av en anslutning för att länka din virtuella nätverksgateway till ExpressRoute-kretsen. Klicka på **anslutning**  >  **Lägg** till för att öppna sidan **Lägg till anslutning** och konfigurera sedan värdena.

   [![Skärm bild för Lägg till anslutning](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Skärm bild för Lägg till anslutning")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. När anslutningen har kon figurer ATS visas informationen för anslutningen i anslutnings objektet.

   ![Skärm bild för anslutnings objekt](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Ansluta ett VNet till en krets-annan prenumeration

Du kan dela en ExpressRoute-krets över flera prenumerationer. Figuren nedan visar en enkel Schematisk över hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Vart och ett av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar i en organisation.
- Varje avdelning inom organisationen kan använda sin egen prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets för att ansluta tillbaka till ditt lokala nätverk.
- En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen och-auktoriseringarna som är kopplade till kretsen, inklusive prenumerationer som är kopplade till andra Azure Active Directory klienter och Enterprise-avtal registreringar.

  > [!NOTE]
  > Anslutnings-och bandbredds avgifter för den dedikerade kretsen kommer att användas för ExpressRoute-kretsens ägare. Alla virtuella nätverk delar samma bandbredd.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administration – om krets ägare och krets användare

Krets ägaren är en behörig privilegie rad användare av ExpressRoute-krets resursen. Krets ägaren kan skapa auktoriseringar som kan lösas av "krets användare". Krets användare är ägare till virtuella nätverksgateway som inte ingår i samma prenumeration som ExpressRoute-kretsen. Krets användare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Krets ägaren har möjlighet att ändra och återkalla auktorisering när som helst. Att återkalla ett auktoriserings resultat i alla länk anslutningar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Krets ägare åtgärder

**Så här skapar du en anslutnings auktorisering**

Krets ägaren skapar en auktorisering. Detta resulterar i att en auktoriseringsregel skapas som kan användas av en krets användare för att ansluta sina virtuella nätverksgateway till ExpressRoute-kretsen. En auktorisering är endast giltig för en anslutning.

> [!NOTE]
> Varje anslutning kräver en separat auktorisering.
>

1. På sidan ExpressRoute klickar du på **auktoriseringar** och anger sedan ett **namn** för auktoriseringen och klickar på **Spara**.

   ![Auktoriseringar](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. När konfigurationen har sparats kopierar du **resurs-ID** och **behörighets nyckel**.

   ![Auktoriseringsregel](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Ta bort en auktorisering för anslutning**

Du kan ta bort en anslutning genom att välja ikonen **ta bort** på sidan för din anslutning.

### <a name="circuit-user-operations"></a>Krets användar åtgärder

Krets användaren behöver resurs-ID och en auktoriseringskod från krets ägaren.

**Så här löser du en auktorisering av anslutningen**

1. Klicka på knappen **+ ny** .

   ![Klicka på ny](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Sök efter **"anslutning"** i Marketplace, markera den och klicka på **skapa**.

   ![Sök efter anslutning](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Kontrol lera att **anslutnings typen** är inställd på "ExpressRoute".
4. Fyll i informationen och klicka sedan på **OK** på sidan grundläggande.

   ![Sidan Grundinställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. På sidan **Inställningar** väljer du den **virtuella Nätverksgatewayen** och markerar kryss rutan **Lös in auktorisering** .
6. Ange **verifierings nyckeln** och **peer-kretsens URI** och ge anslutningen ett namn. Klicka på **OK**. **Peer-kretsens URI** är resurs-ID för ExpressRoute-kretsen (som du hittar under egenskaps inställnings fönstret i ExpressRoute-kretsen).

   ![Sidan Inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Granska informationen på sidan **Sammanfattning** och klicka på **OK**.

**Så här släpper du en auktorisering för anslutning**

Du kan frigöra en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Ta bort en anslutning för att ta bort länken till ett VNet

Du kan ta bort en anslutning och ta bort länken mellan ditt VNet och en ExpressRoute-krets genom att välja ikonen **ta bort** på sidan för din anslutning.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
