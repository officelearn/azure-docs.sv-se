---
title: 'Azure-ExpressRoute: Lägg till en gateway till ett VNet: Klassiskt'
description: Konfigurera en VNet-Gateway för en klassisk distributions modell VNet med PowerShell för en ExpressRoute-konfiguration.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74928050"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln vägleder dig genom stegen för att lägga till, ändra storlek på och ta bort en virtuell nätverks-Gateway (VNet) för ett redan befintligt VNet. Stegen för den här konfigurationen är specifika för virtuella nätverk som skapats med den **klassiska distributions modellen** och som kommer att användas i en ExpressRoute-konfiguration. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om Azures distributionsmodeller**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Innan du börjar
Kontrol lera att du har installerat de Azure PowerShell-cmdletar som behövs för den här konfigurationen.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gatewayen kan du länka ditt VNet till en ExpressRoute-krets. Se [Länka en Virtual Network till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md).

