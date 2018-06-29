---
title: Ta bort ett kluster i Azure Kubernetes Service (AKS)
description: Ta bort och AKS kluster med CLI eller Azure-portalen.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 66dcebb702695a6601f6ed17b85a04d5bb4e01f6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100051"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Ta bort ett kluster i Azure Kubernetes Service (AKS)

När du tar bort ett Azure Kubernetes Service-kluster, den resursgrupp som klustret har distribuerats finns kvar, men alla AKS-relaterade resurser tas bort. Det här dokumentet beskrivs hur du tar bort ett AKS kluster med hjälp av Azure CLI och Azure-portalen.

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
| `--resource-group` `-g` | Namnet på resursgruppen Azure Kubernetes Service. | ja |
| `--no-wait` | Vänta inte tidskrävande-åtgärd ska slutföras. | nej |
| `--yes` `-y` | Fråga inte efter bekräftelse. | nej |

## <a name="azure-portal"></a>Azure Portal

Bläddra till den resursgrupp som innehåller Azure Kubernetes Service (AKS)-resurs i Azure-portalen, markera resursen och på **ta bort**. Du uppmanas att bekräfta borttagningen.

![Ta bort AKS klustret portal](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete