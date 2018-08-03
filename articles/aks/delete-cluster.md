---
title: Ta bort ett kluster i Azure Kubernetes Service (AKS)
description: Ta bort och AKS-kluster med CLI eller Azure-portalen.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8eab17a5c635560d9a5274eb038845238968e02
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439943"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Ta bort ett kluster i Azure Kubernetes Service (AKS)

När du tar bort ett Azure Kubernetes Service-kluster, kvar resursgruppen som klustret har distribuerats, men alla AKS-relaterade resurser tas bort. Det här dokumentet visar hur du tar bort ett AKS-kluster med hjälp av Azure CLI och Azure-portalen.

Förutom att ta bort klustret, kan den resursgrupp som du distribuerat tas bort, som tar bort AKS-klustret.

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
| `--no-wait` | Vänta inte på den tidskrävande åtgärden slutförs. | nej |
| `--yes` `-y` | Fråga inte efter bekräftelse. | nej |

## <a name="azure-portal"></a>Azure Portal

I Azure-portalen bläddrar du till resursgruppen som innehåller Azure Kubernetes Service (AKS)-resurs, Välj resursen och på **ta bort**. Du uppmanas att bekräfta borttagningen.

![Ta bort AKS-kluster-portalen](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az-aks-delete