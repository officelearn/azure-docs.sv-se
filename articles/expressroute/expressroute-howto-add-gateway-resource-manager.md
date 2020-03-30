---
title: 'Azure ExpressRoute: Lägga till en gateway till ett VNet: PowerShell'
description: Den här artikeln hjälper dig att lägga till VNet-gateway till ett redan skapat Resource Manager-VNet för ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037430"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiskt – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln hjälper dig att lägga till, ändra storlek på och ta bort en virtuell nätverksgateway (VNet) för ett befintligt virtuellt nätverk. Stegen för den här konfigurationen gäller för virtuella nätverk som har skapats med hjälp av Resurshanterarens distributionsmodell för en ExpressRoute-konfiguration. Mer information finns i [Om virtuella nätverksgateways för ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Innan du börjar

### <a name="working-with-powershell"></a>Arbeta med PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Referenslista för konfiguration

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gatewayen kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [Länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).
