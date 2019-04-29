---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652884"
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
