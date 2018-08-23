---
title: 'Konfigurera en VNet-gateway för ExpressRoute med PowerShell: klassiska: Azure | Microsoft Docs'
description: Konfigurera en VNet-gateway för en klassisk distribution modellera VNet med PowerShell för en ExpressRoute-konfigurationen.
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.openlocfilehash: ec7e4a326bc0d5e9393db28c83c9d859a93dc8a3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056121"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
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

