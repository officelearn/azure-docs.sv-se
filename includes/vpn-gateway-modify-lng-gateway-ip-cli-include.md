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
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121056"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Så här ändrar du ”gatewayIpAddress” för den lokala nätverksgatewayen

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Du kan ändra gatewayens IP-adress utan att ta bort en befintliga VPN-gatewayanslutning (om du har någon). För att ändra gatewayens IP-adress ersätter du värdena ”Site2” och ”TestRG1” med ditt eget med kommandot [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Verifiera att IP-adressen är rätt i utdata:

```
"gatewayIpAddress": "23.99.222.170",
```
