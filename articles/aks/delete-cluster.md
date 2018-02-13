---
title: Ta bort ett Azure Container Service (AKS)-kluster
description: Ta bort och AKS kluster med CLI eller Azure-portalen.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78056288f45616eda427f8e708efc679f8a5202c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="delete-an-azure-container-service-aks-cluster"></a>Ta bort ett Azure Container Service (AKS)-kluster

När du tar bort ett Azure Container Service-kluster, den resursgrupp som klustret har distribuerats finns kvar, men alla AKS-relaterade resurser tas bort. Det här dokumentet beskrivs hur du tar bort ett AKS kluster med hjälp av Azure CLI och Azure-portalen. 

Förutom att ta bort klustret, kan den resursgrupp som du distribuerat tas bort, som tar bort klustret AKS.

## <a name="azure-cli"></a>Azure CLI

Använd den [az aks ta bort] [ az-aks-delete] kommando för att ta bort AKS-klustret.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Följande alternativ är tillgängliga med den `az aks delete` kommando.

| Argumentet | Beskrivning | Krävs |
|---|---|:---:|
| `--name` `-n` | Resursnamnet för hanterat kluster. | ja |
| `--resource-group` `-g` | Namnet på resursgruppen Azure Container Service. | ja |
| `--no-wait` | Vänta inte tidskrävande-åtgärd ska slutföras. | nej |
| `--yes` `-y` | Fråga inte efter bekräftelse. | nej |

## <a name="azure-portal"></a>Azure Portal

Bläddra till den resursgrupp som innehåller Azure Container Service (AKS)-resurs i Azure-portalen, markera resursen och på **ta bort**. Du uppmanas att bekräfta borttagningen.

![Ta bort AKS klustret portal](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete