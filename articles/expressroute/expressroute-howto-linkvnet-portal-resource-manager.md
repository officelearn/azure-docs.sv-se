---
title: "Länka ett virtuellt nätverk till en ExpressRoute-krets: Azure-portalen | Microsoft Docs"
description: "Det här dokumentet innehåller en översikt över hur du länkar virtuella nätverk (Vnet) för ExpressRoute-kretsar."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: cherylmc
ms.openlocfilehash: 34caed9323ae3067d1dc17ab9c62ebf7a9be855b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

Den här artikeln hjälper dig att länka virtuella nätverk (Vnet) Azure ExpressRoute-kretsar med hjälp av Resource Manager-distributionsmodellen och Azure portal. Virtuella nätverk kan antingen vara i samma prenumeration och de kan vara en del av en annan prenumeration.

## <a name="before-you-begin"></a>Innan du börjar
* Granska de [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.
* Du måste ha en aktiv ExpressRoute-krets.
  
  * Följ instruktionerna för att [skapar du en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och ha kretsen aktiveras med anslutningsleverantören.
  * Se till att du har privat Azure-peering konfigurerats för kretsen. Finns det [konfigurera routning](expressroute-howto-routing-portal-resource-manager.md) artikel routning anvisningar.
  * Kontrollera att privat Azure-peering har konfigurerats och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera anslutning för slutpunkt till slutpunkt.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ instruktionerna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway expressroute använder GatewayType ExpressRoute, inte VPN.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste vara i samma region geopolitiska när du använder en standard ExpressRoute-krets. 
* Du kan länka ett virtuellt nätverk utanför ExpressRoute-kretsen geopolitiska regionen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-krets om du har aktiverat ExpressRoute premium-tillägg. Kontrollera den [vanliga frågor och svar](expressroute-faqs.md) för mer information om premium-tillägg.
* Du kan [visa en video](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) innan du börjar att bättre förstå stegen.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ansluta ett virtuellt nätverk med samma prenumeration till en krets

### <a name="to-create-a-connection"></a>Att skapa en anslutning

> [!NOTE]
> BGP-konfigurationsinformationen kommer inte att visas om nivå 3-providern konfigurerat din peerkopplingar. Om kretsen är i ett etablerat tillstånd, bör du kunna skapa anslutningar.
>

1. Se till att dina ExpressRoute-krets och privat Azure-peering har konfigurerats. Följ instruktionerna i [skapar du en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och [konfigurera routning](expressroute-howto-routing-arm.md). ExpressRoute-krets bör se ut som följande bild:

    ![Skärmbild för ExpressRoute-krets](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. Du kan nu börja etablera en anslutning om du vill länka din virtuella nätverksgateway till ExpressRoute-krets. Klicka på **anslutning** > **Lägg till** att öppna den **Lägg till anslutning** bladet och sedan konfigurera värden.

    ![Lägg till anslutning skärmbild](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. När anslutningen har konfigurerats, visas connection-objektet information för anslutningen.

     ![Skärmbild av anslutningen objekt](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>Ta bort en anslutning
Du kan ta bort en anslutning genom att välja den **ta bort** ikon på bladet för din anslutning.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Bilden nedan visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

![Anslutning över prenumerationer](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Var och en av mindre molnen i stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation.
- Varje avdelning inom organisationen kan använda sina egna prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets att ansluta till ditt lokala nätverk.
- En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-krets och tillstånd som är associerade med krets, inklusive prenumerationer som är länkade till andra Azure Active Directory-klienter och Enterprise-avtal registreringar. 

    > [!NOTE]
    > Anslutning och bandbredd kostnader för dedikerade kretsen tillämpas på ExpressRoute-kretsen ägare. Alla virtuella nätverk dela på samma bandbredd.
    > 
    >

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration - krets ägare och krets användare

Kretsägaren är en auktoriserad Power användare av resursen ExpressRoute-kretsen. Kretsägaren kan skapa tillstånd som kan lösas av krets-användare. Kretsen användare är ägare till virtuella nätverks-gateway som inte är inom samma prenumeration som ExpressRoute-kretsen. Kretsen användare kan lösa tillstånd (en auktorisering per virtuellt nätverk).

Kretsägaren har behörighet att ändra och återkalla tillstånd när som helst. Återkalla ett tillstånd som resulterar i alla anslutningar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Så här skapar du en anslutningsverifiering**

Kretsägaren skapar tillstånd. Detta resulterar i att skapa auktoriseringsnyckel som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgatewayerna till ExpressRoute-kretsen. Ett tillstånd som är giltig för endast en anslutning.

1. I ExpressRoute-bladet klickar du på **tillstånd** och skriv sedan ett **namn** för auktorisering och klickar på **spara**.

    ![Auktoriseringar](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. När konfigurationen har sparats kan du kopiera den **resurs-ID** och **Auktoriseringsnyckeln**.

    ![Auktoriseringsnyckeln](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Ta bort en anslutningsverifiering**

Du kan ta bort en anslutning genom att välja den **ta bort** ikon på bladet för din anslutning.

### <a name="circuit-user-operations"></a>Kretsen användaren åtgärder

Kretsen användaren måste resurs-ID och auktoriseringsnyckel från kretsägaren. 

**Lösa in en anslutningsverifiering**

1.  Klicka på den **+ ny** knappen.

    ![Klicka på ny](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.  Sök efter **”anslutning”** i Marketplace, markerar du den och klickar på **skapa**.

    ![Sök efter anslutning](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.  Kontrollera att den **anslutningstypen** är inställd på ”ExpressRoute”.


4.  Fyll i informationen och klicka sedan på **OK** i bladet grundläggande.

    ![Bladet Grundläggande inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.  I den **inställningar** bladet Välj den **virtuell nätverksgateway** och kontrollera den **lösa auktorisering** kryssrutan.

6.  Ange den **auktoriseringsnyckeln** och **Peer-krets URI** och namnge anslutningen. Klicka på **OK**.

    ![Bladet Inställningar](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. Granska informationen i den **sammanfattning** bladet och klicka på **OK**.


**Att släppa en anslutningsverifiering**

Du kan släppa tillstånd genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
