---
title: 'Lägg till en virtuell nätverksgateway till ett virtuellt nätverk för ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Den här artikeln kan du lägga till VNet-gateway till ett redan skapat Resource Manager-VNet för ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097921"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln får du lägga till, ändra storlek på och ta bort en gateway för virtuellt nätverk (VNet) för ett befintliga virtuellt nätverk. Stegen för den här konfigurationen är avsedda för virtuella nätverk som har skapats med hjälp av Resource Manager-distributionsmodellen som ska användas i en ExpressRoute-konfiguration. Läs mer om virtuella nätverksgatewayer och konfigurationsinställningar för gateway för ExpressRoute, [om virtuella nätverksgatewayer för ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Innan du börjar
Kontrollera att du har installerat det senaste Azure PowerShell-cmdlet. Om du inte har installerat den senaste cmdlets som du behöver göra det innan du påbörjar konfigurationen. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gateway, kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

