---
title: 'VPN Gateway: ändra gatewayens IP-adress inställningar: Azure Portal'
description: Den här artikeln vägleder dig genom att ändra IP-adressprefix för din lokala nätverksgateway med hjälp av Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fa43df8c4f17bff4e97d999c6653bdcb045bfec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84985217"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Ändra inställningar för lokal nätverksgateway med Azure-portalen

Ibland ändras inställningarna för din lokala nätverksgateway AddressPrefix eller GatewayIPAddress. Den här artikeln visar hur du ändrar inställningarna för din lokala nätverksgateway. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i listan nedan:

Innan du tar bort anslutningen kanske du vill ladda ned konfigurationen för dina anslutande enheter för att kunna hämta det definierade PSK-nätverket. På så sätt behöver du inte omdefiniera den på den andra sidan.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

När du ändrar IP-adressprefix är de steg du följer beroende av om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Ändra IP-adressen för gatewayen

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. När du ändrar den offentliga IP-adressen beror stegen du följer på om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan verifiera din gateway-anslutning. Se [Verifiera en gateway-anslutning](vpn-gateway-verify-connection-resource-manager.md).
