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
ms.openlocfilehash: a72f5f5a1d8c64b347185827906f15d3cb9f55ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197864"
---
1. Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen. Mer information om att logga in finns i [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

  ```azurecli
  az login
  ```
2. Om du har fler än en Azure-prenumeration anger du prenumerationerna för kontot.

  ```azurecli
  az account list --all
  ```
3. Ange den prenumeration som du vill använda.

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```