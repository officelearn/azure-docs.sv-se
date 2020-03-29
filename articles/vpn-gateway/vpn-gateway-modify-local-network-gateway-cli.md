---
title: 'VPN Gateway: Ändra gateway-IP-adressinställningar: Azure CLI'
description: I den här artikeln får du hjälp med att ändra IP-adressprefix för din lokala nätverksgateway med Hjälp av Azure CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864051"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Ändra inställningar för lokal nätverksgateway med Hjälp av Azure CLI

Ibland ändras inställningarna för adressprefixet för lokal nätverksgateway eller GATEWAY IP-adress. I den här artikeln visas hur du ändrar inställningarna för den lokala nätverksgatewayen. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Ändra gateway-IP-adressen

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan verifiera gatewayanslutningen. Se [Verifiera en gatewayanslutning](vpn-gateway-verify-connection-resource-manager.md).

