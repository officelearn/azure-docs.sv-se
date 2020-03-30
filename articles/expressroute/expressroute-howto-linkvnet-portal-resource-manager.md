---
title: 'ExpressRoute: Länka ett VNet till en krets: Azure-portal'
description: Anslut ett virtuella nätverk till en Azure ExpressRoute-krets. Hur-till steg.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272921"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Länka ett virtuellt nätverk till en ExpressRoute-krets med Azure-portalen
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

Den här artikeln hjälper dig att skapa en anslutning för att länka ett virtuellt nätverk till en Azure ExpressRoute-krets med Azure-portalen. De virtuella nätverk som du ansluter till din Azure ExpressRoute-krets kan antingen finnas i samma prenumeration eller så kan de ingå i en annan prenumeration.

## <a name="before-you-begin"></a>Innan du börjar

* Granska [förutsättningarna,](expressroute-prerequisites.md) [routningskraven](expressroute-routing.md)och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.

* Du måste ha en aktiv ExpressRoute-krets.
  * Följ instruktionerna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och ha kretsen aktiverad av din anslutningsleverantör.
  * Se till att du har Azure privat peering konfigurerad för din krets. Se [artikeln Skapa och ändra peering för en ExpressRoute-kretsartikel](expressroute-howto-routing-portal-resource-manager.md) för peering- och routningsinstruktioner.
  * Kontrollera att Azure-privat peering är konfigurerad och bgp-peering mellan nätverket och Microsoft är uppe så att du kan aktivera end-to-end-anslutning.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway skapad och fullständigt etablerad. Följ instruktionerna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType 'ExpressRoute', inte VPN.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma geopolitiska region när du använder en standard-ExpressRoute-krets.

* Ett enda virtuella nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd processen nedan för att skapa ett nytt anslutningsobjekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Du kan länka ett virtuellt nätverk utanför den geopolitiska regionen i ExpressRoute-kretsen eller ansluta ett större antal virtuella nätverk till expressroutekretsen om du har aktiverat Premium-tillägget ExpressRoute. Mer information om premiumtillägget finns i [vanliga frågor](expressroute-faqs.md) och svar.

* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) innan du börjar bättre förstå stegen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Anslut ett virtuella nätverk till en krets – samma prenumeration

> [!NOTE]
> BGP-konfigurationsinformation visas inte om layer 3-providern har konfigurerat dina peerings. Om din krets är i ett etablerat tillstånd bör du kunna skapa anslutningar.
>

### <a name="to-create-a-connection"></a>Skapa en anslutning

1. Kontrollera att din ExpressRoute-krets och azure-privat peering har konfigurerats. Följ instruktionerna i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och [Skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-arm.md). ExpressRoute-kretsen ska se ut så här:

   [![ExpressRoute krets skärmdump](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Visa krets")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Nu kan du börja etablera en anslutning för att länka din virtuella nätverksgateway till expressroutekretsen. Klicka på **Lägg** > **till** anslutning om du vill öppna sidan **Lägg till anslutning** och konfigurera sedan värdena.

   [![Lägg till skärmbild av anslutningen](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Lägg till skärmbild av anslutning")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. När anslutningen har konfigurerats visar anslutningsobjektet informationen för anslutningen.

   ![Skärmbild av anslutningsobjekt](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Anslut ett VNet till en krets - olika abonnemang

Du kan dela en ExpressRoute-krets över flera prenumerationer. Bilden nedan visar en enkel schematisk av hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Var och en av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar inom en organisation.
- Var och en av avdelningarna inom organisationen kan använda sin egen prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets för att ansluta tillbaka till ditt lokala nätverk.
- En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen och auktoriseringar som är associerade till kretsen, inklusive prenumerationer som är länkade till andra Azure Active Directory-klienter och Enterprise Agreement-registreringar.

  > [!NOTE]
  > Anslutnings- och bandbreddsavgifter för den dedikerade kretsen kommer att tillämpas på ExpressRoute-kretsägaren. Alla virtuella nätverk har samma bandbredd.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administration - Om kretsägare och kretsanvändare

"Kretsägaren" är en auktoriserad Power User av ExpressRoute-kretsresursen. Kretsägaren kan skapa auktoriseringar som kan lösas in av "kretsanvändare". Kretsanvändare är ägare av virtuella nätverksgateways som inte ligger inom samma prenumeration som ExpressRoute-kretsen. Kretsanvändare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Kretsägaren har befogenhet att ändra och återkalla auktoriseringar när som helst. Om du återkallar en auktorisering tas alla länkanslutningar bort från prenumerationen vars åtkomst återkallades.

### <a name="circuit-owner-operations"></a>Kretsägare verksamhet

**Så här skapar du en anslutningsauktorisering**

Kretsägaren skapar en auktorisering. Detta resulterar i skapandet av en auktoriseringsnyckel som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgateways till ExpressRoute-kretsen. En auktorisering gäller endast för en anslutning.

> [!NOTE]
> Varje anslutning kräver en separat auktorisering.
>

1. Klicka på **Auktoriseringar** på sidan ExpressRoute och skriv sedan ett **namn** på auktoriseringen och klicka på **Spara**.

   ![Auktoriseringar](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. När konfigurationen har sparats kopierar du **resurs-ID:t** och **auktoriseringsnyckeln**.

   ![Auktoriseringsnyckel](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Så här tar du bort en anslutningsauktorisering**

Du kan ta bort en anslutning genom att välja ikonen **Ta bort** på sidan för anslutningen.

### <a name="circuit-user-operations"></a>Kretsanvändaråtgärder

Kretsanvändaren behöver resurs-ID och en auktoriseringsnyckel från kretsägaren.

**Så här löser du in en anslutningsauktorisering**

1. Klicka på knappen **+Nytt.**

   ![Klicka på Ny](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Sök efter **"Anslutning"** på Marketplace, välj den och klicka på **Skapa**.

   ![Sök efter anslutning](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Kontrollera att **anslutningstypen** är inställd på "ExpressRoute".
4. Fyll i informationen och klicka sedan på **OK** på sidan Grunderna.

   ![Sidan Grundläggande inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Markera den **virtuella nätverksgatewayen** på sidan **Inställningar** och markera kryssrutan **Lös in auktorisering.**
6. Ange **auktoriseringsnyckeln** och **peer-krets-URI:n** och ge anslutningen ett namn. Klicka på **OK**. **Peer Circuit URI** är resurs-ID för ExpressRoute-kretsen (som du hittar under fönstret Egenskaper inställning i ExpressRoute Circuit).

   ![Sidan Inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Granska informationen på sidan **Sammanfattning** och klicka på **OK**.

**Så här frigör du ett anslutningsauktorisering**

Du kan frigöra en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Ta bort en anslutning för att ta bort länken till ett virtuella nätverk

Du kan ta bort en anslutning och ta bort länken till ett ExpressRoute-krets genom att välja ikonen **Ta bort** på sidan för anslutningen.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
