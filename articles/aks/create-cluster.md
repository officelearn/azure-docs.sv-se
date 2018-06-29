---
title: Skapa ett kluster som Azure Kubernetes Service (AKS)
description: Skapa ett AKS kluster med CLI eller Azure-portalen.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 684bf44c6c3c67ce1d5c798f3406270d76a8e2fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029240"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Skapa ett kluster som Azure Kubernetes Service (AKS)

Ett kluster med Azure Kubernetes Service (AKS) kan skapas med Azure CLI eller Azure-portalen.

## <a name="azure-cli"></a>Azure CLI

Använd den [az aks skapa] [ az-aks-create] kommando för att skapa klustret AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Följande alternativ är tillgängliga med den `az aks create` kommando. Finns det [Azure CLI referens] [ az-aks-create] för AKS mer information om var och en av de här argumenten.

| Argumentet | Beskrivning | Krävs |
|---|---|:---:|
| `--name` `-n` | Resursnamnet för hanterat kluster. | ja |
| `--resource-group` `-g` | Namnet på resursgruppen Azure Kubernetes Service. | ja |
| `--admin-username` `-u` | Användarnamn för Linux virtuella datorer.  Standard: azureuser. | nej |
| `--aad-client-app-id` | (FÖRHANDSVERSION) ID för ett program med Azure Active Directory-klient av typen ”inbyggd”. | nej |
| `--aad-server-app-id` | (FÖRHANDSVERSION) ID för ett program med Azure Active Directory server av typen ”Web app/API”. | nej |
| `--aad-server-app-secret` | (FÖRHANDSVERSION) Hemligheten för ett program för Azure Active Directory-server. | nej |
| `--aad-tenant-id` | (FÖRHANDSVERSION) ID för en Azure Active Directory-klient. | nej |
| `--admin-username` `-u` | Användarkonto för att skapa på noden virtuella datorer för SSH-åtkomst.  Standard: azureuser. | nej |
| ` --client-secret` | Hemligheten som är associerad med tjänstens huvudnamn. | nej |
| `--dns-name-prefix` `-p` | DNS-prefix för kluster offentliga ip-adress. | nej |
| `--dns-service-ip` | En IP-adress som tilldelats Kubernetes DNS-tjänsten. | nej |
| `--docker-bridge-address` | IP-adress och nätmask som tilldelats Docker bridge. | nej |
| `--enable-addons` `-a` | Aktivera Kubernetes tillägg i en kommaavgränsad lista. | nej |
| `--enable-rbac` `-r` | Aktivera Kubernetes rollbaserad åtkomstkontroll. | nej |
| `--generate-ssh-keys` | Generera SSH offentliga och privata nyckelfiler om saknas. | nej |
| `--kubernetes-version` `-k` | Versionen av Kubernetes ska användas för att skapa klustret, till exempel '1.7.9' eller '1.9.6'. | nej |
| `--locaton` `-l` | Plats för resursgruppen som skapats automatiskt. | nej |
| `--max-pods` `-m` | Det maximala antalet skida distribueras till en nod. | nej |
| `--network-plugin` | Kubernetes nätverk plugin-programmet ska användas. | nej |
| `--no-ssh-key` `-x` | Använd inte eller skapa en lokal SSH-nyckel. | nej |
| `--no-wait` | Vänta inte tidskrävande-åtgärd ska slutföras. | nej |
| `--node-count` `-c` | Standardantalet noder för nod-pooler.  Standard: 3. | nej |
| `--node-osdisk-size` | OsDisk storlek i GB för noden pool virtuell dator. | nej |
| `--node-vm-size` `-s` | Storleken på den virtuella datorn.  Standard: Standard_D1_v2. | nej |
| `--pod-cidr` | Ett CIDR-notation IP-intervall som du vill tilldela baljor IP-adresser när kubenet används. | nej |
| `--service-cidr` | Ett CIDR-notation IP-intervall som du vill tilldela service-kluster IP-adresser. | nej |
| `--service-principal` | Tjänstens huvudnamn som används för autentisering för klustret. | nej |
| `--ssh-key-value` | SSH-nyckelfilen värde eller en nyckel filsökväg.  Standard: ~ /.ssh/id_rsa.pub. | nej |
| `--tags` | Utrymme avgränsade taggar i 'key [= värde]' format. Använd '' ta bort befintliga etiketter. | nej |
| `--vnet-subnet-id` | ID för ett undernät i ett befintligt virtuellt nätverk där du vill distribuera klustret. | nej |
| `--workspace-resource-id` | Resurs-ID för en befintlig Log Analytics-arbetsyta ska användas för att lagra övervakningsdata. | nej |

## <a name="azure-portal"></a>Azure Portal

Anvisningar om hur du distribuerar ett AKS kluster med Azure-portalen finns i Azure Kubernetes Service (AKS) [Azure portal quickstart][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
