---
title: Azure virtuellt WAN partners platser | Microsoft Docs
description: Den här artikeln innehåller en lista över Azure virtuella WAN-partner och hub platser.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: f38cd0565b2e90fe0803d8e815c622e22e954a18
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409712"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuella WAN-partner och virtuella hubben platser

Den här artikeln innehåller information om virtuellt WAN-nätverk stöds regioner och partner för anslutning till virtuell hubb.

Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerade och automatiserade anslutningar mellan olika platser via Azure. Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Detta kan göras antingen manuellt eller med hjälp av providern enheter via en partner som virtuellt WAN-nätverk. Med partner enheter kan du underlätta för förenkling anslutnings-och konfigurationshantering.

Upprätta en anslutning från den lokala enheten är på ett automatiserat sätt till den virtuella hubben. En virtuell hubb är ett virtuellt nätverk med Microsoft-hanterad. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Du kan bara ha en hubb per region.

## <a name="automation"></a>Automation från anslutningspartners

Enheter som ansluter till Azure virtuellt WAN-nätverk har inbyggda automation för att ansluta. Detta anges vanligtvis upp i enhetshantering Gränssnittet (eller motsvarande), som konfigurerar anslutning och konfiguration hantering mellan VPN-enhet till en Azure virtuell hubb VPN-slutpunkt (VPN-gateway) för grenen.

Följande övergripande automation har ställts in i enheten/hantering av en konsol center:

* Behörighet för enheten att få åtkomst till Azure virtuella WAN-resursgrupp
* Ladda upp av gren enhet till Azure virtuellt WAN
* Automatisk nedladdning av Azure anslutningsinformation
* Konfigurationen för den lokala grenen enhet 

Vissa anslutningar-partner kan utöka automatisering som omfattar att skapa virtuella Azure-Hubbnätverk och VPN-Gateway. Om du vill veta mer om automation [konfigurera Automation – WAN partner](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Anslutningar via partner

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

## <a name="locations"></a>platser

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN-nätverk finns i den [virtuellt WAN vanliga frågor och svar](virtual-wan-faq.md).

* Mer information om hur du automatiserar anslutning till Azure virtuellt WAN-nätverk finns i [virtuella WAN-partner - automatisera](virtual-wan-configure-automation-providers.md).
