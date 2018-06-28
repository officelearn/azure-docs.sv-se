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
ms.openlocfilehash: 70a9e2a8f6327c0af92698b49d5b622bebba3661
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313616"
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