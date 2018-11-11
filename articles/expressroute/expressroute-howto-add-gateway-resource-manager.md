---
title: 'Lägg till en virtuell nätverksgateway till ett virtuellt nätverk för ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Den här artikeln beskriver hur du lägger till en VNet-gateway till ett redan skapat Resource Manager-VNet för ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 32e49a11b02afedf69e5aa61ca2f626ffe5a125e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239584"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln vägleder dig igenom stegen för att lägga till, ändra storlek på och ta bort en gateway för virtuellt nätverk (VNet) för ett befintliga virtuellt nätverk. Stegen för den här konfigurationen är avsedda för virtuella nätverk som har skapats med hjälp av Resource Manager-distributionsmodellen som ska användas i en ExpressRoute-konfiguration. Läs mer om virtuella nätverksgatewayer och konfigurationsinställningar för gateway för ExpressRoute, [om virtuella nätverksgatewayer för ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Innan du börjar
Kontrollera att du har installerat det senaste Azure PowerShell-cmdlet. Om du inte har installerat den senaste cmdlets som du behöver göra det innan du påbörjar konfigurationen. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gateway, kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

