---
title: Flytta en offentlig peering på Azure ExpressRoute till Microsoft-peering | Microsoft Docs
description: Den här artikeln beskrivs stegen för att flytta din offentlig peering till Microsoft-peering i ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: 579f8874459004ef6bfa0d0794ab09333e053acb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966133"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Flytta en offentlig peering till Microsoft-peering

ExpressRoute stöder användning av Microsoft-peering med flödesfilter för Azure PaaS-tjänster, till exempel Azure Storage och Azure SQL Database. Nu behöver du endast en routningsdomän för åtkomst till Microsoft PaaS- och SaaS-tjänster. Du kan använda flödesfilter för att selektivt presentera PaaS-tjänstprefixen för Azure-regioner du vill använda.

Den här artikeln hjälper dig att flytta en offentlig peering-konfigurationen till Microsofts peering utan avbrott. Läs mer om routning domäner och peerings [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).


## <a name="before"></a>Innan du börjar

* För att ansluta till Microsoft-peering, måste du konfigurera och hantera NAT. Anslutningsleverantören kan konfigurera och hantera NAT som en hanterad tjänst. Om du planerar att få åtkomst till Azure PaaS och SaaS för Azure-tjänster på Microsoft-peering, är det viktigt att storleksanpassa NAT IP-adresspool på rätt sätt. Mer information om NAT för ExpressRoute finns i den [NAT-krav för Microsoft-peering](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Om du använder offentlig peering och för närvarande har IP-nätverk regler för offentliga IP-adresser som används för att komma åt [Azure Storage](../storage/common/storage-network-security.md) eller [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), måste du kontrollera att NAT IP-adresspoolen konfigurerats med Microsoft ingår-peering i listan över offentliga IP-adresser för Azure storage-konto eller Azure SQL-konto.

* För att flytta till Microsoft-peering utan avbrott, använder du stegen i den här artikeln i den ordning som de visas.

## <a name="create"></a>1. Skapa Microsoft-peering

Om Microsoft-peering inte har skapats, använder du någon av följande artiklar för att skapa Microsoft-peering. Om din anslutning tjänstleverantören erbjuder Hanterade layer 3-tjänster, kan du be anslutningsleverantören att aktivera Microsoft-peering för din krets.

  * [Skapa Microsoft-peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Skapa Microsoft-peering med hjälp av Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Skapa Microsoft-peering med hjälp av Azure CLI](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Verifiera Microsoft-peering är aktiverat

Kontrollera att Microsoft-peering är aktiverat och annonserade offentliga prefix har statusen konfigurerade.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurera och koppla ett flödesfilter till kretsen

Som standard annonserar nya Microsoft-peerings inte alla prefix tills ett flödesfilter ansluts till kretsen. När du skapar en flödesfilterregeln kan ange du listan över tjänstwebbgrupper för Azure-regioner som du vill använda för Azure PaaS-tjänster som du ser i följande skärmbild:

![Sammanfoga offentlig peering](./media/how-to-move-peering/public.png)

Konfigurera routningsfilter med hjälp av någon av följande artiklar:

  * [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure portal](how-to-routefilter-portal.md)
  * [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure PowerShell](how-to-routefilter-powershell.md)
  * [Konfigurera routningsfilter för Microsoft-peering med hjälp av Azure CLI](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Ta bort offentlig peering

När du har verifierat att den Microsoft-peering har konfigurerats och de prefix som du vill använda annonseras korrekt i Microsoft-peering, du kan sedan ta bort den offentliga peeringen. Ta bort offentlig peering genom att använda någon av följande artiklar:

  * [Ta bort Azures offentliga peering med hjälp av Azure portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Ta bort Azures offentliga peering med hjälp av Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Ta bort Azures offentliga peering med hjälp av CLI](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Visa peerkopplingar
  
Du kan se en lista över alla ExpressRoute-kretsar och peer-kopplingar i Azure-portalen. Mer information finns i [se Microsofts peering-information](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
