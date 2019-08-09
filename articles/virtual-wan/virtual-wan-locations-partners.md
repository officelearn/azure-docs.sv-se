---
title: Azure Virtual WAN-partners platser | Microsoft Docs
description: Den här artikeln innehåller en lista över virtuella Azure-partners och nav platser i Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: a871a527a4f8440ea3e388b1a91c19355eab3676
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879309"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuella WAN-partner och platser för virtuella hubbar

Den här artikeln innehåller information om regioner och partner för virtuella WAN-regioner som stöds för anslutning till virtuellt nav.

Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerade och automatiserade anslutningar mellan olika platser via Azure. Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Detta kan göras antingen manuellt eller genom att använda leverantörs enheter via en virtuell WAN-partner. Genom att använda partner enheter kan du enkelt använda, förenkla anslutning och konfigurations hantering.

Anslutning från den lokala enheten upprättas på ett automatiserat sätt till den virtuella hubben. En virtuell hubb är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Du kan bara ha en hubb per region.

## <a name="automation"></a>Automatisering från anslutnings partner

Enheter som ansluter till Azure Virtual WAN har inbyggd Automation för att ansluta. Detta är vanligt vis konfigurerat i användar gränssnittet för enhets hantering (eller motsvarande), som konfigurerar anslutnings-och konfigurations hanteringen mellan VPN-huvudenheten och VPN-slutpunkten för en virtuell Azure-hubb (VPN-gateway).

Följande avancerade automatiserings funktioner har kon figurer ATS i enhets konsolen/hanterings centret:

* Lämpliga behörigheter för enheten för åtkomst till Azure Virtual WAN-resurs gruppen
* Överföring av förgrenings enhet till Azure Virtual WAN
* Automatisk nedladdning av Azures anslutnings information
* Konfiguration av lokal lokal enhet 

Vissa anslutnings partner kan utöka automatiseringen så att du kan ta med att skapa Azure Virtual Hub VNet och VPN Gateway. Om du vill veta mer om automation, se [Konfigurera Automation – WAN-partner](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Anslutning via partner

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Följande partner är planerad i vår översikt för en nära framtid: Fortinet, silver-Top, Velocloud, versa, Nuage Nokia.

## <a name="locations"></a>Platser

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar om virtuella](virtual-wan-faq.md)WAN.

* Mer information om hur du automatiserar anslutningen till Azure Virtual WAN finns i [virtuella WAN-partner – så här automatiserar du](virtual-wan-configure-automation-providers.md).
