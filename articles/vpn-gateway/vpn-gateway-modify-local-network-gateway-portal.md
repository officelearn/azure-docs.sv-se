---
title: "Ändra lokala nätverket gateway IP-adressprefix och VPN-Gateway IP-adress | Azure | Portal | Microsoft Docs"
description: "Den här artikeln vägleder dig genom att ändra IP-adressprefix för din lokala nätverksgateway som använder Azure portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: bdd6f90fe97408bd45a72adf58bfdc190207de46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Ändra inställningar för lokalt nätverk gateway med Azure-portalen

Inställningar för din lokala nätverksgateway AddressPrefix eller GatewayIPAddress ändras. Den här artikeln visar hur du ändrar inställningarna för lokala gateway. Du kan också ändra dessa inställningar med hjälp av en annan metod genom att välja ett annat alternativ från listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Ändra IP-adressprefix

När du ändrar IP-adressprefix beror de steg du följer på om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Ändra IP-adressen för gateway

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. När du ändrar den offentliga IP-adressen, beror de steg du följer på om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan kontrollera din gateway-anslutningen. Se [verifiera en gatewayanslutning](vpn-gateway-verify-connection-resource-manager.md).