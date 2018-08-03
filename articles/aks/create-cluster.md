---
title: Skapa ett kluster i Azure Kubernetes Service (AKS)
description: Skapa ett AKS-kluster med CLI eller Azure-portalen.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5c133c61c989bf19be3e84287cb76a7d110dccc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440481"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Skapa ett kluster i Azure Kubernetes Service (AKS)

Ett kluster i Azure Kubernetes Service (AKS) kan skapas med Azure CLI eller Azure-portalen.

## <a name="azure-cli"></a>Azure CLI

Använd den [az aks skapa] [ az-aks-create] kommando för att skapa AKS-klustret.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Följande alternativ är tillgängliga med den `az aks create` kommando. Se den [Azure CLI-referensen] [ az-aks-create] för AKS för mer information om var och en av de här argumenten.

| Argumentet | Beskrivning | Krävs |
|---|---|:---:|
| `--name` `-n` | Resursnamnet för hanterat kluster. | ja |
| `--resource-group` `-g` | Namnet på resursgruppen Azure Kubernetes Service. | ja |
| `--admin-username` `-u` | Användarnamn för Linux-datorer.  Standard: azureuser. | nej |
| `--aad-client-app-id` | (FÖRHANDSVERSION) ID för ett Azure Active Directory-klientprogram av typen ”intern”. | nej |
| `--aad-server-app-id` | (FÖRHANDSVERSION) ID för ett program med Azure Active Directory server av typen ”Web app/API”. | nej |
| `--aad-server-app-secret` | (FÖRHANDSVERSION) Hemligheten för ett program för Azure Active Directory-server. | nej |
| `--aad-tenant-id` | (FÖRHANDSVERSION) ID för en Azure Active Directory-klient. | nej |
| `--admin-username` `-u` | Användarkonto för att skapa på noden virtuella datorer för SSH-åtkomst.  Standard: azureuser. | nej |
| ` --client-secret` | Hemligheten som är associerad med tjänstens huvudnamn. | nej |
| `--dns-name-prefix` `-p` | DNS-prefix för kluster offentliga ip-adress. | nej |
| `--dns-service-ip` | En IP-adress som tilldelats Kubernetes DNS-tjänsten. | nej |
| `--docker-bridge-address` | En IP-adress och nätmask som tilldelats Docker bridge. | nej |
| `--enable-addons` `-a` | Aktivera Kubernetes-tillägg i en kommaavgränsad lista. | nej |
| `--enable-rbac` `-r` | Aktivera Kubernetes rollbaserad åtkomstkontroll. | nej |
| `--generate-ssh-keys` | Generera SSH offentliga och privata nyckelfiler om saknas. | nej |
| `--kubernetes-version` `-k` | Versionen av Kubernetes du använder för att skapa klustret, till exempel '1.7.9' eller '1.9.6'. | nej |
| `--locaton` `-l` | Plats för den automatiskt skapade resursgruppen. | nej |
| `--max-pods` `-m` | Det maximala antalet poddar som distribueras till en nod. | nej |
| `--network-plugin` | Kubernetes nätverk plugin-programmet du använder. | nej |
| `--no-ssh-key` `-x` | Använd inte eller skapa en lokal SSH-nyckel. | nej |
| `--no-wait` | Vänta inte på den tidskrävande åtgärden slutförs. | nej |
| `--node-count` `-c` | Standardantalet noder för nodpooler.  Standard: 3. | nej |
| `--node-osdisk-size` | OsDisk storlek i GB nodpool virtuell dator. | nej |
| `--node-vm-size` `-s` | Storleken på den virtuella datorn.  Standard: Standard_D1_v2. | nej |
| `--pod-cidr` | Ett CIDR-notation IP-intervall som du vill tilldela pod IP-adresser när kubenet används. | nej |
| `--service-cidr` | Ett CIDR-notation IP-intervall som du vill tilldela service-kluster IP-adresser från. | nej |
| `--service-principal` | Tjänstens huvudnamn som används för autentisering för klustret. | nej |
| `--ssh-key-value` | SSH-nyckelfil värde eller en nyckel filsökvägen.  Standard: ~ /.ssh/id_rsa.pub. | nej |
| `--tags` | Utrymme avgränsade taggar i 'key [= värde]' format. Använd '' att rensa befintliga taggar. | nej |
| `--vnet-subnet-id` | ID för ett undernät i ett befintligt VNet som att distribuera klustret. | nej |
| `--workspace-resource-id` | Resurs-ID för en befintlig Log Analytics-arbetsytan att använda för att lagra övervakningsdata. | nej |

## <a name="azure-portal"></a>Azure Portal

Anvisningar om hur du distribuerar ett AKS-kluster med Azure-portalen finns i Azure Kubernetes Service (AKS) [snabbstarten för Azure portal][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
