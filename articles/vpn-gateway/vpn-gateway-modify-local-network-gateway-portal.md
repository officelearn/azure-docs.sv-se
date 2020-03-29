---
title: 'VPN Gateway: Ändra gateway-IP-adressinställningar: Azure-portal'
description: I den här artikeln får du hjälp med att ändra IP-adressprefix för din lokala nätverksgateway med Hjälp av Azure-portalen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864034"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Ändra inställningar för lokal nätverksgateway med Azure-portalen

Ibland ändras inställningarna för adressprefixet för lokala nätverksgatewayer eller GatewayIPAddress. I den här artikeln visas hur du ändrar inställningarna för den lokala nätverksgatewayen. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i följande lista:

Innan du tar bort anslutningen kanske du vill hämta konfigurationen för dina anslutande enheter för att få den definierade PSK.Before you delete the connection, you may want to download the configuration for your connecting devices for to get the defined PSK. På så sätt behöver du inte omdefiniera det på andra sidan.

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

När du ändrar IP-adressprefix beror de steg du följer på om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Ändra gateway-IP-adressen

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. När du ändrar den offentliga IP-adressen beror de steg du följer på om din lokala nätverksgateway har en anslutning.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan verifiera gatewayanslutningen. Se [Verifiera en gatewayanslutning](vpn-gateway-verify-connection-resource-manager.md).
