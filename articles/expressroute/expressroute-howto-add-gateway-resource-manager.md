---
title: "Lägga till en virtuell nätverksgateway till ett virtuellt nätverk för ExpressRoute: PowerShell: Azure | Microsoft Docs"
description: "Den här artikeln beskriver hur du lägger till en gateway för virtuellt nätverk till ett redan har skapat Resource Manager-VNet för ExpressRoute."
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 3aeddd03e0be548933775164ae790ba208fc13ae
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln vägleder dig genom stegen för att lägga till, ändra storlek på och ta bort en gateway för virtuellt nätverk (VNet) för en befintlig VNet. Stegen för den här konfigurationen är specifikt för Vnet som har skapats med Resource Manager-distributionsmodellen som ska användas i en ExpressRoute-konfiguration. Mer information om gateways för virtuella datornätverk och gateway-konfigurationsinställningar för ExpressRoute finns [om virtuella nätverksgatewayerna expressroute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Innan du börjar
Kontrollera att du har installerat det senaste Azure PowerShell-cmdlet. Om du inte har installerat de senaste cmdletarna, måste du göra det innan du påbörjar konfigurationsstegen. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat en gateway för virtuellt nätverk kan du länka ditt VNet till en ExpressRoute-krets. Se [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

