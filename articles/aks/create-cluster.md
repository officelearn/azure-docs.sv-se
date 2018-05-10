---
title: Skapa ett kluster som Azure Kubernetes Service (AKS)
description: Skapa ett AKS kluster med CLI eller Azure-portalen.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00672b6272ce9c775621e519c327c0b8368bc220
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Skapa ett kluster som Azure Kubernetes Service (AKS)

Ett kluster med Azure Kubernetes Service (AKS) kan skapas med Azure CLI eller Azure-portalen.

## <a name="azure-cli"></a>Azure CLI

Använd den [az aks skapa] [ az-aks-create] kommando för att skapa klustret AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Följande alternativ är tillgängliga med den `az aks create` kommando.

| Argumentet | Beskrivning | Krävs |
|---|---|:---:|
| `--name` `-n` | Resursnamnet för hanterat kluster. | ja |
| `--resource-group` `-g` | Namnet på resursgruppen Azure Kubernetes Service. | ja |
| `--admin-username` `-u` | Användarnamn för Linux virtuella datorer.  Standard: azureuser. | nej |
| ` --client-secret` | Hemligheten som är associerad med tjänstens huvudnamn. | nej |
| `--dns-name-prefix` `-p` | DNS-prefix för kluster offentliga ip-adress. | nej |
| `--generate-ssh-keys` | Generera SSH offentliga och privata nyckelfiler om saknas. | nej |
| `--kubernetes-version` `-k` | Versionen av Kubernetes ska användas för att skapa klustret, till exempel '1.7.9' eller '1.8.2'.  Standard: 1.7.7. | nej |
| `--no-wait` | Vänta inte tidskrävande-åtgärd ska slutföras. | nej |
| `--node-count` `-c` | Standardantalet noder för nod-pooler.  Standard: 3. | nej |
| `--node-osdisk-size` | OsDisk storlek i GB för noden pool virtuell dator. | nej |
| `--node-vm-size` `-s` | Storleken på den virtuella datorn.  Standard: Standard_D1_v2. | nej |
| `--service-principal` | Tjänstens huvudnamn som används för autentisering för klustret. | nej |
| `--ssh-key-value` | SSH-nyckelfilen värde eller en nyckel filsökväg.  Standard: ~ /.ssh/id_rsa.pub. | nej |
| `--tags` | Utrymme avgränsade taggar i 'key [= värde]' format. Använd '' ta bort befintliga etiketter. | nej |

## <a name="azure-portal"></a>Azure Portal

Anvisningar om hur du distribuerar ett AKS kluster med Azure-portalen finns i Azure Kubernetes Service (AKS) [Azure portal quickstart][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
