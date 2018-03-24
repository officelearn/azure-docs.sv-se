---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 865f4a963dfbaa7b8392865f8b0c468734673ba0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Så här ändrar du ”gatewayIpAddress” för den lokala nätverksgatewayen

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Du kan ändra gatewayens IP-adress utan att ta bort en befintliga VPN-gatewayanslutning (om du har någon). För att ändra gatewayens IP-adress ersätter du värdena ”Site2” och ”TestRG1” med ditt eget med kommandot [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Verifiera att IP-adressen är rätt i utdata:

```
"gatewayIpAddress": "23.99.222.170",
```