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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411861"
---
Du kan kontrollera att anslutningen har utförts med hjälp av följande kommandot [az network vpn-connection show](/cli/azure/network/vpn-connection). I exemplet avser '-name' namnet på den anslutning du vill testa. När anslutningen håller på att upprättas visas status Ansluter. När anslutningen har upprättats ändras status till ”Ansluten”.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
