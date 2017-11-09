---
title: "Flytta en offentlig peering på Azure ExpressRoute till Microsoft-peering | Microsoft Docs"
description: "Den här artikeln visar stegen för att flytta din offentlig peering till Microsoft-peering i ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: cherylmc
ms.openlocfilehash: 311e1de3200cd684bbec1329ebd5217b4fb3a2e2
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Flytta en offentlig peering till Microsoft-peering

ExpressRoute stöder nu Azure PaaS-tjänster, till exempel Azure storage och Azure SQL Database med hjälp av Microsoft peering med filter för routning. Du måste nu endast en routningsdomän åtkomst till Microsoft PaaS och SaaS-tjänster. Du kan dra nytta av filter routning selektivt annonserar prefixen för PaaS-tjänsten för Azure-regioner du vill använda.

Den här artikeln hjälper dig att flytta en offentlig peering konfiguration till Microsoft peering utan avbrott. Läs mer om routning domäner och peerkopplingar [ExpressRoute kretsar och routningsdomäner](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Du måste ha ExpressRoute premium-tillägg för att kunna använda Microsoft-peering. Mer information om premium-tillägg finns i [ExpressRoute vanliga frågor och svar](expressroute-faqs.md#expressroute-premium).

## <a name="before-you-begin"></a>Innan du börjar

* För att ansluta till Microsoft-peering, måste du konfigurera och hantera NAT. Anslutningsleverantören kan konfigurera och hantera NAT som en hanterad tjänst. Om du planerar att komma åt Azure PaaS och Azure SaaS-tjänster på Microsoft-peering är det viktigt att storleken NAT IP-adresspool på rätt sätt. Mer information om NAT för ExpressRoute finns i [NAT krav för Microsoft-peering](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Om du har för närvarande en nätverket åtkomstkontrollistan (ACL) för Azure PaaS Tjänsteslutpunkter när du använder Azure offentlig peering måste du kontrollera att NAT IP-adresspool som konfigurerats med ingår Microsoft-peering i åtkomstkontrollistan som konfigurerats för tjänsten slutpunkt.

För att flytta till Microsoft peering utan avbrott, måste du använda stegen i den här artikeln i den ordning som de visas.

## <a name="1-create-microsoft-peering"></a>1. Skapa Microsoft-peering

Om Microsoft-peering inte har skapats, använder du någon av följande artiklar för att skapa Microsoft-peering. Om din anslutning tjänstleverantören erbjuder Hanterade layer 3-tjänster, ber du providern anslutningen att aktivera Microsoft-peering för kretsen.

  * [Skapa Microsoft-peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Skapa Microsoft-peering med hjälp av Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Skapa Microsoft-peering med Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a>2. Verifiera Microsoft peering är aktiverat

Kontrollera att Microsoft peering är aktiverat och annonserade offentliga prefix är i tillståndet konfigurerade.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a>3. Konfigurera och ansluta ett filter för vägen till kretsen

Som standard annonserar den nya Microsoft peerkopplingar inte alla prefix tills ett filter för flödet är kopplat till kretsen. När du skapar en regel för vägen filter anger du listan över grupper för tjänsten för Azure-regioner som du vill använda för PaaS för Azure-tjänster som visas i följande skärmbild:

![Sammanfoga offentlig peering](.\media\how-to-move-peering\public.png)

Använd någon av följande artiklar för att konfigurera filter för routning.

  * [Konfigurera filter för routning för Microsoft-peering med hjälp av Azure portal](how-to-routefilter-portal.md)
  * [Konfigurera filter för routning för Microsoft-peering med hjälp av Azure PowerShell](how-to-routefilter-powershell.md)
  * [Konfigurera filter för routning för Microsoft-peering med Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a>4. Ta bort offentlig peering

När du har verifierat att Microsoft peering har konfigurerats och prefix som du vill använda annonseras korrekt i Microsoft-peering, du kan sedan ta bort offentlig peering. Ta bort offentlig peering genom att använda någon av följande artiklar:

  * [Ta bort offentlig Azure-peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Ta bort offentlig Azure-peering med hjälp av Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Ta bort offentlig Azure-peering med hjälp av CLI](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).