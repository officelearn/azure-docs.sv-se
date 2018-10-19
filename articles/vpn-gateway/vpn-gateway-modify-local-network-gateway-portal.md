---
title: Ändra IP-adressprefixet som lokala gateway- och VPN Gateway-IP-adress | Azure | Portalen | Microsoft Docs
description: Den här artikeln beskriver hur du ändrar IP-adressprefixen för din lokala nätverksgateway med hjälp av Azure portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 12f1f8bbcb103d0882059cadc12bc1a8b9d40bdb
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404553"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Ändra gateway-inställningar för lokalt nätverk med hjälp av Azure portal

Ibland ändra inställningarna för din lokala nätverksgateway AddressPrefix eller GatewayIPAddress. Den här artikeln visar hur du ändrar inställningar för din lokala nätverksgateway. Du kan också ändra dessa inställningar med hjälp av en annan metod genom att välja ett annat alternativ i listan nedan:

Innan du tar bort anslutningen kan du ladda ned konfigurationen för dina enheter som ansluter för att få den definierade PSK. På så sätt kan du behöver inte definiera om den på den andra sidan.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Ändra IP-adressprefix

När du ändrar IP-adressprefixen beror vilka steg som du följer på om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Ändra gatewayens IP-adress

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. När du ändrar den offentliga IP-adressen, beror vilka steg som du följer på om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan kontrollera din gateway-anslutning. Se [verifiera en gatewayanslutning](vpn-gateway-verify-connection-resource-manager.md).
