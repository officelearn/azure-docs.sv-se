---
title: Azure Virtual WAN-partner och platser | Microsoft-dokument
description: Den här artikeln innehåller en lista över Azure Virtual WAN-partner och hubbplatser.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 3976f4114df6222d34f19e2bb44fd05d53057321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123273"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuella WAN-partner och virtuella hubbplatser

Den här artikeln innehåller information om regioner och partner som stöds av Virtual WAN och partner för anslutning till Virtual Hub.

Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerade och automatiserade anslutningar mellan olika platser via Azure. Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Detta kan göras antingen manuellt eller genom att använda providerenheter via en Virtual WAN-partner. Med hjälp av partnerenheter kan du användarvänlighet, förenkling av anslutning och konfigurationshantering.

Anslutningen från den lokala enheten upprättas på ett automatiserat sätt till den virtuella hubben. Ett virtuellt nav är ett microsofthanteradt virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Du kan bara ha ett nav per region.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automatisering från anslutningspartner

Enheter som ansluter till Azure Virtual WAN har inbyggd automatisering för att ansluta. Detta är vanligtvis konfigurerat i användargränssnittet för enhetshantering (eller motsvarande), som ställer in anslutnings- och konfigurationshanteringen mellan VPN-grenenheten till en Azure Virtual Hub VPN-slutpunkt (VPN-gateway).

Följande automatisering på hög nivå ställs in i enhetskonsolen/hanteringscentret:

* Lämpliga behörigheter för enheten att komma åt Azure Virtual WAN Resource Group
* Överföring av filialenhet till Azure Virtual WAN
* Automatisk hämtning av Azure-anslutningsinformation
* Konfiguration av lokal filialenhet 

Vissa anslutningspartner kan utöka automatiseringen till att omfatta skapandet av Azure Virtual Hub VNet och VPN Gateway. Om du vill veta mer om automatisering läser du [Riktlinjer för automatisering för Virtual WAN-partner](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-partners"></a><a name="partners"></a>Anslutning via partner

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Följande partners är griffeltavla på vår färdplan för den närmaste framtiden: 128 Technologies, Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink och VMWare Velocloud.

## <a name="locations"></a><a name="locations"></a>Platser

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om Virtuellt WAN finns i [vanliga frågor och svar om Virtuellt WAN](virtual-wan-faq.md).

* Mer information om hur du automatiserar anslutningen till Azure Virtual WAN finns i [Riktlinjer för automatisering för Virtuella WAN-partner](virtual-wan-configure-automation-providers.md).
