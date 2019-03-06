---
title: 'Lägg till en virtuell nätverksgateway till ett virtuellt nätverk för ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Den här artikeln kan du lägga till VNet-gateway till ett redan skapat Resource Manager-VNet för ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3c91fd6140b460d29b33e7d9b1fabafbbcf99422
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406226"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln får du lägga till, ändra storlek på och ta bort en gateway för virtuellt nätverk (VNet) för ett befintliga virtuellt nätverk. Anvisningarna för den här konfigurationen gäller virtuella nätverk som har skapats med hjälp av Resource Manager-distributionsmodellen för en ExpressRoute-konfigurationen. Mer information finns i [om virtuella nätverksgatewayer för ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Innan du börjar

### <a name="working-with-powershell"></a>Arbeta med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Konfigurationen är en lista

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gateway, kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).