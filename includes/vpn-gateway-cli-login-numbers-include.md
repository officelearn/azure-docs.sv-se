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
ms.openlocfilehash: 82de8eab089e5f666e1a2ce4eab09bfd2895185b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47020089"
---
1. Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen. Mer information om att logga in finns i [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

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
