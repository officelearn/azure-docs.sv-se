---
title: 'Ändra gateway-IP-adressinställningar: PowerShell'
description: I den här artikeln får du hjälp med att ändra IP-adressprefix för din lokala nätverksgateway med PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: dbaef6e0c81a9230b24aa1e85e7fdc421444047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864003"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Ändra inställningar för den lokala nätverksgatewayen med PowerShell

Ibland ändras inställningarna för adressprefixet för lokala nätverksgatewayer eller GatewayIPAddress. I den här artikeln visas hur du ändrar inställningarna för den lokala nätverksgatewayen. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Ändra gateway-IP-adressen

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan verifiera gatewayanslutningen. Se [Verifiera en gatewayanslutning](vpn-gateway-verify-connection-resource-manager.md).