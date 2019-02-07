---
title: Verifiera en VPN-Gateway-anslutning | Microsoft Docs
description: Den här artikeln visar hur du verifierar ett virtuellt nätverk VPN Gateway-anslutning.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: bcece64a85a69c705b0f7f915dafe29123a2cd4a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765763"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verifiera en VPN-Gateway-anslutning

Den här artikeln visar hur du kan verifiera en anslutning för VPN-gateway för både klassiska och Resource Manager-distributionsmodellen.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

För att verifiera en VPN-gateway-anslutning för Resource Manager-distributionsmodellen med hjälp av PowerShell, installera den senaste versionen av den [Azure Resource Managers PowerShell-cmdlet: ar](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

För att verifiera en VPN-gateway-anslutning för Resource Manager-distributionsmodellen med Azure CLI, installera den senaste versionen av den [CLI-kommandon](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 eller senare).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure-portalen (klassisk)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klassisk)

Installera de senaste versionerna av Azure PowerShell-cmdlets för att verifiera VPN gateway-anslutningen för den klassiska distributionsmodellen med hjälp av PowerShell. Se till att ladda ned och installera den [Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) modulen. Använd Add-AzureAccount för att logga in på den klassiska distributionsmodellen.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Nästa steg

* Du kan lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
