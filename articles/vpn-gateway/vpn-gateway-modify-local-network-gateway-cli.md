---
title: Ändra IP-adressprefixet som lokala gateway- och VPN Gateway-IP-adress | Azure | CLI | Microsoft Docs
description: Den här artikeln beskriver hur du ändrar IP-adressprefixen för din lokala nätverksgateway med hjälp av Azure CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 20e8269f0ee7463dcff9a5f66e46ebd81cb3df84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652878"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Ändra gateway-inställningar för lokalt nätverk med hjälp av Azure CLI

Ibland ändra inställningarna för din lokala gateway-adressprefixet eller gatewayens IP-adress. Den här artikeln visar hur du ändrar inställningar för din lokala nätverksgateway. Du kan också ändra dessa inställningar med hjälp av en annan metod genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Innan du börjar

Installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Ändra IP-adressprefix

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Ändra gatewayens IP-adress

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan kontrollera din gateway-anslutning. Se [verifiera en gatewayanslutning](vpn-gateway-verify-connection-resource-manager.md).

