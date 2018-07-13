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
ms.openlocfilehash: 1c3103889bd11df45710cc0d6afaeeba022c9ace
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763013"
---
Du kan kontrollera att anslutningen har utförts med hjälp av följande kommandot [az network vpn-connection show](/cli/azure/network/vpn-connection#show). I exemplet avser '-name' namnet på den anslutning du vill testa. När anslutningen håller på att upprättas visas status Ansluter. När anslutningen har upprättats ändras status till ”Ansluten”.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```