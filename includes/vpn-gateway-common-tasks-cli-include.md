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
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246203"
---
### <a name="to-view-local-network-gateways"></a>Visa lokala nätverksgatewayer

Du kan visa en lista över lokala nätverks-gatewayer med kommandot [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Kontrollera delade nyckelvärden

Kontrollera att värdet för den delade nyckeln är samma värde som du använde till VPN-enhetens konfiguration. Om inte ska du antingen köra anslutningen igen med värdet från enheten eller uppdatera enheten med värdet från resultatet. Värdena måste matcha. Om du vill visa den delade nyckeln använder du [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Visa den offentliga IP-adressen för VPN-gatewayen

Använd kommandot [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) för att hitta den offentliga IP-adressen till din virtuella nätverksgateway. För att läsningen ska gå lätt är utdata formaterade för att visa listan över offentliga IP-adresser i tabellformat.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
