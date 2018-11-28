---
title: 'Konfigurera en VNet-gateway för ExpressRoute med PowerShell: klassiska: Azure | Microsoft Docs'
description: Konfigurera en VNet-gateway för en klassisk distribution modellera VNet med PowerShell för en ExpressRoute-konfigurationen.
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.openlocfilehash: d0425b68f1b241bde4b2100d13d60165e5a1f1fe
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255185"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln vägleder dig igenom stegen för att lägga till, ändra storlek på och ta bort en gateway för virtuellt nätverk (VNet) för ett befintliga virtuellt nätverk. Stegen för den här konfigurationen är avsedda för virtuella nätverk som har skapats med den **klassiska distributionsmodellen** och som ska användas i en ExpressRoute-konfiguration. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Innan du börjar
Kontrollera att du har installerat Azure PowerShell-cmdlets som krävs för den här konfigurationen (1.0.2 eller senare). Om du inte har installerat cmdletarna som behöver du göra det innan du påbörjar konfigurationen. Mer information om hur du installerar Azure PowerShell finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gateway, kan du länka ditt virtuella nätverk till en ExpressRoute-krets. Se [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md).

