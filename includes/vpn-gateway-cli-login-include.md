---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 2ffd8b42bf43658ba488f4c1c92bb7af5b88339e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92756071"
---
Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/) och följ anvisningarna på skärmen. Mer information om att logga in finns i [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Om du har fler än en Azure-prenumeration anger du prenumerationerna för kontot.

```azurecli
az account list --all
```

Ange den prenumeration som du vill använda.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
