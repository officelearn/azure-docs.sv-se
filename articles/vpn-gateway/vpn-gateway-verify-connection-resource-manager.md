---
title: 'Azure VPN Gateway: Verifiera en gatewayanslutning'
description: Den här artikeln visar hur du verifierar en virtuell VPN-gatewayanslutning för nätverk.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780155"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verifiera en VPN Gateway-anslutning

Den här artikeln visar hur du verifierar en VPN-gatewayanslutning för distributionsmodellerna för både den klassiska och Resource Manager.This article shows you how to verify a VPN gateway connection for both the classic and Resource Manager deployment models.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Om du vill verifiera en VPN-gatewayanslutning för Resurshanterarens distributionsmodell med PowerShell installerar du den senaste versionen av [Azure Resource Manager PowerShell-cmdlets](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Om du vill verifiera en VPN-gatewayanslutning för Resurshanterarens distributionsmodell med Azure CLI installerar du den senaste versionen av [CLI-kommandona](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 eller senare).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure Portal (klassisk)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klassisk)

Om du vill verifiera din VPN-gatewayanslutning för den klassiska distributionsmodellen med PowerShell installerar du de senaste versionerna av Azure PowerShell-cmdlets. Var noga med att hämta och installera [modulen Service Management.](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Använd "Add-AzureAccount" för att logga in på den klassiska distributionsmodellen.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Nästa steg

* Du kan lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
