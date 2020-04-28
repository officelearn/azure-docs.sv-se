---
title: 'VPN Gateway: ändra gatewayens IP-adress inställningar: Azure CLI'
description: Den här artikeln vägleder dig genom att ändra IP-adressprefix för din lokala nätverksgateway med hjälp av Azure CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864051"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Ändra inställningar för lokal nätverksgateway med hjälp av Azure CLI

Ibland är inställningarna för det lokala Nätverksgatewayen eller IP-adressen ändring i Gateway. Den här artikeln visar hur du ändrar inställningarna för din lokala nätverksgateway. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Installera den senaste versionen av CLI-kommandona (2,0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Ändra IP-adressen för gatewayen

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan verifiera din gateway-anslutning. Se [Verifiera en gateway-anslutning](vpn-gateway-verify-connection-resource-manager.md).

