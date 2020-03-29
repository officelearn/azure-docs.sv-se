---
title: 'Azure ExpressRoute: Lägg till en gateway till ett VNet: klassiskt'
description: Konfigurera en VNet-gateway för en klassisk distributionsmodell VNet med PowerShell för en ExpressRoute-konfiguration.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928050"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell (klassiskt)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiskt – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

I den här artikeln går du igenom stegen för att lägga till, ändra storlek på och ta bort en virtuell nätverksgateway (VNet) för ett befintligt virtuellt nätverk. Stegen för den här konfigurationen är specifikt för virtuella nätverk som har skapats med den **klassiska distributionsmodellen** och som ska användas i en ExpressRoute-konfiguration. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om Azures distributionsmodeller**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Innan du börjar
Kontrollera att du har installerat azure PowerShell-cmdlets som behövs för den här konfigurationen.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gatewayen kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [Länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md).

