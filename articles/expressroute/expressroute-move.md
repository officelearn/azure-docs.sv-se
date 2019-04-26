---
title: 'Flytta kretsar från klassisk till Resource Manager - ExpressRoute: Azure | Microsoft Docs'
description: Översikt över bryggning klassiska och Resource Manager-distributionsmodeller.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: dfa2bbc735a79555da0421f64ca644adbd7a1701
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60363844"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Flytta ExpressRoute-kretsar från den klassiska till Resource Manager-distributionsmodellen
Den här artikeln innehåller en översikt över vad det innebär att flytta en Azure ExpressRoute-krets från den klassiska till Azure Resource Manager-distributionsmodellen.

Du kan använda en enda ExpressRoute-krets för att ansluta till virtuella nätverk som distribueras både i de klassiska och Resource Manager-distributionsmodellerna. En ExpressRoute-krets, oavsett hur den skapats, kan nu länka till virtuella nätverk i båda distributionsmodellerna.

![En ExpressRoute-krets som länkar till virtuella nätverk via båda distributionsmodellerna](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>ExpressRoute-kretsar som skapas i den klassiska distributionsmodellen
ExpressRoute-kretsar som skapas i den klassiska distributionsmodellen måste flyttas till Resource Manager-distributionsmodellen innan anslutningen till båda modellerna aktiveras. Ingen anslutningsförlust eller avbrott sker när en anslutning flyttas. Alla ”circuit-to-virtual”-nätverkslänkar i den klassiska distributionsmodellen (inom samma prenumeration och mellan prenumerationer) bevaras.

När förflyttningen har slutförts fungerar ExpressRoute-kretsen likadant som en ExpressRoute-krets som har skapats i Resource Manager-distributionsmodellen. Du kan nu skapa anslutningar till virtuella nätverk i Resource Manager-distributionsmodellen.

När en ExpressRoute-krets har flyttats till Resource Manager-distributionsmodellen kan du hantera livscykeln för ExpressRoute-kretsen med hjälp av modellen. Det innebär att du kan utföra åtgärder som att lägga till/uppdatera/ta bort peerings, uppdatera kretsegenskaper (till exempel bandbredd, SKU och faktureringstyp), samt ta bort kretsar enbart i Resource Manager-distributionsmodellen. Mer information finns nedan om kretsar som skapas i Resource Manager-distributionsmodellen och om hur du kan hantera åtkomst till båda distributionsmodellerna.

Du behöver inte din anslutningsleverantör för att utföra förflyttningen.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>ExpressRoute-kretsar som skapas i Resource Manager-distributionsmodellen
Du kan aktivera ExpressRoute-kretsar som skapas i Resource Manager-distributionsmodellen till att vara tillgängliga från båda distributionsmodellerna. Alla ExpressRoute-kretsar i din prenumeration kan aktiveras för åtkomst från båda distributionsmodellerna.

* ExpressRoute-kretsar som skapades i Resource Manager-distributionsmodellen har inte åtkomst till den klassiska distributionsmodellen som standard.
* ExpressRoute-kretsar som har flyttats från den klassiska distributionsmodellen till Resource Manager-distributionsmodellen är tillgängliga från båda distributionsmodellerna som standard.
* En ExpressRoute-krets har alltid åtkomst till Resource Manager-distributionsmodellen, oavsett om den har skapats i Resource Manager eller i den klassiska distributionsmodellen. Det innebär att du kan skapa anslutningar till virtuella nätverk som skapats i Resource Manager-distributionsmodellen genom att följa instruktionerna i [Så här länkar du virtuella nätverk](expressroute-howto-linkvnet-arm.md).
* Åtkomst till den klassiska distributionsmodellen styrs av parametern **allowClassicOperations** i ExpressRoute-kretsen.

> [!IMPORTANT]
> Alla kvoter som finns dokumenterade på sidan med [tjänstbegränsningar](../azure-subscription-service-limits.md) gäller. Exempelvis kan en standardkrets ha högst 10 virtuella nätverkslänkar/anslutningar i både klassiska och Resource Manager-distributionsmodeller.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Styra åtkomst till den klassiska distributionsmodellen
Du kan aktivera en enskild ExpressRoute-krets till att länka till virtuella nätverk i båda distributionsmodellerna genom att ange parametern **allowClassicOperations** i ExpressRoute-kretsen.

Genom att ange **allowClassicOperations** till TRUE kan du länka virtuella nätverk från båda distributionsmodellerna till ExpressRoute-kretsen. Du kan länka till virtuella nätverk i den klassiska distributionsmodellen genom att följa riktlinjerna i [Så här länkar du till virtuella nätverk i den klassiska distributionsmodellen](expressroute-howto-linkvnet-classic.md). Du kan länka till virtuella nätverk i Resource Manager-distributionsmodellen genom att följa riktlinjerna i [Så här länkar du till virtuella nätverk i Resource Manager-distributionsmodellen](expressroute-howto-linkvnet-arm.md).

Genom att ange **allowClassicOperations** till FALSE blockeras kretsen från den klassiska distributionsmodellen. Alla virtuella nätverkslänkar i den klassiska distributionsmodellen bevaras dock. I det här fallet syns inte ExpressRoute-kretsen i den klassiska distributionsmodellen.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Åtgärder som stöds i den klassiska distributionsmodellen
Följande klassiska åtgärder stöds på en ExpressRoute-krets när **allowClassicOperations** har angetts till TRUE:

* Hämta information om ExpressRoute-kretsen
* Skapa/uppdatera/hämta/ta bort virtuella nätverkslänkar till klassiska virtuella nätverk
* Skapa/uppdatera/hämta/ta bort auktoriseringar för virtuella nätverkslänkar till anslutningen mellan prenumerationer

Men när **allowClassicOperations** har angetts till TRUE, du kan inte utföra följande klassiska åtgärder:

* Skapa/uppdatera/hämta/ta bort BGP-peerings (Border Gateway Protocol) för Azures privata, Azures offentliga och Microsoft-peerings
* Ta bort ExpressRoute-kretsar

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Kommunikation mellan klassiska och Resource Manager-distributionsmodeller
ExpressRoute-kretsen fungerar som en brygga mellan klassiska och Resource Manager-distributionsmodeller. Trafik mellan virtuella datorer i virtuella nätverk i den klassiska distributionsmodellen, och de i virtuella nätverk i Resource Manager-distributionsmodellen, flödar genom ExpressRoute om båda virtuella nätverken är länkade till samma ExpressRoute-krets.

Det sammanlagda dataflödet begränsas av den virtuella nätverksgatewayens dataflödeskapacitet. Trafik kommer inte in i anslutningsleverantörens nätverk eller i dina nätverk i dessa fall. Det fullständiga trafikflödet mellan de virtuella nätverken finns i Microsoft-nätverket.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Åtkomst till Azures offentliga och Microsofts peeringresurser
Du kan fortsätta att få åtkomst till resurser som normalt är tillgängliga via Azures offentliga peering och Microsofts peering utan störningar.  

## <a name="whats-supported"></a>Vad som stöds
I det här avsnittet beskrivs vad som stöds för ExpressRoute-kretsar:

* Du kan använda en enda ExpressRoute-krets för att få åtkomst till virtuella nätverk som har distribuerats i klassiska och Resource Manager-distributionsmodeller.
* Du kan flytta en ExpressRoute-krets från den klassiska till Resource Manager-distributionsmodellen. När den har flyttats fungerar ExpressRoute-kretsen likadant som andra ExpressRoute-kretsar som skapas i Resource Manager-distributionsmodellen.
* Du kan bara flytta ExpressRoute-kretsen. Kretslänkar, virtuella nätverk och VPN-gatewayer kan inte flyttas med den här åtgärden.
* När en ExpressRoute-krets har flyttats till Resource Manager-distributionsmodellen kan du hantera livscykeln för ExpressRoute-kretsen med hjälp av modellen. Det innebär att du kan utföra åtgärder som att lägga till/uppdatera/ta bort peerings, uppdatera kretsegenskaper (till exempel bandbredd, SKU och faktureringstyp), samt ta bort kretsar enbart i Resource Manager-distributionsmodellen.
* ExpressRoute-kretsen fungerar som en brygga mellan klassiska och Resource Manager-distributionsmodeller. Trafik mellan virtuella datorer i virtuella nätverk i den klassiska distributionsmodellen, och de i virtuella nätverk i Resource Manager-distributionsmodellen, flödar genom ExpressRoute om båda virtuella nätverken är länkade till samma ExpressRoute-krets.
* Anslutningen mellan prenumerationer stöds i både de klassiska och Resource Manager-distributionsmodellerna.
* När du har flyttat en ExpressRoute-krets från den klassiska modellen till Azure Resource Manager-modellen kan du [migrera de virtuella nätverk som är länkade till ExpressRoute-kretsen](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Vad som inte stöds
I det här avsnittet beskrivs vad som inte stöds för ExpressRoute-kretsar:

* Hantera livscykeln för en ExpressRoute-krets från den klassiska distributionsmodellen.
* Rollbaserad åtkomstkontroll (RBAC) stöds för den klassiska distributionsmodellen. Du kan inte utföra RBAC-kontroller på en krets i den klassiska distributionsmodellen. En administratör/medadministratör för prenumerationen kan länka eller avlänka virtuella nätverk för kretsen.

## <a name="configuration"></a>Konfiguration
Följ anvisningarna som beskrivs i [Flytta en ExpressRoute-krets från den klassiska till Resource Manager-distributionsmodellen](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Nästa steg
* [Migrera de virtuella nätverk som är länkade till ExpressRoute-kretsen från den klassiska modellen till Azure Resource Manager-modellen](expressroute-migration-classic-resource-manager.md)
* Arbetsflödesinformation finns i [ExpressRoute-kretsens etablering av arbetsflöden och kretsstatus](expressroute-workflows.md).
* Så här konfigurerar du ExpressRoute-anslutningen:
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

